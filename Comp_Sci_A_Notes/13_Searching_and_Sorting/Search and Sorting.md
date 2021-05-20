Search and Sorting

1. Audio
2. Text
3. Program files
4. Video
5. Images
6. Notes
7. Databases
8. Settings
9. OS files


Speed is important.
How many times a day do you do some sort of search? 
- Three seconds ….
- Ten seconds … .
When we decide as program designers which searching or sorting algorithms to use, we factor in:

1. The size of the data array
2. The “sortedness” of the data
3. The space efficiency of the algorithm (how much memory it uses)
4. Run-time efficiency (how fast it executes)

2 algorithms:
### Linear Sort:
- Looking for x, in a collection of N items

For each item in the collection:
- Is this x?
	- Yes -- Found it!  Stop looking
	- No -- keeping looking
- Takes max N comparisons



At most how many  comparisons?
### Binary Sort
Looking for x, in a collection of N items
- List must be sorted

Until you’re done (or you’ve run out of items):
- Divide the list in half & look at the middle item
- Is this x?
	- Yes -- Found it!  Stop looking
	- Middle higher than x? 
		- look in the bottom half
	- Middle lower than x?  
		- look in the top half
- Makes max log_2(n+1) guesses rounded down

## Bubble Sort
- 120 comparisons
- 222 Copies
- 16 items
- Algorithm:
	- Loop through all pairs starting at lowest index, greater value is placed at higher index in pair. Once at end, greatest value in set is at end, so repeat again but do not check last object
## Selection Sort
- 120 Comparisons
- 33 Copies
- Algorithm:
	- Loop through all values in list, remembering index of max. Once you reach the last value, move the max to this value, repeat on all values in list not including the last one to be moved
## Insertion Sort
- 79 comparison
- 97 copies
- Algorithm:
	- Note the indexes of a range starting and ending at the first object
	- Move the right bound right one
	- Look at new item added, move to temp, compare it to immediate left:
		- If larger, stop and place here
		- If smaller, swap and check again one left
## Merge Sort:
- 46 Comparisons
- 128 Copies
- Algorithm:
	- Start with size 2 and double every loop:
		- Check 1st of first half and 1st of second half. 
## Quick Sort
- Comparisons
- Copies
- Algorithm:
1. Pick an element, called a pivot, from the array. 
2. Reorder the array so that all elements with values less than the pivot come before the pivot, while all elements with values greater than the pivot come after it (equal values can go either way). After this partitioning, the pivot is in its final position. This is called the partition operation. 
3. Recursively apply the above steps to the sub-array of elements with smaller values and separately to the sub-array of elements with greater values.
