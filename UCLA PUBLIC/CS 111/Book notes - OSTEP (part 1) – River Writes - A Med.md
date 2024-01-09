### Chapter 10 - Multiprocessor Scheduling (Advanced)

- Note that I did follow their advice to read it after completing part 2
- 1 CPU: There is a memory cache that lives by the CPU and contains popular data. Nice.
- Multiple CPUs: There’s a bus connecting the two CPUs, each of which has their own cache.
- Now we have to worry about **cache coherence** - suppose you cache a piece of data D on CPU 1 but then it resumes on CPU 2 - oh no!
- To make the “right thing” happen we can use “bus snooping” to invalidate or update the other copy
- Yes, we have to worry about synchronization too - locks are needed.
- **Cache affinity** - we’d like to keep things running on the same processor for better performance
- Version 1. **SQMS** aka “Single Queue Multiprocessor Scheduling” - make one queue for all CPUs.
    - Advantage: Simplicity!
    - Disadvantage: Reduces performance due to synchronization overheads from locking
    - Disadvantage: Bad cache affinity
- Version 2. **MQMS** aka “Multi-queue Multiprocessor Scheduling”
    - Scalable!
    - Cache affinity!
    - Introduces a problem of load balancing.
    - We need to migrate jobs around, one approach is known as “work stealing,” and we need to balance how much you peek at the other queues with how much that affects your processing power
    - This is a black art
- Linux has three: O(1) scheduler, Completely Fair Scheduler (CFS), and BFS

### Chapter 13 - The Abstraction: Address Spaces

- Early systems were just OS = a set of routines / library + Current Program.
- While the CPU does time sharing, we’d rather leave all processes in memory and switch between them - space-sharing.
- Abstraction of an address space belonging to a process:
    - Code - the code
    - Heap - dynamically-allocated, user-managed memory
    - Stack - the program uses while it’s running to keep track of where it is, allocate local variables, pass parameters & return values
- Program code is fixed at the “top” with the heap “below” it. Then the stack is below that. See illustration p.123
- Note that threads will ruin everything!!!
- VM means Virtual Memory not Virtual Machine
- Goals:
    - “Transparency” which really means “opacity,” i.e. we can’t tell what’s going on, we think we just have memory when we write code & have no idea what’s going on with virtualization
    - Efficiency - we don’t want large time or space overhead
    - Protection & isolation - don’t let processes interact or touch the OS.

### Chapter 14 - Interlude: Memory API

- How to Allocate and Manage Memory
- Two types:
    - Stack / automatic memory
    - Heap memory, which is what the rest of the chapter is about
- `malloc` - a compile-time operator. Takes a size param. Don’t put an actual number here. If doing a string, use `strlen` and add 1.
- `free` - frees the memory.
- Common errors:
    - Forgetting to allocate memory - segfault
    - Not allocating enough memory (note, sometimes it may run once or twice but not always)
    - Forgetting to initialize allocated memory
    - Forgetting to free memory (memory leak)
    - Freeing memory too early
    - Freeing memory repeatedly
    - Calling `free()` incorrectly - it needs a pointer you made with `malloc` earlier
- These things are library functions, but they’re backed by system calls! `brk`, `sbrk`, `mmap`

### Chapter 15 - Mechanism: Address Translation

- Crux: How can we efficiently and flexibly virtualize memory?
- Assumptions:
    - User’s address space must be placed contiguously
    - The address space is less than the size of the physical memory
    - Each address space is exactly the same size
- Dynamic Relocation = Hardware-based Relocation = Base-and-bounds
- Dynamic because it happens at runtime
- Base - added to the virtual address to get the hardware / physical address
- Bounds - check that it’s legal!
    - Can hold the size of address space
    - Or physical address of end of address space
    - Logically equivalent, just different math gets done
- **Memory Management Unit** aka MMU is the hardware component that does this
- Page 147 for example translation
- A **free list** needs to be maintained to know what’s available to be allocated - SICP talked about this!
- Modifying base-and-bounds registers is privileged obviously
- Hardware requirements (copying a table from p. 149):
    - Privileged mode needs to exist
    - Base & bounds registers
    - Ability to translate virtual addresses & check are they within bounds?
    - Privileged instructions to update base/bounds registers
    - Privileged instructions to register exception handlers
    - Ability to raise exceptions
