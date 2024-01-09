- "human faults"
## Statistics
- CVE statistics (Common Vulnerabilities and Exposure)
	- Maintained as some government project
- Published: 1999-2022 (Bhuntel & Rawat 2023 paper)
- 3 categories: OS bugs, Hardware/Firmware bugs, Apps
	- 38k OS, 140k apps, 1.8k HW
	- 16k OS + App, small others (0.5 2 combined, 0.1 all 3)
	- But HW/OS more important
- Top 5 bug types:
	- 3945: Out of bounds write
	- 3802: Improper restrictions of operations in a buffer
	- 3365: Bad input validation
	- 2199: Exposure of sensitive info
	- 2105: Out of bounds reads (probably more common than write but less impactful)
- Example: `tar -cf /dev/rmt0 /` to backup to tape drive
	- readdir, then lstat file, and if symbolic link, follow it and readlink, then read file
	- If between check for symlink and then read, avid could link to other file
### Reality:
- Many, *many* issues are configuration errors, not actual CVE exploits
## Defense Mechanisms
- real-world attacks: force and fraud attacks
- Computer security: force and fraud attacks
	- Focus: fraud attacks (trying to login when not user, fraud is easier)
### Main form of attacks
- Targets:
	- Privacy: unauthorized release of info
	- Integrity: unauthorized tampering of info
	- Service: denial of service
- Goals:
	- Disallow unauthorized access: no complaints, no real check
	- Allow authorized access: complaints right away, checked automatically (files bug reports)
### Threat Modeling
- Focus on most likely/most severe problems and not waste time on smaller issues
- Model attackers, their attacks, and classify them
	- Insider attacks: authorized access used in a bad way
	- Social engineering
	- Networking attack
		- Buffer overrun
		- Denial of service
		- phishing
	- Device attacks (mitm keyboard dongle)
## General functions needed
### Authentication
- Prevent masquerading: pretending you are someone you are not
- Types of authentication factors:
	- Who you are (biometrics)
	- Something they know (password)
	- Something they have (security key)
	- MFA: use 2+ from above
	- Bootstrap authentication: who authenticates the authentication?
- External authentication: user from "outside"
	- Can be expensive: does not happen often
	- Delegate to trusted login code
		- Passwords: must not be easily guessable
		- Cell phone
	- Possible attacks:
		- guessing passwords (captchas)
		- snooping (e.g. record someone typing in their password, or network snooping)
		- Fraudulent servers (certificates)
- Internal authentication: externally authenticated, but must auth for internal answers
	- Should be cheap: happens many times, like whenever reading files
	- Linux/Unix: process descriptor contains user id (uid), group ids (gids) (can have multiple)
		- setuid(num) exists, limited to root for e.g. /usr/bin/login to setup shell when logging in
			- Has effective vs real user id for when using setuid calls, root can change both
		- suid/sgid permission bit: runs as file owner rather than user
			- Sticky bit: `t` in directory: only owner of file can delete from directory
			- setgid: newly created files in directory have same group as directory, allow for collaboration
### Integrity (checksum)
### Authorization (ACL/access control list)
### Auditing (logging)
- Log external authentication, internal usually too expensive
### Access Control
- Direct access: map resource into program's address space (mmap)
	- +Fast
	- -Resource easily corrupted
- Indirect access: issue syscalls (service requests)
	- -slower b/c dangerous to do it fast
	- +more fine grained control
	- example: read syscall to read files copies from kernel memory into user memory instead of sharing memory
	- Increase speed: copy on write (going to be tricky esp. with write)
### Concerns for security checks
- Performance
- Correctness
## Access Models
- Dimensions: principles (users), objects (files, processes, etc), actions (read, write, delete)
	- 3D array of booleans
	- Requires massive number of changes when updating single permissions
- Linux model: 12 bits per file + uid/gid owner
	- not enough: e.g. multiple owners w/o extra system-wide groups
- ACL: Access Control Lists (file access control lists/facl)
	- MS-Windows, also now on linux
	- For each object, maintain list of who can access, and what permissions
	- `setfacl -m u:brian:rwx .`
	- When used, shows `+` in `ls -l`
	- Pros: flexibility
	- Cons: complex, filesystem must store facls
- Capabilities
	- Right to access an object
	- Belongs to process, NOT destination object
	- Build: like fds, or store in fds
- ACLs vs capabilities
	- Capabilities easily works for primary RAM
		- virtual memory protections
	- ACL more flexible but not directly supported by hw
	- ACL: many processes, single object
	- Capabilities: delegate to users ability to delegate restricted capabilities
	- IBM i Series/CHERI (ARM, RISC-V): contains 128 bit pointers, Hardware support for capabilities
	- Network capabilities: encrypt perms + address
# Network Security (OS)
- example: DNS design flaws:
	- easy to forge UDP in any part of the recursive request