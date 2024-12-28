## Basics
- Distributed system def:
	- Set of servers that cooperate over a network to accomplish a task
		- Issue: server too limited, may be a service, not just task
	- Group of interconnected computers cooperating to provide a service
		- Issue: computers --> servers/nodes (since virtualization)
	- A set of interacting components with specific I/O that provides functionality with well defined interfaces
- 3 aspects/**goals**:
	- **Communication** in system over a network rather than a bus
	- **Coordination** among components, all synced on task/service
	- **Transparency** (abstraction to user)
	- Leslie Lamport: "system which failure of computer you didn't even know existed renders your computer unusable" (also dev LaTeX)
- Implied: **heterogeneity**
	- Communication between components are flexible
		- Good: Ethernet, TCP/IP, Bad: proprietary
	- Nodes do not need to be the "same"
		- Good: all OSes, hardware, types, etc, Bad: nodes must be same hardware and OS
	- **Interoperable, composable, extendable**
- Why study?
	- everywhere, widely used
	- interesting w/ lots of hard problems and powerful solutions
	- popular app backends, huge websites, dns, data processing, ML training/testing/inferencing, web searching/indexing, recommendation systems, file sync, music and video encdoing and serving, gaming, blockchain
## Types of distributed systems
- 3 classes:
	- **High performance computing** (simulations, data processing)
	- **Info/database systems** (dbs, indexes/searches)
	- **pervasive** (collecting some sort of data) and **ubiquitous** systems (sensors are everywhere)
		- Cell phone network
		- Speeding cameras
		- sensor network/IoT
		- security systems
- Master node system
	- Range issue: too far to contact master node
	- single point of failure
- Gossip system:
	- Consistency issue: it takes time for changes to propagate
	- Data could be lost if node goes down
	- 
## Distributed System Components
- Computation
- Communication
- Storage (as a form of middleware)
### Computation
- Nodes and edges for communication
	- Nodes are not necessarily server (rpi, phone, ipad, weather station, robot, laptop, car, smart device, scooter, etc)
	- Measure nodes by MTBF (mean time before failure)
- Node roles:
	1. Service provider
	2. Load Balancers
	3. Compute nodes
	4. Databases
- Example: twitter
	- tweets and videos sent to flink (frontend)
	- texts sent to spark for processes
		- written into db from spark
	- videos sent to video encoding system
		- sent to cdn
	- all distributed
- Distributed systems can rely on other distributed systems:
	- databases
	- big data processing (spark, flink)
	- media processing (video encoding)
	- authentication
	- transaction systems
	- distributed systems as a service (google k8s)
### Communication
- Networks: technically distributed but we are handling application layer
	- Ubiquiti Unifi controller
- Link types:
	- Varying operators operators: eduroam, DSL, cable, fiber, satellite, etc
	- Various connection media: electrical, radio, light, microwave, etc
	- Lots of bad things can occur: bad operator, fiber breaks, shark bites, congested cable nodes, misconfigurations, broken/kinked cable, signal loss, interference, solar storm, electrical shorts
- Metrics for links:
	- Latency: ~1ms within same building/datacenter, ~100ms one continent to another
	- Bandwidth
		- 3/4/5G = 3/10/100 Mbps
		- DSL/Cable DOCSIS = 200 Mbps
		- Fiber = 1Gbps
		- Office = 1-10Gbps
### Storage (as a form as middleware)
- Storage:
	- HDDs/SSD
	- Different file systems different performance/security guarentes (fs could be distributed!)
## Desirable Properties and Goals
- What happens if one node in the system goes down? What happens to the data on it?
- How does the user interact with such a complex system with so many
failing parts?
- What happens when multiple failures occur at the same time?
- How do we make sure that certain messages and actions occur in the proper order?
- How do we make sure the system returns the correct answer?
- What happens if the system needs more resources? How do we prevent waste?
### Properties
1. **Scalability/Performance**:
	- Overhead of distributed system vs direct run optimal as node count increases
	- Idea: parallel use of CPU, RAM, disk, network
	- Must manage load balancing, slow/bad components, etc
2. **Fault Tolerance**
	- Idea: replicate data to backup nodes that can take over
	- No single point of failure, replication, but causes consistency issues
3. **Consistency**:
	- Must have well defined behavior
	- strict: should return most up-to-date write (no old data), using locking/concurrency
	- Idea: use locks and concurrency control when performing writes
	- very hard under replication
- Should do this transparently!
- Use API like RPC