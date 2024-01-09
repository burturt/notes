### Chapter 2 - Introduction to Operating Systems

- Both the CPU and memory are virtualized - it’s like each program has access to its own private copy at a time
- Strange things happen when dealing with concurrency. Load, increment, store back don’t happen atomically (all at once).
- Persistence - storing files in the file system. This is messy, the OS does a lot of work and makes things available through system calls (standard library)
- We have to build abstractions
- We care about time complexity
- And space complexity (on disk and in memory)
- Protection/isolation
- Reliability
- Energy efficiency
- Security
- Mobility (portability)
- System call vs procedure call - system calls elevate permission to the hardware privilege level via a trap/trap handler

### Chapter 3 - A Dialogue on Virtualization

- Virtualization is like switching who has the real peach at any given instant in time

### Chapter 4 - The Abstraction: The Process

- A process is a running program
- We use time-sharing to virtualize (space-sharing also exists)
    - This is used in networking
- Low-level part: time-sharing mechanism, a context switch
- High-level part: a scheduling policy
- These should be separate, because modularity
- What makes up a process?
    - Memory (Address space)
    - Registers
    - Program counter / instruction pointer
    - Stack pointer & associated frame pointer
    - Persistent storage devices, open files (I/O information)
- Process API
    - Create
    - Destroy
    - Wait
    - Miscellaneous Control
    - Status
- Loading copies code & static data, initializes heap & stack
- Process states
    - Running
    - Ready
    - Blocked
- Processes can be scheduled & descheduled. What should the scheduling policy be after one process gets blocked & another starts running?

### Chapter 5 - Interlude: Process API

- `fork` is a system call that clones current process and returns both itself (parent) and the copy (child), with different return codes
- which one gets returned first is nondeterministic, so if you want the child done first, you call `wait` in the parent branch, which gives the illusion of determinism
- `exec` is another system call that spawns a different process in the child!
- this is how we make a shell / a repl!
- `pipe` is also a system call!

### Chapter 6 - Mechanism: Limited Direct Execution

- This is the low-level part
- Our crux: Obtain high performance while maintaining control
- We could load the stuff, do the thing, then return. This would just be “Direct Execution” (no limits) - but how to get execution back? What happens if the process refuses to give control back, maliciously or accidentally?
- Introduce **user mode** (limited) vs **kernel mode** (OS runs in this mode). You trap into kernel mode and then return-from-trap back into user mode. This is done via a set of **system calls**
- **Trap table** is loaded on boot, it’s a protected set of allowed trap actions
- When a process is running, the OS is not running, but when a system call is running, the OS **is** running! So we could wait for system calls to regain control. This would be a “cooperative” approach.
- Non-cooperative approach: Have a **timer interrupt** and **interrupt handler**
- What to do when the OS regains control? That decision is made by the scheduler & scheduling policies, the high-level part. Talk about that later.
- If we have to switch, we execute a **context switch** - save register values of current thing onto kernel stack and restore values of next process from kernel stack
- Two types of switches: timer interrupt & OS decides to switch
- What about concurrency? i.e. one interrupt happens during handling another? Yes…this needs to be thought about. Could disable interrupts or put locks.

### Chapter 7 - Scheduling: Introduction

- This is the high-level part
- We’re going to make some really unrealistic assumptions in this chapter, then relax them
    1.  All jobs run for the same time
    2.  And arrive at the same time
    3.  And run continuously
    4.  They use only the CPU (no I/O)
    5.  We know their run-time upfront (most unrealistic lol) (we’ll keep this one for the entire chapter)
- We also need some metrics:
    - Turnaround time
    - Fairness
    - These are at odds with each other
- With all these assumptions, FIFO (First-in-first-out) works
- But if we relax assumption #1 then it sucks
- So we do SJF - Shortest Job First
- But if we relax assumption #2 then it sucks
- We need to relax assumption #3 too
- And we do STCF - Shortest Time-to-Completion First - interrupt the first really long job if we get incoming short jobs
- But yikes! A new metric - Response time! Because turns out computers are interactive!
- Introduce: RR - Round-Robin. We have a time-slice and rotate between them. The overhead is the time it takes for context-switching.
- Fair & Turnaround Time metrics are opposite
- Ok what about I/O? Sometimes the CPU isn’t being used.
- Treat each sub-time-slice sub-job as an independent job. This allows for overlap. See pictures on pages 73 and 74.
- But…..we still need to ditch our oracle (assumption 5)

### Chapter 8 - Scheduling: The Multi-Level Feedback Queue

- Crux: Minimize response time & turnaround time, without any oracle! omg!
- Here’s our priority rules:
    1.  If P(A) > P(B), A runs & B doesn’t
    2.  If P(A) = P(B), A & B run in RR (round robin) using the time slice/quantum
    3.  When a new job comes in, it gets highest priority
    4.  Once a job uses its time allotment at a level, its priority is reduced - this is done regardless of how many *times* it’s given up the CPU to do I/O, to prevent gaming the system
    5.  After some time period `S`, all jobs are moved back up to the top of the queue
- BSD Unix, Solaris, and Windows post-NT use a form of MLFQ