## Basic POSIX idea:
```c
pthread_create();
...
pthread_join();
```
- Embarassing parallelism: no thread reads on shared mem that another thread writes to
	- Easy to do, ideal
- Default threads: unsynchronized
	- race conditions are default behavior for performance
- Want 2 things:
	1. Isolation (know when threads are independently operating)
	2. Sequence coordination (be able to ensure some actions happen after other actions in other threads)

## Example: bank deposit/withdrawel
```c
long balance;
void deposit(long amt) { balance += amt; }
bool withdraw(long amt) { 
	if (amt <= balance) {
		balance -= amt; return true;
	}
	return false;
}
```
- If deposit and withdraw at same time: check happens in withdraw, then deposit calculates new balance (not atomic) in temp storage, withdraw withdraws, and balance sets original deposit value
## Race Conditions
- **Necessary for synchronization:**
	- **Mutual exclusion**
	- **Bounded wait**
### Solution: lock
- lock & unlock function, lock function, then sensitive, then unlock
- Section in lock: critical section
	- At most one thread should execute in critical section
	- What goes in critical section?
		1. look for writes to shared memory
		2. look for dependent reads (reads later used for write)
		3. Keep critical sections as small as possible
```
typedef lock_t;
void lock(lock_t *l) // grab exclusive lock, must not already hold
// Returns null ptr if couldn't get lock
void lock(lock_t *p) {
	while (!cas(p, 0, 1)) {
		continue;
	}
	// OR
	while (xchgl(p, 1) == 1) {
		continue;
	}
}
void unlock(lock_t *l); // release lock YOU HOLD
```
- Locks should be fine-grained
	- Don't have a global lock
### Solution: Atomic actions
- Hardware atomicity
- Instructions that will happen all at once
- Architecture specific
	- Stores/loads not atomic usually
	- x86-64: 1, 2, 4, 8 bytes & byte aligned AND must be done properly
	- ex. `lock incl i` atomic increment
- Not all possible interactions have atomic correlations
	- Some ways around: compare-and-swap atomic instruction
	- `bool cas(int *addr, int old, int new)`, only sets to new if address is equal to old, otherwise return false
- Large objects (>8 bytes) cannot be atomic
	- e.g. strings
- Tiny values (1 bit)	
	- `unsigned int a:1;` 1 bit int val
		- Done via byte load-store, or even whole word
		- Put 2 in one struct, all in one byte, try to change both at once = issues
## Example: pipe
```c
struct pipe {
	lock_t l;
	char buf[1024];
	unsigned r, w;
}

bool write(struct pipe *p, char b) {
	lock(&p->l);
	bool ok = p->w - p>r < 1024;
	if (ok)
		p->buf[p->w++ % 1024] = b;
	unlock(&p->l);
	return ok;
}
// proper way
struct pipe {
	struct bmutex b;
	condvar_t nonfull;
	condvar_t nonempty;
	char buf[1024];
	unsigned r, w;
}
void write(struct pipe *p, char b) {
	acquire(p-b);
start:

	wait(p->nonfull, p->b);
	// Check again pipe is not full
	if (p-> w - p->r == 1024) goto start;
	p->buf[p->w++] = c;
	notify(p->nonempty);
	release(&p->b);
}

```
- 2 locks, one read one write?
	- reader needs to know where writer is to prevent reading past
- Polling issue:
	- spinlock when trying to get lock, using up CPU
	- Solution: blocking mutex
- Issue: blocking mutex doesn't always work
	- e.g. if pipe is full, must still fail even if mutex aquired
### Blocking mutex
- Add process to queue of wanting lock
- Once available, blocking mutex spins up process
- Process table entries:
	- linked list of entries waiting for mutex
	- blocked field so scheduler doesn't run
```c
struct bmutx {
	lock_t l; // spin lock
	bool locked;
	proc_t *blocked_list; // process table entry of threads that are blocked
	// List processes waiting for this mutex
}
void acquire(struct bmutex *p) {
	for (;;) {
		lock(&p->l);
		add self to blocked list;
		self.blocked = true;
		if (!p->locked)
			break;
		unlock(&p->l);
		yield();
	}
	p->locked = true;
	unlock(&p->l);
}
void release(struct bmutex *p) {
	lock(&p->l);
	set all processes in block list to be runnable/remove from block list and for each process set blocked false;
	p->locked = false;
	unlock(&p->l);
}
```
### Semaphore
- Like a blocking mutex but allows N threads to aquire lock, where N is user-chosen
### Condition variables
- Blocking mutex + condition
API:
```c
wait(condvar_t *c, bmutex_t *b) {
	PRECONDITION: *b is acquired;
	release *b, then blocks until some other thread notifies OS condition is true
	reaquires *b, returns
}
notify(condvar_t *c) call this whenever condition is or may have become true
broadcast(condvar_t *c) like notify but wakes up ALL waiters
```
- Avoids spinning