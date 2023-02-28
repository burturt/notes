# 2
1) 
```
DC
DC
DC
AO
CC
AO
CC
DONE
Destructor!
Destructor!
Destructor!
Destructor!
Destructor!
```
2)
- offshore = new Account()
- destructor
- initialize account in initialization list as account(10000)
- semicolon end of class
3)
Construct me with 9
Wow such 19
Construct me with 9
Wow such 19
Copy me
There's another me???
Wow such 9
Where are we?
Twas a good life
Goodbye cruel world
Twas a good life
Goodbye cruel world
Twas a good life
Goodbye cruel world
Twas a good life
4)
```cpp
A(int sz) {
	b = new B;
	arr = new int[sz];
	n = sz;
}
A(const A& other) {
	if (this != &other) {
		b = new B(other.b);
		
	}

}
A& operator=(const A& other) {
	
}