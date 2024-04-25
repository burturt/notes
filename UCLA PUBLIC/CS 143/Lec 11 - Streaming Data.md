- Message broker: send/read to message broker, holds a message queue
	- Durability handled by broker, broker handles state
	- if too big, delete either oldest or random
- Windows:
	- tumbling window: non-overlapping windows of time
	- hopping window: fixed length period, but time periods overlap
	- sliding window: based on events, window +- a time around each event
	- session window: arbitrary window from first to last event
- Stragglers:
	- events arriving late
	- Can ignore, keep window open for a while to capture stragglers, sometimes can correct computation, or include in current window metric
- Potential problems:
	- Lost messages between P and B, or B and C
	- Lock ACKs
	- Offline producers, consumers, or brokers
	- queue overflows
	- producer can't trust broker or consumers
- Minimize message loss
	- Load balancing
	- Acknowledgements
	- Retransmission
	- Deleting messages using TTLs, or at random
	- Reassigning consumer duties from one consumer to another
	- Adding new consumers on the fly
## Bloom Filter
- Compute each hash function, h~1~(e), h~2~(e), h~3~(e), ...
- Each hash number mod length of the bit array is a position in the array
- Check the bits in those positions: if all 1, then *may* have seen e before, otherwise definitely never seen
- **HyperLogLog**: treat bitstring as binary number, and use run lengths and probability to estimate number of distinct objects
	- count approx 4 billion distinct elements with 5 bits: log2 log2 (2^32) = 5

![Screenshot 2024-03-17 at 8.43.47 PM.png](../../_resources/Screenshot%202024-03-17%20at%208.43.47%20PM.png)

