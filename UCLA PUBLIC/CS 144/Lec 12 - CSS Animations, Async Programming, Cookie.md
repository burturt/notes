### JS Animations
```js
// All uses ms
setInterval(callback, interval, param1,...)
const timer = setTimeout(callback, interval, param1,...)
clearTimeout(timer)
clearInterval(timer)
```
### CSS Animations
```css
div .expandMe {
  transition: <property> <duration> <timing-function> <delay>;
	transition: width 3s linear 1s;
}

div .expandMe:hover {
	<property>: val;
}

div {
	animation-name: example;
	animation-duration: 4s;
	animation-delay: 1s;
	animation-iteration-count: infinite; /* repeat forever */
}

@keyframes example {
	from {background-color: red;}
	to {background-color: blue;}
}
```
# Async Programming
```js
let user = db.findOne({userid: user_id});
user.then(fullfillCallback, rejectCallback);
user.then().then().catch(errorHandler);

new Promise((error, reject) => {
	if (successCond) {
		resolve(val);
	} else {
		reject(err);
	}
});

Promise.all([p1, p2, p3]).then((values) => console.log(values)); //resolves all promises then returns only once all are done
```
# Cookies
`Set-Cookie: username=rrosario; expires=Wed, 15 Oct 2035 04:45:00 GMT; path=/mail/; domain=zimbra.cs.ucla.edu;`
- Secure cookie: only over HTTPS
- HttpOnly: cannot be accessed by JS
- Signed cookies
- Zero party data: company can ask users to volunteer data, e.g. survey on which dog you may represent
## Third party cookie replacements
1 Cookies Having Independent Partitioned State (CHIPS), partition access to cookies across sites
2 Related Web Sites (RWS), websites can define which sites are related
3 Web Storage Access API, allows iframes to request storage access permissions
4 Federated Credential Management (FedCM), allow logging into sites without sharing personal info
### JWT
- header.payload.signature
- The header contains the token type (JWT), and the algorithm
used to encode the data: HMAC, SHA256, RSA, HS256 or RS256.
The payload consists of session data, called a claim. Common claims include:
1 Issuer(iss)
2 Subject (sub)
3 Audience (aud)
4 Expiration time (exp)
5 Issued at (iat)
### User authentication
- Username, Session ID
## Storage
- Web Storage API: key/value pairs
- sessionStorage/localStorage, key/value access
- `window.localStorage.key = "val", or window.localStorage.SetItem(key, val) or window.localStorage[key] = val`
- window.sessionStorage.getItem(key), window.sessionStorage.removeItem(val)
- storage sizes are small

### IndexedDB
- IndexedDB: object-oriented db
	- Objects indexed and retrieved by a key
	- async
	- API is error prone: better to use API
- All are same origin
```js
let request = indexedDB.open("myDatabase", 1);

request.onupgradeneeded = function(event) {
	let db = event.target.result;
	let objectStore = db.createObjectStore("myObjectStore", { keyPath: "id" });
	objectStore.createIndex("nameIndex", "name", { unique: false });
};
request.onsuccess = function(event) {
	let db = event.target.result; // Database opened successfully
};

// Read items

request.onerror = function(event) { // Error occurred while opening the database
};

let request = indexedDB.open("myDatabase");

request.onsuccess = function(event) {
	let db = event.target.result;
// Database opened successfully
};

request.onerror = function(event) {
// Error occurred opening the database
};
```