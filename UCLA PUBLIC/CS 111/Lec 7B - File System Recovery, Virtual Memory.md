# File System
- Idea around bad hadware: losing power
## Commit Record
- Costs: reads, writes, space
- Leverage off of atomic small write units to single blocks to implement "large" atomic writes
- Attempt 1: 2 copies of data, have record showing which one is the current copy
- Attempt 2: Use commit record to show old to new state, save space
- BSD file system (Free BSD)
### More generally:
- Process
	- Read/write atomically
	- Commit (write commit record)
	- read/write atmically (performance, cleanup)
## Journal
- Log-based filesystem
	- Originally 2 devices: one device just wrote in append only, so very little movement
- 2 partitions or 2 devices:
	- Log transactions in log partition, then update data, then 
- Cell data:
	- Array of blocks
- Log
	- Append only
- How to work:
	- Put data into log with position
	- commit
	- copy into actual data
	- If power failure, look at latest journal log to fix
### Write-ahead log
- write planned changes into log
- write commit record into log
- then finally copy planned changes into cell data
- Rebooting requires 'redo' operation to recover new data
### Write-behind log
- Write old values into log
- Write new values into cell data
- Write commit record
- Rebooting requires 'undo' operation to recover old data
## Recovery:
- "fsck" phase
- Want to be fast: do not scan entire file system
- Recovery must be idepotent: f(x) = f(f(x))
	- repairs in progress should be crash safe
- Will need commit "abort" ability in case write fails (e.g. out of space)
### Volatile cell data
- Main memory file system
- Uses RAM for file system (speed), but recoverable via log
	- Networked file system
## More complex
- Let users see "inside" transactions
	- Know when looking at data that is tentative
	- Users need ability to do own transactions: need to know if commit actually occurred, and abort if lower level aborts occurred
	- **cascading aborts** or **compensating actions** and repair from underlying abort
## Interaction between block I/O scheduling and commit records
- Result of dallying/reordering writes: commit records won't be written at right time
- Simple (but bad) fix: disable I/O scheduling
- better: tell low level system about write dependencies
# Programs
- Issue: Bad memory references, how to deal with it
## Solutions
- Hire better programmers
- Runtime checking in your program
	- e.g. gcc -fsanitize=address, (2012, written by google)
		- Not perfect, slower program
	- Intel MPX (2013-2018): hardware support for bad pointer
		- 4 128-bit registers
		- Pointers contain base and bounds of memory, validated by hardware
		- Issue: hardware overhead too much
	- CHERI (cambridge university)
		- base/bounds stored in 64 bits, 64 bit pointer, and valid bit in pointer
		- ARM, RISC-V
- SIngle Segment Access
	- 2 new registers: base register and limit register
		- If access memory, must be between BR and LR
		- Must be privileged registers (only changable by kernel)
		- Downsides: process sizes preallocated (can't grow)
			- code must be **position independent**
- Segmented Memory
	- pointers contain segment number and offset
	- Segment table: base and limit into ram
	- Issue: moving around when growing segment may have high cost
		- External fragmentation: so not very popular
	- Code segment, data segment (initialized data), bss (zeroed out data) heap segment, etc
- Paged Memory
	- Like segmented but pages are all the same size
	- More flexibility, but more pages to manage
	- virtual page number (vpn) and offset
		- VPN is index into page table
		- Page tables contains base address into physical memory
		- Use multi-level page table
	- Page tables: divide vpn into 4 13 bit segments
		- hardware caches into translation lookup buffer (TLB) to speed up
		- Each entry: leftover bits at start and end
			- Used for valid bit, readable bit (r-bit), writable, executable (newer), kernel bit (accessible only if in kernel)
			- Kernel can see page table, and user programs cannot
		- Per-process
	- Page table pointer stored in cr3 (privileged) register
		- MMU (memory management unit) looks at this register
		- Do not need to change cr3 and invalidate TLB cache if switching threads
### Page Faulting
- Program attempts to use virtual address that is not allowed by current page table
- Trap into kernel, kernel decides what to do:
	- Simple: terminate program (core dump)
	- Send signal to program (SIGSEGV or SIGBUS), return to signal handler
	- Repair page table to make failing instruction a valid one, and return back to program (reexecute failing instruction)
		- Use for swapping data between RAM and Flash!
		- Page fault = accessing a page not currrently in RAM
- Page fault mechanism:
	- Kernel knows process that trapped, and virtual address (va) of trap
	- Kernel knows map between virtual address to flash
	- Kernel reads secondary storage address (ssa) page into RAM, and must find spot:
		- Question: which page are we evicting from RAM?
		- Pick page to evict
		- Write page from victum page page num into swapmap(p, va)
		- Write secondary data in swap map
		- Read a page from ssa into RAM
- Want to minimize Page faults
#### Page Fault Victim Choosing
- Choosing victim page to be put into swap space
- Policy question. Goals:
	- Minimize number of page faults
		- Maximize cache hits
	- Minimize cache size
- Trace of access: list of accessed pages
1. Pick at random (not smart)
	- locality of reference makes bad (temporal locality)
2. FIFO
	- Issue: increasing RAM could increase cache miss
	- Belody's anomoly: spending more money does not automatically make problems better
3. Look ahead
	- Theoretical best, can't actually predict future
4. Least Frequently Used: remove least previously referenced by count
5. Least Recently Used (LRU) (Oldest removed first)
	- Can be bad
- Sidenote: can use `madvise(buf, size, expected_use)` to help page replacement algorithm
	- e.g. MADV_SEQUENTIAL to evict `-1` index pages since you are going through sequentially, or MADV_RANDOM to just use random, MADV_UNUSED buffer not expected to use soon
	- Implementation options:
		1. Hardware maintains clock, and stores time into page table when accessed
			- Idealized, but asking too much from hardware
		2. Software approximation:
			- Kernel periodically set r/w/x = 0, trap, add one in own page table mimic, restore r/w/x return
- Techniques to help:
	- Demand Paging (lessen startup latency)
		- Only bring first program page with code into RAM, rest of pages are always swapped by default
	- Dirty Bit: 1 if page in RAM != swap, 0 means equal
		- Avoid I/O on swap if RAM contents unchanged from last swap
		- More likely supported by handware, but mostly in software:
			- Copy-on-write: page is read only, trap when trying to write, and at that point set dirty bit and then return back
	- `void* mmap(void *addr, size_t length, int prot (r/w/x), int flags (shared w/ other processes/private (normal)/fixed), int fd, off_t offset);`
		- map file contents `fd` w/ offset and length into memory
		- malloc, free, new, etc uses this
## Illusion of aloneness
- Security
- Sanity: makes programming easier to not manage multiple threads
- RAM is a cache for swap space!
