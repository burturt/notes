## Client-server
- node + react: built on js + posix os + shell
	- Configuration issues, and quoting issues
- Client-server system:
	- server keeps track of state of clients
	- Not the only model. Some others: many client to many server approach, Peer-to-peer (nodes are not all strictly defined as server or client, no one single server that keeps track of state), service workers (primary node delegates tasks to worker nodes)
## Performance Issues
- Throughput: server issue: queries per second?
- Latency: client issue: delay between request and response 
- Often tradeoff. Sample strategies:
	- batch processiong increaes throughput to save ram but increases latency, aka "out of order execution"
	- caching: re-use answer to same question asked before on the client side to improve latency but stale caches issue
		- stale caches become cache validation: check if cache needs to be updated cheaply
# The Internet
- Before internet: telephone system w/ circuit switching
	- When talking, direct connection and no glitches
	- issues: extremely high reliability but extremely inefficient due to dedicated wires, no automatic rerouting if a breakdown in connection occurred 
- P. Baran: packet switching
	- Break conversation up into packets, and let internet route each packet to destination
	- Advantages: robustness in disconnects and more throughput, but with worse latency
- Packets:
	- Header + payload
	- 3 problems:
		- Received out of order
		- Lost
		- duplicated packets
	- Solution: protocols, but can be complicated!
- Layers:
	- Link Layer: hardware protocols between 2 nodes
	- Internet layer: get across multiple layers
		- Uses IP or internet protocol
	- Transport layer: deal with stream of data
		- UDP layer: very thin layer on IP, minimum required data added
		- TCP:
			- Reliable, ordered, error checked
			- Divides stream into packets
			- Retransmission
			- Reassembly
			- flow control
	- Application layer: e.g. WWW (world wide web)
		- IPv4 created at UCLA: defines how a packet looks like
			- Header: length, protocol verison number, destination address (32 bit int), source address, checksum, time to live/TTL: decrement value each node, and discard packet if 0 to prevent infinite loops
		- IPv6: improvement increased address size to 128bits
		- HTTP:
			- wanted ability to link to each other, and create a spider web of physics papers
			- SGML: Standard Generalized Markup Language
				- Standard interchange language for publishers of books, papers, etc