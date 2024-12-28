# Cloud Storage
- Region: geographic, for data locality
- Availability Zone: for reliability, multiple per region
- Object store: S3, GCP Cloud Storage
- Block level storage: AWS EBS, GCP Persistent Disk
- Distributed File System: AWS EFS, GCP Filestore
## Object Store
- Stores unstructured data
- Often used for data lakes
- Flat hierarchy, each object unique identifier
- S3: basically every service can read from S3
- Data organized into buckets, buckets have unique names across all of S3
	- Bucket replicated between and within regions live or on-demand
- Types of offerings:
	- High performance (fast, low latency, costly)
	- Infrequently accessed (cheaper storage, expensive to retrieve)
	- Archival (rarely used, expensive to retrieve, latency hit)
	- Various storage and retrieval costs
	
## Pricing
1. Region
2. Amount of data, GB/Month, discounts if over a threshold
3. per 1000 PUT/COPY/POST/LIST requests (writes)
4. per 1000 GET/SELECT requests (read)
5. 1000 storage class changes
6. Data transfer out of s3 to internet or other regions per GB
7. Monitoring/automation costs per N objects for adaptive storage classes
8. Batch replication: cost per job + cost per million objects
- AWS: Express One Zone, Standard, Standard Infrequent Access, One Zone Infrequent, Glacier Instant/Flexible/Deep Archive, Intelligent Retrieval
- S3: Long term data storage (logs, static assets)
	- Note: lots of small files is pricy due to per-request fees
![Screenshot 2024-11-04 at 2.03.10 AM.png](../../_resources/Screenshot%202024-11-04%20at%202.03.10%20AM.png)
![Screenshot 2024-11-04 at 2.03.21 AM.png](../../_resources/Screenshot%202024-11-04%20at%202.03.21%20AM.png)
- Changing storage class: between $0.01-$0.05 per 1k requests
## Block Storage
- Similar to disk volume
- high performance, faster
- Associated with server instance and mounted to use
- Must be formatted with file system
- Lower latency than object storage
- Data stored in fixed size blocks
### Class
- General purpose SSD (gp2, gp3)
- Provisioned IOPS, higher performance, pay for performance (io1, io2)
	- Databases and prod web servers
- Throughput Optimized HDD (st1)
- Cold HDD (sc1)
### Cost
1. Region
2. Storage class
3. Number of provisioned IOPS above free baseline (gp and io), st1 and sc1 billed only on storage
	- Provides SLA on how many requests handled per second
5. Throughput (MB/s)
6. Implicit File size, as smaller files = increased IOPS and decreased throughput
![Screenshot 2024-11-04 at 2.04.54 AM.png](../../_resources/Screenshot%202024-11-04%20at%202.04.54%20AM.png)
## Distributed File System
- Abstracted away from underlying disk volumes
- Unlike EBS, multiple server instances can read and write to the same file system
- EFS: shared storage, lower latency than S3
### Classes
- Standard, Infrequent Access, Archive
- Each file can be in a different tier/class
![Screenshot 2024-11-04 at 2.05.58 AM.png](../../_resources/Screenshot%202024-11-04%20at%202.05.58%20AM.png)
# Consensus
- Hard and expensive, but allows strong consistency (linearizability)
	- Ensures all operations appears as if executed in some total order, and each operation takes effect instantaneously at some point 
- **Primary-secondary: what happens when primary fails?**
	1. Restrict system to not allow read/writes until new coordinator
	2. Choose existing new node to be primary, but missing context
	3. Backup coordinators, but must also be kept consistent somehow
- Instead, use **consensus protocol** to act as coordinator
- When do we need consensus?
	1. want to make sure messages **delivered in same order** (ordered broadcast problem)
	2. Define a set of processes and **all processes need to know all processes that are part of the group** (group membership problem)
	3. need one processes to **play special role** (leader election)
	4. Need them to take turns **accessing a shared resource** (distributed mutual exclusion)
	5. Need to agree on a commit/abort/rollback decision in **transaction** (distributed transactions)
