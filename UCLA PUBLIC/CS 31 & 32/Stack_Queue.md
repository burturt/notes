empty, size, front/back for queues or top for stacks, push, pop, swap
## Stacks
- ONLY add and remove from end
- Last in first out
- Operations:
	- Create empty stack
	- Push to end of stack
	- Pop from end of the stack
	- Look at top of the stack
	- Is the stack empty?
- Optional operations:
	- Look at any item in the stack
	- How many items in the stack

```cpp
#include <stack>
using namespace std;

stack<int> s; // Define stack s with type int
s.push(10);
s.push(20);

cout << s.top() << endl; // 20
s.pop(); // does not return anything in c++
if (s.empty())
	cout << "Stack is empty!" << endl;
cout << s.size() << endl; // 1

// Note: cpp library leaves pop/top on empty stack of library as undefined
```

## Queue
- First in first out
- 2 active ends:
	- Head/tail or front/back
	- Added to back
- Operations
	- Create an empty queue
	- Enqueue an item
	- Dequeue an item
	- Look at the front item in a queue
	- Is the queue empty?
- Optional operations:
	- Look at the back item
	- Look at any item
	- How many items in the queue
```cpp
#include <queue>
using namespace std;

queue<int> q;
q.push(10); // c++ for some reason calls enqueueing items "push" and removing items "pop"
q.push(20); 
cout << q.front() << endl; // 10
q.pop(); // returns void (again)
if (q.empty())
	cout << "Queue is empty!" << endl; 
cout << q.size() << endl; // writes 1
cout << q.back() << endl; // writes 20
```
## Implementation:
- Circular buffer:
	- If length 100, let position after 99 be 0
	- Note: if using pointers to keep track of start/end, be careful when empty/full!
