- Accruate time required for distributed systems:
	- Schedulers (cron), timeouts, failure detection, retry timers
	- Performance measurement, statistics, profiling
	- Log files and databases
	- Data that are time-sensitive (cache TTL)
	- Determining order of events across a series of nodes
### Clocks Types
- Quartz Clock
	- Crystal oscillates 32,768 times per second
	- circuit reads and pulses every X vibrations
	- Issues: cheap but inaccurate
		- Manufacturing flaws
		- Temperature: 72F optimal
		- ~32 seconds off per year
- Atomic Clock: cs-133or rubidium
	- Extremely accurate but expensive
	- Period of quantum mechanics of cesium-133
	- GPS uses this time & sends it down
		- 31 satellites, each with atomic clock
		- Calculate position using speed of light delay between satellite and receiver, correcting for atmopsheric effects, relativity, etc
### Time definitions
- Speed of rotation of Earth is not constant!
	- 365 x 24 x 60 x 60 x 9192631770
- UTC: based on GMT (greenwich mean time, noon when sun in south as seen from meridian), uses atomic time plus corrections for Earth's rotation
	- Leap seconds: Every June 30th and Dec 31st, 23:59:59 UTC: Either jumps forwards and skips 23:59:59, counts to 23:59:60 UTC then 00:00:00:00, or does nothing
		- Deprecated by 2035
		- OS/software mostly don't deal with it: Linux caused livelock on leap second June 30, 2012 
			- hrtimer many processes deemed as "taking too long", causing many clocks firing and starting sleeping tasks
			- Caused Cassandra to fail to pause Java threads (reddit)
			- Gawker has issue with Tomcat (java web servers)
			- Mozilla hadoop cluster failed
- International Atomic Time (TAI): 1 day = 24 * 60 * 60 * 9,192,631,770 periods of cs-133
## Clock Synchronization
- Battery used to keep clock online in computers
- **Clock drift: hardware error** gradually increasing over time
	- **Difference between 2 clocks is clock skew**
- Computers use NTP: network time protocol (or can use PTP, precision time protocl)
	- Many OS vendors have NTP servers and configured to use them
	- Stratum types:
		- 0: atomic clock of GPS receiver
		- 1: Synced directly with 0 (e.g. NTP server)
		- 2: Synced with stratum 1, etc
	- protocol:
		- Client sends request with timestamp
		- Server responds with time received and time sent
		- Christian's Algorithm: allows us to skew the clock to match up with real time
		- Berkeley Algorithm: make a lot of NTP requests, average them out to avoid latency issues
		- ![Screenshot 2024-10-20 at 9.58.30 AM.png](../../_resources/Screenshot%202024-10-20%20at%209.58.30%20AM.png)
		- Slew the clock: slowly speed up clock to fix skew
			- Prevents processes missing milestones or too many async callbacks all at once
	- If skew < 125 ms, slew clock opver ~5 min
	- Or if < 1000ms, step the clock (slewing takes too long)
	- Else, panic/do nothing
### Clock Types
- **Real-time/Time-of-day**: Based on physical time
	- Issues: leap seconds, skew/stepping issues, elapsed time may be larger than expected or even negative
		- e.g. currentTimeMillis()
	- However, can be compared across nodes when in sync
	- Time since Jan 1, 1970
- **Monotonic clock**:
	- Only counts forward
		- e.g. nanoTime() 
	- \# of seconds since an arbitrary event: process start, boot, etc
	- Use monotonic clock for timing, NOT actual time
	- Can't be compared
![Screenshot 2024-11-04 at 12.46.14 AM.png](../../_resources/Screenshot%202024-11-04%20at%2012.46.14%20AM.png)
# Ordering Messages
- Issue: when messages are only one-directional, e.g. a -> b -> c
	- Monotomic clock can't be compared
	- real-time clock issue: clocks are not synced + skew

## Happens-before
- Correct order: "happens before"
	- **Strict total order**: events on single node/thread must have happened in an order
		- For all m, n: m > n or n > m and m >= n or n >= m, not both (m must not be equal to n)
		- For us: two or more events can't have same timestamp
	- Messages strictly are received after it was sent
	- If we assume unique messages, we know where messages sent from and when
	- Happens-before relation: **partial order**
		- 2 events a and b may not be "before" each other (a || b)
		- Considered **concurrent** if happens
- **Event a happens before b** (a -> b) if and only if one of the following:
	- a and b occurred on the **same node**, and a occurred before b
	- **a is sending of a message m and b is a process receiving it**
	- there exists events c such that a -> c and c --> b **(transitive)**
