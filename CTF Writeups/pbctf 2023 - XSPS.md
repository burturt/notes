## Challenge
The Notes app is back again: http://xsps.chal.perfect.blue/
[XSPS.zip](../_resources/XSPS.zip)
## Writeup
### Initial enumeration
- First thing I did was check the website without going into the source code: we have a home page where we can add a note with text, search through notes, and report a link to what is probably an admin bot.
- Playing around a bit, highlighting a note means it shows up on the home page directly
- First try: can we just inject a script cheese solve this?
- Nope:
	- ![Screen Shot 2023-02-19 at 10.22.16 PM.png](../_resources/Screen%20Shot%202023-02-19%20at%2010.22.16%20PM.png)
	- ![Screen Shot 2023-02-19 at 10.22.24 PM.png](../_resources/Screen%20Shot%202023-02-19%20at%2010.22.24%20PM.png)
- What's happening is that CSP, content security policy, is getting in the way. Basically, content security policy limits what kinds of code in a given page will actually execute, severity limiting our ability to execute injected javascript.
### Poking around CSP protections
- Looking closely at the page, the debugger tab reveals that there is a script being loaded in: ![Screen Shot 2023-02-19 at 10.26.45 PM.png](../_resources/Screen%20Shot%202023-02-19%20at%2010.26.45%20PM.png)
- The script is loaded in here, but using a nonce, a supposed-to-be random value that the server injects into both the http request headers and the script attribute, so that other script tags don't get run.
![Screen Shot 2023-02-19 at 10.27.59 PM.png](../_resources/Screen%20Shot%202023-02-19%20at%2010.27.59%20PM.png)
- Is the nonce actually random? Unfortionately, yes, after a quick (and my first peak) at the provided code: 
```python
@app.after_request
def add_CSP(response):
    response.headers['Content-Security-Policy'] = f"default-src 'self'; script-src 'nonce-{g.nonce}'"
    return response

@app.route('/')
def index():
    return f"""
...
    <script nonce='{g.nonce}' src="/static/js/main.js"></script>
...
```
where nonce is defined:
```python
@app.before_request
def rand_nonce():
    g.nonce = binascii.b2a_hex(os.urandom(15)).decode()
```
it is pretty clear that the nonce is random and not a place to exploit.
- Stepping back to a higher level, one of the quickest ways to determine the CSP rules and potential holes is actually using google's CSP Evaluator: https://csp-evaluator.withgoogle.com/
- Submitting it there results in this:
	- ![Screen Shot 2023-02-19 at 10.26.01 PM.png](../_resources/Screen%20Shot%202023-02-19%20at%2010.26.01%20PM.png)
	- There's the nonce from before, but what's this: base url missing?
### Exploit Attempt 2: Base URL
- So after a quick search, it turns out base is an html tag that allows you to control what the "base" url is for relative paths. For example, if you had a script like `fetch("/")` at `example.com/challenge`, normally this would fetch the content at `example.com/`, but if we specify the base url using this html tag to be something else like `burturt.com`, when the script runs next, it'll actually fetch `burturt.com/` instead of `example.com`. Maybe we can get the admin bot to work? 
- Attempt:
	- Adding a note:
		- ![xsps base injection.gif](../_resources/xsps%20base%20injection.gif)
		- :D (excuse the cloudflare page, I haven't setup my personal website yet)
	- Searching for a note:
		- ![Screen Shot 2023-02-19 at 10.38.52 PM.png](../_resources/Screen%20Shot%202023-02-19%20at%2010.38.52%20PM.png)
		- :(
- It's that darn CSP getting in the way again. When adding a note, a `POST` request is made, in essense redirecting the user across a page, so CSP isn't triggered in stopping us from going across pages. However, searching a note makes a fetch request which *does* get caught by CSP. grrr
- While I was searching the web, I stumbled across https://book.hacktricks.xyz/pentesting-web/content-security-policy-csp-bypass#missing-base-uri. That seems interesting, and in fact, when looking at the page, the script does in fact get loaded using a relative path!
![Screen Shot 2023-02-19 at 11.57.41 PM.png](../_resources/Screen%20Shot%202023-02-19%20at%2011.57.41%20PM.png)
	- Unfortionately, this does not work. Our injected HTML is loaded into the page by this very script, so once we load in the page and change the base url, the script tag has already executed.
### Actually digging around the code
When digging through the short single javascript script on the page, the hashchange listener caught my eye:
```js
window.addEventListener('hashchange', async function(){
    let search_query = JSON.parse(atob(location.hash.substring(1)));
    search_name(search_query);
});
```
This simply takes the hash value and searches for a note. In other words, we can force the admin bot to "search" for our note. In the search name portion:
```js
async function search_name(search_data){
    let should_open = search_data['open']
    let query = search_data['query']

    let notes = await get_all_notes();

    let found_note = notes.find((val) => val.note.toString().startsWith(query));
    if(found_note == undefined){
        document.body.search_result.href = '';
        document.body.search_result.text = 'NOT FOUND'
        document.body.search_result.innerHTML += '<br>'
    }

    document.body.search_result.href = `note/${found_note.name}`;
    document.body.search_result.text = 'FOUND'
    document.body.search_result.innerHTML += '<br>'
    if(should_open)document.body.search_result.click();
}
```
- So if we search and find a note and set "open" to true, then if a note is found, the page will redirect. If the note is not found, the page won't redirect. Hey, we could listen for this in an iframe!
- This code above also confirmes that the search looks for a note with the content starting with the input is correct: `let found_note = notes.find((val) => val.note.toString().startsWith(query));`
- One other observation: the admin bot DOESN'T require the url to be under the challenge domain, so we can have the admit bot visit our website!
### Exploit attempt 3: iframe trickery
- So with an iframe, while the parent can't retrieve the url of the iframe (except the initial url you set it to), you *can* listen for redirects. Since the event above only triggers on a hash **change**, we need to first create an iframe to the website, and then change the hash accordingly, see if it redirects, set back to the website base, change hash, and so on. If the page redirects, then we know that the payload starts with whatever search term we gave it.
- My terrible code implementing this: 
[test.html](../_resources/test.html)
- This code worked in firefox (btw firefox > chrome), my default browser until I tried it in chrome..
	- Turns out that Firefox deviates from the web specification and ALLOWS that behavior to occur. >:(
	- Since the admin bot is a chrome admin bot, this won't work!
### Exploit attempt 4:
- After writing up all of that code just to find out it doesn't work, I looked around for other 
