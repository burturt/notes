- Make sure to delete anything your object allocates with `new`:
```cpp
MyClass::~MyClass() {
	delete ptr;
	// Must use this to delete if arrays
	delete [] arrayPtr;
}
```
- Make sure you implement a proper copy constructor:
	- Called whenever `String x(s);`, `String x = s;`, passing a string by value to a function, returning a string in a function
	- Make sure ANYTHING called in the destructor is unique to a specific instance of a class, e.g. if pointers exist, make sure pointer is updated with new instance of the copy
	- Copy constructor does not technically need to be const
	- Sidenote: member functions of a class can access private members of ANY instance of a class, not just its own
```cpp
// Copy constructor
String::String(const String& other) {
	m_len = other.m_len;
	m_text = new char[m_len+1];
	strcpy(m_text, other.m_text);
}
```
- Make sure you implement a proper assignment function:
	- Delete old data
	- Copy over new data
	- Return new value so that you can do `x = y = z;`, x&y taking on z's value
```cpp
String& String::operator=(const String& rhs) {
	// Check if trying to assign itself to itself
	if (this != &rhs) { // if statement optimization and technically not necessary
		String temp(rhs);
		this->swap(temp);
	}
	return *this; // return reference to object
}
void swap(String& other) {
	... swap m_text and other.m_text
	... swap m_length and m_length
		(can use std::swap for primitives and arrays)
		
}
```
- Last notes:
	- ALWAYS check for two pointers or references to the same type ==> aliasing might occur
	- Make sure functions handle same reference or pointer submitted to multiple inputs