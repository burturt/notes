## What is a file system?
- Files in a file system **persist**
- Map abstract devices onto concrete devices
	- hard drive, flash drives
- Hybrid data structure: some lives on device, some lives in memory
### Storage hirearchy
- More CPU cycles to access as you go down, think exponential

ephemeral:
- Registers
- Caches (L1, L2, L3, L4)
- DRAM

persistant below:
- Flash drive/disk
- Network storage, magnetic tape
### Metrics for I/O
- Utilization
	- Fraction or percent
	- % of I/O capacity in use
- Throughput
	- 1/time
	- Rate of request completion
- Latency
	- time
	- Time between request and response
	- Commonly tradeoff between Latency and throughput/utilization
## Locality of reference
- Spacial locality: if you access memory address m, it's likely the app will access memory address m + 1 and m - 1
- temporal locality: accessing at address m, it's likely to access same location quickly after it
## File System internals
- Lower level, block level
	- Take storage device, divide into blocks of like 8 KiB
	- Can read or write to block
### Cache
- Also have a cache:
	- Copies user data into kernel cache when read/write
	- Fast writes
	- Issue: data just in cache
- Cache coherence: making sure cache is the same as actual storage
- `sync()` syscall: force sync buffer cache to disk
	- `fsync(int fd)` alternative for just one file, faster but still slower
	- `fdatasync(int fd)` makes sure just data is saved, not necessarily metadata
- Cache scheduling: deciding when the cache is used, and who gets to use it
	- In general, unused cache is wasted
	- Want good performance: want high hit ratio
	- Speculation: OS guesses what the process will do later
		- Read optimizations:
			- Prefetching: guess what app is about to read
				- Can use syscalls like `madvise` `fadvise` to tell how to speculate
			- Batching: Even if user asked for 1 block, grab more blocks into cache
				- Multiple high level requests in one low level request
		- Write optimizations:
			- Batching (buffer then write)
			- Dallying: Delay write, so multiple writes to the same data/area is a single low level write
## Hard Drives
- 7200 rpm = 120 Hz
- Section of curved data on disk = sector
- Downsides:
	- disk head *very* close to surface, and collision = data lost
	- Disk head cannot read when moving, connected to all platters
	- Read random sector:
		1. Send command
		2. Seek head to position (~10 ms)
		3. Rotational latency (~4 ms)
		4. Transfer time from disk to controller cache
		5. Transfer time from controller cache to RAM
# Scheduling
- Want: high throughput and no starvation
- Simple model: only look at seek time, one platter, and cost of read is simply distance between current head location and destination
- Avg seek time: 1/3 given random start and end pos
- Possible algorithms:
	- First come first served (FCFS)
	- Shortest seek time first (SSTF)
		- Minimize `|i - h|` for all pending requests
		- Increases throughput but starvation is possible
	- FCFS + SSTF: break input queue into chunks
		- within each chunk, use SSTF
	- Elevator algorithm:
		- elevator goes in one direction until reaching end, then goes back in other direction
	- Circular elevator algorithm:
		- Always increasing direction, at top jumps back to bottom
		- Advantage: fairness (longer for edges)
		- Advantage: large files are read all at once
		- Lower throughput b/c large jump
- Anticipatory scheduling
	- After doing I/O, dally a bit in anticipation of next block being read
# Flash Drives
### Basics
1. Random access is cheap/fast
	- No seek, no rotational latency
2. Flash inherently wears out (especially writes)
	- Issue: if `/tmp` is mapped to one location, will wear out a lot
3. Must erase before writing
	- erasing is slow
### Internals
- 3D array of cells
- I/O ops are parallel across slices of array
- Page = array of cells (+ error correction)
	- ~4 KiB
	- Read/write granularity
- Erasure block = array of pages
	- ~32 MiB
	- Erase granularity
	- Must erase entire erasure block, erase is expensive
- Plane = array of erasure blocks (2D)
- Channel (or die) = array of plane (3D)
### Flash Translation Layer (FTL)
- Flash controller between bus and physical storage
	- Goal: wear leveling, and wear out drive evenly
- Hides geometry and erasure blocks
- Internally contains mapping between physical and virtual pages
- Issue: Garbage Collection
	- When appending new pages, old pages may be invalidated but still taking up space as whole block hasn't been erased yet
## NVMe
- nonvolatile memory express
- Provide interface for high throughput access to flash drives
- Simple set of commands for I/O:
	- Read (ith page)
	- Write
	- Flush: commit all pending writes
		- Flash controller caches
- NVMe controller:
	- Has DRAM with IO submit circular buffer queue and completion
	- Since in DRAM, instead of using instructions you just read/write into memory, which is faster
### ZNS NVMe:
- Source: https://sigops.org/s/conferences/hotos/2021/papers/hotos21-s07-stavrinos.pdf
- Higher level interface
- NVMe Zoned Namespace
- Commands to partition drive into zones of varying sizes
- zones are like erasure blocks:
	- Erase, then only can append
	- zones have states: empty, open, closed (blocked), full, readonly, offline
	- Address zones via zone number + page number
	- No garbage collection
- Send requests in parallel, executed out of order
	- Can write() by page number, at most one pending write request
		- Request specifies page number
		- Lower latency
	- Can append(), lots of pending appends
		- Response specifies page number
		- appends can be done out of order
		- High throughput
- Zones have states:
	- empty, open (writable), closed (no access), full, readonly (faster performance), offline
	- Limited number of open zones
- Performance:
	- Write has lower latency than append (23%)
	- intrazone scales better than interzone
		- I/O to one big zone is better than many smaller ones
	- finish op (open --> full), takes 100s of ms
	- 3x read throughput than traditional flash (when concurrent I/O)
	- reset op ( --> empty), 10s of ms but 20s if lot of activity