Scope and Nesting

# Scope
The scope of a variable is defined as the part of the program in which the variable is valid (where you are allowed to use it). 
Java has rules to keep scope as small as possible, because this usually makes programs less complex.
Narrowing scope as much as possible reduces bugs by making variable usage more obvious. The more “global” a variable is, the more likely you are to mistakenly change a value that used somewhere else (causing unwanted “side effects”).

### How to determinte scope
- for loops: the scope is easy: any variable declared in the initialize part of the for loop is valid only inside the for loop itself.
- Other variable declarations are more complicated:
	1. Work backward from the variable declaration to find the first left curly brace symbol “ { ”, then find the matching right curly brace “ } ”.
	2. The variable’s scope is the section of the Java program starting at the variable declaration and ending at the right curly brace you just found.

### Where's the problem?
```java
public class ScopePractice {
	public static void main (String[] args) {
		System.out.println(“I see no variables here.”);
		for (int i = 0; i < 100; i++) {
			System.out.println(“i is “ + i);
		}
		System.out.println(“I see no variables here, either.”);
		System.out.println(“The value of i is “+ i);
    }
}
```
Problem: i is local within the for loop and cannot be accessed in the last println statement

```java
public class ScopePractice {
	public static void main (String[] args) {
		System.out.println(“Hello World.”);
		int x = 100;
		System.out.println(“The value of x is “ + x);
    }
}
```
Scope problems? No.

### Scope rules
There are some important rules about scope:
- Declare variables only for the scope in which they’re used.
- Attempting to use the variable outside of the variable’s scope is an error.
- It is also an error to use a variable from one method in a different method, even if the second method was called from inside the variable’s scope.
- You can’t have two variables with the same name with overlapping scope.
- You can have two variables with the same name as long as their scopes don’t overlap.
