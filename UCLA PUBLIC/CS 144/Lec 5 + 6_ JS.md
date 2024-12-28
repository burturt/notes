## Features
- high-level
- usually just-in-time compiled (similar to “interpreted”)
- weakly and dynamically typed
- prototype-based object-oriented
- is multi-paradigm
	- event-driven
	- functional
	- imperative
- Based on the ECMAScript standard
## History
- 1995 Netscape wanted to add programming language: added java applets w/ Sun Microsystems, embedded Scheme
	- Wanted language closer to java than scheme, so named JavaScript
- IE released JScript w/ CSS, very different
- ECMAScript 2, 3 released 1998 1999
	- v4 scrapped in 2000 b/c microsoft pulled out
	- Mozilla worked on E4X, ECMAScript for XML, ActionScript 3
- Chrome released V8 engine, eventually agreeing to make ECMAScript 5 2009, EMCAScript 6 in 2015
	- Added class and module notation
	- Future versions use year, e.g. EMCAScript 2013
- JavaScript based on EMCAScript, trademark Oracle
### Differences
- ES6: much cleaner
- ES5 had a lot of flaws, `use strict`
	- `use strict` stops stupidity, e.g. forcing good practice
# JS
```js
let fruits = ["banana", "pitaya", "pineapple", "coconut"];
fruits.type = "tropical";

for (const fruit in fruits) {
	console.log(fruit); // Prints 0 1 2 3 type
}
for (const fruit of fruits) { // only works on arrays, not objects
	console.log(fruit); // prints array values
}

let x = 10; // block-level scoped
const y = 10; //constant
z = 10; // creates globally scoped variable
var a = 100; // See below
```
- Types:
	- number (64-bit floating point, bitwise conver to 32 bit int)
		- NaN (0/0), Infinity (n/0)
	- bigint (suffix n, 64 bit integer), can't mix with number
	- boolean (true, false)
		- 0, -0, 0n, NaN, "", null, undefined falsy
		- `[], {}` are truthy
	- string (immutable), `.length`
	- symbol (unique, immutable, can be used for unique properties, can be used to create private properties and methods in classes, or constants). `let s1 = new Symbol("Key"); sl.description === "Key"`
	- null, undefined
		- undefined: uninitialized var, function parameter not passed, no return value
		- null is missing value
		- null == undefined is true
		- null === undefined is false
		- typeof null is object
		- typeof undefined is undefined
	- For primitives, variable name/binding points to a value, reassigining changes pointer, not value
	- Check type with `typeof x === "string"`

![Screenshot 2024-06-09 at 6.30.45 PM.png](../../_resources/Screenshot%202024-06-09%20at%206.30.45%20PM.png)

## Type coercsion
- Dynamic: type checking performed during execution, not during compilation
- Weak: types usually implicitly converted w/o user knowing
- `&&, ||` convert left to boolean if possible
- `+` everything converted to string if one is string, otherwise math
- all other arithmetic, everything coerced into number, `NaN` if can't convert
	- `"5" * 3 === 15`
	- `"2" / "3" === 0.666...`
- Number, boolean to string: String()
- String to number: Number(), parseInt(), parseFloat()
- Number to boolean: Boolean().
- Object x to string: x.toString() but there are caveats
## JSON
- `JSON.stringify(obj)`
- `JSON.parse(str)`
## Exceptions
```js
class ValueError extends Error {};
function sqrt(x) {
	if (x < 0) {
		throw new ValueError();
	}
}

let res = 0;
try {
	result = sqrt(05);
} catch (error) {
	if (error instanceof ValueError) {
		console.log("Cannot take square root");
	} else {
		throw error;
	}
} finally {
	// close db connection, etc
	// Always runs
}
```
## Variable scope
- Let/const: block scoped
- Nothing: global scope
- var: ignores code blocks, saved function scope, can be redeclared without errors, declarations of var are hoisted to top of function (but assignments, even if in same statement, are not)
- Variables use lexical scope
	- Visibility determined by code order, not function call order
