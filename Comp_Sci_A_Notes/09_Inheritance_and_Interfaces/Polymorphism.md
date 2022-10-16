Polymorphism

```md
Doctor <---- FamilyDoctor
  ^
  |
Surgeon
```


- subclass “extends” superclass
	- Subclass is-a superclass
	- superclass:     More general, less capability
	- subclass:     More specific, more capability

- When a method is overridden in a subclass, and that method is invoked on an instance of the subclass, the overridden version of the method is called.  (The lowest one wins.)

- When you define a supertype for a group of classes, any subclass of that supertype can be substituted where the supertype is expected.
	- Why?
		- Refer to subclass object using reference declared as a super type = ability to write flexible code, easier to develop and extend later
```java
Dog myDog = new Dog();
      1   3    2
```
1. Declare reference variable (myDog).  
2. Create an object (a Dog object).
3. Link the object and the reference.
- Reference type and object type are the same.

- Animal myDog = new Dog();
	- Reference and object type are different...
```java
// Example of why polymorphism
Animal[] animals = new Animal[3];
animals[0] = new Dog();
animals[1] = new Cat();
animals[2] = new Hippo();
for (int i =0; i<animals.length; i++){
    animals[i].eat();
    animals[i].roam();
}
```