# Shell
- command language for running other commands
- determine argument vector from typed commands
	- separated arguments with spaces 
	- arranges for `argv[0]` = command word, `argv[1,2,...]` are the arguments, `argv[-1]` is nullptr
- Tokens/words of the shell
	- add `\` to escape characters
	- Note: if `\` is before a newline, the newline is ignored
- Quoting
	- single quote version: you can put any character in it
		- this includes backslash, so escaping needs to be done outside single quote
	- Single quotes can be combined with an implicit concatenate, e.g. `echo ab'c d'ef` is "abc def"
	- Double quote: some characters inside double quotes are special
		- ex. `$` says interpreit text as variable
		- `\$` becomes actual dollar sign
		- Special characters: `	\ $ \ ' " ` and "`"
- Reserved words
	- `exit`: 
	- `if then else fi`
```bash
if test -r food
then cat food
else echo "foo missing!"
fi
```
- test if file is readable
```bash
if test -f foo
then echo "foo is present but not necessarily readable"
else echo "foo missing"
fi
```
- `$?` contains exit code
	- Consult after fail
		- e.g. `cat`: 0 = ok, 1 = I/O error
		- `grep`, 0 = found match, 1 = no match, 2 = serious error (e.g. file not found/readable)
```bash
if grep eggert /etc/passwd
then echo eggert known
elif test $? -eq 1
	then { echo 'eggert missing'; cat /bin/false; }
else echo system error
	fi
fi
```
- `[` aliased to test, so `if [ $? -eq 1 ]` same as `if test $? -eq 1`
- `!` negates a command result, MUST have space around it
- `if ! grep eggert /etc/passwd; then echo eggert missing; fi`
	- Aside: in bash, `!!` at end of line means text of previous command, `!*` arguments of previous command
>  builtin, !, %, ., :, @, [, {, }, alias, alloc, bg, bind, bindkey, break, breaksw, builtins, case, cd, chdir, command, complete, continue, default, dirs, do, done, echo, echotc, elif, else, end, endif, endsw, esac, eval, exec, **exit**, export, false, fc, fg, filetest, fi, for, foreach, getopts, glob, goto, hash, hashstat, history, hup, if, jobid, jobs, kill, limit, local, log, login, logout, ls-F, nice, nohup, notify, onintr, popd,
     printenv, printf, pushd, pwd, read, readonly, rehash, repeat, return, sched, set, setenv, settc, setty,
     setvar, shift, source, stop, suspend, switch, telltc, test, then, time, times, trap, true, type, ulimit,
     umask, unalias, uncomplete, unhash, unlimit, unset, unsetenv, until, wait, where, which, while – shell built-
     in commands
	 
- do `s=$?` then use `$s` in future commands to store result code
- OR use
```
case $? in
 0) echo ok ;;
 1) echo trouble;;
 2) echo missing;;
 *) echo something else
esac
```
- Last `*` is a glob pattern
- Globs:
	- * for anything, `[abc]` for one, `ab|cd` for either
```bash
while test -f foo
do rm -i foo
done

for i in a b 27 c
do echo $i
done

for i in *.c

```
- `break` and `continue` exist
- `:`/`true` are commands that always succeeds, `false` always fails
- keywords are only special if first (in the command spot), but tokens are always special
- Token examples:
	- `&&`, `||`
	- `grep a f || { cat f; echo ouch; }`
	- (): act like {}, but when in parenthesis, the commands are run as a subprocess, which affect variable scope
	- `&` backgrounds the task, don't wait for command to finish
- Variables:
	- `ij=string`, no spaces around `=`, can quote string, can put multiple on one line
	- If command after assignment statement, then command will get shell variables
	- `export ij` means 
- `sleep n` to wait n seconds
- `wait PID` waits for process to finish
## Variables
- `$?` - exit status of recent command
- `$!` - process id of background command
- `$1`, `$2`, etc are the arguments
	- `set a b c` sets arguments to a b c
	- `shift` moves all arguments left one, and discards $1
- `$0` is shell command
- `$*` expands to all arguments except $0
- `$@` is like array
- `$(command)` takes output of `command`
	-  e.g. `cat $(grep -v eggert *)`, you can also technically use \` and \`;
## Shell command expansion
- Script is transformed into "expanded" script in a simpler language
	- Constantly being expanded for each command
	- Shell runs off of expanded script 
## Stages of shell expansion
- 0. Figure out where the command is, delimit the command
	- Tokens that end lines: newline, semicolon, vertical bar
	- Commands are now sequence of words
- 1. Tilde expansion
	- When a word starts with a tilde (~) expands to home directory
	- `~/` is path from home directory
	- `~text` is home directory of user with name text
	- Unique: tilde only works at start of the word
- 2. Variable expansion
- 3. Command substitution
	- `$(command)` gets expanded into output of that command
	- Output of command expansion can run commands or be invalid, so be careful
	- Since this happens after splitting, end line tokens don't work and will be treated literally
	- Works in double quoted strings
- 4. Arithmetic expansion
	- `$((x + 5))` would expand to the value of the variable x + 5
	- compare to `$( (command))`: need space vs no space
	- Works in double quoted strings
- 5. field splitting
	- Splits old words from dollarsign expressions that can be split into multiple words for later argument feeding
	- Splits using separater characters: `$IFS`
		- default `Tab space newline`
		- Example: set IFS to `:` and iterate through path
		- Applies only to stuff not originally split, so for $ expressions
- 6. Pathname expansion ("globbing")
	- Take patterns and expand into sequence of file names
	- `*` match any sequence of characters
	- `?` match one character
	- `[ ]` match 1 charcater in a set
		- Set Negation: `[! ]`
	- Hidden files: `*` and `?` do not match hidden files starting with `.`
- 7. I/O redirection
	- `<` and `>` and `>>` append
	- `<>` open for both input and output
	- `-` in many commands means read from stdin
	- Done Left to right, and can overwrite redirects
	- Redirection only is valid: `>x`
	- Heredoc (creates temporary file and then inputs it)
 ```
<< EOF
contents
EOF
```
- EOF can be any word you want, and quote the delimiter word if you don't want $() interpreitation
- `2>&-` close file
- `2>&1` redirect from file string 2 to 1
- Shell tries to connect programs to I/O
# Files
- Working with file names with slashes
- Tree structure file system
	- Note: can have multiple pointers to the same leaf, so not really a tree
	- Use `ln source dest`  where source needs to exist and dest is the link file name
- File name components: sequence that contains no slashes or null bytes that sit inside directories
## Types of files
- Directories: can have files inside them
- Files: normal files
- Symbolic link
	- `ln -s source dest`
- Special file types
