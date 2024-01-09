# Model OS resoucres:
- One idea: classes that under the hood are pointers to memory
	- Not usually done: not enough protection for kernel
- Instead, OS use handles (opaque identifier for object)
	- e.g. file descriptor
	- Requires syscalls
## Process (single-threaded)
- Programming "running" (runnable, may not actually be running at any given point in time) on a virtual machine
- Can do:
	- Ordinary instructions
	- syscalls
### I/O
- 2 types:
	- Network, streaming, etc stream data (one time receive), no hard cap
	- Storage devices, request/response, finite
## Syscalls
- Should be orthogonal
	- Ideally syscalls are independent of the types of syscalls we're using (other processes, files, network connection)
	- Multics, Unix, Linux, prev OS were large and complex
### File syscalls
- Linux: treats both the same
	- open/close/read/write, but `lseek(fd, offset, whence)`
	- 0 start, 1 current loc, 2 end, only for storage
- open: allocates an fd
- close: deallocates fd
- kernel saves in process table the fd link
	- fd limit: `ulimit -a`
### Process syscalls
- `pid_t fork(void)`: clones current process, both running simultaneously 
	- Return: -1 if fork failed, 0 means process is child, >0 means process is parent, and is PID of child
	- Clones registers (except rax b/c return value), fd, memory
- `_Noreturn void _exit(int)`, pass in exit status. 0 = success, any other = fail, max value 255
	- c `exit(int)` does extra stuff before calling syscall exit
- `int kill(pid_t p, int sig);`: force stop *another* process with signal `sig`
	- 0 for success, -1 if not
- `pid_t waitpid(pid_t pid, int *status, int options);`
	- Call on own immediate child only
		- If parent exits before child, then child's parent set to 1
		- `systemd` calls `waitpid(-1, &st, WNOHANG)` to get PID if one exists, but immediately fail if not
			- Known as reaping processes
	- kill and exit does NOT deallocate process from process table
		- if exit but never waited, it is called **zombie**
	- returns when child exits
- `execvp(char[] path, char*[]`: converts current process to running program
- Processes run in isolation EXCEPT:
	- waitpid
	- kill
	- read/write to file
	- shared memory (`shm*` syscalls calls), but rare
	- network connections (sockets)
	- pipes
	- resource limitations (e.g. fork bombs)
### Example fork program: print date
```c
void printable(void) {
	pid_t p = fork();
	if (p < 0) error();
	if (p == 0) {
		execvp("/usr/bin/date", (char *[]){"date", NULL});
	}
	int st;
	if (waitpid(p, &st, 0) < 0) error();
	if (!WIFEXITED(st) || WIFEXITSTATUS(st) != 0)
		error();
}
```
#### posix fork alternative
- Said parent should not be able to fiddle with child before `exec`
```c
int posix_spawnp(pid_t *restrict pid, const char *restrict file,
                       const posix_spawn_file_actions_t *restrict file_actions,
                       const posix_spawnattr_t *restrict attrp,
                       char *const argv[restrict],
                       char *const envp[restrict]);
```
- restrict: promise that pointer is unique to point at data
### File Redirection
- Equivalent of `./a.out <a >b 2>&1`
```c
p = fork();
if (p == 0) {
	// fiddle around with fds
	int f = open("a", O_RDONLY | O_RDWR | O_CREATE); // read only, read write, create
	if (f < 0) error();
	// dup(fd) will return duplicate fd
	if (dup2(f, 0)) { // make std a duplicate of f
		error();
	} 
	// dup2(1, 2) < 0
	if (f != 0) close(f);
	execlp("./a.out", ...);
	
}
```
- pipes: `pipe(int fd[2])`
	- fd set to e.g. `{5, 7}`, 5 is read end, 7 is write end, so 7 --> 5
	- Implemented with circular buffer
#### What could go wrong fd?
- Trying to use a closed fd/never opened: write fails, returns -1, errno == EBADF (bad fd)
- leaks (fail to close fd but open more)
- open, but resource has gone away (e.g. unplugged USB drive): return -1, errno == EIO or similar
- read past EOF: return 0 means EOF
- read but no data yet (e.g. network): hangs/blocks
#### What could go wrong pipes?
- everything that goes wrong with fd
	- read no data, read no data and no writers (closed write fd, same as EOF),
- write to pipe but no readers:
	- returns -1, errno == ESPIPE
		- Issue: many programs don't count for failing prints
	- (default): send SIGPIPE to program, default action is kill program
- Process leaves write fd in pipe open, causing read on read end to hang forever
