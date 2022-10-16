Nested Loops

## Control Structure:
- A syntatic structure of code that contains other statements and dictates a code's path
	- for loops, while loops, etc
- Why is a loop a control structure?
	- "Controls the flow"
	```java
	for (int i; i <= 3; i++) {
		System.out.println("Hello!");
	}
	```
	
### Print numbers 1-1000, each loop cannot execute more than 10 times!
```java
public class Loops {
	public static void main(String[] args) {
		for (int i = 0; i <= 9; i++) {
			for (int j = 0; j <= 9; j++) {
				for (int k = 0; k <= 9; k++) {
					System.out.println(k + 10 * j + 100 * i + 1);
				}
			}
		}
	}
}
```