### Rules for include vs class (in header file):
- Must use #include "file.h" IF
	- You declare a regular variable of that class's type
	- You declare a container (e.g. array or vector) of objects of that type
	- Create an object of that class type
	- Use a member (method) of that class type
- Use class instead IF:
	- Use the class to define the parameter to a function
	- Use the class as the return type
	- Use the class to define a pointer or reference
- Using class:
	- In header, put `class ClassName;`
	- In cpp, use full include
	- Remember to use `->` if calling function of pointer class!
### Multiclass rules:
- NEVER `using standard` in header, only in cpp
- NEVER include cpp files directly; include the header file only
	- The cpp definition of the header will include the header as well
calc.h
```cpp
// Header guards - ALWAYS INCLUDE
#ifndef CALC_H
#define CALC_H

class Calculator {
public:
	void compute();
	...
}
#endif
```
