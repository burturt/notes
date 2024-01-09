- Want reasonable responses to unusual events
	- e.g. return EFULL if full 
### Eggert Terminology
- Software errors:
	- error: human mistake
		- e.g. thought 1000 blocks, but only 990
	- fault: wrong code (bugs)
		- occurs because of an error
	- failure: bad and visible behavior of running code
		- Hardware error also lead to this
- File system correctness invariants: always true statements about the file system
## Goals
- Durability: survive limited failures in hardware
	- e.g. bad hard drive block, power failure
- Atomicity: actions never half finish: they finish or don't happen at all
- Performance: good performance despite above goals
# Potential Algorithms
- assume: read ith block, write ith block, write ith block is NOT atomic
- Golden rule of atomicity on arbitrary devices:
	- never overwrite the only copy of data you have, instead have 2 copies
## 2 copies:
- Use double space for all files, switch between 2 storage
	- Write to other storage, then use new area, and go back
	- Have 3 blocks, 0 or 1 to indicate which is used
		- if A == B, use that, if B == C, use that, else use A
## Lampson-Storgis assumptions
- Storage writes can fail, and can corrupt other blocks
- A later read can detect the bad block (checksums, read soloman codes)
- Blocks can decay spontanously, and just fail
- Failures are rare
- Repairs can be done in time (before catastrophic failure)
- Writes are atomic
## BSD sample file system
- Start to end:
	- Boot block
	- superblock (describes system)
	- free block bitmap
		- 0/1 free/not free, one bit per block
		- Present only for speed
	- inode table
		- Lists all inode-datablock pairs
	- data blocks
- Directory rename issue
### Invariants & consequences:
- Every block is used for exactly one purpose: boot, super, bitmap, inode, data, indirect
	- disaster (data loss)
- All reference blocks are initialized appropriately (e.g. lazily initialize new file system)
	- disaster (data loss)
- All referenced data blocks are marked used in the bitmap
	- disaster (data loss)
- All unreferenced data blocks are marked free in bitmap
	- free space leak
	- Break this invariant to allow atomicity
- Link count is not less than actual number
	- disaster
- Link is not greater than actual number
	- free space leak
### directory rename
- read directory 1
- increment counter
- write
- remove
- decrement counter
### Backups
- `tar cf /dev/mnt0 /usr`
	- issue: backup is not equal to any single state/snapshop
- Instead, want snapshots, with atomic snapshots