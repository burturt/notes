- Kubernetes: container orchestration
	- Not designed for distributed systems
	- Mostly used for distributed systems via replication
		- Container orchestrated with availability, fault tolerance, load balancing, etc
- Tupperware/Twine: Facebook Kubernetes
## Containers
- Container: unit of software
	- Packages up code and dependencies so apps can run quickly and reliably regardless of machine
	- Bootstrap script: script to set up container
- Docker: platform with develop, ship, and run apps
	- docker container: lightweight, contains everything needed to run app:
		- Contains code, dependencies, runtimes, system tools/libraries, settings, data cache
		- share the os kernel
	- Containers built from container images on Docker Engine
- Before containers: vagrant popular to manage VMs
	- Hypervisor types: 1 = Sits on top of OS but bypasses it, aka bare metal, 2 relies on OS entirely
		- VirtualBox (2), VMWare (2), Hyper-V (1), Xen (1), Parallels (2), Kernel KVM.
- **containerd**: core container runtime, open source
	- Part of Open Container Initiative (OCI) - lightweight compared to Docker
	- Manages lifecycle (image pulling, snapshots) of containers and runtime
	- relies on **runc**, lightweight container runtime also part of OCI, which actually executes containers
- 
### Dockerfile
```docker
FROM node:lts-alpine
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
EXPOSE 3000
```
- Steps:
	- 1: Author Dockerfile
	- 2: `docker buildx build -t hello-app .`
	- 3: `docker run -d -p 8080:8080 hello-app`
	- 4: Use app (visit ip)
	- 5: push: `docker tag hello-app gcr.io/cs-134/hello-app`
		- `docker push gcr.io/cs-134/hello-app`
- Commands:
	- docker ps
	- docker container
		- ls
		- stop
		- rm
		- exec
	- docker image ls
	- docker rmi <image_id>
## Kubernetes
- Designed to coordinate containerized apps
	- Predictability: want to know it's going to work and how
	- Scalability: add more/delete replicas
	- High availability
- Services provided:
	- Container Orchestration
	- Automated rollouts and rollbacks
	- Self-healing
	- Load Balancing
	- Horizontal Scaling
	- Service Discovery
	- Secret and Configuration Management
		- etcd
### Architecture
- Master (control plane): gateway into cluster
	- Exposes Kubernetes API (interact with kubectl)
	- Health checking within cluster
	- Scheduling of work
	- Coordinates communication between components
	- Logical abstraction, can contain multiple physical servers for HA
	- Components:
		- etcd: lightweight distributed kv to store config info, service discovery, leader election, distributed locking
		- kube-apiserver: API that allows clients to manage cluster/K8s workloads. Bridge between components and allows communication between them. Interact with kubectl
		- kube-controller-manager: Each object in Kubernetes has controller: repeatedly checks state of component, and fixes state if not same as spec
		- kube-scheduler: assigns work to specific worker nodes based on available capacity ("bin packing")
		- kube-proxy: maintains network rules on nodes, reflects services as defined in k8s api on each node 
		- kubelet: agent that runs on each node, managing containers in pod, communicates with master
		- cloud-controller-manager: glues k8s and cloud components
- Node: runs user containers
	- Container runtime, containerd, CRI-O, Mirantis, Kata, gVisor (aka runc for all)
	- kubelet: agent that runs on each node, managing containers in pod, communicates with master. Authenticates node into cluster via kubelet, receives work/commands through manifest, controlling container runtime
	- kube-proxy: manages networking, lightweight load balancing, forwarding request to proper container

![Screenshot 2024-11-25 at 9.27.33 PM.png](../../_resources/Screenshot%202024-11-25%20at%209.27.33%20PM.png)


![Screenshot 2024-11-25 at 9.29.59 PM.png](../../_resources/Screenshot%202024-11-25%20at%209.29.59%20PM.png)

# Kubernetes Objects
## Pod
- Contains one or more tightly coupled components
	- e.g. a docker compose deployment
- Users do not manage pods directly
- Pods are stateless, and assets are static/ephemeral
## Replication Controller and Sets
- Replication controller: manages ensuring specified number of pod replicas are running at any given time:
	- Pod template YAML blueprint to define functionality of pod
	- Horizontally scales number of replicas to match state to spec
	- Handles upgrades of pods to new versions of app while remaining online and available
- Replication sets: replication controller to provide easier identification of pods
	- No rolling updates
## Deployments
- Defined in YAML files
- Define how set of pods should be deployment to run
- Define network and storage config for pods
- Use replication sets and add lifecycle management 
## Stateful Set
- Imposes unique identity via order number on pods within set
	- Allows developer to identify behavior of specific pods (e.g. "the fifth one")
	- Numbers persist even if pod rescheduled on different machine
	- Persistent volumes follow pod onto new machine
- Used for pods with persistent data, stable networking, or databases in coordinated way
## Daemon Sets
- assigns copy of a pod on each machine to do maintenance task
## Jobs/Cron Jobs
- executes a job one or scheduled
## Others:
- Service: load balancer, allows accessing group of pods as a single entity
- K8s uses volume abstraction that allows data to be shared by all containers within pod and available until pod terminates
- Labels and annotations allow tagging pods with data to retrieve group of, or individual pods