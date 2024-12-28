# RPC (Remote Procedure Calls)
- Generalized "function call": distribute function calls across machines
- RPC: **"location transparency"**
	- System hides where a resource is located
- Historical:
	- SOAP/XML-RPC (1998)
	- Facebook/Apache Thrift (2007)
	- Google gRPC (2015) (protobufs)
	- Twitter/X Finagle
	- REST (often with JSON)
	- AJAX in web browsers
- How it works:
	- Client calls interface w/ message
	- Marshalling (e.g. python pickle, JSON, Protobuf) of parameters
	- Client stub: packages and sends over network
	- Server stub: unmarshalls response and sends to app
![Screenshot 2024-11-03 at 10.32.14 PM.png](../../_resources/Screenshot%202024-11-03%20at%2010.32.14%20PM.png)
![Screenshot 2024-11-03 at 10.32.22 PM.png](../../_resources/Screenshot%202024-11-03%20at%2010.32.22%20PM.png)
## Benefits
- Easy to use
- Hides networking details vs raw sockets
- Supports independent component development/evolution
- Allows for efficient packaging of arguments/return vals
- Supports authentication
- Location independence
## Failures
- Client and/or server may crash and reboot
- Packets may be dropped
- Network or server might be very slow
- Note: they all look the **same** to client!
### Failure Modes:
1. **At least once**
	- Client stub waits for ACK
	- If not ACK after fixed amount of time, resend
	- Repeat until ack or until give up and error
	- Useful for read-only and idempotent operations (repeat executions equal to one execution, like create user)
2. **At most once**
	- When server detects copy of request, return previous response
	- Determine duplicate by having transaction ID:
		- Contains client ID (IP), time of day
		- client ID + sequence number
		- Large random number
	- Problems:
		- retired XIDs and buffer of previous responses **grows without bound**
			- TTL
			- On next client request, specifies upper bound on sequence number
				- Tells server "seen up to N bytes", server deletes responses < N
		- Handle duplicate request while original is still executing?
		- Server crash and restarts, resetting list?
			- Server routinely save the old buffer (responses) and seen buffer (XIDs) to disk
			- Otherwise, can reexecute old requests
3. **Exactly once**
	- Combo of retry of At-Least-Once + Duplicate filtering of At-Most-Once
	- Record pending and successful RPCs in case of crash/restart
# MapReduce
- **Divide-and-conquer**: **similar** b/c splits data into **smaller complete chunks** operated on by different tasks (but not split up **recursively**)
- **Shared nothing**: no state is maintained for physical processing of data
- **Split-apply-combine**: break into pieces (split), operate on each piece independently (apply), them summarize (combine)
- **embarrassingly parallel system**: Little/no effort required to split problem into parallel subtasks
- Implemented into Hadoop and Apache Spark (via Hadoop)
- Main idea: **huge dataset of records**
	- Can be **grouped** on some use case (lines, webpages, pages, etc)
	- Want to **compute some metric (aggregate)** across each group
	- Want to do it quickly without special hardware
- How?
	- Some input data with same format
	- Take data and place into some group, identified by key, and export some value associated with record
	- Combination of "group" and value forms key-value pair, stored and used in further computation
	- Sort key-value pairs by key, treat as group, and compute summarizing function associated with group/key
	- Output keys and summary values to user, sorted by key, one summart value per group/key
## Steps
- Map phase, shuffle (sort to redistribute) phase, reduce phase
- Some input data, all same format
- Take data from record and group by a key (not necessarily unique), and export some value associated with that record
- This group and export value forms key-value pairs (map)
- Treat same keys as a group, and compute summarizing function on values with same key
- Output keys and summary values sorted by key, one summary value per group/key
### Example:
- 2 sentences, each sentence is split by space
- Key is word, value is just 1: `("the": 1), ("the": 1), ("cat": 1)`
- Combine all key-value, then partition by keys, so `("the", [1,1])`
- Sum values within partition
```
map: (k1, v1) -> list(k2, v2)
reduce: (k2, list(v2)) -> (k2, f(list(v2)))
```
### Paper Implementation
- Beauty: only implemented on commodity hardware
	- Job consists of tasks, and each task mapped by scheduler onto nodes in cluster
- Map Reduce program is standalone and contains:
	- Map phase code
	- reduce phase code
	- configuration
	- other code necessary to run job
	- In orig implementation, reduce waits for map, though may fetch data early
	- Hadoop: reducers can start working as map phase completes
