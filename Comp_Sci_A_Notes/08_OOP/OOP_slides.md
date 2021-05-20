OOP_slides

OOP slides

### Point class -- an example
```java
import java.awt.*;

Point p1 = new Point(7,2);
p1.x = 5;
|
|
v

public Point(int initialX, int initialY) {
	x = initialX;
	y = initialY;
}
```

### Add a method from the class
void translate(int dx, int dy)

p1.translate(2,2);
Point p3 = p1;
p3.y = 9;

## Part 2: Making your own objects

- Objects contain construtors, create objects with new string
- Start your constructor with the keyword public
- Follow with the class name and parameters just like regular methods
This is a design question—which parameters do you think should be autoinitialized? When does it make sense to proscribe an initial state?

Example objects:
```java
public class Student {
	private String name;
	private int gradeLevel;
	private double gpa;

	public Student(String n, int gl, double g){ 
	    name = n;
		gradeLevel = gl; 
		gpa = g;
	}
}

public class Dog {
	private String breed;
	private double weightInKg;

	public Dog(String b, double w){ 
		breed = b; 
		weightInKg = w;
	} 
	public Dog(double w){
		weightinKg = w;
	}
}

```
- Don't expect java to autoinitialize. 
- You will always need to write your own constructor to initialize your new objects.
- `private` means only methods within the method can access the instance variables

### "Object-Oriented"
- An object is a combination of data AND methods. The book refers to these as state (content, or data) and behavior (methods, or what is to be done with the data).
- The behavior can modify or report the data contained by the object.
- The book refers to the data as the “state” of the object
- The code that uses the objects is called **client code**. You’d never create a model of something (create an object) if you weren’t going to use it with other programs (client code).
- Class:
	- blueprint (or outline) that tells Java how to make a particular set of objects.
- Instance:
	- Each object is called an instance of that class.
	- The object myDog is an instance of the Dog class. So is the object teachersDog and sistersDog.
- Componenets: 
	- Fields: which outline what data (state) the object will hold
	- Methods: which determine the behavior of each object
	- Constructors: code that initializes each object as its being constructed with the new keyword
	- A class uses encapsulation to protect the object’s data from outside access (by the client code). You do this by making each field private.
- Instance methods ("non-static") are defined within a class blueprint.  They can only be called when attached to an object/instance of the class using dot notation.
- Static methods do not need to be attached to an object.  

## Part 3 More Objects
- `this` keyword:
	- Reference to object rather than other local variables
	- Avoid confusion about reference
- Encapsulation:
	- Prevent changing object fields
	- Apply rules to object fields
	- Maintain consistency to rules for object fields
- public vs private
	- In *general*:
		- All instance variables should be declared as private.  You will lose points on the AP test if they are not.
		- All getters, setters and constructors should be public
		- Other instance methods could be either
