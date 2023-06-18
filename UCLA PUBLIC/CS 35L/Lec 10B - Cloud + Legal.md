# Cloud Computing
- Marketing term
## Aspects:
- On-demand, self-service
	- Low barrier to entry
- Rapid elasticity (scalability)
- Multitenancy for shared resources
	- Allow for peaking 
- Measured service: requires keeping track of resource usage
- Broad network usage ("location independent")
## How to do it
- Infrastructure as a service: Customer provides OS, apps, etc, only hardware provided
	- AWS
- Platform as a Service: provider supplies middleware (like library versions, programming language runners)
	- Google App Engine
- Software as a Service: service provider runs the entire software stack, customer simply configures it
	- Salesforce
### Virtual Machine
- Customer will get its own set of processors
- One way: process per customer, but no isolation
- VM: software pretending to be hardware, e.g. simulate arm cpu for x86
	- Can provide hard barrier, but large performance hit
	- If you limit architecture in VM to be same as actual machine, can pass through many instructions and run instructions at full speed, but not fast enough
- Issue: too many simulated instructions
- Result: containers
### Containers
- Virtual OS same as Physical
- Share many files, like executable code, kernel, etc
- Docker:
	- Has objects
		- Container: env to run app
		- Image: template to build container
		- Service: lets containers scale across multiple docker deployments
	- Registries: repository for docker images
		- Can push/pull
- Dockerd: docker daemon
	- Actually can run containers
	- Container are linux processes 
	- `docker` command actually just talks with docker daemon
- Docker porcelain:
	- docker maintenance or management programs, like kubernetes, AWS Elastic Container Service, etc
	- Addresses load balancing, resource constraints, logging/monitoring, security, updates (on a running system)
	- Updates: blue green updates: start up new set, then switch over
## Serverless Computing
- Replace containers with serverless computing
- Code is ignorant of server infrastructure, and infrastructure handles the heavy work, including routing and distributing work
- Advantage: simpler code
- Disadvantage: efficiency
# Legal Aspects
- Several kinds of law that are quite different:
	- Commercial law (contracts, economics)
	- Criminal Law (crimes)
	- International law
	- Admiralty Law (overseas)
- Important commercial law areas:
	- Copyright law: encourage creative arts by allowing authors to make money off of creations
		- Life of author + 75
	- Patent laws: originally for inventions, give inventors exclusive rights
		- Expire in 20 years
	- Trade secrets: unlike patent, you don't publish
	- Trademarks: prevent impersonation/false advertising
	- Personal Data (rapidly evolving)
	- AI law (in the future)
		- Restrictions on AI
- Enforcement: via commercial law
	- sue others who have infringed on rights by using something without permission
	- Almost always money, want to avoid since costly and takes a long time
	- **Technical protections**
		- Use technical protections to prevent stealing
		- e.g. SaaS
- Permission: License
	- A grant of permission to use software
	- NOT a contract, but can be part of a contract
	- Free software/Open source software
### License levels
- Public domain: no rules, given away free
- Academic license: do anything, but must give credit (BSD)
- Reciprocal License: must redistribute changes under same rule as original
- Forced contribution: must give changes back to original authors, and original authors own changes
- Proprietary license