Arrays: Shifting Elements

| 5 | 4 | 3 | 2 | 1 |
| - | - | - | - | - |
How do we construct this array?
How do we initialize it?
How should we move the 5 to the end of the array?
```java
int[] distanceDriven = new int[5];

for (int i = 0; i < 5; i++) {
	distanceDriven[i] = 5 - i;
}
/* OR */
int[] distanceDriven = {5, 4, 3, 2, 1};

int lastElement = distanceDriven[0];
for (int i = 0; i < distanceDriven.length - 2; i++) {
	distanceDriven[i] = distanceDriven[i + 1];
}
distanceDriven[distanceDriven.length - 1] = lastElement;

int firstElement = distanceDriven[distanceDriven.length - 1];
for (int i = distanceDriven.length - 1; i > 0; i--) {
	distanceDriven[i] = distanceDriven[i - 1];
}
distanceDriven[0] = firstElement;
```
