## Virtualization
- Emulated machine that shares memory but is separate
	- Higher level sees a fake emulated ram with lower interpreter watching all instructions
	- Issue: too much overhead, but tricks have allowed this to run at full speed
### Virtualization at full speed
- Want function to act like a function BUT with hard modularity
- Originally done via a "trick" for x32:
	- /0, `*(NULL)`, etc, hardware trap
	- Trap table by number (ISR), with pointers to code location to continue execution at
	- Make these codes as simple and reliable as possible
	- `int 0x80`: interrupt 0x80 is actually transfer to kernet]
		- Protected transfer of control to kernel mode
		- Pushes ss (stack segment) and esp (32-bit stack pointer), eflags (permissions of code), cs (code segment), eip (32-bit instruction pointer), error code onto kernel stack
		- Set flags for kernel
		- Set esp kernel stack
		- Set eip ISR (0x80)
		- Eventually `rti`/`reti` (return to instruction) to reverse back to user mode
	- Syscall convention:
		- eax - syscall #
		- ebx ecx edx esi edi ebp, 1-5 arguments
- x64: `syscall` instruction
	- rax syscall number
	- rdi rdi rdx r10 r8 r9
	- Destroys rcx r11, rax result on success, -errno on failure (-1 - -4095)
- Good performance only if syscall is rare relative to normal instructions
	- App execute unpriv instructions directly to hardware and syscalls to kernel which then run privileged calls
- `int` and `syscall` are privileged instruction that traps if run in user
- What needs to be accessed in virtualized machine:
	- ALU (same speed)
	- registers (usually same, but stop/restrict certain registers like `cr3` that controls virtual memory bounds)
	- primary memory (same speed), but kernel buffers, etc not full speed
	- I/O (typically not)
- We want it to be fast BUT safe
	- Stuff not directly access is done via syscalls
### Pocess Table/Context Switching
- Process table in kernel
	- has each process ID's saved register
	- When loaded, registers including rip into memory

## Issues:
- Kernel can mess with apps/apps not protected from kernel
- Kernel not fully protected from apps: imagine infinite loop in app
