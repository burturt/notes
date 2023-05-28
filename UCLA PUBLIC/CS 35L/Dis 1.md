`tar -czvf assign1.tgz files`
`tar -tvf assign1.tgz` to verify


# Linux
- Linux: family of OS based on linux kernel
- shell: program that takes commands from keyboard and gives commands to OS, typically interacted using a terminal application
- Unix File System
	- Methodology for logically organizing data  (easy to manage)
	- tree structure: `bin` binaries, `home` home folders, etc
# Pipes
- 0 is stdin, 1 is stdout, 2 is stderr
- `2>` to output stderr
- `0<&-` close pipe
# bash scripts
```bash
echo $# (number of arguments)
echo $0 (name of script)
echo $1 (first argument)
echo ${15} (combine)
```