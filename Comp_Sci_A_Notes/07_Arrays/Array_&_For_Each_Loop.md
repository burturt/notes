Array_&_For_Each_Loop

Array & For Each Loop

### Using for-each loop
```java
for (<type> <name> : <array>){
	<statement>;
	<statement>;
	…
}
```

### Example
```java
// Declare array
int[] fallTemperatures = {55, 50, 59, 69, 48, 30, 48};

// Declare counter variable
int above = 0;
// Traversing the array (in a traditional way)
for (int i = 0; i < fallTemperatures.length; i++) {
	if(fallTemperatures[i] > 32){
		above++;
	}
}

// Traversing using for-each
for (int i : fallTemperatures){
	if (i > 32){
		above++;
	}
}
```

### Last notes
- for-each only gets value of element, not index number
- for-each cannot edit value in array