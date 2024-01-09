## Processes vs Threads
- Processes:
	- Expensive: fork clone requires tons of data copied (memory, registers, fd)
	- Not copied but separate: accumulated execution time (child at 0), file locks (child has none), pending signals (child has none)
	- After fork: very little ability for child-parent communication
- (kernel) Threads:
	- Cheap
	- Like processes but share as much as possible
	- Share memory, fd, accumulated execution time, file locks, pending signals
		- NOT registers or shared stack memory
- REALLY cheap threads
	- Avoid kernel context switching
	- 
# pthread.h
```c
#include <pthread.h>
// pthread_t is a int, etc
// pthread_attr_t show attributes like real-time thread
// start func called in thread with args arg, use struct to enter multiple args
int pthread_create(pthread_t *thread, pthread_attr_t const *attr, void (*start) (void *), void *arg);
// returns -1 if fails

// wait for a thread to finish (waitpid for thread)
int pthread_join(pthread_t thread, void **value_ptr);
// send signal to thread
int pthread_kill(pthread_t thread, int sig);
// "polite" kill, kill only at cancellation points (which are syscalls marked for cancellation)
// Avoid worrying about critical sections
int pthread_cancel(pthread_t thread);
```
- Notes:
	- Signals are sent to a random thread (many programs just ignore signals or block on all except one)