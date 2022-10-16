Conditionals & Loops - While & For

### Review: Boolean
- primitive, only true or false
- Evaluate using operators:
```java
3 + 2 * 2 == 9
```

### While Loops:
- Use if you don't know the number of iterations
```java
while (test) {
	<statement>;
}
```

### Examples:
```java
int n = 91;
int factor = 2;
while (n % factor != 0) {
	factor++;
}
System.out.println("First factor is " + factor);
// "First factor is 7"
// Body was run 5 times
```

### For loops:
__Parts:__
1. Initialize variable.
2. Test to continue
3. Loop body
4. Update variable
__Syntax:__
```java
for (initialize variable; test; update the variable) {
	Statements;
}
```
__Rewrite above loop using for:__
```java
int n = 91;
int factor = 2;
for (int i = factor; n % factor != 0; i++) {
	factor++;
}
System.out.println("First factor is " + factor);
```