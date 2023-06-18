- Memory Management Unit does lots of work
- Page table: array of page table entries (PTE) that maps virtual pages to physical pages
- Page hit: hit physical memory (cache hit)
- Page fault: hit non-physical memory (cache miss)
	- Move into memory and evicts if necessaray 
	- Waiting until the miss to copy the page to DRAM is known as demand paging
- Programs access a set of active Virtual Pages called **working set**
	- If working set < main memory, good performance
	- If not, **thrashing**: constant swapping
![Screen Shot 2023-06-09 at 8.22.25 PM.png](../../_resources/Screen%20Shot%202023-06-09%20at%208.22.25%20PM.png)
- VM pages can have memory protection bits, like read/write/exec
## Math
![Screen Shot 2023-06-09 at 8.23.55 PM.png](../../_resources/Screen%20Shot%202023-06-09%20at%208.23.55%20PM.png)
![Screen Shot 2023-06-09 at 8.24.11 PM.png](../../_resources/Screen%20Shot%202023-06-09%20at%208.24.11%20PM.png)
- Page Table Entries cached in L1
	- Translation Lookaside Buffer (TLB) speeds up translation, maps virtual page numbers of physical page numbers
	- Uses VPN/Virtual Page Number to access TLB
![Screen Shot 2023-06-09 at 8.43.09 PM.png](../../_resources/Screen%20Shot%202023-06-09%20at%208.43.09%20PM.png)
![Screen Shot 2023-06-09 at 8.43.14 PM.png](../../_resources/Screen%20Shot%202023-06-09%20at%208.43.14%20PM.png)
- With a TLP, steps 2/3 replaced with cache
