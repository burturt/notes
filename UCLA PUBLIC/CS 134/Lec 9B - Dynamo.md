- Focuses on **availability and partition tolerance**, not strong consistency
	- Uses **eventual consistency** instead:
		- Allows for very temporary inconsistencies between replicas
		- Replicas eventually agree if updates stop arriving
- Highly available **sharded** kv store
	- DynamoDB uses Dynamo under the hood
- Proprietary, but Basho created open-source implementation called Riak
### Availability Definitions
- All requests receive a reponse
- All requests receive a meaningful response
- All requests receive a meaningful response in a reasonable amount of time * this is what Dynamo uses
### Partitions
- Network partition: network split, cannot reach each other. Temporary and unintentional
- Partition tolerance: ability for system to continue operating in network partition
	- If network partition, can *only* guarantee availability or consistency
# New Concepts
## Application Specific Conflict Resolution
- The ability for application-specific resolution resolution when replicas disagree
	- e.g. shopping cart union
	- Issue: removing from cart may be undone with union or seeing wrong replica
- Default is consider last write
- Conflict resolution: must first detect, then resolve
	- Detection: anti-entropy detection
## Anti-entropy with Merkle Trees
- Dynamo uses data structure called Merkle Tree to detect inconsistencies
- Merkle tree: hash nodes all the way up (like git) to detect where differences exist
	- Traverse tree, skipping node if hashes match
	- Once found leaf of traversal, compare values and update
	- O(log n) finding
## Gossip
- Issue: inconsistency in view (group membership problem)
	- Dynamo uses gossip protocol to update which nodes are up/down
	- Uses heartbeat monotonic clock system to get last seen online
## Quorum
- In Dynamo, client can write to any number of replicas directly, and can read from any number of replicas directly
- N = number of replicas, W = number must respond to write to be considered, R = number that needs to respond for read to consider complete (but does NOT need to respond with same value, up to app)
	- Adjust R and W upwards to get strong consistency, lower values are faster
	- Allows changes between consistency, availability, fault tolerance, and allows flexibility on system
	- Read one write all does not give strong consistency
- R + W > N: Guarantees quorum overlap, and know at least one value read is correct
- W = N
- R = N
- Not allow async writes: large performance hit
- No partitions
- Optimistic concurrency control: can get strong consistency using transactions 
### Tail Consistency:
- Use 99.9% percentile as benchmark for latency instead of average (essentially worst case latency)
## Hashing for Shard Allocation
- Hashing key determines shard to put kv pair on
- Hash using MD5, then take mod to determine which machine
- No issue with hash collisions or key skew, but no real balancing if node full
## Rebalancing: Consistent Hashing
- Create N tick locations, where N >> number of nodes
- Assign ticks using hash(IP) % N, hash(key) % N to determine where it is
	- If node not at tick location, keep checking in increasing tick numbers until running into node. This is primary
	- Nodes after in ring are replicas, e.g. M = 3 replicas below
	- Each node keeps track of preference list of replicas, may be > M in case nodes are offline
- If new node added, move data backwards
	- ![Screenshot 2024-12-05 at 4.22.56 PM.png](../../_resources/Screenshot%202024-12-05%20at%204.22.56%20PM.png)
- If node offline, next node in chain/in ring order becomes primary, but also must replicate further to keep 3 replicas
![Screenshot 2024-12-05 at 4.21.15 PM.png](../../_resources/Screenshot%202024-12-05%20at%204.21.15%20PM.png)
![Screenshot 2024-12-05 at 4.24.15 PM.png](../../_resources/Screenshot%202024-12-05%20at%204.24.15%20PM.png)
![Screenshot 2024-12-05 at 4.57.14 PM.png](../../_resources/Screenshot%202024-12-05%20at%204.57.14%20PM.png)

![Screenshot 2024-12-05 at 4.57.39 PM.png](../../_resources/Screenshot%202024-12-05%20at%204.57.39%20PM.png)
- Advantage: move minimal number of keys moved whenever adding/removing keys
	- roughly K/N
- Minimize by adding more nodes: create **virtual nodes** by mapping nodes to multiple ticks
	- Catch: When assigning virtual nodes, they should NOT be in a particular sequence, e.g. could be nodes 1, 2, 3, 2, 1, 3
	- Problem: If physical node goes down, many virtual nodes may be affected
	- Virtual nodes: allows different number of keys per physical nodes w/ different storage by having different number of virtual nodes per physical node