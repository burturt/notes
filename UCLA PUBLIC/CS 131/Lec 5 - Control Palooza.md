- Expression: combo that when evaluated resolves into a value
- Associativity: order of evaluation, usually left-to-right (except exponentiation and assignment)
- Iteration: iterator must have a .hasNext() and .next(), iterator is generated by iterable object
	- iterator can be a class, or using a generator
	- generator: closure that can `yield` and wait until told to "resume" using `next()`
	- Raise StopIteration for `__next__` python function
- Python:
	- for i in a:
		- Calls `__iter__` on a to get iterator, then calls `__next__` on each iteration and the returned value is set to i
	- If `yield` is added, automatically converts function into closure that returns, and each `yield` is value returned by calling `next()` or `__next__`