## Fallacies
- Joy, Lyon, Gosline first 4
- By L Peter Deutsch, last 4
1. Network is reliable
2. Latency = 0
3. Bandwidth = $\infty$
4. The network is secure
5. There is a single administrator
6. The topology won't change
	- Hosts go down/up, network links unreliable
7. The transport cost for additional data is zero
8. The network is homogeneous
	- Nodes and connections vary
## Solve the problem:
1. Abstraction
2. Lots of code written without worrying about Fallcies
- Standard approach: Remote Procedure Call (RPC)
	- When calling function, send data to remote server and wait for response instead of calculating itself
	- backward compatibility
## RPC differences from orginary call/syscall
- caller + callee don't share address space
	- safer but slower
- disagreement on data representations
	- Architectural differences: big vs little endian
	- Solution: network byte order representation, typically big endian
		- Marshaling (pickling): converting to serial format
		- Unmarshaling: opposite marshaling
### Performance
- Marshaling/unmarshaling
- copying
- network latency/throughput limits
	- Pipelining: don't wait for response to send next request
	- Multiplexing: responses can be made in any order
	- Issues:
		- increased complexity
		- what if requests are dependent?
		- what is a request fails? Partial success/failure?
- Caching: Improve latency at expense of RAM and stale caches
- Prefetching: guess future requests on client
## Sample RPC
### X protocol for windowing systems
- x = XDrawPixel(3, 5, Blue)
- client: send x, send y, send color, read reply
- server: recv x, recv y, recv color, draw pixel, write "ok"
### HTTP + JS
- do computing locally
## RPC (dis)advantages
+:
- Client + server RAM are distinct, neither can trash the other

-:
- Messages can be lost
- Messages can be corrupted
- Network might be down or slow
- Server might be down or slow
	- down vs very slow hard to distinguish, or network vs server down
## Fixes
- Corruption: use checksum to detect, resend if detected
- No response:
	1. Keep trying: send duplicate request (at least once RPC)
	2. Return error if no response in time (at most once RPC)
	3. Exactly one RPC (expensive)
	- 1 & 2, extra work, 3 is expensive to do to pretend program is actually not distributed