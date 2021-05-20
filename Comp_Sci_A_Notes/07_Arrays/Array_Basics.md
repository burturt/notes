Array_Basics

Array Basics

### What is an array?
- An array is an indexed structure that holds multiple values of the same type

| H | o | l | a |   | c | h | i | c | o | s |
| - | - | - | - | - | - | - | - | - | - | - |
| 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 |
- The values stored in an array are called elements
	- Individual elements are accessed using an integer index (the position).
- indexes start at 0 and end at legnth - 1. ("zero-based indexing")

### Constructing an Array
- Since an array is an object, you have to contruct it (you can't just declare it as a variable).
`double[] ages = new double[4];`
- `type[] name = new type[size];`
- We use the keywork new since we're constructing an object
	- Then we tell java # of elements. In this case 4.
- example: construct an array of 9 integer:
- `int[] testScores = new int[9];`

### Filling an Array
- Both of these samples are only 1/2 done - they are filled with default 0s as java does that by default (for char, double, and int) or false (for boolean). Our array looks like this:

| 0.0 | 0.0 | 0.0 | 0.0 |
| --- | --- | --- | --- |

- to fill in this array, we need to fill in the values for each location:
- `ages[0] = 17;` (the int is autoconverted into a double)

| 17.0 | 0.0 | 0.0 | 0.0 |
| ---- | --- | --- | --- |

- `name[index] = value;`

### Traversing an array
- If you have a really big array, you can use a scanner to get user input or elsewhere:
```java
for (int i = 0; i < array.length; i++) {
	array[i] = input.nextInt();
}
```
- As you move across the array (in this case to fill each element with a user-inputted value), we call this “array traversal.” You’ll need to do this a lot in the future!