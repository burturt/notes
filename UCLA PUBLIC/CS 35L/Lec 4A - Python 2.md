# Python
- Can use as extension language (run as part of larger application), but usually with python "in charge"
```python3
types = [str, int, float]
fields = [t(v) for t,v in zip(types, line.split(","))]
```
- Python as "glue" language: glue types together
### Motvation/history
- Motivated by CS education
- Replace BASIC (which was used also as an education language)
	- FORTRAN: too complicated, so make something simpler, and BASIC became more complex, started being a pain to teach (e.g. indent)
- ABC: replace issues with indents and programming issues
	- Made indenting part of language to force indenting
	- Came with IDE
	- Lots of methods like array.sort() and dictionaries
	- Technique: little languages methods
- Perl: first successful little language unification
	- Multiple ways to do it
- Python: negative reaction to perl and predecessor to ABC:
	- There is a good way to do things and python has "it"
	- One language but with more discipline 
## OOP
- Every entity is an object that has:
	- Identity (like c++ address)
		- `id(obj)` returns int
	- Type
		- `type(obj)` returns object describing type
	- Value
- Identity and type is immutable, value possibly mutable
## Builtin types
- `None` (NoneType)
- Numbers:
	- int
		- unlimited magnitude
	- float
	- complex
		- uses j for sqrt(-1)
	- boolean
- Sequence (int index)
	- List (array)
	- Tuple (immutable array)
	- Buffer
	- String (immutable buffer)
- Mappings (any type index)
	- dict
- Callable
	- function
	- method (within a class)
	- class
- internal
	- Code
	- traceback
## Sequences and Mappings
- Sequences:
	- Array from 0 to n-1
	- s[i] to get value
	- using negative numbers to access from end
		- s[-1] = last item, s[-len(s)] = s[0]
	- s[i:j]: get values i up to and not including j, length j-i
		- i defaults 0, j defaults length
	- min(s), max(s)
	- list(s) - makes new list that is a copy, even if s isn't a sequence
- Mutable sequence:
	- use bracket to change characters in strings
	- `s[i:j] = t` replace elements i through up to j with elements of t
		- Can change length
	- del s[i] (deletes ith element and shifts everything over)
	- del s[i:j]
- List
	- l.append(v)
		- add v to end
		- Very fast
	- Python lists are not linked lists: implemented with dynamically allocated array (e.g. vector)
	- l.extend(s) (appends elements of list s)
	- l.count(x) (count appearances of x)
	- l.index(y) (index of first returned value)
	- l.insert(i, v)
		- typically O(len(s) - i), worst O(len(s))
	- l.pop(i) pop at index, or just l.pop() (same as l.pop(-1))
		- -1 is O(1)
	- l.remove(r) (remove first instance of element r)
	- l.reverse()
	- l.sort()
	- max(l)
- String (and immutable)
	- s.join(t)
		- ','.join(['ab', 'cd']) == 'ab,cd'
	- s.split(sep)
	- s.replace(old, new)
		- returns new copy instead of in place
		- Replaces all instances of old with new
- Dictionaries
	- d = {"abc": 1, "xy": 27}
	- d[k] get element at k
	- KeyError if absent 
	- d.get(k [, v]), get at key k, return optional v if key not found
	- d.has_key(k)
	- del d[k]
	- len(d) = number of items
	- d.clear()
	- d.keys() list of kes
	- d.values() list of values
	- d.items(): returns immutable list of tuple pairs, that update
	- d.update(e) Update dictionary d with key-value pairs from dictionary e
	- d.popitem() remove last item added (pre 3.7 random item)
## Functions
```python
def f(a, b, *c, **d):
	# c = [21, 24]
	# d = {"de": 19, "gh": 27}
	len(c)
	return

f(b=27, a=3, 21, 24, "abc", de=19, gh=27)

class c(a, b): # parent classs a and b
		def method(self, x):
```
Python `__dict__`: dictionary on all objects that maps names associated with class with values
- e.g. `c.__dict__['n'] = method`
- Python class definitions are dynamic and can be changed
- `__init__(self, x, y)`: "constructor"
- `__str__(self)` change how convert to string
- `__cmp__(self, other)`
	- -1 if <, 0 if =, 1 if >
- `__hash__(self)`: return int and representative of content
- `__lt__(self, other)` for less than operator return bool
- `__nonzero__(self)` return true/false for `if obj:`
- `__add__(self, other)` add together and return new object