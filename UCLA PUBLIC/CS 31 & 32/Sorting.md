## Selection sort
- Find largest item, swap this item with last item
- Now ignore last item and sort rest of items
	- Each step, looking through one fewer items
- O(N^2) always
## Bubble sort
- Loop through items, swap pairs of items if larger item is in front of smaller item
- Position of the second item last swap is known to start the sorted section, don't need to check this item and beyond in future loops
- Best case O(N) (mostly sorted)
- Worst case O(N^2)
- Average case O(N^2)
## Insertion sort
- Take items one at a time (look at first item, then first 2, then first 3) and place in proper sorted position in the already sorted set in the beginning
- Best case O(N) (mostly sorted)
- Worst case (N^2)
- Average case O(N^2)
- "Almost sorted" case: O(N)
## Merge sort
- If size greater than N, sort left and right half and combine by looping through left and right together
- Recursive algorithm
- Best, Worst, and Average: O(N log N)
## Quicksort
- Pick a value, move all values above that to the right and move values below to the left, and then sort both left and right side
	- One implementation to split: start on outside, and move inward. Find 2 numbers in wrong locations and swap, and keep going
	- Lastly swap the value left of end with the pivot 
- On case of 2: just comare directly
- On case of 1/0 just return
- Average O(N log N), worst case O(N^2)
	- DON'T use if mostly or already sorted/inverse/many repeated values!
- Generally quicksort performs better than merge sort except with worst case senario
### Quicksort optimizations
- Optimization: if 9 items or less, leave unsorted and then use insertion sort
- Optimization: monitor recursive depth: if 2 logN deep, switch to merge sort OR heap sort
- Above 2 optimizations together is **Introsort**
- Issue: worse case is *bad*: how to get pivot that's about even split?
	- Instead: take random sample and take median (or "middle item") of random sample as value chosen, usually 3 items, rather than any single item