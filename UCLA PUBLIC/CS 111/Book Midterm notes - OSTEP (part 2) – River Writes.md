### Chapter 26 - Concurrency: An Introduction

- A multi-threaded program has more than one point of execution: multiple PCs (program counters)
- Each thread has its own private set of registers
- Context switch when changing threads
    - Instead of Process control blocks (PCBs) we have **Thread Control Blocks** (TCBs), however Page Table is NOT swapped out
- Multithreaded address space
    - One heap, multiple stacks
    - See page 288 for illustration
    - Stacks are called **thread-local**
- Why use threads?
    - Parallelism
    - Avoid blocking progress due to slow I/O
    - Could indeed be separate processes, but that’s more overhead
- The main thread is also a thread! So if you spawn 2 child threads, that’s 3 threads total!
- See page 291 for a thread trace example
- When we have shared data, we get **race conditions** due to the context switches, making our program **indeterminate** (and wrong), and the output not **deterministic** (and wrong)
- Code containing race conditions is called a **critical section**
- We need **mutual exclusion** for our critical sections
- What we want is **atomicity** - the instruction sequence of the critical section must happen “as a unit”
- Crux: What synchronization primitives do we need? What support do we need from hardware & OS?
- Also, sometimes one thread has to wait for another!

### Chapter 27 - Interlude: Thread API

- A reference chapter
- Use `pthread_create` to create a thread.
    - First argument is a pointer to the thread of type `pthread_t`, use it to interact with the thread
    - The second argument is some attributes, usually `NULL` for our purposes
    - Third argument is a **function pointer** - what function should the thread start running in?
    - Fourth argument is the args passed to the function
    - See page 305 for an example
- `pthread_join` to wait for a thread to complete
    - First argument is the pointer to the thread from before
    - Second is a pointer to the value that you expect to get back
    - See page 306 for an example
- Locks!
- `pthread_mutex_lock`
- `pthread_mutex_unlock`
- They need to be initialized
- Also you should check return codes!
- Make wrappers for things that do these things!!
- Condition variables! (Chapter 30)
- `pthread_cond_wait`
    - Takes both a condition variable AND a lock
- `pthread_cond_signal`
- We always need to have a lock held when modifying `ready` and when sending a signal
- `wait` releases the lock and assumes you had a lock because you need to
- ALWAYS USE WHILE NOT IF!!!!!!!!!!!!!!!!!!
- Don’t just spin until `ready == 1` without using condition variables:
    - Spinning wastes CPU cycles
    - It’s error-prone
    - Use condition variables!

### Chapter 28 - Locks

- We want atomicity!
- The canonical example is incrementing a `balance`
- Two states:
    - Available aka unlocked aka free (0 threads have it)
    - Acquired aka locked aka held (1 thread has it)
    - Note the data type could have other private information (what’s the queue like for who gets it next? which particular thread has it atm?) but the public information is binary
- **Mutex** means **mutual exclusion**, this is the POSIX name for a lock
- Note that our code samples use capital-P `Pthread_mutex_lock` etc implying wrappers that are doing all the good return-code-checking and initialization stuff for us. Yay!
- We want to build a lock. What do we need to do?
    - The lock must work! i.e. **provide mutual exclusion**
    - It should be **fair** and **not starve anyone**
    - **Performance** - not have lots of time overheads. Three cases:
        - No contention
        - Contention (1 CPU)
        - Contention (many CPUs)
- One attempt: Lock: disable all interrupts. Unlock: Re-enable interrupts. Holy shit YIKES!!!
    - Positive: Simplicity
    - Negative: YIKES!!!!!!! You need to **trust**
    - Negative: Does not work on multiprocessors
    - Negative: You can miss interrupts from I/O such as “disk finished read request”
    - Negative: Inefficient
    - However could be used in some OS code for accessing internal data structures - OS trusts itself
- Next also failed attempt: Using one flag variable. Has a race condition (see page 320)
- Yay we need hardware support
- Test-and-set spin lock (aka “atomic exchange”)
    - Correct!
    - Not fair
    - Not great on 1 CPU, but ok on multiple CPUs
