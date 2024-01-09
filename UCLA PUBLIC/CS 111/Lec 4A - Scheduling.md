- Scheduling needed when more threads than cores
- CPU scheduling: specific case of more general problem: I/O, CPU, GPU, network, Memory, etc
# Dispatch mechanisms for scheduling
- Scheduler: kernel code (needs the CPU to determine CPU usage)
	- Fast but not stupid
## I/O Models
- Busy waiting: thread keeps cpu while waiting for I/O
	- `while (!ready) continue;`
- Polling:
	- `while (!ready) yield()`
	- Let another program to run for some time while waiting
- Blocking: `while (!ready) tell kernel to wait for device, then yield`
	- Smart kernel: only returns execution when device is ready
## Types of scheduling:
- Cooperative scheduling
	- When program calls syscall, it will call scheduler
	- programs do syscalls
	- Assumes all processes are friendly, and takes a single progress to not do a syscall to cause issues
	- example: orig macOS used cooperative scheduling
	- Used in embedded systems: simpler
- Preemptive scheduling
	- Hardware clock: every 10 ms, signal sent to CPU to timer trap/interrupt
		- Manual syscall every 10 ms
		- Human reaction time
## Scheduling metrics
- Arrival time: time task arrives
- Exec time: first execution starts
- Wait time: time between arrival and first exec
- Response time: time between arrival and **1st output**
- Turnaround time/latency: time from arrival to finish
- Burst/run time: time from start to finish on CPU
- Average Latency
- Average wait time
- average response time (interactive)
- throughput: # of jobs per time
- Utilization: total useful CPU time / total CPU time
# Scheduling policies
- Think about scheduling scale:
	- Long term: which processes are allowed in?
	- Medium term: which process live in RAM?
	- Short term: which processes get a CPU?
	- A program could check by `getclock` and compare
## Example policies
| process | arrival time | run time | first output |
| -- | -- | -- | -- |
| A | 0 | 5 | 2 |
| B | 1 | 2 | 1 |
| C | 2 | 9 | 4 |
| D | 3 | 4 | 1 |
### First come first served, FCFS
- `AAAAA|BB|CCCCCCCCC|DDDD`

| process | wait time | Turnaround |
| -- | -- | -- |
| A | 0 | 5 |
| B | 4 + D | 6 + D |
| C | 5 + 2D | 14 + 2D |
| D | 13 + 3D | 17 + 3D |
| avg | 5.5 + 1.5D | 10.5 + 1.5 D |
- Could also calculate variance
- Issue: long wait time b/c later processes wait for earlier one
- Guarentee: no process starvation
### Shortest Job First
- `AAAAA|BB|DDDD|CCCCCCCCC`

| process | wait time |
| -- | -- |
| A | 0 | 
| B | 4 + D | 
| C | 9 + 3D |
| D | 4 + 2D |
| avg | 4.25 + 1.5D |

- Issue: starvation: runnable process never runs
### Preemption Scheduling: Round Robin: FCFS + preemption
- Quantum: change to next process every this time length
- `A|B|C|D|A|B|C|D|A|C|D|A|C|D|A|CCCCC`

| process | wait time |
| -- | -- |
| A | 0 | 
| B | 1 + 1D | 
| C | 2 + 2D |
| D | 3 + 3D |
| avg | 1.5 + 1.5D |

- If process put at beginning of the queue, then starvation is possible
	- New processes are at end of line
- Lower utilization due to context switching
## Priority Scheduling
- Not all processes equal
- Linux: user defined priority is "niceness"
	- Highest priority first + preemption
- Priorities can be static or dynamic
	- Can be user or system assigned
	- High priority: low number
	- Low priority: high number
- SJF: priority = run time
- FCFS: priority = arrival time
- Closer to linux: priority = arrival time + niceness and preemption
- Multilevel priority scheduling:
	- multiple batches of processes with schedulers within
## Priority inversion
- Problem: If low priority locks resource, then high priority takes over and waits on resource, cpu scheduler might go to medium and run for a while and never give low priority chance to unlock for high to continue
	- Fix: if high priority waits on lock, process holding lock gets high priority
## Real time scheduling
- Hard and soft:
	- Hard deadlines vs soft deadlines
- Hard:
	- Nuclear power plant, car breaking system
	- Predictability trumps performance
		- ex. disable caching in cpu
	- No interrupts/traps/signals, poll instead
- Soft:
	- Video player
	- Idea: earliest deadline first
	- "rate monotonic system"
		- threads get same rate of activity to hit deadline