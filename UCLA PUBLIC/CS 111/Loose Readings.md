# Software Interface Standards
- Orig compatibility anti-goal for vendor lock-in
- Later, with the rise of Independent Software Vendors (ISVs) and killer applications, the situation was reversed:
	- ISVs too much work to make for multiple platforms
	- Hardware sales linked to software ISV sales
- Application Programming Interfaces: can have issues, but allows for portability
- Application Binary Interfaces (ABI): binding of an Application Programming Interface to an Instruction Set Architecture. (ISA)
	- Much more portable, aka x86, x86-64, etc
# Threads
The basic model is:
- Each time a new thread was created:
	- we allocate memory for a (fixed size) thread-private stack from the heap.
	- we create a new thread descriptor that contains identification information, scheduling information, and a pointer to the stack.
	- we add the new thread to a ready queue.
- When a thread calls yield() or sleep() we save its general registers (on its own stack), and then select the next thread on the ready queue.
- To dispatch a new thread, we simply restore its saved registers (including the stack pointer), and return from the call that caused it to yield.
- If a thread called sleep() we would remove it from the ready queue. When it was re-awakened, we would put it back onto the ready queue.
- When a thread exited, we would free its stack and thread descriptor.
- People eventually wanted preemptive scheduling
## Issues with user mode threads:
- Syscall block can block the process completely
- If OS does not know that there are multiple threads, then it can't use multiple cores
## Performance
- If non-preemptive scheduling can be used, user-mode threads operating in with a sleep/yield model are much more efficient than doing context switches through the operating system. There are, today, light weight thread implementations to reap these benefits.
- If preemptive scheduling is to be used, the costs of setting alarms and servicing the signals may well be greater than the cost of simply allowing the operating system to do the scheduling.
# Network IPC
- IPC: Inter process communication
- socket(2) ... create an inter-process communication end-point with an associated protocol and data model.
- bind(2) ... associate a socket with a local network address.
- connect(2) ... establish a connection to a remote network address.
- listen(2) ... await an incoming connection request.
- accept(2) ... accept an incoming connection request.
- send(2) ... send a message over a socket.
- recv(2) ... receive a message from a socket.
