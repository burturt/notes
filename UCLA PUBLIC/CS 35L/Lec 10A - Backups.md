# Backups
- Problem: 100 ZB/year
	- K, M, G, T, P E, Z (10^21)
	- 2/3rds is copies: making this fraction smaller can mean huge savings
## Failure Model
- Think about how the system can fail in how backups can help
	- storage stopped working
		- Could be whole drive failure or just some small chunk of data
	- User deletes/corrupt files by mistake
	- Outside attack intentionally destroys files
	- Insider attack deleting files
- What are the chances?
	- Flash drive reliability rating: 1000 TBW (terrabytes written)
	- Disk drive: AFR (annualized failure rate), aka percent failure chance of using constantly for entire year
## What to backup?
- Data: do we need to backup *everything*?
	- Except: OS installation files, /tmp
	- Also backup metadata (info about files)
## Unit of backup
### Size unit
- Break data into smaller blocks
	- Allows building lower level backup system
		- e.g. only copy changes in larger files
- Files as units
### Time unit
- How often?
	- Extreme: backup every change,
	- Extreme: no backups
	- Should backup at important times
## Garbage collection
- Reclaim storage from no longer needed backups
## Cheap backups
- Do them less often
- Backup less data
- Garbage collect more often
- Use cheap drives
- Remote backups, trust 3rd party
- Data grooming: remove unnnesseary data, e.g. removing old accounts
- Deduplication: looks for duplicates of file content, and replaces with pointers
- Incremental backups: when backing up units, first backup is full copy, copy entire new files and ignore unchanged
	- Expensive restores
- Delta backups: backup only actual line-by-line changes, avoiding full file recopies
- Compression
- **encryption**: separate, but ensuring
- Staged backups: primary copy, then secondary copy, and so on eventually to flash
## Recovery
- Most CPU and I/O is doing backups, but also remember recovery!
- MUST test regularly, ideally automatically
## Control:
- User-controlled backups: users manage
- File system controlled backups: take burden of backing up away from user to file system
### Versioning File System
- Similar to git, but automatic
- Going to be less nice
- After editing a file `foo.c`, `foo.c` will be the oldest copy, but `foo.c.1`, `foo.c.2`, etc with previous versions
- Only keep "important" versions
	- e.g. syscall to save file descripter
- Old and traditional system, TENEX ex os
- Issues: per-file, storage overhead to store differences and lots of copies, hard to manage (many copies to dig through)
### Snapshotting File System
- Exact copy of file system as of a certain time, identical to what it was at the time
- Want snapshots to be cheap:
	- Make snapshots are read-only
	- Points to existing files until edited, then points to snapshot-only files
## Application-Specific Compression
- Example: every line of code must be stored once
### Interleaved History
- Interleaved history: gigantic table of all of the lines that have ever appeared, and each version will specify which lines from this table are in a given version
	- Recovery can be done with a sequential pass through repository to extract any version
	- **SCCS**: proprietary source code control system, orig unix
- Downside: cost of retrieval equal to length of history, and gets worse over time
### Revision Control System (RCS)
- Assumes sequential history
- Immediately after table of contents (list of version contents), include current version at the very beginning
	- Super fast: just copy first parts of the lines
- Stored reverse deltas for history: show changes going backwards
- Get historic content by taking current version and then applying changes until arriving at the version wanted
- RCS: developed at Purdue under GPL
- CVS adapted on RCS
- SVN took both concepts, bitkeeper better but paid, linux got into a fight because of licensing
	- Git created to be freely available