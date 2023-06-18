CISC: complex instruction set computer
- Instructions are of variable length
- Most instructions can have any type of operand
- Instructions can execute complex behavior
RISC: reduced instruction set computer
- All instructions are the same length
- Instructions have standardized operands (registers and constants)
- Instructions execute relatively simple behaviors
![Screen Shot 2023-06-11 at 10.49.21 AM.png](../../_resources/Screen%20Shot%202023-06-11%20at%2010.49.21%20AM.png)


## MIPS
- Rd = Rs op Rt
- Can only operate on registers
### Registers
- 32 32-bit registers named from $0-$31 for a variety of different use cases
- 32-bit PC program counter register (like rip)
- 32 32-bit registers named from $f0-$f31 for floating point arithmetic
- 2 32-bit registers HI and LO for multiplication and division
### Syscalls
- 1: print int
	- $a0 = int
- 4: print str
	- $a0 = str addr
- 5: read int
	- $a0 = result stored here
- 8: read str
	- $a0 = str addr, $a1 = length limit
- 10: exit
