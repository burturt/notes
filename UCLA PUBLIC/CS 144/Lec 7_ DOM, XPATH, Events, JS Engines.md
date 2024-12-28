- Document Object Model: HTML is tree containing structure
- `window` represents root, `window.document` = `document` contains page content
- Several node types:
	- Element: HTML element + tag
	- Text: text enclosed in element between tags (child of element)
	- Attribute: associated with element but not child (not part of DOM)
## Nodes as JS Objects
parentNode: the node’s parent node.
firstChild: the node’s first child
lastChild: the node’s last child
nextSibling: the node’s following neighboring node at the same level (sibling).
previousSibling: the node’s preceding neighboring node at the same level (sibling).
childNodes: all children of the current node.
- Returns null if doesn't exist
### Metadata of nodes
- nodeName: The name of the node, either capitalized tag (e.g. P) or #text for text nodes
- nodeType: Element (1), Attribute (2), Text (3)
- nodeValue:
	- Text enclosed by the tag for text and comment nodes.
	- Attribute value for attribute nodes. (object)
	- null for everything else.
- textContent: text enclosed by two tags for Element node.
- attributes: the node’s attributes
## Getting nodes directly
- `document.getElementById("id");`
- `document.getElementsByClassName("class")`
- `document.getElementsByTagName("tag")`
- Returns HTMLCollection, not NodeList
- `document.querySelector("query")` or `document.querySelectorAll("query")`
## NodeList and HTMLCollections
- NodeList contains any node type, not just HTML elements
	- HTMLCollections are live whereas NodeLists can be static or live
	- NodeLists iterated/indexed like array, HTMLCollections cannot, but erferences using array-like indices or a key
## Nodes
textContent: get the text content of a node and its descendants as they appear in the HTML file.
innerText: get the rendered text content of a node and its descendants.
innerHTML: HTML describing all descendants
outerHTML: Similar to innerHTML but includes HTML for the current node
getAttributes() / setAttributes() as covered earlier
- Modify attributes by doing `element.className = "error"` or `element.style.color = "#000000"` (though use classes instead)
### Adding to DOM
```js
// create the node
const newElement = document.createElement("P");
const newText = document.createTextNode("Go Bruins!");
newElement.appendChild(newText);
// add it to the DOM
parent.appendChild(newElement); // or newText
// or, insert it in a specific place.
parent.insertBefore(newElement, existingSibling);
// Remove via the parent node.
theParentNode.removeChild(oldNode);
// or directly
oldNode.remove();
```
# Events
```html
<!DOCTYPE html>
<html lang="en">
<head><title>Event Example</title>
<link rel="stylesheet"type="text/css" href="colors.css">
</head>
<body>Click me!</body>
<script>
function getRandomInt(max) { return Math.floor(Math.random() * max); }
function changeColor(event) {
let colors = [ "red", "green", "blue", "purple", "orange", "pink" ];
document.body.className = colors[getRandomInt(6)];
}
document.body.addEventListener("click", changeColor);
</script>
</html>
```
- Dont use `onclick="changeColor()"`
	- style/structure/content separate, less readable (not defined in same place as function), can only bind one function with `onevent`, function is specified as string, which is `eval`'d, functions inline must be globally accessible, some security policies do not allow, bad performance if implemented with anonymous function '
## Event object
- `event.target`: target element including HTML
- `event.currentTarget`: the element that has the event listener attached to
- `event.type`: event type (click)
- Simulate events:
```js
function simulateClick{
	const event = new MouseEvent("click", {
	view: window,
	bubbles: true,
	cancelable: true,
	});
	const cb = document.getElementById("checkbox");
	cb.dispatchEvent(event);
}
```
- Uses JIT, parse, compile, execute, optimize, loop
- JS single-threaded
	- Only one event handler runs at a time
	- Documents contents never modified by 2 threads
	- While script running, browser stops responding to user input
- Loading: `document.readState`: loading, then `interactive`, then `complete` after `document.onload` event/callback called
- `window.location` current URL, edit to load another page
- `window.history` can be used to reference browsing history
- `window.history.back()` + `.forward()` to click back/forward buttons
### Bubbling
- Events propagate up
- `event.stopPropagation()` to stop parent event handlers firing
# JS Engines
- V8: Google Chrome + Node.js
- SpiderMonkey in Firefox
- Chakra in IE
- JavaScriptCore in Safari

### XPATH
- `$x(xpath)` for selector
	- `div div > p	//div//div/p[@id="id"]`, `/` starts root `//` is anywhere
#### Descendant selectors

| CSS                          | Xpath                                                    | 
| ----                         | ----                                                     | 
| `h1`                         | `//h1`                                                   | 
| `div p`                      | `//div//p`                                               | 
| `ul > li`                    | `//ul/li`                                                |
| `ul > li > a`                | `//ul/li/a`                                              |   
| `div > *`                    | `//div/*`                                                | 
| ----                         | ----                                                     | 
| `:root`                      | `/`                                                      | 
| `:root > body`               | `/body`                                                  |  

#### Attribute selectors

| CSS                          | Xpath                                                    | 
| ----                         | ----                                                     | 
| `#id`                        | `//*[@id="id"]`                                          | 
| `.class`                     | `//*[@class="class"]` *...[kinda](#class-check)*         |    
| `input[type="submit"]`       | `//input[@type="submit"]`                                |  
| `a#abc[for="xyz"]`           | `//a[@id="abc"][@for="xyz"]`                             |
| `a[rel]`                     | `//a[@rel]`                                              |   
| ----                         | ----                                                     |
| `a[href^='/']`               | `//a[starts-with(@href, '/')]`                           | 
| `a[href$='pdf']`             | `//a[ends-with(@href, '.pdf')]`                          |   
| `a[href*='://']`             | `//a[contains(@href, '://')]`                            |       
| `a[rel~='help']`             | `//a[contains(@rel, 'help')]` *...[kinda](#class-check)* |   

#### Order selectors

| CSS                          | Xpath                                                    |
| ----                         | ----                                                     | 
| `ul > li:first-of-type`      | `//ul/li[1]`                                             |
| `ul > li:nth-of-type(2)`     | `//ul/li[2]`                                             |  
| `ul > li:last-of-type`       | `//ul/li[last()]`                                        | 
| `li#id:first-of-type`        | `//li[1][@id="id"]`                                      | 
| `a:first-child`              | `//*[1][name()="a"]`                                     | 
| `a:last-child`               | `//*[last()][name()="a"]`                                | 

#### Siblings

| CSS                          | Xpath                                                    |
| ----                         | ----                                                     |
| `h1 ~ ul`                    | `//h1/following-sibling::ul`                             | 
| `h1 + ul`                    | `//h1/following-sibling::ul[1]`                          |   
| `h1 ~ #id`                   | `//h1/following-sibling::[@id="id"]`                     |   

#### Prefixes

| Prefix | Example               | What     |
| ---    | ---                   | ---      |
| `//`   | `//hr[@class='edge']` | Anywhere |
| `./`   | `./a`                 | Relative |
| `/`    | `/html/body/div`      | Root     |