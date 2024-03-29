# Registers
![Screen Shot 2022-11-16 at 3.39.50 PM.png](../_resources/Screen%20Shot%202022-11-16%20at%203.39.50%20PM.png)
## Setting Registers
- `mov eax, 0x539` Think `eax = 0x539`
- `mov rbx, rax` think `rbx = rax`
- Can move between partial registers of the same size
- Note: writing to 32 bit partial, will zero out the rest of the register
### Extending Data
- Extend signed data: use `movsx` to do a sign-extending move between different sized
	- Prserve two's complement

![Screen Shot 2022-11-16 at 3.45.23 PM.png](../_resources/Screen%20Shot%202022-11-16%20at%203.45.23%20PM.png)
- exch: swap registers
## Special registers are special
- You cannot directly read from or write to `rip`
	- ip = instruction pointer
- Be careful with rsp
	- Contains sp = stack pointers
- Some other registers are, by convention, used for certain things
## Other registers!
- Modern x86 processors have a lot of other registers
- Registers for us by OS itself
- Registers for floating point computations
- registers for crunching large data fast (32 512-bit "zmm" registers)
# Memory
- Registers are expensive, and limited number
- Memory: store lots of data and have *fairly fast* access
- Process memory is addressed linearly
	- from `0x10000 to 0x7fffffffffff`
	- Each memory address references **one byte** in memory
	- means 127 terabytes of addressable RAM!
- Memory is virtual!
	- Process' memory starts out partially filled in by OS
	- Process can ask for mre memory from OS later
## Memory Stack (temporary data storage)
- Stack has several uses
- Registers and immediates can be **push**ed onto the stack to save values:
```asm
mov rax, 0xc001ca75
push rax
push 0xb0bacafe # note: can only push 32-bit immediates, to push 64-bit much push from register
push rax
```
- Stack: c001ca75, b0bacafe, c001ca75
- Values can be **pop**ped back off of the stack to any register in reverse order:
```asm
pop rbx # rbx set to 0xc00lca75
pop rcx # rcx set to b0bacafe
```
- Each entry is width of architectures
	- Note: pop technically doesn't remove bytes from stack, just moves stack pointer
### Addressing the stack
- CPU stores stack location in `rsp`
	- Generally high memory location, grows towards smaller memory locations
	- push decreases rsp by 8, pop increases it by 8
- You can also move data between registers and memory with mov
- Load 64-bit value stored at memory address 0x12345:
```asm
mov rax, 0x12345
mov rbx, [rax]
```
- Store 64-bit value in rbx intro memorty at address 0x133337:
```asm
mov rax, 0x133337
mov [rax], rbx
```
- Equivalent to `push rcx`:
```asm
sub rsp, 8
mov [rsp], rcx
```
- Each addressed memory location contains one byte 
	- 8-byte (64-bit) address write at 0x133337 will write to addresses 0x133337 through 0x13333f
### Controlling write sizes
- Use partials to store/load fewer bits!
- Load 64 bits from addr 0x12345 and store the lower 32 bits to 0x133337
```asm
mov rax, 0x12345
mov rbx, [rax]
mov rax, 0x133337
mov [rax], ebx
```
- Load 8 bits from ah to addr 0x12345
```asm
mov rax, 0x12345
mov bh, [rax]
```
- Don't forget: changing 32-bit partials (e.g. by loading from memory) zeros out the whoel 64-bit register. Storing 32-bits to memory has no issues
## Memory edianess
- Data on most modern systems is stored *backwards*, in *little endian*
```asm
mov eax, 0xc001ca75 # rax: c0 | 01 | ca | 75 
mov rcx, 0x10000
mov [rcx], eax # (0x10000) 75 | (0x10001) ca | 01 | c0
mov bh, [rcx] # reads 0x75
```
- Bytes are ONLY shuffled for multi-byte stores and loads of registers to memory
	- Individual bytes NEVER have their bits shuffled
	- Writes to the stack w/ push/pops behave just like any other write to memory
