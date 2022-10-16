Inheriting and Overriding Methods

- Overloading vs Overriding:
	- Overloading is using the same name but different parameters in a method header in the **same class**. Both methods exist at the same time.
	- Overriding is **completely replacing** a method when inheriting objects
- Super keyword: Use within a subclass to refer to superclass constructor, fields (if public), and methods (if public)
	- Super will not "skip" a level up one when using super, i.e. calling super from a lower class will call the middle class that overwrites the top class
	- super.super does not work - you can only call what the immediate superclass wants you to be able to call and cannot bypass it