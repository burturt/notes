## Scripting vs functions (lisp/python)
- Pros: integration, ease of use, less hassle/easier pacaging, less overhead, more efficient
- Cons: less flexibility/less modular, and lose command line integration
## Lisp
- Super old, "root language", many languages take inspiration from it
- Used as AI language, original slow but now more practical
- Now developed many variants:
	- Lisp 1, 1.5 (1950-60s)
	- Scheme (1970s)
	- emacs lisp (1970s)
- Lisp is an extension language: give flexibility to tailor application by making it programmable
	- Object oriented system: members, strings, but also buffers, windows, frames, processes
- Number sizes:
	- fixnums: (small integers)
	- bignums (large ints)
	- floats (decimals)
	- Differentiating: have a "tag bit" in both the pointer to variable or the variable int itself
		- fixnums don't use pointers
		- 3 bit tag field for float in pointer, but bignum has a tag in variable data itself 
		- Tag bit put in the last 3 bits: bottom 3 bits are already 0 because it's already taking 8 bits
		- Connection to UTF 8
## Emacs Lisp
- In scratch buffer: `C-j` to 
- commands: `(command arg1 arg2)`
	- `(eq 1 2)`
		- emacs uses `t`/`nil` for true/false
		- compares pointers: so if 2 identical floats or bignums, will still be nil
	- `(equal arg1 arg2)`
		- does value equality, not pointer equality
- Char/characters
	- `?a` is the character a
	- In lisp, chars are actually just ints
- cons: fundamental intermediate structure
	- lisp = list processing  
	- cons/pair: simple compound data structure 
	- `(cons item1 item2)` makes pair of 2 items
	- `nil`: also represents empty list
	- `(car x)` get first item in pair
	- `(cdr x)` get second item in pair
- List
	- Lists: first element is item, second element is pointer to next one
	- use `(list item1 item2 item3 ...)`
- Hashtable: keys can be anything
- Char table: hash table but for keys that are characters
	- used by e.g. emacs to remember key combos
- Markers: pointer into a buffer that moves along with the content
- Buffer
- Frame/window: specifies rectangular screen, window is subset of frame
- Terminal: different terminals can view same windows
- Process: type of object
- function:
	- `(lambda (x) (+ x 1))` results in a function
	- lambda: nameless functions
	- Can define with name using `(defun myFunction (param1 param2) "doc_string" body)`
- Special forms:
	- (keyword a b) where it looks like calling a function but isn't
	- e.g. `(if (= a b) (cons a b) (cdr b))`
	- Note: first is condition, second is then part (must be one), third and all after can be anything
	- Can use `(when (condition) (then command))` to run multiple thens
	- Can use `(unless (condition) (else commands))`
	- Can use `(progn (cmd1) (cmd2))` to group several expressions into one; returns values of last block
- Cond:
	- `(cond ((= a b) a) ((< a b) b) ((> a b) a))` multiple ifs
- `not` negation, converts to t or nil
- `and`: short circuit and evaluation
- `or`
### Variables
- `(setq varname varval)`
- Can use `setq` on local variables in lambda functions
- also `(let ((var val) (var2 val2)))`

### Format
- Emacs programs are represented as lists
	- Close debugger by `C-]`
- put `'` before to quote with only one at the beginning
	- Rule: put in front of expression means don't evaluate this, keep as data