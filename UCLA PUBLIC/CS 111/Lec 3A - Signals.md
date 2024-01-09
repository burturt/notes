### Example issue: grep
- Grep uses ANSI escape sequences
	- e.g. `test ESC[1;31m highlighted ESC[0m`
	- Does NOT output reset escape code on ^C
		- Needs to output 4 bytes after call
# Signals & Alternatives:
- File /proc/self/controlC that returns whether user does ^C
	- Issue: requires each program to add ability, requires polling, has overhead
## Application areas for signals
- Unusual events
	- Power loss
	- User: control-C (SIGINT)
		- kill -9 (SIGKILL), **cannot be ignored**
		- C-z (SIGTSTP. terminal stop aka suspend)
		- SIGCONT: continue suspended process
	- SIGSTOP: process stopped another process, not a user, like SIGTSTP but **can't be ignored**
	- SIGUSR#, like SIGUSR1: custom user specific signals
	- I/O
		- SIGPIPE: write to pipe with no read
		- SIGIO: when I/O issues
	- Death of child: SIGCHLD, usually want to do waitpid on it
	- User went away (SIGHUP): hangup
	- Timer expired: SIGALARM after doing `alarm(int sec)`;
	- Invalid program, SEGSEGV, SIGBUS (invalid addresses)
		- SIGFPE: floating point exception, but includes int so bad arithmetic
	- SIGILL: illegal (invalid) instruction
		- `asm("loadcr3 %rax")` or `asm(".byte 0xff")`
## Signal Handling
- Possible Actions:
	- Nothing: process ignores it (SIGCONT)
	- Program immediately exits: exit status 128 + signd#
	- Process stops (SIGSTOP, SIGTSTP)
	- **Calls a signal handler (in user mode)**
		- Process execution continues to signal handler function
		- Use `signal` syscall to set up signal handler
		- `typedef void (*sighandler_t) (int)` 
			- sighandler_t is a pointer to a function that takes int and returns void
		- `sighandler_t signal(int signum, sighandler_t handler);`
		- `signal(SIGHUP, SIG_IGN);` to just ignore signal
```c
void handle_control_c(int sig) {
	write(1, "\033[0m", 4);
	_exit(3);
}
// in main
signal(SIGINT, handle_control_c);
```
### Issues with signals:
- Could happen between *any* 2 instructions
- As such, memory could be screwed up by signal handler functions
	- e.g. use malloc may stomp on stack
- Rules:
	- No malloc, only "async-signal-safe" or sometimes "reentrant" (safe to call itself) function calls
		- exit not safe
		- `_exit`, `write`, `signal` safe
- Critical section: code should NOT be interrupted
	- Keep short and sweet
	- Mark this to cause signals to pile up and arrive at end
	- `pthread_sigmask(int how, sigset_t const * restrict set, sigset_t * restrict oldset);`
		- sigset_t: set of signals, oldset tells you what was set originally