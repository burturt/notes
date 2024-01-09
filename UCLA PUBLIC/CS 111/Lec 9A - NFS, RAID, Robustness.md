# Network File System
- Protocol for sharing files on network (SMB, etc)
- NFS file handles: file descriptors but must work over network
	- file handles must survive crashes
	- Specify file without reference to server state in RAM
		- use device inode pairs but with way to access file given inode number
- Result: Stateless servers
	- Server state in RAM can be lost (server reboot) without losing user data and without the client noticing (except for performance)
## Protocol
- looks like syscalls but not quite
- `LOOKUP(dir_filehandle, name)`, name is free of slashes
	- Response: filehandle and attributes
- `CREATE(dir_filehandle, name, attrs)`
	- Response: filehandle and attributes
- `MKDIR(dir_filehandle, name, attrs)`
	- Response: filehandle and attributes
- `REMOVE(dir_filehandle, name)`
	- Response: status
- `RMDIR(dir_filehandle, name)`
	- Response: status
- `READ(filehandle, offset, size)`
	- Response: data
### Differences compared to linux
- Translate lseek and read --> single READ
- open, unlink, read: even if no links, do not actually remove file until all processes are done
	- Solution: clients keeps track of NFS files open on client but with link count 0
		- rename to `.nfs192631`
		- Once actually closed/exits, remove the temp file
		- Only works if everyone on same client: 2 people each holding process
	- Solution: stale file handler
		- if reading from file that doesn't exist, read fails, errno = ESTALE
### Problems
- Synchronization: possible to read old data after write occurs b/c latency
- Caching: syscall might think it is succeeded when only written into local cache
	- Other clients will read old data
- NFS does not guarentee **write-to-read consistency**
#### Solutions:
- `fsync(fd)`: force flush all caches on file
	- Heavy on NFS: flush and wait for responses
	- Not many people know
- close-to-open consistency:
	- close force flushes cache to server
	- close can now fail!
## NFS nowadays:
- Many drives connected via 2 connections to a bus, and multiple nodes connected to this bus, with multiple network connections
- Highly Available (HA): keep running with no loss of data if any single HW component fails
- Benchmarks for HA servers: SPEC storage solution benchmarks:
	- genomics
	- swbuilds (building linux kernel)
	- NetApp 8-nodes AFF A900
		- 2 nodes/HA pair, each 2 TiB ECC RAM, 128 GiB NVRAM (non-volatile), 50x 100 GbE network connections for internal and external, 96x 2TB NVMe drives
		- ![Screen Shot 2023-12-09 at 5.39.30 PM.png](../../_resources/Screen%20Shot%202023-12-09%20at%205.39.30%20PM.png)
			- Want to be before "knee" where capacity is hit, and exponential ms latency
# RAID
- Media failure (drive failures)
- Logging is not enough
- Redundant Arrays of Independent Drives
- RAID 0-5 exists, 6/7 marketing
Assume A, B, C, D, etc is the data split up
## RAID 0
- Simulate 1 big drive with multiple smaller ones
- Simplest: concatenation
- Striping: distribute data across all 3 drives to encourage concurrent drive usage
	- e.g. [A1B1C1] [A2B2C2] [A3B3C3], appears to system as [A1A2A3B1B2B3C1C2C3]
	- Note: hard to grow full system with striping
## RAID 1
- copy data across both drives, mirroring
	- [A] [A]
	- Write: write to both drives
	- Read: read from either drive
	- Drive failure: replace bad drive, and then rewrite drive by copy data from one to other
		- Can take hours, and drive can fail during this period
		- Performance suffers during recovery
	- Costs 2x storage
## RAID 4
- N physical drives per logical drive
- example with 4 drives: [A] [B] [C] [parity: A ^ B ^ C]
	- Read: just read corresponding drive
	- Write: write to corresponding drive *and* if changing 1 to 0 or 0 to 1, change parity bit
		- Write 2 blocks (in write block and parity), but blocks often cached
	- Drive failure: if parity, recompute, else use parity XOR to recover others
- Downside: parity drive is hot: bottleneck (throughput and latency) and significantly more wear
- Growth: very easy, initialize to 0!
## RAID 5
- RAID 4 + striping (almost)
- example 4 drives: [A1B1C1P1] [C2A2P2B2] [B3P3C3A3] [P4A4B4C4]
	- Parity exclusive or of same index in drives, e.g. P4 = A1 ^ C2 ^ B3
- Advance: better throughput and more equal wear (especially on flash)
- Growth: difficult: requires rebuild to move stuff around
# RAID Failure Rates
- Individual drive failure
	- AFR: Annualized Failure Rate
		- Actual: Bathtub curve (PDF)
			- High failure rate at beginning, low flat for ~4 years, then slow increase
	- RAID 4 rate: low initially, but CDF larger than just 1 drive over time
![Screen Shot 2023-12-10 at 8.08.19 PM.png](../../_resources/Screen%20Shot%202023-12-10%20at%208.08.19%20PM.png)