- OS needs to do some stuff too (p. 150 for the table):
    - Allocate memory when a process is created
    - Do stuff when a process is terminated
    - Manage memory via a free list
    - Do stuff when a context switch occurs! Save & load the right base & bounds values
    - Provide exception handlers
- See page 151 for a timeline chart thing of Limited Direct Execution for memory

### Chapter 16 - Segmentation

- How to not waste space between the stack & the heap? Remember in our picture there was ALL THAT EMPTY SPACE omg
- Ok now we need THREE base and bounds pairs, for the code, heap, and stack
    - The heap has an offset to the start when we translate
    - And the stack grows backwards!
- Sparse address space = lots of empty space
- “Segfault” comes from segmentation! (p. 158)
- Which segment does an address refer to?
    - Explicit approach - based on the address. Examine the bits to figure it out.
    - Implicit approach - based on what code was running. PC (program counter) => Code, Stack/base pointer => Stack, else => Heap.
- Btw, some memory can be shared! For example, shared libraries.
- On context switch, the OS needs to save & restore the segment registers
- How to manage the free list now? We’ll look at that next chapter

### Chapter 17 - Free-Space Management

- Paging (which we’ll see later) makes it easy, but variable size makes it hard.
- External fragmentation = when the free space is all chopped up and we can’t find a good space to allocate
- Internal fragmentation can also be a problem, if too big a piece is handed out and not fully used, but we’re talking about external fragmentation.
- We’re assuming compaction is impossible, however in higher-level languages that’s not always true, sometimes you can. But if you have pointers it’s hard to know when and where they’re used.
- We’ll talk about (1) splitting and coalescing, (2) tracking the size of allocated regions, (3) building a free list INSIDE OF THE FREE SPACE woah cool
- (1) pretty much what it sounds like. If it’s like `A` and then you allocate and it’s like `{used}{used}A` and then you free up the first part then it’s split. And then if you free up the middle, you have to coalesce.
- (2) add a header to each space, so there’s some overhead: the actual size is `|header|` + `|requested|`. The header will ALSO contain a “magic number” as some kind of checksum or something.
- (3) if the space is free, continue to have a header! Only instead of the magic number, we put the address of the next free block! Woah magic!
- We also need a system call (`sbrk` in Unix systems) to grow the heap if we’re OOM
- How should we allocate memory?
    - Best fit - go through everything, and pick the smallest that’s big enough (slow)
    - Worst fit - go through everything, and pick literally the biggest (slow, avoids having stupidly tiny pieces)
    - First fit - get literally the first one that fits (fast)
    - Next fit - maintain a pointer to where you ended the last search, and get literally the first one that fits (fast, and avoids clumping at the start)
    - Segregated lists - keep an EXTRA free list of some chunks that are precisely the right size for popular requests. Cool!
    - Buddy allocation - Only allocate in sizes of `2^n`. If your “buddy” is freed (the buddy differs by just one bit! cool!) then immediately coalesce. Wastes some memory, but finding a buddy is very quick, so coalescing is quick.
- Searching lists can be slow, so this is an area where we want to optimize

### Chapter 18 - Paging: Introduction

- Chopping things up into fixed-size pieces (as opposed to variable-sized, as with segmentation)
- The address space (virtual) is divided into **pages**
- The physical memory is divided into **page frames**
- By the way, if 32-bit is a tennis court, 64-bit is the size of Europe!
- Paging advantages: simplicity, flexibility
- We need to do **address translations** using **page tables**. Page tables are **per-process** (except for “inverted page tables,” talk about those later).
- Virtual address has two parts:
    - Virtual Page Number (VPN) (first `n` bits)
    - Offset (last `m` bits)
- Look up in the Page Table to get the **Page Table Entry** (PTE) to find **PFN** aka **Physical Frame Number** (also sometimes called “PPN” or Physical Page Number). Note that the offset doesn’t have to be translated because the virtual page & physical frame are the same size.
- Page tables are **big** and cannot be stored in hardware
- They also are **slow**
- See page 194 for a very cool memory trace graph

