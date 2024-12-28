- First gen frameworks (PHP, ASP.NET, java servlets) (2000)
- Second gen frameworks (Django, Ruby on Rails, Laravel)
	- First to use MVC and had ORM
- Third gen: AngularJS
	- Still used MVC, agnostic to web server/db capabilities. JS in browser + templates, more universal
- Fourth gen: Angular v2, React, Vue
	- Soften MVC, focus on components rather than HTML/DOM
	- Closer to software engineering, not web dev
- Interpreting server-side scripting:
	- Module compiled into server, .e.g mod_php or mod_python
		- Deprecated
	- CGI: language interpreter opened for each request, using stdin/stdout for each request
		- *very* slow
	- fastCGI: interpreter runs as a daemon and executes as code arrives (e.g. PHP-FPM for Nginx)
```nginx
server {
	listen 80;
	server_name example.journaldev.com;
	root /var/www/html/wordpress;
	index index.php;
	location / {
		try_files $uri $uri/ /index.php$is_args$args;
	}
	location ~ \.php$ {
		fastcgi_split_path_info ^(.+\.php)(/.+)$;
		fastcgi_pass unix:/var/run/php7.2-fpm-wordpress-site.sock;
		fastcgi_index index.php;
		include fastcgi.conf;
	}
}
```
- For Python/Rby, use Nginx as a reverse proxy
	- forwards specific requests to WSGI (Web Server Gateway Interface) (e.g. gunicorn, uWSGI)
	- Flask/Django use WSGI, FastAPI used Asynchronous Server Gateway Interface (ASGI)
# Interacting with servers
- XMLHTTPRequest: note sync mode deprecated, but async still supported
	- supported by all browsers, flexible (e.g. can modify headers), async, can do cross-domain when allowed
	- cons: complex, limited error handling, security risks w/ CSRF, fewer features and controls
	- Alternatives: Axios and Fetch
- Can work with any text (html, text, json, xml)
```js
const xhr = new XMLHttpRequest();
xhr.open(http_method, url_or_api_endpoint, true);
xhr.onreadystatechange = function() {
	if (xhr.readyState === 4 && xhr.status === 200) {
		const responseData = JSON.parse(xhr.responseText); // or xhr.responseXML for XML
	} else {}
};
xhr.send();
// POST request
xhr.open("POST", url);
xhr.setRequestHeader("Content-type", "application/x-www-form-urlencoded")
xhr.send("id=123456789&type=uid");
```
- 0 UNSENT, 1 OPENED, 2 HEADERS_RECEIVED, 3 LOADING, 4 READY
## CORS
Access-Control-Allow-Origin: specifies which origins (or all) are allowed to access the resource.
Access-Control-Allow-Method: specifies the HTTP methods allowed when accessing the resource.
Access-Control-Allow-Headers: specifies which headers can be included in the request.
Access-Control-Allow-Credentials: specifies if browser should send credentials (cookies, auth headers).
## REST APIs and others
- REpresentational State Transfer
- Before REST, RPC (remote procedure calls) standard way
	- RPC: call function on remote server
	- browser packages arguments, sends as message, server runs, return value sent to browser, allows arbitrary code to be on the server, but uses lots of resources and bandwidth but only one RTT
- Developed by Roy Fielding's 2000
	- Server exports resources using unique URI names
	- REST supports CRUD: POST (C), GET (R), PUT (U), DELETE (D)
- GraphQL: similar to REST
	- Server exports schema describing resources and supported queries
	- Client specifies which properties it wants
	- Can fetch many different resoucres in one request
# Websockets
```js
let socket = new WebSocket("ws://www.example.com/socketserver");
// Callback on opening connection.
socket.onopen = function(event) {
	socket.send(JSON.stringify(request));
};
// Callback on receiving a response from server.
socket.onmessage = function(event) {
	JSON.parse(event.data);
};
```
- `socket.io` provides wrapper
# Node.js
- Based on Chrome V8
- Allows JS to run on any machine, any OS
- No browser specific APIs for HTML DOM
- Adds fs + networking
- Single-threaded execution model, though worker threads are allowed w/ shared memory
	- No concurrency issues, requires async programming + callback, not great for CPU intensive
- Electron (cross platform desktop apps), Cordova (cross platform mobile app), Ionic: UI toolkit for apps
## adding modules
- CommonJS: require
	- `let http = require('http');`
- ES6 Module: import
	- `import http from 'http';`
	- Must set extension to `.mjs` or add `"type": "module"` in package.json
```js
import http from 'http';
let httpServer = http.createServer((request, response) => {
response.writeHead(200, {
	'Content-Type': 'text/plain'});
response.write("Hello, Node\n");
response.end("PATH: " + request.url);
})
httpServer.listen(1919);
console.log("I am here!"); // immediately prints
```
- `npm init -y` to start
- `sudo npm install -g nodemon; nodemon app.js`
- 