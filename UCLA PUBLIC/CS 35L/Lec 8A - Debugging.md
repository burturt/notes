## Static Analysis for Correctness
```c
int hash (char *, ptrdiff_t);
// const: promies not to modify pointer data THROUGH the pointer
int hash (char const *, ptrdiff_t);
// char const *

// Caller:
{
	char buf[100];
	buf[0] = 0;
	hash(buf, 100);
	return buf[0]; // since const, compiler knows it will be 0
}

int hash (char *, ptrdiff_t) __attribute__((pure, access(read_only, 1, 2))));
// C23: [[reproducable]]: value only looks at global variables and arguments, e.g. const
// [[unsequenced]]: Value is independent of state (only looks at arguments to determine return value/changes)
int my_alloc(int n) __attribute__((malloc (my_free, 1)));
// Tells compiler to warn if my_alloc is called but never calls my_free on the return value
__attribute__((malloc(free, 1))) // tells that user of function must call free themselves with the return value as the first argument to free
// Note: if pointer is not returned but stored globally, compiler is not smart enough to follow 
```
```c
// Example:
int f(int a, int b) {
	#ifdef __GNUC__
	a += b;
	#endif
	return a*a+1;
}
// [[maybe_unused]]: tells gcc that a value is possibly unused
int f(int a, [[maybe_unused]] int b);
```
- Static analysis: fast (low to no performance hit), increased confidence to 100% about savings, but not enough to catch all bugs
## Dynamic analysis:
- Constantly inspect program while running for bugs
- Catch a wider variety of bugs
- Downside: runtime performance cost, not 100% confidence that a bug is gone if no trigger
### Example by-hand dynamic analysis
```c
return a*b
// To check:
#include <stdchkint.h>
if (chk_mul(&r, a, b))
	return -1;
return r;
```
- Issue: signed int overflow is undefined behavior in C/C++
### Compiler dynamic analysis
`gcc -fsanitize=undefined foo.c`: gcc will generate code to crash on most undefined behavior
- Makes code more reliable for no extra effort, but slower
	- Example, warn bit shift left either into negative number or into sign bit
- `gcc -fsanitize=address`
	- Catch subscript errors, e.g. dereferncing bad pointers, subscript into array beyond end
	- Catches most but not all
- Above flags are mutually exclusive
- `gcc -fwrapv`: enables reliable int wrap around
	- Used in linux kernel 
- `gcc -fsanitize=leak`: looks for memory leaks in program
- `gcc -fsanitize=thread`: dynamically look for race conditions in parallel code
### Portability checking
- Example: works on x86_64 but not on arm
- `gcc -m32 foo.c`: generates 32 bit executable (32 bit pointers)
- How to check: run using different compiler options and platforms, e.g. `-O0` and `-O2`, and enable `-Wall` for all
### Test Cases
- SUPER importmant
- Test driven development: (TDD) write test cases first before writing code
- Reliability testing: e.g. regression testing (always write a test whenever fixing a bug to make sure a bug stays fixed)
### Using a better software platform:
- Example: avoid C and C++ and use Java if having issues with memory leaks
	- Java cleans memory automatically via garbage collector
### Defensive Programming
- Exception handling around code to catch errors elsewhere
- Assertions: require certain things to hold true for it to continue executing
```c
#include <assert.h>
assert(i < n); // abort + error if false
```
- Traces and logs: Keep track of what happened so if the program crashes, you can see what goes on
- Barricades: inner part of program is safe space, but outside world may not
	- Add checks on data being passed into the application, and once passed, it is considered clean
- Interpreters/Virtual Machines:
	- Interpreter: program that runs programs, often with a lot of extra runtime checking
	- VMs: walling off a program, aka support for a hardware level interpreter
# How to debug
- Don't guess! (doesn't scale)
- Stabilize the failure: make failure reproducable
- Locate failure source/cause
	- May require a lot of reasoning about previous behavior
# Debugger
- Program execution history exploration tool
- Run program under debugger's control: pause execution, examine and change state
- `gcc -g3`: adds lots of extra info for a debugger, e.g. variable names
- Watch out for optimization + debugger due to compiler reordering statements
	- `gcc -Og`: optimize for debugging
## GDB
- `run ARGUMENTS`: run program with arguments ARGUMENTS
- `attach PID`: attach and take over already running program, like a server
	- Can only attach to one of your own processes
	- OS will stop from attaching multiple gdb sessions to one process