- Closures: functions within functions refer to the external variables, and are packaged together
- Modern usage (before current block-level scoping):
	- Maintaining state between function calls
	- Creating factory functions and singletons (enforce only one instance)
	- Partial function evaluation and currying (nested functions)
	- Event handling
	- Timeouts and intervals
## Embedding JS
- External: `<script type="text/javascript" src="myscript.js"></script>` in head
- Inline: with `<script>` tags
- Code quality checkers: `jslint`, `jshint`, `eslint`
	- `prettify` code
## Function Methods
- `toString()`
- call
- bind
- function definitions are hoisted, and can be used before definition in same scope
## Regex
```js
let re = /ab+c/; // no quotes. Raw RegExp literal
let re2 = new RegExp("ab+c"); // string pattern

// .search returns index
'XXX abbbbbbc'.search(/ab+c/); // 4
'XXX ac'.search(/ab+c/); // -1

// .test returns true/false
/HALT/.test(str);
/halt/i.test(str);
/[Hh]alt [A-Z]/.test(str);

// .exec iterator over matches
let str = "This has 'quoted' words like 'this'";
let re = /'[^']*'/g;
re.exec(str); // Returns 'quoted'
re.exec(str); // Returns 'this'
re.exec(str): // Returns null

// Match is similar for string patterns
str.match(/'[^']*'/g); // returns array of results
str.replace(/'[^']*'/g, 'XXX'); // returns replaced string, only replaces one
```
## Objects
- Object = data + methods
```js
class Animal {
	// constructor always has this name
	constructor(habitat) {
		this.habitat = habitat;
	}
	about() { return `habitat: ${this.habitat}`}
	home() {
		console.log(`I live in ${this.habitat}.`)
	}
};
kingCow = new Animal("pasture");

class Mammal extends Animal {
	constructor(habitat, species, sound) {
		super(habitat);
		this.species = species;
		this.sound = sound;
	}
	about() {
		return `${sound} I am a ${species}. I live in the ${habitat}.`;
	}
	// home is missing. We use the base class' method.
}
let c = new Animal("pasture", "cow", "moo");
c.home();
c.about();

```
- Until ES2022, classes can only enforce present of properties that are functions, not values
	- Must add to prototype to enforce property existance, e.g. `Animal.prototype.homePlanet = "Earth"`
- No private/public fields until 2022
	- Private members should be prefixed by `_`, can make truely private prefix with `#`
- `this`:
	- When in class or object method, refers to instance or class
	- `this` bound to `currentTarget`/DOM element where event handler set
	- anywhere else, global enviornment (e.g. window for browser, global object node.js)
	- Arrow functions do not provide own "this" binding
## Modules
```js
// main.js
import { normal } from './mylibrary.js';
normal(5, 4, 2.1);

// main2.js
import * as statlib from './mylibrary.js';
statlib.normal(5, 4, 2.1);

// in mylibrary.js
export default function () { ... }

// in main.js
import func from './mylibrary.js';
```
## ...
- Rest Parameters
```js
function greet(greeting, ...names) {
	console.log(greeting);
	names.forEach(name => console.log(name));
}
greet("Aloha!", "Alice", "Bob", "Charlie");
```
- Spread Operator
```js
function sum(x, y, z) {
	return x + y + z;
}
const numbers = [1, 2, 3];
console.log(sum(...numbers));
```
## Set, Map, WeakSet, WeakMap
```js
const a = new Set([1, 2, 3]);
const b = new Map([
	[1, "one"], // [key, value]
	[2, "two"],
	[4, "four"],
]);
console.log(a.union(b)); // Set(4) {1, 2, 3, 4}
```
- Weak: must contain garbage collectable items only (no primitives)
	- If only reference is in weak, it is removed
	- weakmap: only keys applied
## Extras
- `test = item.get ?? "undeclared"` if null or undefined, replace with default value
- `catalog.nonexistant?.name`
	- If part w/ ? does not exist, whole expression evaluates to undefined instead of erroring