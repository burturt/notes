# Parallization basics
- Can use `fork()` syscal to fork, returning 0 for child or child PID for parent
- Must reap child (clear return value) before parent dies so resources are not wasted
- Can use `execve(filename, arv, envp)` to change execution to arbitrary executable
### Deadlock
- Mutual exclusion (at least one held resource must be non-shareable)
- A thread can hold a lock while asking for a lock
- No one can forcefully steal a lock (preempt)
- It’s possible for at least 2 threads to hold a lock that the other is waiting for (the whole cycle thing for
resource graphs mentioned in class)
- Coherence: In a shared memory multiprocessor with a separate cache memory for each processor, any one instruction operand can have multiple copies: one in main memory and one in each cache memory. Coherence means changes in the values of shared operands are propagated throughout the system.
- Consistency: The order in which memory operations (from any process) appear to
execute with respect to one another
# OpenMP
Compile with `gcc -fopenmp int.c` on macos using homebrew (not xcode) gcc
```c
#include <omp.h>
omp_set_num_threads(10);
int i = omp_get_thread_num();


#pragma omp parallel
// put above code block to indicate start parallelism
#pragma omp barrier
// put in parallel block: wait for all other threads to finish up to this point before continuing
#pragma omp critical
// Only allow next statement to execute one thread at a time
#pragma omp atomic
// Use when doing a simple update, use atomic to be faster
#pragma omp for
// Place immeidately above for loop to parallelize for loop
// Must come after parallel pragma
```
- Can also do `num_threads(NUM)` directive
Include a schedule to affect how loop iterations are mapped onto thread: 
- schedule(static, chunk): deal out blocks of iterations of size chunks (default 1) and deal them out to threads. Decides at compile time which thread will do what task.
	- when you know how much work time per chunk ahead of time
- schedule(dynamic, chunk) Each thread grabs a "chunk" whenever it is ready to do more crunching. Decides at runtime which thread will do
	- when you don't know how long each chunk will take
- schedule(runtime): pass schedule and chunk size via env or other commands earlier
- schedule(auto): schedule is left up to runtime to choose, does not need to be anyo fo the boave
```
#pragma omp parallel
{
	#pragma omp for
	for (...)
}
// same as 
#pragma omp parallel for
for (...)
```
## Locks
```c
#pragma omp parallel for
for(i=0;i<NBUCKETS; i++) {
	omp_init_lock(&hist_locks[i]);
}
#pragma omp parallel for
for(i=0;i<NVALS;i++) {
	ival = (int) sample(arr[i]);
	omp_set_lock(&hist_locks[ival]);
		hist[ival]++;
		hist[i] = 0;
	omp_unset_lock(&hist_locks[ival]);
}
for(i=0;i<NBUCKETS; i++)
	omp_destroy_lock(&hist_locks[i]);
```
## Reduction
- Reduction clause: `reduction (operator: list)`
- Create a local copy of the variable in the list, and initialized using indentity
- Updates occur in local copy
- Local copies are reduced into a single value using operator
## Barrier
- Implicit barriers: after for loop pragma, a barrier is added to the end
	- Can be removed by adding `nowait`
## Sections
- Can use sections to divide up work, or use tasks to add a "task" to the queue (often used in single section)
	- `sections` then `section` headers in section - all sections will execute before continuing out of sections
	- in `single nowait` add `task`, `taskwait` barrier equivalent. 
- Task dependencies: add `depend(IN: VAR, OUT: VAR)`, task will wait for IN var on another task with same var on OUT
## Flush
- Use flush to ensure synchronization of variable value
## Privates
- firstprivate: copy value from outside loop
- lastprivate: copy value from last scheduled task to outside parallel section (e.g. last run in a loop)
- private: mark variable as private to a thread
- 