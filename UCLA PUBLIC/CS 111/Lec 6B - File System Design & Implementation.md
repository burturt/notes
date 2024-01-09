
- Performance of file systems:
	- Latency
	- Throughput
	- Space efficiency
- Robustness/safety
	- How well does fs perform with I/O errors/power failure
	- Crashes
	- Bad devices
### FS are hybrid data structures
- Partly in RAM, L1 cache, and 2ndary storage
#### NUMA architecture
- Non-uniform memory access
- NUMA RAM: RAM access closer to CPU is faster than further away
## inodes
- Tells you:
	- Metadata
	- addresses of file data
	- File type
- Does NOT tell you:
	- File name
	- inodes can be pointed to by multiple files
- Issue:
	- Can require multiple seeks across disk to read just one file
	- Deletion is more complex: reference counts (link count) to inode, and only fully delete when 0 references AND no processes reading from file
		- Prevents `(rm a; sort) < a`
		- Issue, if count is 0 but crashed before removed, it's still there
### fsck
- Looks at `/dev/sdc` raw data
- Scans for problems: e.g. files with link count 0
## Renames of files
- Rename in current directory: read one block, write one block
- Rename between different directories: 
	- Increment inode link count
	- Create an entry in dest directory
	- Remove entry in src directory
	- Decrement inode link count
- `ln` is steps 1 and 2 for `mv`
## Directory entries for varying length file name components
- linux limit: 255 bytes, but space wasted on short names
- ext2/3/4: directory entries
	- 32-bit inode #
	- 16-bit next directory entry length
	- 8-bit name length
	- 8-bit file type - *Duplicated from inode for caching, find command like stuff*
	- File name (length given by above number)
	- Deletion: update directory entry length to jump to next one
		- Limit writing needed for directory entry deletion
	- Does NOT contain data like file size b/c it can change, but file type doesn't change
- components: between/after slashes
### Issue
- No hard links to directories
	- Do not allow hard links to directories
	- Link count for directory = # of subdirectories + 2
	- Prevent cycles in data structure
# Current Working Directory
- Stored in process table
	- Change it in using `chdir`
- open syscall looks relative to current working directory of process
- If starting with `/`, usually set inode like 1 or 2
## Example bad code
```c
int main (int argc, char **argv) {
	if (chdir(argv[1] < 0))
		return perror("chdir");
	return 0;
}
```
- Issue: when running this program, the currect directory will change own process's current directory, then go away, unchanging the caller (shell)
- cd in shell is a builtin
# Root directory
- Can change root directory with `chroot()`
	- Runs in little sub area of full file system
	- Each process has own chroot
- Issue: `chroot` allows changing uneditable files (e.g `/etc/passwd`)
	- Result: chroot usable only by root
# File Types
## Symlink links
- `ln -s src dst` = `ln -s symlink_destination symlink_file_name`
- file whose contents are interpreted as a file name
- During namei (name to inode) transversal, directory/file links are expanded
- Dangling symbolic links: symlinks don't necessarily have to actually point to a file
- `namei` has a symlink limit: number of symlinks to follow before erroring out ELOOP
- Editing symlinks:
	- `link`/`unlink` syscall does not follow symlinks on last component
	- `lstat("foo", &st)`: give status of files
## Other file types
- d = directory
- _ = regular file
- l = symlink
- c = character special file
	- `mknod /dev/null c 1 3` (root, 1 and 3 are major and minor kernel numbers)
- b = block special file (c but read in blocks)
- p = mkfifo: create named pipe (file that points to pipe)
## Multiple Drive Management
- inodes are specific to drives
- every file as inode AND device (dev) number
- Table of mounted filesystems:
	- May have lots of devices
	- lots of filesystem types: e.g. cameras usually use VFAT, but probably ext4 on linux
	- Maps device number-inode pairs to devices
	- namei always consults mount table on every traversal
### Mount/Unmount
- `mount /dev/sdc1 /home/bigusr`: move root of file system (first argument) and overlay over /home/bigusr (must already exist)
	- `umount /dev/sdc1`
- Issue: mount won't allow multiple mounting to prevent cycle
- Issue: cannot unmount if any process current directory is on mount
- Object Oriented: Filesystem (abstract) parent with ext4, VFAT, etc children
	- Use C class hirearchy with structs
```c
struct fsapi{
	DIRENT(*dlookup)(DIR, FNC);

}
struct fsapi const vfat {
	...
}
struct vfatfsapi {
	structure fsapi parent;
}
```
### Linux process structure w/ files:
- Task points to filestruct points to file (open file), inode (open or not), points to inode operators, points to code like unlink and dirlookup
	- file (open file) points to file operations, which points to code for read and write
