- Method 1: own server
	- residential ISP not allowed
	- Dynamic IP
	- Expensive
	- Manage everything
- Shared Hosting provider
	- Cheap, limit access
	- Can be security risks, sharing with scam sites, etc
- VPS
	- Can be expensive, virtualized, richer set
- Cloud
	- Allow distributed modes and separation of concerns
	- Components for serving, storage, load balancing, and content distribution
	- Flexible cost
- Managed Cloud Service/PaaS
	- Tailed for specific app
	- Vendor lock-in
- Rent VM (IaaS)
- Things to consider:
	- Disk space (app, data used, logs, though can live elsewhere)
	- Disk speed (depends on type and interface like SATA III)
	- Memory: every visitor uses memory, if run out, disk thrashing, more memory can improve w/ file caching
	- CPU: should be done on own machine, issue for AI/video encoding/games
	- Network speed + latency
- Nowadays bottleneck is Network I/O (NVMe 2-7GBps)
- DNS lookups can be slow, disable reverse DNS
- Capacity Planning; SLA (parallel requiest count, acceptable latency), needed workload (rps, RAM, CPU, data transfer, disk I/O), measure!
	- Splunk, DataDog, New Relic, etc
	- CPU load: `top, htop, ps -axl, pstree`
	- Disk I/O: `iostat, iotop`
	- Network; `netstat -is` `lsof -i :port`
	- RAM `free -m, vmstat, memstat`
- Cloud: RAM, CPU speed/core count, disk type, capacity, throughput, static + growing data, filesystem requests, IP address
- Caching: cache database results in RAM cache
	- Can be done at Encryption, HTTP, Application, Database Layer
	- Memcached (strings only)/Redis key-value stores, can be distributed
- **edge-side include**: `<esi:include src="part1.html" />`, replaced by reverse proxy provider with correct content
- Browser caching;
	- cache-control: private,max-age=31536000 (private = not stored on server)
	- Expires: Wed, 21 Oct 2023 07:28:00 GMT
	- Last-Modified: Wed, 21 Oct 2023 07:28:00 GMT
	- ETag: "33a64df551425fcc55e4d42a148795d9f25f89d4"
- Sent from client:
	- if-modified-since: Wed, 21 Oct 2023 07:28:00 GMT
	- if-none-match: "33a64df551425fcc55e4d42a148795d9f25f89d4"
	- 304 not modified if not modified
## Example stack
1 Nginx with microcaching
2 Node / Tomcat / WordPress / Rails
3 MongoDB / MySQL / PostgreSQL
4 Linux
5 Hardware (Many CPU cores, Lots of RAM, SSD)
- Scaling in data center:
	- Each machine is organized into a rack organized into a cluster in a data center
	- Hardware failures due to power and heat, power supply/hdd/network adapter failures
	- expensive and difficult to cool, bad carbon footprint
	- difficult to keep machine state consistent across cluster
- Database hardest to scale
	- Note: CAP theorem (distributed system can be 2 of 3: consistent, available, and partition tolerance)
	- MariaDA: sharing vis Spider storage, MySQL sharing, PostgreSQL replication and sharding
- Scaling: separation of concerns (separate app, logs, user data, old logs, hot files in distributed file system, caching node, load balance, serve via CDN, do computational intensive work on separate dedicated nodes w/ queue, store relational on cluster) 
![Screenshot 2024-06-08 at 2.25.10 PM.png](../../_resources/Screenshot%202024-06-08%20at%202.25.10%20PM.png)
- Kubernetes: each deployment contains pods, where containers run. If pod fails, controller kill pods and creates a new one, K8s cluster uses declarative syntax
	- Pods are stateless, and data stored on statefulset (volumes/dbs)
	- load balancing built in