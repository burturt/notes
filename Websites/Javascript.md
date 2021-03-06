Javascript

Javascript

### Functions
```javascript
var test = function(x, y) {
	return x - y;
}

var testxy = test(5, 6);
```
### Arrays
```javascript
var array = ["meow", 1, "woof", 5, "caw", 255];
console.log(array[0]);

var animals = [
	["cat", 5],
	["dog", 7],
	["crow", 155]
];
console.log(animals[0][1]); // 5

console.log(array.toString());
console.log(array.length);

array.push("bark")
array.push(2)

var last_item = array.pop(); // removes last item
var first_item = array.shift(); // removes first item

array,unshift("Animal types"); // adds to beginning
```