- Compare-and-swap is more powerful than test-and-set **in general**, but in this context it’s identical
- Load-Linked & Store-Conditional another option
- Also Fetch-And-Add, which gives fairness
- How to avoid spinning?
- System call `yield`
- In Solaris, we have a spin to acquire a guard lock, then we can `park` ourselves (and someone will `unpark` us). There’s also another system call called `setpark` to avoid a race condition involving being about to sleep.
- Linux uses **futex** = **fast userspace mutex** with `futex_wait` and `futex_wake`. There are also two-phase locks: spin, then sleep (hybrid approach).

### Chapter 29 - Lock-based Concurrent Data Structures

- We need to make data structures **thread-safe**.
- Start with counters
    - One option is just to wrap literally everything in the same lock
    - But this doesn’t scale well (see graph p.339)
    - Soooooooo we can have a global counter that lags behind “local” counters.
    - How many local counters? One per CPU
    - lol counting is hard
    - Code p.341
- Linked lists, but only insert & lookup
    - Assume that `malloc` itself is thread safe
    - Could just put one lock around everything, but that doesn’t scale well, again
    - When dealing with concurrent code, having a single return path out of the function is a good idea
- Queues
    - One lock for head (pop), one for tail (push)
- Hash table
    - One lock per bucket/list, uses `List_Init`, `List_Insert`, `List_Lookup` from earlier
- Don’t prematurely optimize!

### Chapter 30 - Condition Variables

- A thread may wish to check if a **condition** is true before execution
- Could try using a shared variable but this is inefficient (see code sample page 352)
- Two operations:
    - `wait`
    - `signal`
- You wait, providing a condition variable plus a lock
- They signal you, providing the condition variable
- See page 353 for an example
- Producer/consumer (bounded buffer) problem
- Assume there’s exactly 1 space in the buffer
    - We need a while loop
    - And TWO condition variables to ensure we always alternate between consumer & producer
    - See p. 361 for correct solution
- Mesa semantics - use while
- Covering condition - `pthread_cond_broadcast` - use if you cannot wake up the correct thread and must wake up everyone (but usually this shouldn’t be necessary)

### Chapter 31 - Semaphores

- Dijkstra and colleagues invented these!
- Two routines:
    - `sem_wait(sem_t *s)` - decrement the value by 1, wait if it’s negative
    - `sem_post(sem_t *s)` - increment the value by 1, wake a thread if there’s one waiting
    - (Also `sem_init`)
- `sem_init` takes 3 args:
    - `s` - pointer to semaphore
    - 2nd param is always `0` unless we want it shared across different processes (we won’t do that here)
    - 3rd param is the initial value of the semaphore. This is very important!!!
- When there are threads queued, the (negative) value of the semaphore = how many threads are waiting for it
- Implementing a lock as a (binary) semaphore:
    - Initial value is 1 (surprise, it’s called binary)
    - One thread gets it for free, then the value is 0
    - So the others must wait
- Making a parent wait for the child - initialize the semaphore with `0`, see p. 371 for code
- Producer/Consumer (Bounded Buffer) problem
    - Two semaphores, like there were two CVs (condition variables) before
    - Producer initialized with MAX
    - Consumer initialized with 0 (remember that `sem_post` can be called a lot of times and increase the value a bunch)
    - We need to be careful to add mutual exclusion, without introducing deadlock (we’ll talk about that more later)
- Reader-writer locks
    - There can be one writer at a time but many readers
    - Big starvation problem
    - We could look at the number of writers in queue to avoid starvation
    - Code p. 377
- The Dining Philosophers Problem
    - Five semaphores
    - We need to change the order of `left` & `right` for one of the philosophers to avoid deadlock
