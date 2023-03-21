- Hash function:
	- Produces uniformly distrubted values
	- cheap
	- deterministic
	- does NOT need to produce unique outputs
```cpp
#include <functional>
string s = "hello";

unsigned int x = std::hash<string>()(s); // constructs a hash object of template type string and calls the overloaded operator() function on it
```
- Hash map:
	- For a fixed number of buckets, operations are O(N) with a *tiny* constant of porportionality
	- Load factor: # of items / # of buckets
	- Avg steps to find item: 1 + L/2
	- For a fixed maximum load factor: average O(1) lookups
		- Non-incremental rehashing: O(1) average
		- Incremental rehashing: always O(1)
```cpp
#include <map> // requires < opreator
map<string, double> ious;
ious["bob"] += 5;
// If looping through using iterator .begin to .end, MUST be sort ordered

#include <unordered_map> // requires == operator and hashh function
```