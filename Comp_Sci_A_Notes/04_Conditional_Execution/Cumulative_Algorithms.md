Cumulative Algorithms

### Adding 16 numbers using pseudo code:
```md
1. Initialize sum to 0
2. Loop:
	- Get new number
	- Add new number to sum
3. Return sum
```
- Actual code:
```java
import java.util.Scanner;
public static double sum() {
	double sum = 0;
	String nextNumber = "yes";
	Scanner input = new Scanner(System.in);
	while (!(nextNumber.toLowerCase().charAt(0) == 'n')) {
		System.out.print("Enter a number: ");
		sum += input.nextDouble();
		System.out.print("Continue? (y/n): ");
		nextNumber = input.next();
	}
	return sum;
}
```
* Note: only use Scanner once in your program!
### Twist: keep track of max and min numbers
- Initialize max and min to first number
- in loop, if the next number is bigger than current max, replace max, or if smaller than current min, replace min