## Address Calculation
- You can do some limited calculation for memory addresses
- Use `rax` as an offset off some base address (in this case, the stack)
```asm
mov rax, 0
mov rbx, [rsp + rax*8] # read qword right at stack pointer
mov rax, 1
mov rcx, [rsp + rax*8] # read the qword to the right of previous one
```
- Get calculated address with Load Effective Address (lea)
```asm
mov rax, 1
pop rcx
lea rbx, [rsp+rax*8] # rbx holds computed address for double-checking
mov rbx, [rbx]
```
- Address calculation has limits:
- `reg + reg*(2, 4, or 8) + value`
## RIP-relative addressing
- `lea` can directly address the rip register
```asm
lea rax, [rip] # Load the address of the next instruction into rax
```
- You can use mov to read directly from those locations
```asm
mov rax, [rip]
```
- Or even write (caveats apply):
```asm
mov [rip], rax
```
### Writing Immediate values
- You can write immediate values, but you MUST specify their size
- write 32-bit 0x1337 to address 0x133337:
```asm
mov rax, 0x133337
mov DWORD PTR [rax], 0x1337
```
- Might want DWORD instead of DWORD PTR
## Other Memory Regions
- Other regions might be mapped in memory!
# Control Flow
- Change flow: `jmp LABEL`
```asm
	mov cx, 1337
	jmp STAY_LEET
	mov cx, 0
STAY_LEET:
	push rcx
```
- Note: jump is signed, meaning you can jump backwards
- Think jmp as adding value to rip
	- note: `eb fe` jumps to itself
## Conditional Jumps
![Screen Shot 2022-11-20 at 12.30.17 AM.png](../_resources/Screen%20Shot%202022-11-20%20at%2012.30.17%20AM.png)
- Uses result of last operation
- `rflags` register: has flags, such as carry flag, zero flag, overflow flag, signed flag, etc
	- Update using `cmp` or `test`
		- `cmp` is same as `sub	` but discarding result
		- `test` same as `and` but discards result
- Common patters:
```asm
cmp rax, rbx;
jg MEMBORY;


ja LABEL # unsigned rax > rbx
cmp rax, rbx; jle LABEL # signed rax <= rbx
test rax, rax; jnz LABEL # rax != 0
cmp rax, rbx; je LABEL # rax == rbx
```
## Looping!
- We can implement a loop!
```asm
mov rax, 0
LOOP_HEADER:
inc rax
cmp rax, 10
jb LOOP_HEADER
```
## Function calls!
- Assebly call split into functions with `call` and ret`
	- call pushes `rip` and jumps away (enter function)
	- ret pops `rip` and jumps to it (return)
```asm
mov rdi, 0
call FUNC_CHECK_LEET
mov rdi, 1
call FUNC_CHECK_LEET
call EXIT

FUNC_CHECK_LEET:
	test rdi, rdi
	jnz LEET
	mov ax, 0
	ret
	
	LEET:
		mov ax, 1337
		ret
	
EXIT:
	???
```
## Calling Conventions
- Agreements on argument passing
	- x86: push arguments (in reverse order), then call, return value in eax
	- amd64: rdi, rsi, rdx, rcx, r8, r9, return value in rax
		- function promises to return `rbx, rbp, r12, r13, r14, 15` back to original state if modified, and technically rsp
		- "callee-saved"
# System Calls
- Interacting with outside world, exist
- System call: makes a call into the Operating System
	- `syscall` triggers the system call specified by the value in rax; arguments in rdi, rsi, rdx, r10, r8, and r9
	- return value rax
```asm
# Reading 100 bytes from stdin to the stack: n = read(0, buf, 100);
mov rdi, 0 # stdin file descriptor
mov rsi, rsp # Read the data onto the stack
mov rdx, 100 # the number of bytes to read
mov rax, 0 # system call number of read()
syscall
# read returns the number of bytes read vis rax, so we can easily write them out: write(1, buf, n);
mov rdi, 1 # stdout file descriptor
mov rsi, rsp # write data from the stack
mov rdx, rax # number of bytes to write (same as read in)
mov rax, 1 # system call number of write()
syscall
```
- System call list: https://syscall.sh/
- Getting constants:
```python3
from pwn import *
int(constants.AF_INET)
```
## "String" arguments
- String is a bunch of contiguous bytes in memory, followed by a 0 byte
```asm
mov BYTE PTR [rsp], '/'
mov BYTE PTR [rsp+1], 'f'
mov BYTE PTR [rsp+2], 'l'
mov BYTE PTR [rsp+3], 'a'
mov BYTE PTR [rsp+4], 'g'
mov BYTE PTR [rsp+5], 0

