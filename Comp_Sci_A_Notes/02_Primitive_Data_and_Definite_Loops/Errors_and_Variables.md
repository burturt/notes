Errors and Variables

### Class Header:
- Begins any class defintion
- `public class <ClassName> {`
- Rules:
	- Starts with `public class`
	- Should start with a captial and always start with a letter
	- Ends with an open curly bracker

### Methods: 
- are what the program can DO
- For now, all classes must contain a main method, which starts with a method header:
	- `public static void main(String[] args) {`

### Statements:
- Steps in a method
- Always end in a semicolon
- Represents a complete order/command
- Part of a class, is executed
- Cannot be run by itself, needs to be inside a method method

### Print & Println:
- Sends text to terminal
- `System.out.println("my literal string");`
- `System.out.print(variable);`
- ln adds a line after printing, print doesn't

### Types of errors:
- Compile / Syntax:
	- Bad grammar, spelling
	- Caught by the compiler
- Logic:
	- Code that doesn't do what you want it to do
- Runtime:
	- Logic errors that cuase your program to stop running

### Variables
Steps for using a variable:
- Declare with name and type
- Initialize it: store a value into it
- Use it: use the value in the variable for something

### Declaration:
- Variable declaration: sets aside memory for storing a value
	- Variables must be declared before they can be used
- Syntax:
	- `type name;`
		- The name is an identifier
		- `int x;`
		- `double myGPA;`

### Assignment
- Assignment: stores a value into a variable
	- Value can be an expression; variable stores the result
- Syntax:
	- `name = expression/value;`
		- `=` is an assignment statement, NOT equality evaluation
		- `int x;`
		- `x = 3;`
		- `double myGPA;`
		- `myGPA = 1.0 + 2.25;`

### Using Variables
- One given a value, a variable can be used in expressions:
```java
int x;
x = 3;
System.out.println("x is " + x); // x is 3
System.out.println(5 * x - 1); //14
```
- You can assign a value more than once:
```java
int x;
x = 3;
System.out.println(x + " here"); // 3 here
x = 4 + 7;
System.out.println("now x is " + x); // now x is 11
```

### Declaration & Initialization
- Variable can be declared/initialized in one statement
- Syntax:
	- `type name = value;`
	- `double myGPA = 3.95`
	- `int x = (11 % 3) + 12; // 14` "11 mod 3 plus 12"

### Assignment and Algebra:
- Assignment uses `=`, this is NOT an algebraic equation
	- It means "store the value at right in variable at left"
	- Right side expression is evaluated first, then its result is stored in the variable at left
- What happens here?
```java
int x = 3;
x = x + 2;
```
- `x = 5`
- A variable can only store a value of its own type
	- `int x = 2.5; // ERROR: incompatible types`
- An int value can be stored in a double variable.
	- Value is converted into double
	- `double myGPA = 4;`
	- `double avg = 11 / 2;` --> `double avg = 5;`, so this will store 5 NOT 5.5
		- If you want it to store 5.5, do `double avg = (double) 11 / 2;`

### Compiler errors
- A variable can't be used until it is assigned a value
	- `int x; System.out.println(x);` // ERROR: x has no value
- You may not declare the same variable twice
	- `int x; int x;` // ERROR: x already exists

### a=? b=?
```java
int a = 15;
int b = 12; 
a = a - b;
b = a + b;
a = a - b;
b = a;

System.out.println(a + " " + b); // -12 -12
```