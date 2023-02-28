| | | |
| --- | --- | --- |
| Mammal | Shape | Generalization, superclass, **base class** |
| Dog, Cat | Circle, Rectangle | specialization, subclass, **derived class** |

Automatic conversions:
- Derived* --> Base*
- Derived& --> Base&

- C++ declaring inheritance:
```cpp
class Circle : public Shape {
	
}
```

## Binding
- Solves issues with declaring both base class and dervied class functions to override base class function
- Decides when the compiler should choose which implementation to use:
	- Static binding: "at compile time"
		- aka every single derived class uses the base class function implementation
	- Dynamic binding: "at run time"
		- aka derived classes can override base class implementations
	- C++ defaults to static: more efficient
- Force dynamic: add keyword `virtual` to the function declaration
	- Required in base class declaration, optional but recommended in derived class declaration
	- Needed if function is overridden in derived classes
	- If you DON'T declare virtual, and if a derviced class overrides a function, the function will only run on an actual instance of a derived class, not when it is referenced as a base class.
- **TL;DR make all functions in a base class that could POSSIBLY be ever overrideen virtual**
## Calling base class implementation
```cpp
void WarningSymbol::move(double xnew, double ynew) {
	Shape::move(xnew, ynew);
	// do other stuff
}
```
## Make overridable function not have an implementation
- Wacky syntax:
	- `virtual void draw() const = 0;
	- Other languages use `abstract`
	- Creates a "pure virtual function"
	- Using a "pure virtual function" makes the class an **abstract base class** (ABC)
	- If a derived class doesn't declare a pure virtual function from a base class, it is also an abstract class and cannot be constructed
- Note: it is a compilation error to contruct a base class with a pure virtual function
## Destructors
- If a class is designed to be a base class, declare a destructor for it, make it virtual, and implement it
	- C++ will automatically destruct the base part of a class
```
class Shape {
	virtual ~Shape();
};

class Polygon: public Shape {
	~Polygon();
	Node* head;
};

Shape::~Shape() {}
```

## Updated list for construction/destruction:
- Construction:
	1. Construct the base part
	2. Construct the data members
	3. Execute the body of the constructor
- Destruction:
	1. Execute the body of the destructor
	2. Destroy the data members
	3. Destroy the base part
## Private members:
- Derived classes DO NOT have direct access to base class private members
### Constructing:
```
Circle::Circle(double r, double x, dobule y) : Shape(x, y), m_r(r) {};
```
- Use this to call shape's constructor in the member initialization list
- If you don't call the constructor in the member initalization list, it'll try to call a default constructor for Shape.