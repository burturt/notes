- STL does NOT refer to the full default libraries, but mainly only containers, e.g. stack, queue, etc
- Goal: make things as uniform as needed
	- ex: always have a .size() function for any size
	- using pop() and back() to queue
## Vectors
```cpp
vector<int> vi;
vi.push_back(10);
vi.push_back(20);
vi.push_back(30);
cout << vi.size(); // 3
cout << vi.front(); // writes 10
cout << vi.back(); // 10
vi[1] = 40; // valid
vi[3] = 50; // undefined behavior
vi.at(1); // 40, throws exception if invalid idx
vector<double> vd(10);
// vd.size() is 10, each element is 0.0
vector<string> vs(10, "Hello");
// initialize each element to "Hello"
int a[5] = {10, 20, 30, 40, 50};
vector<int> vx(a, a+5);
// vx.size() is 5, filled with array
```
### Notes:
- pointers may become invalid ("invalidation") as reallocation occurs
- vectors generally use arrays in memory
## Lists
- Almost identical to vectors
- Via doubly-linked list
- Does NOT have a .at() or idx referencing
```cpp
// Only showing additional methiods compared to vector

#include <list>
using namespace;

list<int> li;
li.push_back(3);
li.push_front(5);
// size, front, back, pop_front, pop_back, etc exists

// Actually how to visit all items
for (list<int>::iterator p = li.begin(); p != li.end(); p++)
	cout << *p << endl;
// Note: end iterator is *just* past the last item. It does NOT point to the actual last item!
// Can use iterator ++, --, and ==. CANNOT do += 2 or similar
// if list is empty, li.begin() == li.end()
// If empty, do NOT try to use star operator!

// Convert to vector
// Aka give pointer to first value and just past the last item
vector<int> vi(li.begin(), li.end());


// Insert:
list<int>::iterator q = li.insert(p, 40);
// inserts a 40 before the iterator item pointed to by p
// returns iterator of added item

// Erase:
list<int>::iterator q = li.erase(p);
// erases item at iterator p
// returns iterator of item following p

// Vectors:
// Vectors have iterators as well! .begin() and .end
// Vector iterators CAN be added/subtracted from like += 2
// Vectors have insertion/erasing even though it is not efficient

```

## Notes:
- ANYTHING that changes the length of a container may cause all previous iterators to become invalid. Use the returned value or the iterators given by container methods instead.