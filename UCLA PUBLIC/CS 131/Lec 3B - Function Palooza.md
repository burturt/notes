- Value: variable name directly bound to the storage that holds the value
	- Changes to one does not change any others
- Reference: directly bound to another variable's storage, like an alias
	- Assignments and method calls to one changes others
- Object Reference: Variable name is bound to a pointer, pointer points to object/value
	- Method calls change others, assignments do not
- Name/need: variable name is bound to pointer that points to expression graph that can be evaluated to get value
	- Name: evals every time
	- Need: evals at most once (caches result)
- Pass-by-Value-Result: Pass by value but copy result back to caller scope
	- Pass by result: updates argument once completed, but does not pass input value in
- Pass by macro expansion: C++ macros: copy-paste the literal text
- **Aliasing**: 2 input parameter to function refer to same value/object, causing issues

![Screenshot 2024-11-11 at 1.10.50 PM.png](../../_resources/Screenshot%202024-11-11%20at%201.10.50%20PM.png)

# Functions
- Formal parameters: variables in function definition
- Arguments/actual parameters: values in function call
- **variadic function**: function with variable number of arguments
	- Python `*`, java `...`, etc
# Returning Results/Error Handling
- **B**ug: flaw in program logic
	- Reuiqres aborting
	- Out of bounds array access, divide zero, illegal cast, etc
- **U**nrecoverable **E**rrors: non-bug errors without recovery
	- OOM, network host not found, disk is full
- **R**ecoverable **E**rrors: non-bug errors where can recover
	- File not found, malformed email address, etc
- **R**esult: indication of output/status of operation
## Types:
- Error Objects (RE/R)
	- Create inherited objects from error class
- Result/Optional Objects (RE/R)
	- Result Object: function returns a valid value or distinct error
	- Optional Object: function returned valid value or generic failure output (e.g. -1)
- Assertions (B/UE)
	- Statement/clause that verifies assumptions about state
	- Verifies preconditions, postconditions, and invariants
		- Precondition: true at start of function
		- Postcondition: true at end of function
		- invariants: true across entire function or class's executions
- Exception Handling (B/UE/RE)
	- Separate handling exceptional situatuions from core problem solving logic
	- Catch/throw/finally
		- Finally block *always* runs before returning control to surrounding area
	- Uses exception objects to describe error 
	- Only use exceptions with languages that support automatic memory management
		- C++: use smart pointers to prevent memory leaks
	- **Exception handling guarantees** for a given function
		- "No-throw" guarantee: function will never throw exception, and handles exceptions raised internally without leaks
		- Strong Exception: if exception thrown, program's state will be "rolled back" to just before function call
		- Basic Exception: If exception thrown, program is left in valid state (e.g. no memory/resource leaks)
- Panics (B/UE):
	- Abort execution immediately
	- Contains error message/stack trace

![Screenshot 2024-11-17 at 3.19.03 PM.png](../../_resources/Screenshot%202024-11-17%20at%203.19.03%20PM.png)

# First class Functions
- Functions act just like any other value: passed/returned to/from other functions, assigned to variables, stored in data structures, compared for equality, can be anonymous
	- "first-class citizens"
- Second class functions: passed as arguments but not assigned to variables
- Third class functions: can be called but nothing else
- Lambdas:
	- Free variables captured (may be copied by value or reference)
	- Parameters & return type
	- Function body
# Polymorphism
- Ad-hoc polymorphism:
	- Define specialized versions of same function for each type we want to support
	- "Overloading"
- Parametric Polymorphism: define single version of function that can operate on many potentially unrelated types
	- Templates: each usage of template function with type generates a new function
		- All operations used in templated code must be supported by type
	- Unbounded Generics: **all** code inside function must be type-agnostic for all possible types T
		- e.g. comparing object references, assignment, instantiation
		- Type checking done on parameterized creation, e.g. one created for Ducks, requires only Ducks
	- Bound Generics: generics with restrictions, e.g. allow comparisons
- Subtype/Supertype polymorphism: define function that accepts a supertype