## Notes
- For STL functions, give pointers pointing to where the array/container starts and just past where it ends
- end iterator (pointing just past the end) is returned if a function that returns an iterator can't find a good end result
	- Check if end using EQUALITY, not greater than/less than comparison as it may be undefined!
## Using
### Find If
```
#include <algorithm>
// NOTE: You MUST include algorithm to use algorithm, and while it may be imported via iostream or whatever, not necessary
#include <string>

// Find if: find 
template <typename Iter, typename Func>
Iter find_if(Iter b, Iter e, Func f);

example function:
bool isNegative(int k) {
	return k < 0;
}

// use:
vector<int>::iterator p = find_if(vi.begin(), vi.end(), isNegative);
```
### Sort
```
// Return true if first item comes earlier, false if first item must come later or order doesn't matter
// Allows comparing arbitrary values, including like Employee.salary()
bool isGreater(int i, int j) {
	return i < j;
}

sort(vi.begin(), vi.end(), isGreater); // sorts greatest to least