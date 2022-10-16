HFJ: Abstract Classes

Interface: a 100% abstract class
Abstract class: A class that can't be instantiated. Use for when a class should not be instantiated
- i.e. an "Animal" class should not be instantiated itself
	- Compiler prevents this
- Make a class abstract by adding `abstract` before class delcaration in the header.
- A "non abstract" class is called a concrete class
- Abstract methods have no bodies
	- ALL classes with abstract methods must be abstract
	- You can mix abstract and non-abstract methods
- You can implement an abstract class
	- When implementing an abstract class, you can overwrite abstract methods. You MUST create methods defined in the abstract class if you implement it
	- `public class Dogs implements Animal {`

This helped me understand abstract classes more throughouly, and now I think I can properly use abtract classes in my own code to prevent weird instances of classes that are in an inheritance tree but should not be instantiated by themselves.