### Chapter 19 - Paging: Faster Translations (TLBs)

- Omg finally we’re talking about TLBs! (They have been throwing around this initialism forever)
- TLB = Translation Lookaside Buffer
- “Address Translation Cache” would be a better name for TLB
- TLB is really really important and the reason we can use paging
- Part of the Memory-management unit (MMU)
- See page 200 for example control flow algorithm
    - Look in cache. Cache hit? Yay!
    - Cache miss? Not yay.
    - Sigh ok find the PTE
    - Enter it into the TLB
    - Retry instruction
    - Also there’s a bunch of validity checking
- PTBR - Page Table Base Register
- Things that help caching:
    - Spatial locality (array elements are on the same page)
    - Temporal locality (and we access them back-to-back)
- Caching is OP but constrained to the laws of physics (it has to be small)
- Who handles a cache miss?
    - Hardware - **CISC** - Complex-Instruction Set Computers
    - Software - **RISC** - Reduced-Instruction Set Computers. They have a trap handler that’s slightly different from normal: the return-from-trap is a RETRY, not a continue. Also, their instructions need to…be read from memory…which requires use of a TLB…..let’s not infinite loop here kthx.
- Both VPN & PFN are in the cache, making it “fully-associative.” (could be anywhere) Also some other stuff…
    - Protection bits: rwx
    - Valid
    - Address Space ID - remember context switches?? We don’t want to have to swap out the entire TLB every time we do one, lol. Keep in mind some VPN could point to the same PFN due to code sharing (binaries, shared libraries)
- A DBMS may want very large page size

### Chapter 20 - Paging: Smaller Tables

- Ok, we dealt with time overhead, now we have to deal with space overhead
- Remember page tables are per-process, and mostly empty space
- One solution: Bigger pages! But, then we have internal fragmentation and wasted memory
- Hybrid approach: Combine pages & segments. One page table per logical segment instead of per process, and do away with indexing the empty space
    - Need base & bounds registers for the address of the PAGE TABLE for the segment
    - But…then we have segmentation problems: not flexible (required to have the architecture of code/stack/heap), and we have difficult external fragmentation problems again because memory is now fragmented in different sizes (in units of PTEs)
- Multi-level page tables. Top level is the **page directory** and if that entire swath has zero valid PTEs, we just, don’t. See page 220 for pic.
    - Now PFN can mean Page Frame Number (from a Page Directory) OR Physical Frame Number (from a Page Table)…
- Keep in mind TLB misses now take THREE loads from memory instead of just one (two to get the address + the actual real one)
- It’s possible to need more than two levels, too
- Another option: Inverted page table - instead of one per process, we have one for the ENTIRE SYSTEM. We’d have to make a hash table to make lookups faster, and no more detail is really given here.
- Page tables are maybe swapped to disk

### Chapter 21 - Beyond Physical Memory: Mechanisms

- It’s time to talk about swap space!
- Create the illusion of way more physical memory than you actually have
- Btw, for something like a program binary, swap space isn’t the only on-disk location that it can go
- We need to add a **present bit** to the PTE - is the page present in physical memory atm? If not, it’s a **page fault**
    - Note, it’s not REALLY a fault. But the OS has to handle it, and the way HW tells the OS to do things is via faults, so it’s a fault.
- It takes ABSOLUTELY FOREVER to handle a page fault, but it’s ok because other processes are happening while this one is blocked. Yay!
- (btw what if memory is full? We will need to develop a page-replacement policy later.)
- See p. 236 and 237 for page-fault control flow algorithm (hardware) and (software), respectively.
    - TLB hit? Yay
    - Miss? Not yay.
    - Ok Look for PTE.
    - Present? Yay, insert.
    - Not present? PAGE_FAULT YO
    - Okay go to OS (the **page fault handler**)
    - Decide on a PFN (page frame number). Is anything free? If not then evict some loser
    - READ FROM DISK OH MY GOOOOOOOOOOOOOOOOD
    - Okay set it present in the PTE and set the PFN
    - RETRY ALL THE WAY TO THE BEGINNING DUDE IT’S STILL NOT IN THE TLB
