


- cstring: array of chars
	- can ONLY use double quote assignment at var declaration
	- pass by reference automatically when putting `char myString[]` as function argument
		- Equivalent fo `char* myString`
- Basic helper functions in cstring library:
	- strcpy(dest, src), copies source to dest
	- strncpy(dest, src, num), copies num chars from src to dest
	- strcat(dest, src), copies src string and adds to end of dest
	- strncat(dest, src, num), copies num chars from src to end of dest
	- strchr(str, chr), NULL if chr no in str, otherwise returns memory address
	- strlen(str), length of str
	- strcmp(str1, str2), 0 if equals, nonzero if not
- cctype library: char helper functions
	- isalpha(c), isdigit(c), isalnum(c), isspace(c), islower(c), isupper(c), isblank(c), isxdigit(x) (hex digit), ispunct(c), isprint(c) (is printable), iscntrl(c) (not printable), etc
	- toupper(c) and tolower(c)