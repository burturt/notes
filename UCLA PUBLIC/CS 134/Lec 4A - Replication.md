![Screenshot 2024-10-26 at 7.30.16 PM.png](../../_resources/Screenshot%202024-10-26%20at%207.30.16%20PM.png)

## Reliable Delivery
- Reliable delivery def: P~1~ process sends message m to P~2~. If neither P~1~ nor P~2~ crashes, and not all messages are lost, then P~2~ eventually delivers m.
	- not all messages are lost: some transmissions fail
- **liveness property**: Something good eventually happens ()
- **Safety property**: Nothing bad happens (e.g. invariants stay satisfied)
- 2 failure: crash failure, omission failure
	- Crash failure: node crashes during execution, and stops responding entirely
	- omission: A -> B, B never responds
		- Send omission: A never seems to send the message
		- Receive omission: node B fails to receive a message or a message from another node
	- crash failure is specific omission failure
### Basics
- Want to send m from A to B:
1. Buffer m
2. Attempt to send m. If no ACK:
	1. Wait a timeout
	2. Retry
3. Receive ACK, then unbuffer m
- Questions:
	- What should the timeout be?
		- Too short: send a lot of messages, could overwhelm recipient, but unlikely anything would change in system status in short period
		- Too long: don't make enough progress, a lot of blocking
	- What is ACKs from B are lost?
		- Keep retrying!
		- Issue: **idempotency** (applying multiple times = applying once)
			- f(x) = f(f(x)) = f(f(f(x)))
- Reliable delivery: at-least-once delivery
- At-most-once: send once, either delivered or not
- Exactly-once: not realistic on asynchronous network
	- Kafka (msg broker, streaming system)
	- Assumptions:
		- Messages are idempotent (at least once)
		- An effort for the server to deduplicate messages (at most once)
### Reliable Broadcast
- Broadcast: message sent from process to all processes in same group (including itself)
- Simulate broadcast as multiple unicast messages
- **Reliable Delivery: If a correct process delivers m, then all correct processes deliver m**
	- deliver = process accepts messages and acts on it
- Correct process: depends on failure model
	- Crash model: process is correct if and only if doesn't crash
	- Omission model: general message loss
	- Byzantine model: no byzantine behavior happens before P~1~ and P~2~ (snooping, corruption)
- Solution: Every process that receives a message forwards messages to all other broadcast group
	- Process does not deliver the message until broadcasted message to everyone in group
	- Issue: broadcast storm/duplicates sent. Don't duplicate/forward duplicate messages received
	- Fault tolernace involves making lots of copies of messages and data
## Replication
- Purpose: **redundancy** and performance
- Why copies?
	- **Fault tolerance**, (redundancy) to try not to lose data
	- **Data locality**
	- **Load balancing**
- Cons with replication:
	- **Higher upfront + inflight cost**
		- May not need to replicate as much as chosen, lots of network I/O
	- **Consistency**!
- **Strong consistency: clients cannot tell data is replicated**
	- **Transparency** goal
	- Guarantee: only guarantee is have one node
### Primary-backup
- Pick primary P, n backups/replicas
- All reads and writes executed on primary
- When write:
	1. Broadcast in parallel to all replicas
	2. Each replica delivers write and sends ACK to primary
	3. Once all replicas have ACKd, write is considered committed and delivered on primary
	4. An ACK is sent to client
- **Rating**:
	- Fault tolerance: pretty good
	- Data locality: Bad, single primary
	- Load balancing: Bad, primary serves all requests
- Cons:
	- Primary is the **bottleneck**!
- When primary crashes:
	- What if primary crashes before committing?
		- Elect new primary, most up-to-date replica wins
		- Use "write-ahead log" or WAL
			- Keep track of each request and result, and reply it when primary received request
			- **Must be on a different node**
		- Each message assigned client token
- Why not let client read from backup?
	- Possible to get inconsistency: write value and not propagated before read from backup, inconsistency
		- If **prioritize availability over consistency,** may be OK
- Most DBs uses primary-backup
![Screenshot 2024-11-04 at 1.25.27 AM.png](../../_resources/Screenshot%202024-11-04%20at%201.25.27%20AM.png)
### Chain Replication
- No primary and replicas, have head, tail, and middles
- Client writes to head, head propagates messages through chain (middle nodes), and eventually when received at tail, commit point. ACK sent from tail to **client**
- Read from tail
- **Rating**:
	- Fault tolerance: pretty good
	- Data locality: Bad, single primary
	- Load balancing: Better, one node for reads, one for write
- What if node goes offline?
	- Act like linked list removal
	- If head, then elect new head from middle nodes, head sends to first middle, middle before elected node points to node after
	- If tail dies, elect new tail
	- If middle, then just remove and fix pointer to skip node
![Screenshot 2024-11-04 at 1.29.21 AM.png](../../_resources/Screenshot%202024-11-04%20at%201.29.21%20AM.png)
- If head/tail crashes, need record of what messages are still propogating
	- Usually done with log/journal stored elsewhere, can be replayed
### Comparison
- 2 RTT to client, but RTT(CR) >> RTT(PB)
- PR: 4 node "hops" writes, 2 node "hops" read
- CR: 2L + NL = L(N + N) to write, 2L to read
	- Scales in latency with each node
# Active vs Passive Replication
- Active Replication: executed on primary first, then replicated to backups and executed on each of them
	- Each replica executes operation
	- E.g. Forward message "increment X"
- Passive: execute only on primary, and replicate result/state to replicas
	- E.g. forward message "X = 5" when orig op "increment X"
- Active replication preferred: when operation results in a state that is very large
	- "State Machine Replication"
- Passive replication preferred: computation is very expensive, do operation just once
	- Can designate primary as beefy machine



## CDN: Content Delivery Network
- Provides caching, failover, load balancing, routing around network congestion
- Provide data locality, hybrid of replication and sharding (splitting large db into smaller chunks)
- AWS CloudFront, GCP Cloud CDN, Azure Content Delivery Network
- CDN picks edge server with best performance:
	- Usually geographically close
	- If cache hit, return content, otherwise retrieves from origin like S3, MediaPackage channel, or HTTP server
- CDN: only static content, PUT, POST, PATCH, DELETE, OPTIONS, etc routed to origin
- Workflow: specify origin server, upload files to origin, create CloudFront distribution, CF creates subdomain for file urls (or use domain), cloudfront sends edge server info about how to request file from origin
- Uses: video on demand, live video, accelerate static website content delivery, encryption and customization, serve private content