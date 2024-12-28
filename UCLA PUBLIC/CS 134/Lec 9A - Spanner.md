## Intro
- Relational DB, started as kv store
- Created by google, orig for google advertising
	- Replicated for high availability and low latency
	- Allowed distributed transactions across multiple geographically distance data centers
- Cloud: identical nodes in different availability zones and regions
- Replace sharded MySQL: unwieldy, too much interaction needed, not scalable, poor fail over, PB replication struggled with consistency
- Challenges:
	- Since paxos only requires majority, local shard serving does not contain latest data
	- Transaction may require data from multiple shards
### Implementation:
- **Two-phase commit** protocol to ensure all replicas updated
	- Variation: allows follower to become leader or transaction coordinator if leader fails
- Two-phase locking for rw transaction serializability
- Hugh number of messages transmitted
- **Synchronized time** for read only
- CockroachDB: open source inspired by Spanner, Spanner is GCP cloud
- Optimized for read-heavy workloads
- **Global consistency**
### Data Distribution Model
- Data divided into small chunks called splits or tablets
	- Each split has range of data stored on set of nodes
	- Each split distributed across data center: sometimes all, sometimes part
- Both sharded and replicated
	- **Sharded**: data is divided and load balanced across data centers for performance/fault tolerance, **data locality**, lite form of replication
	- **Replication**: for **fault tolerance**, *maybe* low latency
### Replication Model
- Replication done via Paxos with leader node
- Each shard has its own leader
![Screenshot 2024-11-27 at 1.25.07 PM.png](../../_resources/Screenshot%202024-11-27%20at%201.25.07%20PM.png)
- Replication: data center failures, low latency
- Sharding: scalability + geographic partitioning
- Paxos: communicate over long distance, and relies on only majority of data centers
### Transactions
- Paxos: used **within shards**
- 2PC: used **between shards**
- Read only
	- Reads performed from local replica
	- Does NOT use locks, 2PC, or transaction manager
	- Guarantees: **serializable**, and external consistency
	- Use **snapshot isolation**: assume node times are synchronized
		- Each transaction has timestamp: rw start of commit (after promise, etc), ro start of transaction
			- **Multiversion Concurrency Control** (MVCC)
		- When reading, read only writes before ro transaction start timestamp
			- Note: in Spanner specifically, replica will block transaction until it catches up to ro transaction timestamp
		- Disadvantage: memory or disk persist every version
![Screenshot 2024-11-27 at 1.39.47 PM.png](../../_resources/Screenshot%202024-11-27%20at%201.39.47%20PM.png)
	- Problem: clock skew
		- Snapshot isolation does not guarantee external consistency, but **serializability**
		- If timestamp too large, not an issue as it will block until later time
		- If timestamp too small, then we miss more recent commits
		- Soln: clients asks a time master for current time
			- Time master returns "TT interval" that is guaranteed to contain the current time
			- **true time scheme**
			- 2 rules: given an interval, pick LATEST time, e.g. r/w right before commit, or for ro, get timestamp and delay commit until believe all timestamps above timestamp assigned
				- Prevents picking timestamp in future
![Screenshot 2024-11-27 at 1.50.14 PM.png](../../_resources/Screenshot%202024-11-27%20at%201.50.14%20PM.png)	
- Read-write (in same transaction - read all values then write)
	- All read/writes go through leader in shard
	- When reading, lock value
	- When writing, pick transaction leader
		- Pick any leader node, one per transaction not per shard
		- Send write to leader, and leader does Paxos Prepare/Promise within shard
		- Leader then sends 2PC OK/Promise to transaction leader
		- Client executes next write, repeat
		- Transaction leader: once done with writes, sends 2PC Commit message to leaders who then does commit message in paxos within shard
	- Release locks and release transaction leader