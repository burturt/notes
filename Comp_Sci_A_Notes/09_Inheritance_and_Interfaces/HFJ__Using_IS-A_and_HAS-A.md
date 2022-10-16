HFJ: Using IS-A and HAS-A

- Is-a: a test for when something should extend another thing
	- A triangle is a shape
	- A lion is an animal
	- A tub is not a bathroom
	- the is-a test MUST apply between any 2 classes in an inheritance tree!
		- A lion is a feline and a feline is an animal, so a lion is an animal. This logic must work for all objects in the tree.
	- This relationship only works in one direction
- Has-a: a test for when something should have a field/instance variable that may be an object
	- A bathroom has a tub
	- A computer has a cpu
	- A triangle does not have a shape

When to use inheritance:
- When one subclass is a specific type of a superclass
- When behavior should be shared among multiple classes (possibly use interfaces as well)
- Do NOT use inheritance just to reuse code.
- Do NOT use if it does not pass the is-a test

I find useful the review of when to and when not to use inheritance for my own code.