Scanner_Slides

Scanner Slides

### Java's Math Methods
See table 3.2 in textbook pg 154
- Common ones: `Math.random`,`Math.sqrt`, `Math.pow`, `Math.round`
- Use "dot notation" to call methods. 
```java
public static double pokemonRandom(){ 
	return Math.random() * 100; //random returns number between 0 and 1
}
```
## Scanners:
- Setup Scanner by importing it: `import java.util.Scanner` or `import java.util.*`
- Always capitalize "Scanner"
- Name the scanner something useful (scanner, console, userInput)
- Construct with the NEW keyword, and System.in
	- `Scanner userInput = new Scanner(System.in);`
- Get the next stuff from the user. Add a println statement to prompt user
```java
System.out.println("Hey! Gimme some integers!");
int hp = userInput.nextInt();
```