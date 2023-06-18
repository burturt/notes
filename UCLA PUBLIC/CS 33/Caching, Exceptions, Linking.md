![Screen Shot 2023-06-01 at 3.03.26 PM.png](../../_resources/Screen%20Shot%202023-06-01%20at%203.03.26%20PM.png)
- If we encounter a hit on the way up:
	- Write-through: (change memory in cache and update to next levels immediately)
	- Write-back: (change memory in cache but defer write to next level until it is evicted from cache). Requires the use of dirty bit
- If no hit:
	- Write-allocate: (pull into cache and change local copy)
	- No-write-allocate: (change copy in higher level without pulling into cache)
- Miss Rate: misses / total accesses = 1 – hit rate
- Hit time: How many cycles needed to get the data if you hit the cache
- Miss Penalty: If there is a miss, how many additional cycles needed.
	- Example: cache hit time of 1 cycle, miss penalty of 100 cycles
	- 97% hits: 1 cycle + 0.03 * 100 cycles = 4 cycles
# Exceptions
- Program State Control Flow: Jump, Call, Return: Change RIP within the Program
- Exceptional Control Flow: Exception: Give the control to OS to handle
Some events (context switch, signals, non-local jump)
	- asynchronous exception: Caused by external events: eg: Timer or I/O interrupt
	- Synchronous exception: caused by events within the program
		- Traps: Intentional: system call, breakpoint
		- Fault: Unintentional but recoverable (page fault)
		- Aborts: Unintentional, bad
# Linking
- Why divide program into multiple Files and generate multiple object files and link them?
	- modularity: collects common functionalities in separate binaries
	- Efficiency: If we change one source file, no need to recompile other
## Static:
![Screen Shot 2023-06-05 at 8.51.51 PM.png](../../_resources/Screen%20Shot%202023-06-05%20at%208.51.51%20PM.png)
- All programs will contain copy of library
### Packaging
![Screen Shot 2023-06-05 at 8.52.02 PM.png](../../_resources/Screen%20Shot%202023-06-05%20at%208.52.02%20PM.png)
## Dynamic
![Screen Shot 2023-06-05 at 8.52.47 PM.png](../../_resources/Screen%20Shot%202023-06-05%20at%208.52.47%20PM.png)
- Loads only one copy of library into memory