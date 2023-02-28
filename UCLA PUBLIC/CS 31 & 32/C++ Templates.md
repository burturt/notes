```c++
template<typename T>
T minimum(const T& item1, const T& item2) {
	if (a < b) return a;
	return b;
}

// Use with any variable type as long as all parameters with type `T` are the same and the type works with >/< operators
// You must put the template statement before all uses in a method header or at top of class
// Calling class functions or creating new objects with custom type:
TripleItem<int> triInts(9999, 5555, 6666); // TripleItem class with ints
```
## Caveats:
- When the compiler works with templates, automatic type conversion like double to int DOES NOT work
	- Call MUST match template exactly
- Compiler may give errors if using type that doesn't work
	- e.g. comparing 2 `Chicken` classes with `<`
- Arrays are passed as pointers
	- Make sure you aren't comparing pointers instead of actual data!
	- **Solution: you can overload templates for specific types**
- Make parameters const and pass by reference as needed
- Be careful initializing variables:
```cpp
template<typename T>
T sum (const T a[], int n) {
	T total = 0; // uh-oh, if we use a string, this is an error, but if we leave it off, then it doesn't work for double since not initialized
	T total_correct = T(); // correct way to do it - works on both classes and primitives, initialized to 0
	...sum them...
	return total
}
```
## Successful call to template functions:
1. The call must match some template
	- For matching, only conversions considered for any type A are:
		- A --> A&
		- A --> const A
		- array of A --> A*
2. The instantiated template must compiles
3. The instantiated template must do what you want
# Declaring a template class
```
template<typename T>
class Stack {
public:
	Stack();
	void push(const T& x);
	void pop();
	T top() const;
	int size() const;
private:
	T m_data[100];
	int m_top;
}

template<typename T>
Stack<T>::Stack() : m_top(0) {}

template<typename T>
void Stack<t>::push(const T& x) {
// stuff
}

// Multiple Templates
template<typename T, typename T2>

... more declarations ...


// Using the stack:
int main() {
	Stack<int> si;
	si.push(3);
	Stack<Coord> sc;
	sc.push(Coord(3,5));
}
```
## Template class notes:
- Compiler will ONLY instantiate and compile methods that were used for a type in template object declaration
- For instantiated methods from template classes that ARE NOT deduced during compile time, automatic type conversion like int to double does occur since this doesn't happen during compile type