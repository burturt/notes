```c
// rs.o contains object file
// 32 bit mode
void read_sector(int s, char *buf);
// main.c for computer system since it is run by the bios and not OS
void main(void) {
	char buf[512];
	int s = 3 * 1024 * 1024 / 512; // sector number
	long long w = 0;
	bool let0 = false;
	for (;;s++) {
		read_sector(s, buf);
		for (int i = 0; i < sizeof(buf), i++) {
			if (!c) goto out;
			char c = buf[i] & ~('a' - 'A'); // mask out bit for lower/upper case
			let1 = 'A' <= c && c <= 'Z';
			w += let1 && ~let0;
			let0 = let1;
		}
	}
	out: write_num(w);
	for (;;); // Loop forever
}
// wn.c 
void writenum(long long n) {
	// Writes to screen using legacy IBM screen format, showing a 25x80 character memory-mapped screen
	// Ram is dual-ported, and display/graphics cards keeps inspecting same memory
	// 16 bits per character: display color 8 bits, ascii 8 bits
	// Display example: 7 = grey on black
	// Copy the number string into the array in memory and it'll show up on the screen
	// 0xB8000 is the start of in-memory array
	unsigned short *d = (unsigned short *) 0xB8000;
	d += 25 * 80 / 2; // center number
	do {
		*--d = 7 << 8 | n % 10 + '0';
	} while (n /= 10);
}
```
## Compiling
- Must ensure initial IP points at main
## Drawbacks/features to add
- Avoid needless duplication of read sectors (firmware reading master boot record already has one)
	- Issue: function in firmware must be at specific location, and backwards compatibility makes changes hard (e.g. increase size)
	- Issue: firmware code is OS- and Device-agonistic and less efficient
- Utilization: cpu 50%, io buffer 50% busy
	- Solution: double buffering: alternate buffers to keep working on stuff faster
- DMA: direct memory access
	- `ins` instruction does Disk Controller --> CPU --> RAM, instead we want just DC --> RAM to only do one send over BUS, not 2
### Concurrency:
- Allow for other tasks to run while waiting for I/O for example:
```c
while (inb(0x1f7) & 0xC0 != 0x40)
		schedule_other_programs();
```
### Problems when adding features:
- Too much hassle to change all our code
- Too much hassle to share code
- Too much hassle to run in parallel/concurrency
- Too much hassle to recover from faults
# Modularity and Abstraction
- Good modularity:
	- Boundary of modules are simple
	- Hard boundaries
	- Performance: simplifying interface can make underlying code more complex (e.g. read only 512 blocks but any number can be requested)
	- Robustness: want system to keep running even if one module fails/is buggy (C++ fails here)
	- Simplicity
	- Flexibility/Lack of Assumption
- Good abstraction:
	- Give a simpler view of the modules, but still with good modularity
	- API is **simpler** than underlying OS
## example: bad interface for input
- `char *readline(void);`
	- Returns pointer to memory location of data
	- Issue: no newline may be necessary in input
		- Avoid arbitrary restrictions
## Linux interface:
`ssize_t read(int fd, char *buf, size_t bufsize);`
- ssize_t: 64bit signed, return # of bytes read, 0 if EOF, -1 if error (details in errno, which is thread-local storage)
- size_t: 64 bit unsigned
## Example: factorial
```c
long fact(long n) {
	if (!n) return 1;
	else return n * fact(n-1);
}
```
```asm
movq $1, %rax
testq %rdi, %rdi
jne L8
ret
.L8
pushq %rbx
movq %rdi, %rbx
leaq -1(%rdi), %rdi
call fact
imulq %rbx, %rax
popq %rbx
ret
```
- weak/soft modularity: callee sav/calling conventions
	- soft: works when everyone follows it
	- honor system
	- function could for example just corrupt stack and write over it
## System abstractions
- Memory: continuous area for program to store data
- Interpreter: processor that can execute instructions
	- ip (rip) + enviornment pointer (rbp, rsp) + instruction set
- Link: communication link from one interpreter to another (pipe, network connection, etc)
### Ways to do abstractions
- client-server organization, with separation between client and server (link)
	- just complicated: 2 separate machines
- Virtualization