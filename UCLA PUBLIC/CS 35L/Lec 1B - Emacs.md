# Program generalization:
- Before: output = function of input
- Program: the code that's running
	- Static objects
- Process
	- Program + internal data
	- Input: Keyboard
	- output: Screen
	- Other I/O things too:
		- Files
		- Fetch/post resources from web
		- Interact with windowing system instead of just command line
		- Run another program, and be "in control"
- I/O can be to files, outside world, and one process to another
	- Pipes
	- Interprocess communication method (stream of data)
# Emacs cmdline
- Why not windowing system? Cloud servers use commandline due to distance from server and guis would be laggy/slow
- `char`: 8 bit
	- ASCII: 7 bit with front bit always 0
		- First 32 charcaters: control characters
			- 00: null (terminate strings)
			- C-a 0x1
			- C-c 0x3
			- C-x 0x18
		- 32/0x20: space
		- 0x7F control character
	- Control: "turn off the 00110000 bit"
	- Meta: "turn on the 01000000 bit"
	- enter key = ^M
	- Tab key = ^I
		- traditionally tab stop every 8 columns
	- C-sp and C-@ same (null byte)
	- Output char: `putchar(3)` or `putchar("\3")` in C
- M = "Meta"
	- MIT: want to type multiple characters, terminology 
- `emacs` - start
- Exit: C-x C-c
- Type a character:
	- Shows character on screen and remembers what you typed
	- Internal state changed:
		- Lisp (Emacs lisp/scripting language)
			- decisions, control other C code
			- Objects
		- C code (low level I/O, signals, CPU/GPU intensive, objects for where efficiency is important, etc)
			- Lisp interpreiter
			- Low level objects
				- You don't need object oriented progrmaming to have object oriented code
- M-x shell RET
	- Opens shell
- C-q: quote the next character
	- Insert the next control character literally
- C-z: return to the shell, suspend process
- C-x C-f open/create file
- Moving around:
	- C-a go to start of line
	- C-e go to end of line
	- M-< go to start of buffer
	- M-> go to end of buffer
- C-h help, C-h t tutorial
## Emacs Buffer
- Object that C code maintains
- contains a buffer: byte sequence. Unlike strings, easily editable
	- Cheap insertion
	- Implemented line an array: [part 1^gap, part 2] where ^ is the cursor location
	- Moves chunks of data from part 1 to 2 when cursor moves and waits until it thinks you are ready to insert
- point: where cursor is
- gap: where the buffer allows insertion
### other buffers
- C-x C-b: list buffers
	- type f to go to highlighted buffer
- C-x b go to buffer
- 2 buffer approach/display
- C-x 2: split into 2 buffers
- C-x 1: go back to 1 buffer
- C-x b
- C-x C-q: disable read only buffer mode
- C-x C-s: save
### marking
- C-x h select entire buffer
- C-space (C-@): set mark
- Space between set mark and current position is the "interesting region"
- M-w: copies region into temporary area
- C-x C-f filename: create or open file
- C-y: copy into current buffer
## Modes
- Emacs is a "modeful" editor
- Responds to commands depends on its mode, e.g f in buffers
- C-g interrupt
## Other commands
- C-x d: list directory
	- R: rename
- C-j: execute line as lisp
- C-u C-x =
- C-x 8 RET to search for unicode
- M-x shell RET to open shell