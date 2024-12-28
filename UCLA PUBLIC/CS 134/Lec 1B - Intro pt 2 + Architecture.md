## Potential faults:
- Can in parallel (2 unrelated, same time) or cascade (1 causes another)
	- Can happen on different threads, cores, and nodes
- env dependent/can seem non deterministic 
- can happen at any logical level, not just low level physical machines
- Can spread quickly (insidious)
- can be transient: hard to find root cause
- can depend on order of execution (race condition)
- can occur long after system deployed
## Why build distributed system?
1. Distribution is inherent (sensor networks, email, cell towers, DNS)
2. Resource sharing (big data, DNNs)
3. Improved performance (low latency, sharded architecture)
4. Better reliability
- **Golden rule**: if you do not need to distribute, don't!

### Metal model:
- Architectural
- Process and implementation
- Communication
- Coordination
- Consistency
- Fault Tolerance
- Security
## 8 Fallacies of Distributed Systems:
1. Network is reliable
2. Latency is zero
3. Bandwidth is infinite
4. The network is secure
5. Topology does not change
6. There is one administrator
7. Transport cost is 0 (cloud costs money to send data)
8. Network is homogeneous
## Thought Experiments
### The Two Generals problem: model of networks and lossy links
- 2 separate armies must attack same time
	- Need to **coordinate** attacks, using messengers to **communicate**
	- Any given messenger may be captured or 
- How to communicate if no guarantees of messengers surviving & no feedback?
- Generals can never be certain, but can be solvable: allow for one to fail after other, but not other way around
### Byzantine Generals Problem: model of node behavior
- 3+ generals
- When message is sent, is it always received
- One or more generals may be traitors
- Malicious generals can collude, best to get all honest on same page
- Math: possible if **1/3rd or less are malicious**
- In practice, can use cryptography but still have trust issues
- **"Byzantine fault tolerant": guarantee work even if some malicious nodes**
- **Summary**: Use of ACKs, out of order messages, and latency
## System models
- Specifies assumptions about faults that may occur
- Need model for network behavior (message loss), node behaviors (crashes), timing behaviors (latency)
- Most systems assume bidirectional messages
### Network Model/Message Loss
- **Reliable links**
	- Message received if it sent, order not guaranteed
- **Fair-loss links**
	- Msgs can be lost, delayed, duplicated, reordered, but keep retrying, message eventually received
	- Can become a reliable with retry + dedup (aka TCP without timeout)
- **Arbitrary links**
	- May have malicious adversary that can corrupt messages (eavesdropping, changing/dropping/spoofing/replaying message)
	- Can become fair-loss with TLS/encryption
- **Network partition**: link drops/delays all messages for an extended period of time
### Node Model
- Crash-stop/fail-stop:
	- node is faulty if it crashes. Node stops executing forever when crashed (kubernetes)
	- **No incorrect or inconsistent messages are sent**
- Crash-recovery/fail-recovery:
	- node can crash at any moment. Lost RAM, disk preserved, service/node resumes later
- Byzantine/fail-arbitrary:
	- Node is faulty if it deviates from the algorithm
	- Conflicting messages can be sent
### Timing/Synchrony Model/Latency
- Synchronous:
	- upper bound on latency, predicable
	- Algorithm completion time is predictable
- Partially Synchronous
	- Mix of sync and async
- Asynchronous:
	- no upper bound
	- Arbitrary delays
	- Can pause execution
# Architecture
- Defines how comments are **connected**, how **exchange data**, and how **jointly configured**
## Software Architectural styles
### Layered Architecture:
![Screenshot 2024-11-03 at 9.55.23 PM.png](../../_resources/Screenshot%202024-11-03%20at%209.55.23%20PM.png)
- Application-interface (HTML)
- Processing (Query generator (adding context), HTML generator, ranking algorithm)
- Data/Storage (literal db storage)
### Service-Oriented architecture
![Screenshot 2024-11-03 at 9.55.34 PM.png](../../_resources/Screenshot%202024-11-03%20at%209.55.34%20PM.png)
- Client wants object, client and server anywhere on network
- State maintained individually, no shared state
- Nodes communicate ad-hoc as necessary
- Encapsulation is important: separate concerns
- Services must be independent, but may contain microservices (self-contained) or even other distributed systems
- Ex: REST (Representation State Transfer): URLs to PUT, POST, GET, DELETE requests, messages are fully-described, and stateless requests
	- provides state transfer
