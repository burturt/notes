Methods and Planning Code

### History:
- Punch Cards!
	- All data input used to be via physical punch cards
	- Holes represent = 0, No hole = 1
- Bugs:
	- Bugs would physically get stuck in relays, so a saying became "there's a bug in the computer!"

### Good Programming practices:
1. **Write the pseudocode __before__ starting to write the program**
2. **Design and write the program in layers and modules**
3. Use different and meaningful names for everything
4. Initialize variables properly and completely
5. Keep the scope of variables as local as possible
6. **Minimize duplicating of code**
7. Keep your code simple!
8. Minimize use of GoTo (Broadcast) statements
9. Comment and document your code
10. **Test and debug as your proceed**
11. Test for a full range of conditions
12. **Check back to the requirements repeatedly**

## Ways to simplify Designing code:
- Decomposition: diving a problem into smaller, more manageable pieces
- Procedural decomposition: diving a whole program into a series of individual steps or actions to program 1 at a time
- Structure diagram: a way of organizing your approach to building a larger program

## Example pseudocode:
Goal:
```md
+------+
|      |
|      |
+------+
+------+
|      |
|      |
+------+
+------+
|      |
|      |
+------+
```
Pseudocode:
```
printPlusesAroundDashes:
	Print 2 pluses around dashes
printVerticalLinesAroundSpaces:
	Print 2 vertial lines around spaces
printBox:
	printPlusesAroundDashes()
	printVerticalLinesAroundSpaces()
	printVerticalLinesAroundSpaces()
	printPlusesAroundDashes()
printBox()
printBox()
printBox()
```

## Syntax for a Static Method
```java
public static <return type> <methodName> (<list of arguments>) {
	statement;
	statement;
	...
}
```
Return type: 
- void = no value returned
- Otherwise, you can return a single value of any type defined in the class