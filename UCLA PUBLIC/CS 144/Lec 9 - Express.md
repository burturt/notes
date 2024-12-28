## Express
- Express v4 released 2014, v5 still in dev
- Express v4 does not support HTTP/2
- alternatives: Koa, Fastify (check out), Nest
- 3 functionalities: URL routing, middleware integration, view template engine integration
```js
// Requires "type" = "module" until v22
import express from 'express';
let app = express();
// Include params, can be regex as well
app.get('/name/:name', (req, res, next) => {
res.send("Hello" + req.params.name);
});
// Handle multiple methods
app.route('/course/:crsnum').get(() => {}).post(() => {})
// Handles all HTTP methods
app.all("/test" () => {})
app.listen(1919);

```
```bash
mkdir express-demo; cd express-demo
npm init -y
npm install express
node app.js
```
## Handler
- request: `.app` returns express app, `.body` returns body, `.query` returns key-value pairs from URL
- response: response to send to client, `.status(200)`, `.append(header, val)`, `.redirect(status, URL)`, `.send(body)`
	- `.send()`, `.sendFile()`, `.json({})`, `.render("template.ejs", {})`
- next: next handler to call
	- If calling next with param, calls error handler
## EJS
`<%` for control flow, `<%=` print w/ escape html, `<%-` print w/no escape
- Express.Router() creates "mini website", then `app.use("/path", router)` for route
	- Call `.get`, `.post`, etc on route, and will be subpath
### Middlware
- `express.static`: serve static files from file system
- `body-parser` to parse e.g. JSON in body
- `cookie-parser`/`cookie-session` to work with cookies
- `app.use((err, req, res, next) => {});` error handler, declare at end after all other middlware
	- Can be attached to specific paths, and multiple error handlers can be chained and called in sequence with `next(err)`
## Misc
- Create skeleton: `express --view=ejs`, `npm i` `npm start`, app.js, public/, routes/, views/