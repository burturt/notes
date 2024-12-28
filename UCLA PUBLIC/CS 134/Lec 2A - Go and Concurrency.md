## Why Go?
- Concurrency is built in
- Good support for threads and RPC
- Garbage collection
- Type safety
- Simple (OOP is non standard)
- Compiled (static and strongly typed)
### Concurrency
- 2 tasks start, execute, and complete in **overlapping time periods**
- Semantic property
- Multiple order-independent tasks executing for overlapping periods of time
- NOT simultaneous execution
- Time: refers to **window/period of time**
### Parallelism
- 2 transactions literally execute **at the same time**
- Implementation property
- Simultaneous physical execution of tasks at hardware layer
- Tasks are usually related, results combined into 1
- Time: refers to **instant of time**
## Goroutines
- Lightweight threads, executed on Go runtime, not OS
- Each goroutine "thread" has address space, program stack, registers
- Supports `start/go` to execute, `exit` to finish execution/crash, `stop` to pause execution of blocked thread allowing scheduling another thread, `resume` to continue after stop
- Threads used for:
	- I/O concurrency (spawn thread to do network/disk IO)
	- Multicore parallelism
	- Convenience (bookkeeping and timing threads)
- Thread challenges:
	- Race conditions (execution order matters)
	- Coordination (threads may not be entirely independent)
	- Deadlock (2 threads waiting on each other)
- In go: schedule as many threads as you want
- Threads are not free: take memory, slow to initialize, and challenging to work with
- Example: launch n goroutines to square all entries in a slice in parallel, where n is provided by caller
```go
package main
import "fmt"

func square(s []int) {
	ch := make(chan int, len(s))
	for i, val := range s {
		go func(index int, value int) {
			s[index] = value * value
			ch <- index
		}(i, val)
	}
	for i := 0; i < len(s); i++ {
		<-ch
	}
}
```
### Locks
- Use lock to ensure only one thread updating at any given time
```go
import (
	"sync"
);
var (
	mu sync.Mutex
	balance int
);
func Deposit(amount) {
	mu.Lock()
	defer mu.Unlock() // defer = run after function exists, guaranteed at some point
	balance += 5
}
```
### Channels
- Shared resource that threads can read and write to
	- Like unix pipe
	- Thread requests item from channel, makes updates, and returns item to channel
- Allows easy communication between threads
```
package account

type Account struct {
	balance chan int
}
func NewAccount(init int) Account {
	a := Account {
		balance: make(chan int, 1)
	}
	a.balance <- init
	return a
}
// a *Account: method receiver
// Says that in method, will update a and receives pointer rather than copy
func (a *Account) Withdraw(v int) {
	bal := <- a.balance
	a.balance <- (bal - b)
}

func (a *Account) CheckBalance() int {
	bal := <- a.balance
	a.balance <- bal
	return bal
}
```
### Channels for Coordination
```
package main

func main() {
	result := make(chan int, numWorkers)

	for i := 0; i < numWorkers; i++ {
		go func() {
			doWork()
			result <- i
		}()
	}

	for i := 0; i < numWorkers; i++ {
		handleResult(<-result)
	}
	fmt.Println("Done!")
}
```
- Channels:
	- When pulling, blocks until one item received
	- When pushing, may block if no space left in buffer/unbuffered
#### Example: timeout
```go
result := make(chan int)
timeout := make(chan bool)

go func() {
	response := // ask
	result <- response
}()

go func() {
	time.Sleep(5 * time.Second)
	timeout <- true
}()

select {
	case res := <-result:
		processResult(res)
	case <- timeout:
		fmt.Println("Timeout!")
		// At end of main, all leftover threads are killed
}

```


### Select statement
```go
select {
	case money := <- data:
		buySnacks(money)
	case money := <- mom:
		buySnacks(money)
	// If default statement left out, then select statement will block until channel received
	case default:
		starve()
		time.Sleep(5 * time.Second)
}

// Empty select blocks forever
select {}
```
### Concurrency issues to look for:
- Must use locks - no concurrent/parallel access to variables
	- `go run -race test.go`
- livelock: if using loop to check for values, include a delay to allow other goroutines to also acquire lock
- Can use Cond as well to wait in lower loop:
	- `cond.Wait()` to suspend goroutine until another thread does
	- `cond.Broadcast()` 