- Sidenote: these are not my notes since I skipped this lecture and no zoom recording was published. They are from Ryan Yang
## Protocol
- http 1.0 (over tcp):
	 ```
	 GET classes/cs35l HTTP1.0
	 [auxiliary info]
	 Host: cs.ucla.edu

	 ```
	 - Response:
	 ```
	 Meta data, e.g. Content-Type: 50
	 Body
	 ```
	 - Then drop
 - http/2:
	 - mostly about efficiency
	 - Header compression: agreement on compression
	 - Server push (avoid polling)
	 - Pipeline: connection can be kept alive; simultaneous requests without waiting for response 
		- Issues: out of order issues (requires labeling), and browser needs out of order
	- Multiplexing: tcp connection for multiple sessions, e.g. same web server but 2 domains using one single tcp connection
- http/3
	- Fix head of the line problem: if a packet goes missing, all future packets are held back until found
	- Built on QUIC not TCP, like TCP v2 with UDP
	- More multiplexing 
# HTML
- Derivated from SGML: declarative instead of imperative
- HTML: markup that expresses document semantics w/o specifying printing details
- HTML slowly evolved from declarative back to imperative
- HTML element: `<` e.g. `<p> STUFF </p>`, `<section>`
	- Each called tags
	- Self closing (contains nothing) tag: `<br/>` (void elements)
- HTML Attribute: `<p attribute="value" id="testing">`
- Raw Text Element: contains only text, but normally elements can nest
- HTML is a tree
```html
<head>
	<meta charset="UTF-8">
</head>
<body>
	<section>
		<p>test</p>
	</section>
</body>
```
- Attributes are attached to nodes in tree
- Compression happens at a level above, increasing cpu work at server and client but decreasing network
- important: `<a>` element
	- `href="URL"` (universal resource locator)
	- default resource is current website
## Problems
- How could things go wrong?
	- Missing closing tags/extra closing tags
	- Be strict when you generate, but be generous in what you accept
		- Put tags in the last place that is still a valid document
- How do you upgrade?
	- e.g. make something that doesn't exist like a video player
	- Add new attributes
	- 0) just muddle through
	- 1) On request, send html version, servers respond with specific version, but also much more work for server
	- 2) server sends client html version of response
	- 1&2: document type definition (DTD): lists all elements and attributes and syntactic definition
		- Came out one for version 2, 3, etc
		- additional whether closing tab is allowed or not
		- Failed: web adapted much faster than standards could decide, and
	- 3) HTML5: evolving standard, committees too slow
### XML
- Bare bone html with no sematics
- Allows for sending trees over the web
- Use infrastructure that already exists
- Attempts to fix: xHTML (Strict html)]
# Document Object Model (DOM)
- Model for tree data structure
	- Nodes are elements, leaves are often text
	- Allow OOP to examine and modify trees
- Used with javascript
## Cascading style sheets
- Separate declarations into 2 categories: content (html) and presentation/style (css)
	- When rendering, uses style
	- Style is "cascading" and can come from multiple sources
		- User defined
		- From attribute itself
		- Ancestor element
- Attempts to be mostly declarative but more complex preferences
## Javascript
- Designed specifically for web browser
	- Can be hooked into html via `<script>` tag
	- More powerful and dangerous