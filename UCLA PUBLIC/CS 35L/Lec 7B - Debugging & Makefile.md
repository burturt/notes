- Debugging: consists of both performance and correctness debugging
## Attributes in GCC
### Performance attributes
- Give advice to compiler to help it optimize
```c
#if ! __GNUC__
#define __attribute__(x)
#endif
char buf[8192] __attribute__((__aligned__(4096))); // change byte alignment
void report_error(char const *msg) __attribute__((cold)); // tells compiler rarely called
// Example: creates cold section that makes function call not cached using jmp to a call instruction
__attribute__((hot)); // very often called
```
## Profiling:
- Run program on typical input, gather statistics of which parts of code will be executed, and use stats to define hot and cold functions
- `gcc --coverage`
- Problems:
	- Gathering stats slows down execution
	- "typical" input is a judgement call and can cause issues
- Can use profiling to check test case coverage (reliability)
## Static Checking:
- `static_assert(true/false expression)`
	- Document additional assemptions
	- Generates no instructions: only runs during compile
	- Can only use constant expressions
### Compiler static checking flags
- Allow customizing static checking in compiler
- `-Wall`: run common static checks:
	- `-Wcomment`: check multiline comments for multiple open comment tags
	- `-Wparentheses`: warn for order of operation unclarity (often with bit shifts)
		- controverial: assumes && and || needs parenthesis to ensure correct execution order
	- `-Waddress`: catch when `==` is used to compare strings
	- `-Wstrict-aliasing`: casting between pointer types
	- `-Wmaybe-uninitialized`: warn against possibly uninitialized variable referencing, with possible false positives
- `-Wextra`: more controvertial warnings:
	- `-Wtype-limits`: type limit size comparisons that are weird, and warns against portable code
- `-fanalyzer`: looks for even more stuff, and looks at all functions together when catching errors
	- very slow
	- Do NOT use with `-flto` because really slow
### Static checking keywords:
- `__Noreturn`: function does not exit, e.g. after calling `exit(0)`
- `__attribute__((pure, access(read_only,1,2)))`
	- pure: self-contained, and same input will cause same output
		- Allows re-ordering calls
	- access: only will read_only 1 index argument, and max read size of pointer (2)
# Makefiles
- Motivation: nicer than just a shell script to compile, and prevent recompiling everything
- Make: allow incremental builds:
```makefile
W = -Wall -Wno-comment

include a/submakefile.make

default: myprog

foo.o: foo.c inc.h
	gcc -c $(W) foo.c
myprog: foo.o bar.o
	gcc foo.o bar.o -o mprog
```
- `make -j10`: allow compiling multiple steps at once using threads
- `-j` means unlimited threads
- Issues: missing dependencies = wrong answers when compiling (especially with -j)
	- extra dependencies: inefficient