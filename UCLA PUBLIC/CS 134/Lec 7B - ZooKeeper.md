- Centralized service for maintaining configuration information, naming, distributed synchronization, and providing group services
	- Coordination service
	- Alternatives: Ambari, etcd (K/V)
- Coordination features:
	- **Naming**, identify resources
	- **Configuration management**: one place to store config details
	- **Synchronization**: Plays adult in room, by ordering messages
	- **Group Services**: Group membership, consensus, leader election
- Provides:
	- Semaphores, queues, leader election, group membership, barriers (stop and wait), configuration
	- NOT lock: **wait-free synchronization**
![Screenshot 2024-11-15 at 2.05.42 PM.png](../../_resources/Screenshot%202024-11-15%20at%202.05.42%20PM.png)
- ZooKeeper: stores data via hierarchical namespace
	- Data has tree-structure data registers called **znodes**
	- Data stored in memory (fast!)
![Screenshot 2024-11-15 at 2.09.18 PM.png](../../_resources/Screenshot%202024-11-15%20at%202.09.18%20PM.png)
- Zookeeper: strictly for small data, aka **metadata**
## Design Goals
- Simple
- Replicated
	- Clients connect to any server, one server is leader
	- Server state:
		- Copy of full data tree
		- Transaction logs
		- Snapshot of system state from memory, taken every once in a while and persistent
	- Majority of servers available = zookeeper service online
	- Clients:
		- Connect to single ZooKeeper server via TCP:
			- Requests and responses, watch events, and heartbeats
			- Clients pick at random, pick another if fail (can be literal random, colocated geographically, or load balanced)
- Ordered
	- Writes: timestamped and respect a **total order**
		- Used to make synchronization primitives
- Fast
	- 10:1 reads:writes
### Features
- **znodes** (NOT system) store version numbers and timestamps for:
	- data updates
	- ACL changes
- Data in znodes read/written atomically
	- Writes replace all relevant data
- No appends
- Container Node: super parent node, contains metadata common to all of its children
	- Once children killed, container deleted
- TTL nodes: autodeleted after time
- **Watch**: client can watch on znode changes
	- Can be one time or repeated
	- to be notified about a change
- **Guarantees**:
	- Sequential consistency: Total order, updates from client applies in order sent
		- FIFO
	- Atomicity: updates are fully successful or appears to be never attempted
	- Single System Image: client sees same view of the service regardless of machine
	- Reliability: once update applied, data is durable and persists until overwrite
	- Timeliness: client's view is up to date within some time bound
		- **Bounded staleness consistency**
![Screenshot 2024-11-18 at 10.40.27 PM.png](../../_resources/Screenshot%202024-11-18%20at%2010.40.27%20PM.png)
- Hadoop, kafka, apache hbase, solr, apache storm, apache mesos, cassandra uses zookeeper


![Screenshot 2024-11-18 at 10.41.34 PM.png](../../_resources/Screenshot%202024-11-18%20at%2010.41.34%20PM.png)

![Screenshot 2024-11-18 at 10.42.17 PM.png](../../_resources/Screenshot%202024-11-18%20at%2010.42.17%20PM.png)

# Zab
- ZooKeeper Atomic Broadcast
	- Variant of Paxos, but contains ordering
	- Used to keep ZooKeeper servers in sync
- **Guarantees**:
	- Reliable delivery: m delivered to 1 server -> delivered to all
	- Total order: m delivered before n on 1 server -> m before n on all servers
	- Causal order
	- Prefix property: if m is last message delivered for leader, any messages proposed before m by L must be delivered
- **Assumptions**:
	- Assume messages are **not processed out of order** and messages are **not lost** in transit
	- **Only failure is server failure**
- Relies on timeouts to ensure consensus can be reached
	- Uses time for liveness but not correctness
	- FLP property
- Process:
	- Write request: forwarded from server to leader
	- Leader atomic broadcast, using paxos
	- On commit, saves to local database and broadcasts commit to other servers to also commit
	- Response returned about write
- Follow fails: not a big deal, requires quorum of followers
- Leader fails: each server maintains state, so can recover
### Modes of operation
- When follower first joins cluster or after leader failure, starts in recovery
- Once leader has quorum of followers, starts to broadcast messages
	- Followers sync with leader to received missed messages
	- Returns to recovery if leader loses quorum or leader fails
- **Broadcast**:
	- Leader receives write request
	- Leader sends proposal to all followers with proposal number **zxid** that is monotonically increasing
	- Followers respond with ack (vote)
	- Leader sends commit message to all followers if quorum is met. Commits message to itself and followers persist update when receiving commit
	- Communication over FIFO channels to maintain message ordering
	- All messages are idempotent
- **Recovery**
	- Want to pick server with highest proposal number to be leader (has most data)
	- Guarantees:
		- Must never forget which messages were delivered
		- Let go of messages that were skipped
			- e.g. if message never committed, must ignore
	- When leader comes back online, it must deliver message to all followers
	- After new leader elected, before proposing messages:
		- all followers must have seen all proposals and have delivered all committed
		- leader queues list of proposals and commits for any new followers
		- then leader adds follower to broadcast group
		- Uses transaction log + snapshot