- Seriously. FOREVER.
- When does the OS actually make replacements irl?
    - We have a High Watermark (HW) and Low Watermark (LW).
    - We want HW open, but we’re willing to go until LW is open.
    - When LW is triggered, free until HW is open.
    - This is done in the background by a **swap daemon** aka **page daemon**

### Chapter 22 - Beyond Physical Memory: Policies

- This is the high-level part
- Physical memory is…a cache! Woah.
- We want to minimize number of cache misses.
- **AMAT** = **Average Memory Access Time** = Time to read from Memory + P(Miss) \* Time to read from Disk.
    - Note you’re ALWAYS paying the time to read from Memory, and sometimes ALSO paying the time to read from Disk.
    - Even a tiny miss rate will quickly dominate the AMAT
- If we have an oracle, the optimal policy is to pick the page that will be accessed the furthest in the future. We can compare against optimal to judge our policies
- Assume “Cold-start” aka “Compulsory” failures at the beginning
- Types of cache misses “Three C’s”
    - Compulsory aka Cold-start
    - Capacity
    - Conflict (not relevant here because page caches are fully-associative)
- Belady’s Anomaly - when you increase cache size, the hit rate should increase
    - “Stack property” - cache of size N+1 definitely contains all contents of cache of size N
    - my note - obeying this is like obeying Condorcet winner. maybe you just should? but, maybe not, there’s other properties too
- First-in, first-out
    - Easy to implement
    - Doesn’t obey stack property
- Random - also easy
- Least Recently Used
    - Uses history
    - We could also do least-frequently used, and this also exists
    - Principle of locality
- Most-frequently used & Most-recently used also exist, maybe these are occasionally good? But usually not
- With no locality, no one cares what we do
- 80-20 workload, 80% of references to 20% of pages, LRU is good
- Looping-sequential, random isn’t kicking out the pages we’ll need again, so that’s good
- Approximate LRU with some hardware support
    - Scan all pages in “clockwise” order
    - Set a bit to 0 for them as you scan
    - Demote the first 0 you get to, which means it wasn’t used since the last time you scanned
    - If you didn’t get to a single 0 then just go through all and demote on the 2nd rotation
    - Another option is to prefer “clean” pages (i.e. “dirty” bit is set to 0, so add an extra step)
- We can also prefetch pages - page P was used, so P+1 will probably be needed soon
- And write to disk in clusters, making them close together
- Constantly paging out is called “thrashing,” this is not good. Some OSs kill processes. Buy more memory.

### Chapter 23 - Complete Virtual Memory Systems

- VAX had small page size –> big page tables.
    - Split user address space into two, with a page table per part, and no space needed for the space between stack and heap
    - User page tables in kernel physical memory, so it could be swapped to disk
- Page 0 is invalid so it can detect null pointers
- System registers are not changed on context switch, belonging to the kernel but available to processes (protected)
- Page Table Entry has: valid, protection, dirty, 5 bits for OS use, PFN. No reference bit!
- FIFO with a second-chance list, and avoiding letting processes be memory hogs
- Two lazy tricks:
    - Demand zeroing of pages - you need to zero the page for security reasons when it’s allocated, but you don’t do it til it’s actually used
    - Copy-on-write (COW) - if a page is copied from one AS to another, you don’t actually copy it until either the original or the copy is written, instead just mapping to the original for now
- Now we talk about Linux
- Two types of kernal memory
- Kernel logical addresses
    - Can’t be swapped to disk
    - Direct mapping between kernel logical addresses & the first portion of physical memory
        - Easy to translate
        - Actually contiguous
- Kernel virtual address
- 4 KB page size
- Also “huge page” support - originally through system calls `mmap`, `shmget`, now transparently
- Lots of security concerns
    - Buffer overflow / privilege escalation (defended by NX bit, No-execute)
    - Return-oriented Programming (defended by Address space layout randomization, ASLR, kernel address space layout randomization, KASLR)
    - Meltdown / Spectre - KPTI, Kernel Page-Table Isolation