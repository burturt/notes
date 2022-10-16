Conditionals -- Lite

## What is a Boolean
- Primitive type
- Only 2 possible values -- true or false
- Can be declared and intialized just like int and doouble
- `boolean done = true;`
- `boolean prime = false;`

## if statements
```java
if (test) {
	<statement>;
	<statement>;
}
```
## Test operators:
| Operator | Function |
|--------|-------|
|`==` |equal to|
|`!=` |not equal to|
|`<` |less than|
|`>` |greater than|
|`<=` |less than or equal to|
|`>-` |greater than or equal to|

`3 + 2 * 2 == 9` --> false
- Equality operators are run after arithmitic but before assignment

## Overloading methods:
- 2 methods with same name, different arguments
- Compiler will use the matching arguments to run appropriate method
```java
public static int sum(int num1, int num2) {
	return num1 + num2;
}
public static int sum(int num1, int num2, int num3) {
	return num1 + num2 + num3;
}