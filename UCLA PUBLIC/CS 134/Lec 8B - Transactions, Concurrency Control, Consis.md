- Transaction: serious of operations, executed in order, and are treated as a single unit of work
	- e.g. bank transfer, exchanging courses, purchase, swappping seats
	- May contain multiple non-atomic operations
## ACID
- **ACID**: Atomicity, Consistency, Isolation, and Durability
	- **Atomic**: happens all or none (roll back if fail, like corruption, constraints, system issues)
		- All nodes commit correct result, or none done
		- Maintain data integrity even in face of errors:
			- Data integrity constraints and expectations, link failure, programming or logic errors (i.e. division by zero), disk failure, system outage, etc
	- **Consistent**: when transactions are executed concurrently, invariants remain satisfied (e.g. no money lost or gained in transaction)
	- **Isolation**: multiple transactions can occur independently, transparently, without interference, and concurrent transactions can see intermediate results
		- Transaction A should not see intermediate results of B before B finishes
		- **Serializability**: Ordering of operations that yields same result as if executed serially
			- non-parallel transaction schedule is called serial if interleaved parallel transaction schedule can be reduced to a serial schedule OR guarentee same result as a serial schedule
				- Original parallel schedule called serializable
		- Does NOT guarantee serializability if exists parallel schedule that returns illegal values
	- **Durability**: changes of successful transaction apply even in failure or system failures
		- Basically written to disk
## Concurrency Control (Isolation and Consistency):
### Locking: Pessimistic
- 2PL/**Two-Phase Locking**
	- Must acquire lock before using data to read/write
	- Must hold lock until transaction committed
- 2 phases: grow phase, shrink phase
	- Do not get new locks after releasing any: aborts/rollback can see intermediate values ("dirty read")
- Guarantees serializability, but can be slow b/c blocking
- Does **not** prevent deadlock
	- How to avoid: calculate dependency graph with locks and kill transactions, or timeout
### Validation before commit: Optimistic
## Guaranteeing Atomicity
- Need transactions in distributed system:
	- Data that is replicated needs to stay consistent even on transaction changes
	- Chained/sequential transaction: work in single work unit performed on multiple nodes
- Issues with transactions:
	- Illegal inconsistencies (solved with serializability)
	- Deadlocks (deadlock detection)
	- Consistency failure on node participating in a transaction
	- Node failing in middle of transaction
	- Network partition
- Locking: frequent conflicts
- Validation: rare conflicts
#### Consistency
- ACID Consistency: Invariant that must be satisfied in database system. Transaction transform from one consistent state to another
- Consistency models: eventual, strong, read-your-writes, refer to guarentees about order of operations by distributed system
- CAP Consistency: client always sees the most recent write, nothing is out of data
- Replica consistency: state of replicas in a distributed system
-
- **Two-Phase Commit**
![Screenshot 2024-11-25 at 10.35.13 PM.png](../../_resources/Screenshot%202024-11-25%20at%2010.35.13%20PM.png)
- Prepare phase: coordinators asks all participants to prepare (vote) to commit
- Commit: coordinates either commits or aborts, and notifies participants which occurs
	- Actors:
		- Client wishes to perform transaction
		- Participants involves in data read/write of transaction
		- transaction coordinates that coordinates commit/abort decision
- Failure:
	- If node in transaction fails, not a big deal since node can't communicate with coordinator and unanimity required
	- If coordinator fails: persist decision
		- If crashes after prepare but before decision, nodes are in limbo: in-doubt transaction
			- Nodes can neither commit nor abort, and algorithm blocks until coordinator recovers
	- **Fault tolerance Two-Phase Commit**:
		- Consensus algorithm with **total order broadcast** (Paxos commit)
		- Every participant uses total order broadcast to transmit vote
		- If node A suspects B has failed, node A will abort on B's behalf
			- May introduce race condition if B is slow: use total order to decide result
			- Only first vote counted
		- Each node uses vote result internally to decide whether to commit or abort
		- Phase 1: Prepare
			- Send Prepare(T, R) to all nodes r (sent from client or from elected leader)
			- When received, votes own, Vote(T, r, vote) to R
		- Phase 2: Commit
			- If undecided and first vote and vote to commit, add r to set of nodes voted to commit, and if last one, commit and save result as decided
			- If vote to not commit, abort
		- Voting on behalf: If B fails, A can vote for Vote(T, B, abort)
![Screenshot 2024-11-25 at 10.39.40 PM.png](../../_resources/Screenshot%202024-11-25%20at%2010.39.40%20PM.png)

