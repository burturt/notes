## People
- Tim O'Reilly: Web 2.0 (dynamic interactive web)
- Gavin Wood Web3
- `tim berners-lee`: invented WWW
- `Nicola Pellow` line mode browser
- `Brendan Eich` embedded scheme into browser
- `Ryan Dahl` node.js
- Marc Andreessen and Eric Bina create mosaic (windows only), Andreessen created netscape
# Intro

- Mime types: image/png, image/jpeg, image/gif, multipart/form-data, text/plain, text/css, text/html
## Beginnings
- Tim Berners-Lee created WWW
	- Consists HTML to represent page, HTTP to exchange messages/documents, client to display and edit hypertext documents, now browser, server to host (httpd)
- HTTP: stateless, web server does not retain memory of exchange
## HTTP Versions
- 0.9: GET /mypage.html, return just HTML
- 1.0: Added headers, error status codes, content-type non-html
- 1.1: Host field for virtual hosting (cheaper hosting), keep-alive, cache controls, content negotiation (language, encoding, type)
- 2: binary, parallel unrelated requests, streams, compress headers, server push
- 3: Additional performance improvements, QUIC over UDP
## Methods
- GET, POST (may have side effect), HEAD, PUT (uploading/updating/modifying), DELETE
- OPTIONS: get capabilities, TRACE: echo back received requests, CONNECT: setup tunnel through proxies or websockets, PATCH: partial update rather than replace (PUT)
- GET / HTTP/1.1
- HTTP/1.1 200 OK
- 1xx informational, 2xx success (200 OK, 204 no content), 3xx redirect, 404 not found, 400 bad request, 401 unauthorized, 403 forbidden, 418 I'm a teapot, 429 too many requests, 500 internal server error, 502 bad gateway
## DNS
- Cache hirearchy: local UA cache, then OS DNS cache, router cache, then private LAN DNS server, ISP DNS Server
## Intermediary servers
- WBI: Web Based Intermediary
- Chained request/response
## History of Browsers
- NCSA Mosaic, UIUC 1993
- Netscape Navigator, cross-platform	
	- Created client-side JS
- Internet Explorer: licenses code for Mosaric, develops CSS
- 1996 Opera
- IE included in windows 1996
- 1999: netscape open sourced, mozilla created
- 2003: apple releases safari
- 2004-2006: firefox releases
- 2008: chrome released
# HTML Tags
- Represents content and structure, not the style
- Tags are enclosed in <>, text is anything not in <>
- Tags that are opened must be closed, `<x> --> </x>`, unless element is void like br or hr, `<br/>`
- Tag types:
	- Block-level, always start on a newline, and margin aded around
		- Always use full width available 
	- inline: does not start on new line
		- only uses as much width as needed
- Tags can have attributes, should be lowercase
- id and class attributes special: id are unique keys, class are styled in a specific way
## Tag types
- b --> strong
- i --> em
- tt --> code
- s --> del
- u --> ins
## Structure elements
- div: block level
- span: inline
- lists: ol or ul, with li
## Enriching content
- `<a href="URL">text</a>`
- `<iframe src="somead.html" width="200" height="300"/>`
- `<img src="powellcat.png" width="789" height="605"/>`
- `&lt; &gt; &quot; &nbsp; &amp;` to show characters as-is. nbsp prevents inserting line break and prevents combining spaces
## Tables
- Use ONLY for display structured data, NOT structure content
- can use rowspan and colspan to have cells occupy multiple rows or columns
```html
  <table>
    <caption>
      Mountain Biking Mileage by Bike Model and Type
    </caption>
    <colgroup>
      <!-- Works better with simpler tables -->
      <col span="2" style="text-align: left">
      <col span="6">
      <col style="text-align: right">
    </colgroup>
    <thead>
      <!-- First row contains everything except years 2020/2021 -->
      <tr>
        <th scope="col" rowspan="2">Bike Type</th>
        <th scope="col" rowspan="2">Bike</th>
        <th scope="col" rowspan="2">2019</th>
        <th scope="col" colspan="2">COVID WFH</th>
      </tr>
      <!-- Second row is mostly empty except for 2020, 2021 -->
      <tr>
        <th scope="col">2020</th>
        <th scope="col">2021</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <!-- row 1 -->
        <th scope="row" rowspan="2">Cross country</th>
        <th scope="row">Trek Fuel EX 7</th>
        <td>367</td>
        <td class="summary">532</td>
      </tr>
      <tr>
        <!-- row 2 -->
        <th scope="row">Stumpjumper FSR Comp 6 Fattie</td>
        <td>0</td>
        <td class="summary">1218</td>
      </tr>
    </tbody>
    <tfoot>
      <!-- Summary final row -->
      <th scope="row" colspan="2">TOTAL MILES</th>
      <td>367</td>
      <td>102</td>
      <td class="summary">1791</td>
    </tfoot>
  </table>
```
# HTML 5 
```html
<!DOCTYPE html>
<html>
<head><title>...</title>
	  <meta name="viewport" content="width=device-width, initial-scale=1.0">

</head>
<body>...</body>
</html>
```
## header:
- title
- link for css styles
- meta: specify character set (utf-8)
- script for js code (can be in body as well)
## History of HTML
- HTML 1.0: 18 tags, based on SGML (standard generalized markup language)
	- no frames, pixels, images, forms, tables
- HTML 2.0: html, body, head
	- images, videos, audio, meta tag, forms, tables, headers, paragraphs, lists, DTD specification
- HTML 3.2: CSS, client-side image maps (click on parts of an image), better tables and forms, frames, and basic client-side scripts
- HTML 4.01: enhanced CSS and JS, character sets, semantic markup (div/span), SEO metadata, accessibility
	- Huge step forward
- XHTML (2000)
	- Strict validation of HTML
	- merge between XML and HTML
- HTML5: revolutionary
	- semantic tags: main, header, footer, etc
	- multimedia w/o flash
	- enhanced for inputs
	- local storage APIs
	- geolocation
	- drag and drop