### PubSub Architecture
- Bus to transfer messages: kafka, message queue
- **Subscriber** subscribes to messages in topics, or content-based by key
- **Publisher** adds messages to system
- middleware handles distribution of messages:
	- Sends data directly to subscriber (push model)
	- Subscriber fetches (pulls) the message after getting notification (push)
- **Temporal coupling**: both nodes must be online, message lost otherwise (decoupled: only one needs to be online)
- **Referential coupling**: both systems have a name to refer to each other when sending messages (identity of nodes does not matter)

|                         | Temporally coupled | Temporally decoupled |
|-------------------------|--------------------|----------------------|
| Referentially coupled   | Direct (TCP)            | Mailbox (Email)             |
| Referentially decoupled | Event-based        | Shared data space (recorded lecture)   |
## System Architecture
### Layered
- Modules/components with similar functionalities are organized into horizontal layers
- Client-server
	- Client sends msg to server, server replies
	- Only assumes sync communication
- Multitiered / n-tiered
	- User interface, Application, Database, one is split between client/server, server does everything else
	- ![Screenshot 2024-10-03 at 8.40.01 PM.png](../../_resources/Screenshot%202024-10-03%20at%208.40.01%20PM.png)
	- Multi tired: e.g. client contacts web server, contacst cgi server, contacts data server, then returns data back to client
### Peer-to-Peer
- Decentralized in theory
	- Every node has same role and same power: no master, nodes act as both client/server, etc
	- In practice: there may be centralized nodes (e.g. superpeers) for coordination, and add client-server capabilities to interface
	- Generally, P2P refers to nodes **within administrative zone**, not between them
- Examples: BitTorrent, Cassandra (distributed db), Bluetooth radio
	- Napster popularized it (audio network)
	- Diaspora, Tor, WWW examples of original peer-to-peer
- Nodes **share resources** with peers (cpu processing, disk storage, or network bandwidth (piggybacking to relay messages)) w/o need for centralized coordinator
	- **Nodes both consume and supply resources** (in client-server server supplies resources, clients consume)
- **Form overlay (logical) network** on top of physical network
	- Nodes connected at application layer, but built on top of lower network stack layers
- **Structured**: overlay network organized into topology (binary tree, ring, grid, etc)
	- Typically used for indexing data, distributed db (BitTorrent)
	- **Distributed Hash Table (DHT)** to **assign ownership** of part of file
		- filename + chunk hashed = key, mapped to node ID
		- Challenge:
			- having one hash table **introduces centralized** aspect, so distribute it
				- Updating hash table when files are moved between nodes
			- Must route through overlay network to find value - **not simple to determine path**
				- Every node **maintains list of neighbors**
				- Nodes must **probe for potential new nodes**
					- routing/DHT protocols include CAN, Chord, Tapestry, and Pastry
- **Unstructured**
	- examples: KaZaa, Gnutella
	- Each node has **dynamic list of neighbors**
		- New node contacts **well known node** to get initial list of peers
	- Creates **random graph**, where each edge in network exists with a probability
	- Finding data:
		- **Flooding**: query all nodes, those nodes query neighbors, etc until found, then return to forwarder or source, can use TTL
		- **Random walk**: same but on forward, only forward to one more neighbor, not all
			- Can take forever: start n walks in parallel
- **Hierarchical Layers**
	- ![Screenshot 2024-11-03 at 10.04.35 PM.png](../../_resources/Screenshot%202024-11-03%20at%2010.04.35%20PM.png)
	- **Super peers**: stores an index of data to localize a search in network
	- **Weak peers**: peers associated with a single super peer, all super peers know where all data is stored to the super peer associated with it
	- If super peer goes down, leader election!