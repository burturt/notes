- "deadly embrace"
- example: why no copy syscall?
	- deadlock issue: if you copy between 2 files in both direction, deadlock
- Idea: 2 threads aquire a lock the other needs
```c
t1:
	acquire(&b1);
	acquire(&b2);
t2:
	acquire(&b2);
	acquire(&b1);
```
## Solutions:
- Lock order:
	- Every action must acquire locks in ascending address order
## Solutions to deadlock
- Lock ordering: pick some order and be consistent
	- To lock n objects, grab locks in increasing order
	- If you can't get a lock, give up all locks and try again
- System detects deadlocks dynamically
	- issue: locks must be able to fail if able to cause deadlock
	- How to detect deadlock: 
		- Graph between threads and objects, and if a cycle is found, deadlock occurs
		- O(N) worst case, but usually much better
- Deadlock is a race condition, 4 conditions for deadlock:
	1. Mutual Exclusion (exclusive access)
	2. Hold one lock and wait for another
	3. Circular wait (cyclic pattern of dependencies, lock ordering to avoid)
	4. No preemption of locks
# OS Security
- Case study: Android, 2-10
- Android system:
	- Hardware, then kernel, then HAL (hardware abstraction layer, aka drivers), then split between native libraries (C/C++) and JVM, then app framework (java), then apps
- Stats (Garg & Baliyan):
	- Vulnerability locations
	- 9% apps, 5% app frameworks, 33% in native libraries, 1% JVM, 2% hardware abstraction layer, 53% kernel
- "Don't retrofit security"
## Timeline of security features
### Android 2 (2009)
- Disabled execution of code in stack or heap
	- Problem: buggy code causes issues
	- Aimed at native libraries
- Format string vulnerability protections
### Android 3 (2011)
- Support for encryption of user data
- Apps cannot write to memory cards directly
### 4 (2011)
- Adpoted SELinux
	- Secure Extensions for Linux
	- Kernel Module developed by NSA ~2000
	- Implements Mandatory Access Control
		- Enforced by a centrally configured mechanism
		- (Discretionary access control is normal perms, and gives discretion to power)
	- More flexible
	- `ls -z` to list MAC
	- `ps -efZ`: processes have user + role + domain information
- ASLR support
- RELRO
	- Relocation Read Only
	- Makes GOT table read only after dynamic linked
- Don't let kernel address leak into user space (e.g. from /proc directory)
### 5 (2014)
- Full disk encryption
- Smart lock: auto lock system smartly
- Android application sandboxes reinforced with SELinux
	- Each app gets own username, runs in own sandbox
	- Only for google's own apps
### 6 (2015)
- Extended SELinux sandbox to 3rd party apps
- Default perms on home directories changed 751 (rwxr-x--x) to 700 (rwx------)
- Automatic security patching
- Added fingerprint authentication support
### 7 (2016)
- File-based encryption
	- Protect against other apps
- Improved SELinux, ASLR, and verified boot
### 8 (2017)
- Google Play Protect (PHA: potentially harmful app search)
	- Cheap checks on client, expensive on google's servers
	- Google tests are conservative, and can be kept secret
- Rollback protection for verified boot
	- If unverified kernel, rollback to last verified state
- All apps run with a seccomp-bpf filter
	- Limits syscalls
### 9 (2018)
- sandboxing is mandatory for all non-root apps
- Kernel control flow integrity (CFI) (ARM)
	- Ensure instruction pointer isn't hijacked
	- Ensure only jumping to intended jump solutions
	- Defends against ROP attacks
### 10 (2019)
- Device + file encryption became mandatory
- Removed access to /sdcard/DCIM
## Mobile application attacks
- Overlay attack: overlay transparent window over password input for example
- Web injection attack: inject JS into another app
- WebView Injection:
	- WebView is embedded browser in app
	- Pollute legit URL when visiting
- Cookie stealing
## Reflections on trusting trust (Ken Thompson)
- Replace login.c file that has a backdoor
	- Compile bad login.c file, but distribute login.c source code
	- Detectable if others compile kernel themselves
- Edit gcc to now compile linux with bad login
	- Distribute new gcc version
	- if someone compiles gcc, then it will generate good gcc
- Edit gcc to include its own malware
- Moral of the story: you must trust someone to boostrap