Author: CryptoCat
Challenge Text: Something weird going on at this pizza store!! https://pizzaparadise.ctf.intigriti.io 

## Challenge


![Screenshot 2024-11-16 at 1.05.10 PM.png](../_resources/Screenshot%202024-11-16%20at%201.05.10%20PM.png)

## Solving!
- Let's start by visiting the page:
![Screenshot 2024-11-16 at 1.05.14 PM.png](../_resources/Screenshot%202024-11-16%20at%201.05.14%20PM.png)
- There's like some sort of pizza place website. It has some fire looking pizzas, and claims to be "The best pizza in town!" I'm not sure where this pizza location is located because none of the links or buttons work, but I'm sure it's the best in its town.
![Screenshot 2024-11-16 at 1.10.34 PM.png](../_resources/Screenshot%202024-11-16%20at%201.10.34%20PM.png)
- Nothing on the site. What about robots.txt?
![Screenshot 2024-11-16 at 1.05.29 PM.png](../_resources/Screenshot%202024-11-16%20at%201.05.29%20PM.png)

- Oh cool. /assets/ returns a 403 forbidden, but obviously we should checkout this secret page:

![Screenshot 2024-11-16 at 1.05.35 PM.png](../_resources/Screenshot%202024-11-16%20at%201.05.35%20PM.png)
- We are given some credentials page. Let's try logging in:
![Screenshot 2024-11-16 at 1.05.51 PM.png](../_resources/Screenshot%202024-11-16%20at%201.05.51%20PM.png)

- Oh cool. It says invalid, but if we look in the network console, no network request was made. Because of that, the validation must happen client-side. A quick look at js scripts reveals:


![Screenshot 2024-11-16 at 1.05.57 PM.png](../_resources/Screenshot%202024-11-16%20at%201.05.57%20PM.png)

- The hash looks like a SHA256 hash. crackstation.net:

![Screenshot 2024-11-16 at 1.06.08 PM.png](../_resources/Screenshot%202024-11-16%20at%201.06.08%20PM.png)

- "Logging in" with the username and password reveals this page:

![Screenshot 2024-11-16 at 1.48.27 PM.png](../_resources/Screenshot%202024-11-16%20at%201.48.27%20PM.png)
- Huh. Trying to download a file reveals a path that looks like some LFI:



![Screenshot 2024-11-16 at 1.06.35 PM.png](../_resources/Screenshot%202024-11-16%20at%201.06.35%20PM.png)



- As proof of concept, let's try getting the `/etc/passwd` file. This exists world-readable on basically every linux computer, and surely they are using linux for their backend:
![Screenshot 2024-11-16 at 1.06.57 PM.png](../_resources/Screenshot%202024-11-16%20at%201.06.57%20PM.png)

- Oops - there's clearly some sort of filter. Going back to the original file path, maybe it's a prefix check of /assets/images?
![Screenshot 2024-11-16 at 1.07.08 PM.png](../_resources/Screenshot%202024-11-16%20at%201.07.08%20PM.png)

- Aha! Perfect. But where's the flag?
- My first thought is to look at the enviornment variables. On linux, we can view the enviornment variables for a given process at the `/proc/PRODID/environ` given a process ID or just use `self` for our own process. Since this acts like a file we can read from, just visiting this page results in:

![Screenshot 2024-11-16 at 1.07.59 PM.png](../_resources/Screenshot%202024-11-16%20at%201.07.59%20PM.png)

- Darn, no flag. However, we do have some paths: php has some config files in `/usr/local/etc/php`, `/var/www/html/` is the directory of the web server, `/var/log/apache2` for apache2 logs, etc
- Maybe we can try looking at files in `/var/www/html/`?


![Screenshot 2024-11-16 at 1.08.26 PM.png](../_resources/Screenshot%202024-11-16%20at%201.08.26%20PM.png)


- Nope, but we have the php file that we might be able to read from. Since php evaluates the php file before returning, any comments or variable values aren't sent to the browser. So let's get the source code of this php file:

![Screenshot 2024-11-16 at 1.08.46 PM.png](../_resources/Screenshot%202024-11-16%20at%201.08.46%20PM.png)


- Success!