#### Steps
1. First **split** the data into M pieces, about 16-64MB per piece and start the program on **all machines**
2. **One copy executes on the master node**, the others execute on the worker nodes. There are **M map tasks and R reduce tasks**. Master picks an i**dle worker** and **assigns a map or reduce task**.
3. A worker doing a map task **reads an input split** and any possible input key-value pairs (optional), executes the map function on them, and then **stores the intermediate key-value pairs in memory buffer.**
4. Intermediate key-value pairs are **written to disk**, partitioned into R groups by a **partitioning function**. The **locations** of these files are sent to the master, so that the reducers know where to fetch them.
5. When the master notifies a reduce worker of the locations of these files, the **reduce worker asks the map worker** for the data from its local disk. Once it has read all of the intermediate key-value pairs from the map workers, it sorts (in RAM or on disk) by the intermediate keys so all pairs with identical keys are local. **Why do we need to sort?** The reduce worker may process **multiple different keys**.
6. The reducer worker **iterates over the sorted key-value pair**s and sends all pairs with the same key to the reduce function which computes over the values. The result is **output to a running file** for this reduce task. Each reduce task **writes to its own file**, for a total of **R output files.**
	- Useful for future MapReduce jobs on output
8. When all reduce tasks are finished, the **master signals the user code that it is done** and moves on with the next instruction which could be another MapReduce job, or something entirely different.

![Screenshot 2024-11-03 at 10.56.57 PM.png](../../_resources/Screenshot%202024-11-03%20at%2010.56.57%20PM.png)


### Nodes
- Master: maintains location of key-value pairs
	- Assignment if input splits to map tasks
- Picking a reducer:
	- When map task completed
	- Hashes key, and assigns a worker to start reduce task if hash not seen, otherwise assigns to same worker of previous hashes
	- hash(key) mod R common.- Fault tolerance:
	- Binomial: P(X >= 1) = 1 - P(X=0) = 1 - (10000 choose 0)*(0.001)^0*(0.999)^10000
### Map Failure

![Screenshot 2024-11-03 at 10.58.50 PM.png](../../_resources/Screenshot%202024-11-03%20at%2010.58.50%20PM.png)

- If master does not receive ping in certain period of time:
	- Master marks worker as failed
	- Marks **all** tasks that the worker was processing as idle since data lost
	- Starts another map task, same split, on another worker
	- communicates re-execution of map tasks to reduce tasks, and data from failed worker is discarded
### Reduce Failure
- Note worker failed
- Start new **only incomplete** reduce tasks, task re-fetches data, but only partitions that have not successfully been processed
	- Data integrity: only finished tasks are written to global file system
- Requires map tasks maintain intermediate data until partition completely processed
### Master Failure
- In Hadoop 1.x: entire job fails, and user code can choose to re-execute it
- Hadoop 2.x: Replaced JobTracker with ResourceManager, with active and standby
### Optimizations
- Locality: assign map tasks on or near workers that already contains split to avoid fetches
- Backup Tasks: long running tasks can be **preemptively duplicated**
	- Hadoop: **speculative** execution
## Refinement
- **Partition Functions**: make a separate partition function to **equally distribute keys**
	- Default `hash(key) mod NUM_PARTITIONS`
	- ex. hash just domain, not URL, to keep all urls from same domain together in same output file rather than randomly spread out across partitions
	- **Key skew**: in partition, check the key, and **if it's very common, salt the key/hash using random number**, but this r**equires additional reduce phase**
- Heartbeat: check if nodes are online
- **Load balancing!**
- **Ordering guarantees**:
	- All intermediate key-value pairs are sorted by key
	- Forces output to **also be sorted** ascending order
	- Useful for downstream uses
- **Combiner**: reducer but **run on mapper**
	- Reduce on individual mapper before sending to save resources
- **Input/Output types**
	- Simplest: one line = one record
	- However, can write **custom readers**: specify start/end of record
	- Custom writer: write whatever output
- Side Effects
	- Allow for extra outputs from map/reduce operations
- Skipping Bad Records
	- Send packet to master if bad record found, if multiple failures on record, skip when reissuing execution of task
- Local Execution
	- Sequential implementation for debugging to help make tasks
- **Status information**
	- Use UI to see hadoop job status
- Counters
	- Counters to keep track of stats
	- e.g. number of input key/value pairs produced, output key/value pairs, calculate fraction of data that is X
## Uses of MapReduce
- Large scale ML
- clustering problems for google news and froogle
- processing data reports of queries
- Extracting properties of web pages for new products
- large-scale graph computations
- large-scale indexing
- Google uses Flume for batch processing, and Millwheel/Dataflow for stream processing
