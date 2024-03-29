## Problems to solve:
- Persistence (files)
- Get work done (shell/emacs) (operartions)
- write programs/develop software (emacs) (development)
# shell
- little language: language to run other programs
	- Configuration language
	- Issue: often mixing multiple languages together, causing overlapping confusion
- Instead of one language to do everything, have ASL (application specific languages)
	- Design languages to deal with that application
- simple language in shell: regular expressions
	- Used in grep, etc
	- Grep: standard text searching, looking for patterns in text files
	- `grep pattern files`
		- searches files for any line that contains something that matches the pattern
	- patterns: `#include *<stdio.h>`
		- **lookup: 3 kinds of regex**
		- star: repeat 0 or more times
		- up carrot ^: require character AFTER at start of line
		- dollar sign: require character before end of line
		- [abc] any character in brackets
			- [a-zA-Z0-9] 
			- ^ inside set: not operator
				- if you want to actually match, just don't put it first
				- NOTE: special characters inside and outside not the same set! Be careful
			- Order doesn't matter
			- Putting bracket in bracket expression: put it first `[][{}()]`
			- Minus sign escaping: put it last or first `[^-az]`
			- `[[:alpha:]]`, `[[:digits:]]`
				- with open bracket, colon + close bracket
				- Stuff between colon defines a character class
				- Same as what is in `<ctype.h>`
				- standard grep, no ascii, emacs has ascii
					- in grep, would need `[null - dell]`
		- `.` any single character
		- `\` escape special characters
			- standard: undefined if escaping regular characters
		- `grep 'a\(bcd\)*e'`
			- Need backslahes to make parenthesis work in grep
				- use `-E` to "extend" grep and have `()` not be literal characters by default
				- ERE (extended regular expressions)
					- Slightly slower but more extensive. Don't always agree
					- Also has `a(bc|de)f`: matches abcf or adef
					- Also has `?`: matches zero or one time
					- `+` is one or more times
			- matches ae, abcde, abcdbcde, etc
		- Limit number of matches: `{min, max}` (inclusive)
			- leave max out to mean unbounded
	- `\1`: match exactly the same that the first group
	- `grep -P` use perl regex
## Other Regex tools
- Perl, JS, Python, etc have their own regex flavors
- sed (stream editor): `s/ab*c/de/`: reads standard input, and runs regex replace `ab*c` with dc
	- `s/\[/(\; s\]\)/` replace open bracket/parenthesis with close
- `awk` (sed ++)
- Perl and python extensions of regex
	- "pick size of little language to fit the size of problem"
# Unicode
- Wide set of charcaters, represents thousands of characters
- Byte has 8 bits, ascii uses 7 bits.
	- Original made multiple standards (ISO 8859-x)
		- ASCII+128 other charcaters
		- x showed which set of extra characters you wanted, e.g. 1 + 15 (15 added euro symbol) western europe
		- issue: can't fit always, and need to know which character set to use
- Unicode: 0 to 2^20 something, large character sets
	- "Lets make our characters wider!"
	- C: C unsigned short: 16 bits
	- Issue: redo all text files (don't know how to read, 8 bit ascii or 16 bit unicode approach)
	- Issue: 16 bits isn't acutally enough, so they used 32 bits
		- 32 bit efficiency downside: takes 4x space
	- Issue: "uni" as there are different visual representations of characters
- Solution: UTF-8
	- want encoding upwards compatible with ascii, space efficient, and unambiguous character boundaries (if you start middle of character byte, you know where next character starts)
		- encode into byte string, decode to character string
- 3 kinds of bytes:
	- `0xxxxxxx` = ascii character
	- `10xxxxxx` = always represents 2nd or later byte in multibyte character
	- `1110xxxx` (1 followed by some number of 1s followed by 0)
		- Number of ones is the count of bytes in this character 
		- Max 4 for UTF-8
- Convert e.g. U+03AF
	- binary without leading zero: 	1110101111
	- divide into groups of 6, starting at end: 1110 101111
	- place into bytes starting at end: `11001110 10101111`
- Issues:
	- Invalid bytes exist: what to do with invalid bytes? e.g. `111111100`
	- End of file before multibyte char ends: partial character
	- Byte that should be part of multibyte character leads with a 0: encoding error
		- standalone `10xxxxxx` byte
	- Multibyte character that represents an ascii character aka multiple representations for the same character (2 bytes and 1 byte)
		- aka overlong encoding, can be used to bypass defenses