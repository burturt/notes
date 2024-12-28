# CAP
- C: Consistency: Nodes read the latest write, and all nodes see the same data at the same time
- A: Availability: Every request receives a non-error response, no blocking
- P: Partition Tolerance: The system continues to operate despite network partitions
- Distributed systems MUST satisfied Partition Tolerant
	- If not designed to be distributed/is run on one mode, it is not partition tolerant, and CAP theorem doesn't really apply even if configured to be CP or AP
- CAP refers to configurations of distributed systems
- "CA" really should be "CAP does not apply"
- CAP applies **when a network partition occurs**:
	- During network partition, is it **strong consistency** (but potentially unavailable) or available (usually eventually consistent, not strong consistency)
	- both C and A may not apply even w/o network partition depending on design
# Byzantine Fault Tolerance
- Byzantine Fault Tolerance: property of distributed system that tolerates arbitrary failures (accidental or intentional), including malicious ones
- Traitor node: May send different messages to different nodes to cause inconsistency
	- Flaky nodes: ex. unexpectedly returns NULL, other nodes can't handle
	- Malicious actors: sends any arbitrary message that causes ENTIRE system to provide wrong answer (or crash)
- Use in Life/death or financial reasons
- Examples:
	- Bitcoin: Byzantine failures b/c don't trust everybody/anybody that uses the bitcoin network. Keep online, and don't let hackers take money
	- Airliners (777/787): fly-by-wire (electronic control). Bad/malicious input should not cause crash or incorrect result. Use redundancy, voting systems, pilot override
## Broken Assumptions
- Can adversary see all messages? some? none? (e.g. wifi vs wired)
- Can nodes fail or be corrected by adversary?
- Does attacker have limited or unlimited resources?
- Static vs dynamic attack strategy? (are they able to adapt strategy)
- Is communication time between nodes bounded or unbounded? i.e. timeouts
- Is the network fully connected? Some nodes communicate via relays (like routers)
- Doe we or adversary use randomized algorithsm? If so, proof/theory goes out the window
- Quantum/binary computing? If so, rip encryption
### Revisit 2 Generals Problem
- Can probabilistically solve: send 100 messages, and respond with 100 ACKs
### Revisit Byzantine
- Issues:
	- Honest generals do not know who malicious generals are, but can be possible to find out
	- Malicious generals can collude
	- Best is get honest generals to agree
- Problem can be solved if fewer than 1/3 of generals are malicious
### Oral Messages
- Vote attack or retreat, broadcast to everyone
- Assume point-to-point communication is reliable & no cryptography
- Each person receives message from a general, then asks all other generals what they heard from this general to determine if conflicting