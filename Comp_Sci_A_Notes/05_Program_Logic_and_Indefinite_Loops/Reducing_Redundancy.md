Reducing Redundancy

```java
if (x < 30){

a = 2;

x++;

System.out.println(“Spongebob Squarepants! “ + x);

} else {

a = 2;

System.out.println(“Spongebob Squarepants! “ + x);

}
```
becomes

```java
a = 2;
if (x < 30) {
    x++;
}
System.out.println(“Spongebob Squarepants!” + x);
```

```java
int sum = 1000;
Scanner console = new Scanner(System.in);
System.out.print("Is your money multiplied 1 or 2 times? ");
int times = console.nextInt();
System.out.print("And how much are you contributing? ");
int donation = console.nextInt();
if (times == 1) {
    sum += donation;
    count1++;
else if (times == 2) {
    sum += 2 * donation;
    count2++;
}
total += donation;

```


### Hourglass!

Pseudo code
```
startEndLine
topHalf
middle
bottomHalf
startEndLine

StartEndLine()
	print 1 |
	print 10 "
	print 1 |

middle()
	print ||

printHourglassLine(n, c, first, last)
	print n _
	print first
	print c :
	print last
	
bottomHalf()
	loop (see table below) calling printhourglassLine

topHalf()
	loop (see table below) calling printhourglassLine
```
| loop | n spaces | c colons |
| ---- | -------- | -------- |
| 1    | 1        | 8        |
| 2    | 2        | 6        |
| 3    | 3        | 4        |
| 4    | 4        | 2        |
| i    | i        | -2i + width |



| loop | n spaces | c colons |
| ---- | -------- | -------- |
| 4    | 4        | 2        |
| 3    | 3        | 4        |
| 2    | 2        | 6        |
| 1    | 1        | 8        |
| i    | i        | -2i + width |
