## Typescript
- Javascript issues: type coercion, trivial errors only caught until execution, can pass fewer parameters than specified for a function
	- Example: `a || 0 + b || 0` --> if string, type coersion forces toString + concat
- all JS is TS
- Statically type checked
	- optional type declaration
	- strong type system
- Static type checking:
	- Makes large codebases easier to manage
	- Checking more rigid, predict what is going to happen with code
- Typescript features:
	- types
	- interfaces like in OOP, stronger OOP
	- decorators
	- generics (templates)
	- enums
	- behavior depends on flags passed to compiler
- `--save-dev`: save only as dev
### TS commands
- `tsc hello.ts` compiles ts to js, must separately run `node hello.ts`
	- `-noImplicitAny`
	- `-strictPropertyInitialization`: must initialize in constructor
### Types
- string
- number
- boolean
- `Array: number[], Array<number>, Promise<number> (generic)`
- null
- undefined
- any, void (can only be undefined or null), never (function nevre returns, e.g. throws error or infinite loop)
`let a: number = 1;`
- union type: `(number | string)` allows either
```ts
let a: number = 1;
function sayHello(name: string): void {}

let k: (x:string) => void = sayHello;
```

- ts: contextual typing
- `{ f: string, l?: string }` optional L
	- can have superset of types
- Narrowing: check type, and only call functions on the narrowing type
	- Array.isArray, typeof x === "number"
- Type assertion: `let input = document.querySelector(" ") as HTMLInputElement`: forces into narrower subclass
	- Same as `<HTMLInputElement>doc.qs("");`
### Other features
- `enum e {a = 2, b = 4, c = 6, d}; // d is 4, can set to string instead of nums`
	- Access: e.a (returns in map), e["a"], e[2] returns "a"
- Cannot pass fewer parameters to function (must specify optional adding `?` after var name)
- Rest parameters: `function f(a, ...c) {}`, parameters after the first is passed in as array for c
### Class
```ts
class Coordinate {
	x: number; // Required to declare local vars
	y: number;
	constructor(x: number = 0, y?: number) {
		this.x = x;
		this.y = 0;
		if (y !== undefined) {
			this.y = y;
		}
	}
}
```
- `protected` and `private` exists for vars/functions
```ts
class Coordinate {
	constructor(public x: number = 0, protected name: string = "") {}
}
```
- Function and constructor overloading allowed in TS, not in JS
- `super()` must be used in TS if inheriting
- js private required private, ts private are only enforced compile-time
- ts fields can be `readonly`, only modified in constructors
- Classes, methods, and fields can be `abtract`
```ts
interface Domesticated {
	name: string;
	age?: number;
}
class HouseCat extends Animal implements Domesticated {
	// must implement interface, can add getters and setters
	get name(): string (return this.name; }
	set name(name: string: {this.name = name}
}
```
### Generics
```
class Pair<T> {
	private: x: T;
	constructor(private x: T) {}
}
let p = new Pair<number>(2);

function log<T>(arg: T: void) {}
```
### Decorators
```
@sealed
class TalkingCow {}
// prevents future properties or methods being added after declaration
```
# SPA
- Single-Page Application
	- App served in one page, without scrolling (maybe components, and parts of the page overwritten with new dynamic
	- No refreshing needed
### Fetch
- `let response: Promise = fetch(URL)`
- Call response.text() or response.json() (also a Promise)
```js
fetch("/", {method: "POST",
  headers: {"Content-Type": "application.json"},
  body: JSON.stringify(data)})
.then(res => {
	if (!res.ok) throw new Error(res.status);
	return res.json();
})
.catch(() => handleError())
```
- Promise rejected only if network error, 4xx/5xx resolves normally
	- `response.ok` is false if not 200, can get .status, .statusText, .url
- Follows same origin policy
	- Bypass: proxy request
	- use CORS to allow (old JSONP deprecated)
```
<?php
function getKeywordSuggestionsFromGoogle($keyword) {
	$keywords = array();
	$data = file_get_contents('https://suggestqueries.google.com/complete/search?output=chrome&q='.urlencode($keyword));
	if (($data = json_decode($data, true)) !== null) {
		$keywords = $data[1];
	}
	return $keywords;
}
echo json_encode(getKeywordSuggestionsFromGoogle($_GET["q"]));
?>
```
### Back Button
- Often back button kicks app out of page
- Before HTML5: edit fragment identifier (anchor, `#whatever`)
	- Back button change URL, handle `hashChangeEvent`
- Now: Session History API
	- `history.pushState(object, title, url)`
	- `history.replaceState(object, title, url)`
	- When back button hit, `popState` event triggered, intercept this
# Component Based Development
- Create library of commonly used, independent modules
- Each component is generic
	- Split app into hirearchy of small, simple components, develop each components separate, combine later