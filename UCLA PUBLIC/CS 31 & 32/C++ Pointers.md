A pointer is a variable that holds a memory address, rather than holding data like most variables. A pointer has a data type, and the data type determines what type of address is held in the pointer. Ex: An integer pointer holds a memory address of an integer, and a double pointer holds an address of a double. A pointer is declared by including * before the pointer's name. Ex: int* maxItemPointer declares an integer pointer named maxItemPointer.

Typically, a pointer is initialized with another variable's address. The reference operator (&) obtains a variable's address. Ex: &someVar returns the memory address of variable someVar. When a pointer is initialized with another variable's address, the pointer "points to" the variable.

- Pointers!
	- Declare a pointer: add a `*` after the variable type
		- Note: can also add `*` before variable name instead when declaring
	- Get memory address of a variable: add a `&` to the beginning of the variable name
	- Convert pointer memory address to literal variable: add `*` to beginning of pointer variable
		- Can use to reference variable value instead of replacing pointer, i.e. `*valPointer = 5`
	- `nullptr` is for a pointer that doesn't point to anything (yet)
	- 

```c++


int main() {
   int someInt;
   int* valPointer;

   someInt = 5;
   cout << "someInt address is " << &someInt << endl;

   valPointer = &someInt;        
   cout << "valPointer is " << valPointer << endl;

   return 0;
}

```
#### Sidenote: returning references
- if the return type is `&type` like `&int`, this returns a REFERENCE rather than a pointer or object