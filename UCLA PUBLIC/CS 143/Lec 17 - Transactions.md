```sql
START TRANSACTION
DELETE FROM final WHERE uid IN ('222', '333');
UPDATE final SET score = score + 15;
COMMIT;
```
- **ACID**: Atomicity, Consistency, Isolation, and Durability
	- Atomic: happens all or none (roll back if fail, like corrption, constraints, system issues)
	- Consistent: database must be consistent before and after (e.g. no money lost or gained in transaction)
	- Isolation: multiple transactions can occur independently, transparently, without interference
	- Durability: changes of successful transaction apply even in failure
	- Provides high consistency
- NoSQL: **BASE**
	- Basically Available (distributed available nodes) Soft-state (data values may change over time as unknown when system is consistent) Eventually consistent (data may become consistent over time)
	- High availability and eventually consistency
## Schedules
- Serial: execute transactions one after another
- Conflict-serializable: acts just like a serial schedule but is actually interleaved
	- still guarantees consistency
- Swap test:
	- 2 reads can be swapped
	- any writes cannot be swapped
- Precedence graph:
	- Draw from Ti to Tj if A(X) before B(X) where A or B or both is a write
	- if no cycles than conflict serializable
![Screenshot 2024-03-17 at 5.27.17 PM.png](../../_resources/Screenshot%202024-03-17%20at%205.27.17%20PM.png)
## Translation Isolation Levels
- Allowing concurrent allows for improved throughput, and reduced waiting time
	- non-serial schedule may lead to inconsistent state, but conflict serializable still guarantees for consistency 
- Serializable
	- Equivalent to serial
	- Poor performance, max consistency
	- No new data can be read 
- Repeated Read
	- if Ti reads X, no other TXN Tj can update until done
	- default in mysql
	- data read cannot change, but new data in a set can appear (phantom read)
- Read Committed
	- Ti can only read data from Tj that committed
	- Read data was valid at some point; can change midway (non-repeatable/fuzzy reads)
- Read Uncommitted
	- Ti can read data from any other TXN even if not committed
	- Allows dirty read
- All levels prevent dirty writes (all writes are atomic, no in-between states)