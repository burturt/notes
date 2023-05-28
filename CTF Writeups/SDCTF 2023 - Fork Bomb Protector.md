# Original Challenge
MISC/Fork bomb protector (100)
k3v1n
181 solves / 100 points

We built a playground for people to try out Linux. We are tired of customer complaints about malicious fork bombs rummaging the server, hogging system resources, and bringing everything down to a crawl, so we built our own proprietary fork-bomb protector. As an "unintended" consequence of that, people cannot run commands normally. Our genius head of the engineering team suggests this to be a security "feature", not a bug, since this essentially turns our product into a restricted shell. Bye bye, RCEs!

[nofork.py](../_resources/nofork.py)

Connect via: `socat FILE:$(tty),raw,echo=0 TCP:nofork.sdc.tf:1337`
# Solution
- So, we are given an ip adddress to connect to and a python file. Going off of what the description says or reading the python source code, it is clear that process forking has been disabled:
```python
# Ban all fork-related syscalls to prevent fork bombs
def init_seccomp():
    f = SyscallFilter(defaction=ALLOW)

    f.add_rule(FORBID, "fork")
    f.add_rule(FORBID, "vfork")
    f.add_rule(FORBID, "clone")
```
- Just to start off, connecting causes an issue with the shell failing to connect:
![Screen Shot 2023-05-16 at 1.50.00 PM.png](../_resources/Screen%20Shot%202023-05-16%20at%201.50.00%20PM.png)
What a great start. In linux, "forking" is the process in which one process or program (in this case, the shell) creates a new process, "forking" its own process and then having the child (the new process) change into the program. However, some commands in the shell, known as built-ins, don't spawn a new process and run directly in the shell, returning control back to you after running the command. `man builtin` for more information.
In addition to builtins, we also have processes that occur inside the shell directly: [Shell Expansion](https://effective-shell.com/part-6-advanced-techniques/understanding-shell-expansion/) and [redirection](https://web.archive.org/web/20230412163832/https://wiki.bash-hackers.org/howto/redirection_tutorial).
- Example: when you run `cat FILE`, your shell will fork. When a program forks, it ends up with 2 identical copies of itself, but the parent and child know which one they are. As such, the parent will simply return control back to you, but the child will instead "change" itself into the `cat` program. The cat program will then read the file's contents, and output it to stdout, which is connected to your terminal.
- However, if we instead do like `cat <FILE`, after forking, the shell sets the `cat` process's stdin to the contents of the FILE, and `cat` simply takes stdin and sends it out to stdout. This is important: we can put the contents of the FILE into the stdin of a command WITHOUT forking the program!
## Exploitation
- After looking through a lot of the commands in `builtin`, `read` caught my eye: the read command allows for interactive scripting: it waits for user input (stdin), and then once it hits a newline, it stops reading in. Running `read <flag` gave a "No such file or directory" error, but running `read <flag.txt` simply returned without any errors.
- Now that the file was read in using the `read` command, simply use the `echo` command (another builtin) to print out the value of the env variable `$REPLY`, which is where `read` stores its output. - Success!:
![Screen Shot 2023-05-16 at 2.06.26 PM.png](../_resources/Screen%20Shot%202023-05-16%20at%202.06.26%20PM.png)
## Intended solution
- So as it turns out, the intended solution was actually different. Another builtin command is `exec`. Recall the forking process: what `exec` does, is instead of forking itself and having the child change into the target program, it simply changes the shell process to become the target program! A side effect of this is that once the program exits, the shell is no longer running
- Normal program execution (notice how the command is spawned as a subprocess aka fork of the shell):
![Screen Shot 2023-05-16 at 2.11.17 PM.png](../_resources/Screen%20Shot%202023-05-16%20at%202.11.17%20PM.png)
- Running a program with exec (notice how the shell with PID 4785 *becomes* sleep and then when sleep exits, it doesn't know to return bach to zsh):
![Screen Shot 2023-05-16 at 2.09.19 PM.png](../_resources/Screen%20Shot%202023-05-16%20at%202.09.19%20PM.png)
- Since this is a remote server, we can simply reconnect multiple times, doing `exec ls`, and then just `exec cat flag.txt` works.