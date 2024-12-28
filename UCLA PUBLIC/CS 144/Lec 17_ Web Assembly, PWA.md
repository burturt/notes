# WebAssembly
- C, C++, C#, Rust (technically also go and python)
- Use with js
- Before webassembly: CGI server-side, or special toolkits
- Goals:
	- fast, efficient, and portable (near-native speed on all playforms)
	- readable and debuggable
	- secure (sandbox)
	- doesn't break the web (play nice with other technologies)
## How it works
- Web platform: VM runs app code (JS)
	- Web APIs to control browser
	- With wasm, VM now runs wasm
	- JS can call wasm code, wasm can call js code	
- Compile code into wasm code, wasm VM is in browser, wasm creates bindings that can be called in js
- 4 components:
	- Module: the wasm code itself
	- memory: resizable ArrayBuffer for low level memory access
	- table: resizable typed array of reference to functions, etc
	- instance: module paired with state (memory + table)
## How it looks
```lisp
(module
	;; add(a, b) returns a+b
	(func $add (export "add") (param $a i32) (param $b i32) (result i32)
		(i32.add (local.get $a) (local.get $b))
	)
)
```
### Using WASB
- Porting C/C++ with Emscripten, WasmFiddle++, WasmExplorer
- Write directly in assembly level
- Write rust app and target wasb
- Using assembly Script (looks like TS)
- Emscripten:
	- Compiles C to wasm, generates glue code in js file, that can be called
	- `emcc filename.c -o foo.html`
## wasm in rust
- `yew`: create entire web app in rust
- Build part in rust use `wasm-pack` 
	- `cargo install wasm-pack`, create a new projectv `pimc`: `cargo new -lib pimc`
	- Set up dependencies in Cargo.toml
	- Write rust in src/lib.rs
	- Build with `wasm-pack build --target web`
```rust
use wasm_bindgen::prelude::*;
use rand::Rng;
// Function to compute Pi using the Monte Carlo method
#[wasm_bindgen]
pub fn compute_pi(samples: u32) -> f64 {
	let mut inside_circle = 0;
	for _ in 0..samples {
		let x: f64 = rand::thread_rng().gen();
		let y: f64 = rand::thread_rng().gen();
		if x * x + y * y <= 1.0 {
			inside_circle += 1;
		}
	}
	let pi = 4.0 * (inside_circle as f64) / (samples as f64);
	return pi;
}
```
```js
// Import our outputted wasm ES6 module
// Which, export default's, an initialization function
import init, { compute_pi } from "./pkg/pimc.js";
const runWasm = async () => {
	// Instantiate our wasm module
	const piCompute = await init("./pkg/pimc_bg.wasm");
	// Call the Add function export from wasm, save the result
	const res = piCompute.compute_pi(1000000);
	// Set the result onto the body
	document.querySelector("#output-wa").textContent = `Pi: ${res}`;
};
runWasm();
```
- File structure:
```
|-- Cargo.lock
|-- Cargo.toml
|-- index.html <-- Create a new index.html file
|-- pkg
| |-- hello_wasm.d.ts
| |-- hello_wasm.js
| |-- hello_wasm_bg.wasm
| |-- hello_wasm_bg.wasm.d.ts
| |-- package.json
|-- src
| |-- lib.rs
|-- target
|-- CACHEDIR.TAG
|-- release
|-- wasm32-unknown-unknown
```
## AssemblyScript
- `npm init`, `npm install --save-dev assemblyscript`, `npx asinit .` 
- Add code to `assembly/index.ts`, compile `npx run asbuild`, add hooks in index.html, `npm start` server
```ts
// The entry file of your WebAssembly module.
// Function to generate a random double value between 0 and 1
function randomDouble(): f64 {
	return Math.random();
}
// Function to compute pi using Monte Carlo method
export function compute_pi(samples: i32): f64 {
	let countInsideCircle: i32 = 0;
	for (let i: i32 = 0; i < samples; i++) {
		let x: f64 = randomDouble();
		let y: f64 = randomDouble();
		if (x * x + y * y <= 1.0) {
			countInsideCircle++;
		}
	}
	return 4.0 * f64(countInsideCircle) / f64(samples);
}
```
```js
import {compute_pi} from "./build/release.js";
console.log(compute_pi(100000));
```
# PWA: Progressive Web Apps
- Shares in common mobile apps
- PWAs can be installed on device
	- standalone, does not explicitly require browser but needs engine
	- operate in background and offline
	- can respond to push messages, display notifications
	- Can use entire screen
	- distributed over app stores and web
- In addition:
	- Web app manifest, explains how to install (name, icon, splashscreen, etc)
	- service worker: offline ability
		- Uses fetch handler
- Usually SPA, not required
- Progressive Enhancement: provide baseline functionality to all, give best experience to modern browsers
- `<link rel="manifest" href="manifest.json" />` in each HTML page
```json
{
	"name": "My PWA",
	"icons": [
		{
		"src": "icons/512.png",
		"type": "image/png",
		"sizes": "512x512"
		}
	]
}
```
- Must be served under HTTPS
- Use PWABuilder to package app for app store
## Best Practices
1 Adapt to all browsers (use feature detection)
2 Adapt to all devices (semantic HTML, responsive design)
3 Must provide a solid offline experience
4 Support deep links for navigating through the app
5 Make it fast
6 Make it accessible
7 Provide an app-like experience (e.g. integrate with OS, notification API)