## What is consensus?
- Each process starts with opinion that may differ, and after consensus protocol, all must agree
	- Must do so in presence of asynchrony and/or failures
- Properties:
	- Termination: All correct processes eventually decides on a value, e.g. no confusion or ambiguity
	- Agreement: All correct processes eventually agrees on the same value
	- Validity (Integrity/non-triviality): Value chosen must come from domain of input values
- How can we always satisfy Termination and Agreement?
	- Just output a constant!
- Under async network and crash failure model, no consensus algorithm satisfies all three!
	- Must compromise, usually termination compromised
# Paxos
- Story: part-time parliament where legislators are not all present at the chamber at the same time
	- Must reach a consensus
	- Similar to distributed system: not all nodes always online
- **Majority wins** decision rule, so majority of processes must be online for algorithm to terminate
	- Requires 2m + 1 processes to handle failure of m processes
- Each process plays one of 3 roles:
	- A **proposer** proposes a particular value to be considered.
	- An **acceptor** receives proposals and only accepts them if a quorum of acceptors receives the same message.
	- A **learner** is in charge of executing the request and sending a response to the client.
- Nodes must persist data & know how many is needed for majority
- 3 phases:
	- **Prepare phase**: establish the latest generation (by proposal number) and gather any already accepted values
	- **Accept Phase**: Propose a value for this generation for replicas to accept
	- **Commit Phase**: Let all replicas/learners know that a value has been chosen
## Algorithm
### Phase 1
- Proposer P~i~ sends `Prepare(n)` to at least a majority of acceptors
	- n is proposal number. Acceptors will not accept messages containing proposal numbers <= already seen numbers
	- Proposal numbers must be globally unique, and higher than any proposal numbers that this particular proposer has used before
- Acceptor receives `Prepare(n)`:
	- Is n biggest it has seen?
		- If yes:Has acceptor already accepted proposal?
			- If yes, send `Promise(n, (n', val'))` of other proposal
			- Otherwise replies with `Promise(n)`
		- If no: ignore
- If majority of acceptors respond with promise, they have signaled they are willing to participate in this round of consensus building
- **Milestone 1: majority of acceptors have Promised, no Id < IDp can make it through**
![Screenshot 2024-11-04 at 2.12.03 AM.png](../../_resources/Screenshot%202024-11-04%20at%202.12.03%20AM.png)
### Phase 2
- Proposer: when majority acceptors accepted:
	- Did any responces contain accepted value from other proposals?
		- Yes: Pick val' with highest number n' and send `Accept(n, val')`
		- No: send `Accept(n, val)`, val chosen by proposer
	- Not a command, but still a suggestion
- Acceptor: is this highest value of n it has seen?
	- No: ignore it
	- Yes: store n, val, and set a bit that we have accepted a proposal, reply with `Accepted(n, val)` and forward same message to *all* learners for downstream processing
- **Milestone 2: Reached consensus of value among acceptors, but NOT known to system**
- **Milestone 3: If proposer/learner gets majority of accept for specific ID, they know consensus reached on value**


![Screenshot 2024-11-04 at 2.12.26 AM.png](../../_resources/Screenshot%202024-11-04%20at%202.12.26%20AM.png)
## Example with full


![Screenshot 2024-10-31 at 3.13.40 PM.png](../../_resources/Screenshot%202024-10-31%20at%203.13.40%20PM.png)


- Dualing Proposers/Contention:
![Screenshot 2024-10-31 at 3.21.33 PM.png](../../_resources/Screenshot%202024-10-31%20at%203.21.33%20PM.png)

- Conclusion:
	- Paxos is a consensus algorithm to achieve data consistency and fault tolerance
	- Ensures group of nodes agree on a single value (Paxos) or sequence of values or operations (MultiPaxos)
- Where Paxos used:
	- Distributed databaes like Google Spanner
	- Google chubby lock service
	- Neo4j, graph db
	- Cassandra uses Paxos for leader election and lightweight transactions
	- Dynamo uses for leader election
# Google paxos

![Screenshot 2024-10-31 at 3.34.59 PM.png](../../_resources/Screenshot%202024-10-31%20at%203.34.59%20PM.png)