- Building CVs out of semaphores is hard
- Further reading: [The Little Book of Semaphores](https://greenteapress.com/wp/semaphores/)

### Chapter 32 - Common Concurrency Problems

- Atomicity-Violation Bugs - need locks
- Order-Violation Bugs - need condition variables
- Deadlock Bugs - T1 has L1 & needs L2, while T2 has L2 & needs L1
- Encapsulation can cause hidden deadlock (e.g. Java making vector addition thread-safe)
- Conditions for deadlock - ALL of these must occur:
    - Mutual exclusion
    - Hold-and-wait - the threads hold resources (locks) while waiting for more
    - No preemption - these resources can’t be forcibly removed
    - Circular wait - there’s a cycle in the “needing” graph
- Prevent any of the above, and you’re fine!
- Circular wait - if you can make a total ordering or even a partial ordering on your lock acquisition you’re good.
    - Cute aside: Do it based on the **address** of the lock
- Hold-and-wait - all of the locks could be acquired atomically. But this is a performance concern, and encapsulation makes this hard to do
- No preemption - `pthread_mutex.trylock()` can try to acquire. One problem is **livelock** where two threads keep trying to compete for the same 2 locks, but we could solve this with random noise.
    - Concern: Freeing up resources that were dependent on the previous thread actually having the lock, like you `malloc` expecting to keep the lock then you lose the lock…well gg
- Mutual exclusion - what if we wrote **lock-free** code? Remember compare-and-swap? That’s pretty useful.
- What if we just try to avoid deadlocks by scheduling things nicely? This is hard to do but can work for embedded systems
- Sometimes, “meh good enough” and just fail on occasion

### Chapter 33 - Event-based Concurrency (Advanced)
- Threads are hard
- Threads lack control
- So what if we didn’t use threads?
- **Event-based concurrency** - the **event loop** - see page 402 for the (short) pseudocode of an event loop
- An **event handler** processes each event
- `select` or `poll` system calls are available
- Asynchronous interfaces return immediately after being called and are non-blocking; synchronous calls are blocking
- Blocking system calls are a problem
- We need asynchronous calls for everything - **AIO control block** provided by a MAC for async I/O
    - Very confusingly-named `aio_error` is used to find out if an `aiocb` is done
    - Having to **poll** is very resource-intensive
    - Some systems use **interrupts** with Unix **signals** to inform when a request is done instead of polling
- State management is also tricky!
    - We need to do **manual stack management**
    - We use a **continuation** - record the result in a data structure indexed somehow. Then we can look up the continuation once it’s done.
- When there’s multiple CPUs we do still need locks gg
- Page faults still result in blocking - this is considered **implicit blocking** as opposed to **explicit blocking**
- If APIs change from async to synchronous we’re screwed, so event-based servers can be fragile
- async disk I/O never integrates with async network I/O as well as you’d want it to
### Chapter 36 - I/O Devices

- How should I/O devices be integrated into systems? What are the mechanisms? How to make them efficient?
- We make a hierarchical structure, see page 420
    - Memory bus
    - General I/O bus
    - Peripheral bus
- Why hierarchical? Physics & cost. To be fast it must be short! And high performance is expensive
- A canonical device has **hardware interface** & **internal structure**. Complex devices will have **firmware** installed on them (e.g. RAID controllers)
- The example/canonical interface has three registers: Status, Command, Data
- Here’s an example interaction
    - Poll (are you busy?)
    - Send data down register (if main CPU is involved then this is “programmed I/O” aka **PIO**)
    - Write data to command register
    - Poll (are you done?)
- Polling is inefficient so instead we can go to sleep and wait for a hardware interrupt
- Interrupt calls **Interrupt Service Routine** (**ISR**) aka **interrupt handler**
- This allows for overlap
- Are interrupts better?
    - Maybe not, because there’s overhead
    - Also you could livelock, all the CPU does is process interrupts
    - Two-phased / hybrid approach may be better - poll a bit then sleep
- **DMA** aka **Direct Memory Access** - [DMA](https://en.wikipedia.org/wiki/Direct_memory_access) can orchestrate transfers between devices & main memory without CPU intervention so the CPU does way less work. See picture p. 425 for a timeline
- How does the OS talk to devices?
    - Have explicit privileged I/O instructions
    - “memory-mapped I/O” - device registers are like memory locations, and OS can load/store as if it were memory
- Abstraction: device drivers
    - These represent most of Linux kernel code
    - Disadvantage: You can lose some of the HW capability due to imprecision/generalization