- Broadcast Protocols: delivering one message to multiple recipients
	- Order which they deliver messages matters a lot (e.g. bank transactions)
	- **Logical clocks**: count number of events occurred (e.g. counter of events)
## Logical Clocks
- Count number of event occurred
- Purpose: correctly capture order of events in distributed systems
	- Can we guarantee whether b came after a/whether b could not have caused a
	- If a -> b, then T(a) < T(b)
	- Use: Serialization, Debugging
### Lamport clock
- Lamport clock are **consistent** with **potential** causality
	- consistent: if a -> b, then L(a) < L(b), but not converse
	- Still useful: contrapositive is possible
- Space: O(E), Time: O(E)
- Not very scalable due to message transmissions, no fault tolerance (single node crash breaks relationship)
- Uses sequence numbers
- Each node has counter t incremented on each local event e (right before e is executed)
	- L(e) = t
- **Algorithm**:
	- On init, set t to 0 on node
	- Right before executing an event on node i, increment t
	- If event transmitted from i to j: set t~j~ to node j
	- When j receives messages, update local clock to t~j~ = max(t~i~, t~j~) + 1
- Results:
	- If a -> b, then L(a) < L(b)
	- But L(a) < L(b) does not imply a -> b
	- L(a) can equal L(b)
	- For any events a, b, if L(a) < L(b), then a **may have** caused b, but b could not have caused a (don't know if a -> b or if a || b)
#### Partial Ordering
- Creates a **partial order**/happens-before relation
	- **Irreflexive partial order**
	- reflexive: for a, a -> a
		- Not actually true: required for true partial order
	- antisymmetric: if a -> b and b -> a, then a = b
	- Transitive: if a -> b and b -> c, then a -> c
- True partial order:
	- ![Screenshot 2024-11-04 at 12.49.15 AM.png](../../_resources/Screenshot%202024-11-04%20at%2012.49.15%20AM.png)
- Can get full total order by **breaking ties using process ID**
	- Partial order: some events not part of ordering, ambiguity total order: forces all events to be part of ordering but has arbitrary 
	- essentially prioritizes certain processes
	- Issue: L(a) < L(b) does not say which actually came first since tiebreaking has no causal significance
- Typical uses:
	- Distributed databases
	- Event order in distributed systems
	- Versioning in distributed filesystems
	- Messaging systems
	- Blockchain
	- (but all really vague)

![Screenshot 2024-10-31 at 4.23.27 PM.png](../../_resources/Screenshot%202024-10-31%20at%204.23.27%20PM.png)

### Vector Clock
- **characterize** **causality**
	- a -> b **if and only if** V(a) < V(b)
- Start with Lamport clock
- Create a vector clock:
	- n nodes on system
	- vector timestamp is V(a) = (t1, ..., tn)
	- t1 is number of events observed by node ni
	- each node has vector timestamp T
	- On an event at node Ni, increment T(i)
	- Attached T to every message
	- Recipient merges T into its own local count vector
		- Take element-wise max, then increment own 
![Screenshot 2024-10-20 at 10.36.08 AM.png](../../_resources/Screenshot%202024-10-20%20at%2010.36.08%20AM.png)
- Ordering:
	- **a -> b if both**:
		- **Every** counter in T1 <= T2
		- **At least one counter** in T1 is < T2
	- concurrent, a || b if a not before b, and b not before a (one in a > one in b, and vice versa)
		- Example: (2,2,0), (0,0,1)
	- We can now establish reverse order: timestamp to causality
![Screenshot 2024-10-20 at 10.41.55 AM.png](../../_resources/Screenshot%202024-10-20%20at%2010.41.55%20AM.png)
- Vector clocks: forces a **causal** order
	- Creates a partial order
	- Lamport clocks only creates a partial/full order
- Uses:
	- **Serialization**
	- **Debugging:**
		- A -> B means A could have influenced B, but not sure
		- B could not have caused A
	- BASE databases (Cassandra/DynamoDB)
	- Git/mercurial for versioning
	- Kafka use vector clocks to maintain order messages in queue
	- Google docs edit synced
	- Not scalable, but heavily used
# Execution Delivery Properties
- Broadcast: one node send messages, all nodes **deliver**
- Multicast: sender only sends messages to some in group
- Deliver: messages are sent/received by network, but only broadcast from app and delivered to app after some time
- Properties:
	- FIFO delivery: first sent, first received.
	- casual delivery: if m1 sent before m2, then m1 must be delivered before m2 regardless of original sender
	- total order deliver: if m1 delivered before m2 on one node, then m1 delivered before m2 on all nodes