mov rdi, rsp # read data onto the stack
mov rsi, 0 # open file read only
mov rax, 2 # system call number of open()
syscall
```
## Constant arguments
- Some system calls require archaic "constants"
	- e.g. open() has a flags argument to determine how the file will be opened
	- We can figure out the values of these arguments in C!
```c
#include <stdio.h>
#include <fcntl.h>
int main() {
	printf("O_RDONLY is: %d\n", O_RDOLY);
}
```

## Quitting program
```asm
mov rdi, 42 # return code
mov rax, 60 # system call number of exit()
syscall
```

# Building Programs
- Put the file in an assembly file:
```asm
# .intel_syntax tells assembler we are using intel assembly
# noprefix tells it that we will not prefix all register names with %
.intel_syntax noprefix
mov rdi, 42
mov rax, 60
syscall
```
- Assembling:
```bash
# nostdlib tells gcc to not include standard lib
gcc -nostdlib -o quitter quitter.s
```
- It will complain about start location, can add this to top:
```asm
.global _start
_start:
# code
```
## Running program
- Run like any other other
- Can check return code with special `$?` variable
## Reading assembly
- `objdump -M intel -d quitter`
- `objcopy --dump-section .text=quitter_binary_code quitter`
## Debugging
- Debuggers use a special debug instruction: `int3`
	- When `int3` breakpoint instruction executes, the debugged program is stopped
	- debugger itself can set breakpoints
### Resources
- `gdb` is your go-to debugging experience
- `strace` lets you figure out how your program is interacting with the OS
- `rappel` lets you explore the effects of instructions


add reg1, reg2       <=>     reg1 += reg2
sub reg1, reg2       <=>     reg1 -= reg2
imul reg1, reg2      <=>     reg1 *= reg2
- division:
mov rax, reg1; div reg2
Notice: to use this instruction you need to first load rax with the desired register
you intended to be the divided. Then run div reg2, where reg2 is the divisor. This
results in:
rax = rdi / rsi; rdx = remainder
The quotient is placed in rax, the remainder is placed in rdx.

shl reg1, reg2       <=>     Shift reg1 left by the amount in reg2
shr reg1, reg2       <=>     Shift reg1 right by the amount in reg2
Note: all 'regX' can be replaced by a constant or memory location

A function is a callable segment of code that does not destory control flow.
Functions use the instructions "call" and "ret".

The "call" instruction pushes the memory address of the next instruction onto
the stack and then jumps to the value stored in the first argument.

Let's use the following instructions as an example:
0x1021 mov rax, 0x400000
0x1028 call rax
0x102a mov [rsi], rax

1. call pushes 0x102a, the address of the next instruction, onto the stack.
2. call jumps to 0x400000, the value stored in rax.
The "ret" instruction is the opposite of "call". ret pops the top value off of
the stack and jumps to it.
Let's use the following instructions and stack as an example:
                            Stack ADDR  VALUE
0x103f mov rax, rdx         RSP + 0x8   0xdeadbeef
0x1042 ret                  RSP + 0x0   0x0000102a
ret will jump to 0x102a

rdx = rax for function
rbx = src_addr
rcx = foo address, `call rcx`

mov rdx, 0
mov rcx, 0x403000
mov rbx, rdi
cmp rbx, 0
je END
LOOP:
cmp [rbx], 0
je END
cmp BYTE PTR [rbx], 0x5a
jg LOOPCONT
mov dil, BYTE PTR [rbx]
call rcx
mov BYTE PTR [rbx], al
add rdx, 0x1
LOOPCONT:
add rbx, 0x1
jmp LOOP

END:
mov rax, rdx
ret

