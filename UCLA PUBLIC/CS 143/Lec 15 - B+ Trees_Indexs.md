- A B+ Tree builds a multilevel index as a multiway tree:
balanced, self-balancing n-ary tree
requires that all paths have the same length from root to leaf contains a root, internal nodes, and leaves.
the root is either a leaf, or has 2 or more children.
has high fanout (n) to reduce the number of block reads
represents one disk seek and block transfer at each level of the tree
- ![Screenshot 2024-03-14 at 7.00.07 PM.png](../../_resources/Screenshot%202024-03-14%20at%207.00.07%20PM.png)
	- Height/depth of 3
- n = number of possible pointers in nodes
	- n-1 possible values in node
	- Leaf node must contain at least (n-1)/2 (round up), not more than n-1
![Screenshot 2024-03-14 at 7.03.03 PM.png](../../_resources/Screenshot%202024-03-14%20at%207.03.03%20PM.png)
- Insertions and deletion require time
- Additional space overhead, as original ordered index + extra leaf and non-leaf nodes that form
- wasted space since nodes only need to be half full
- Time to rebuild ordered index with overflow blocks >>>> insertion/deletion/query of B_
- Each node is a disk block,
	- Every traverse is 1 seek + 1 block transfer
	- search for one: h * (t~s~ + t~t~)
	- if searching for range: may need to follow next pointers in leaves
		- Assume leaf nodes are ordered on disk (no seeks)
## Indexes in SQL
```sql
CREATE INDEX name_idx ON table_name USING (btree | hash | gist | spgist | gin | brin)
(
	attribute1 [ASC | DESC] [NULLS FIRST|LAST]
);
```
- speeds up lookups on these search keys (automatic on PK)
# Other indexs
- Hash index:
	- Good for random access
	- Good at equality queries, bad at range
	- get hash of key, then take mod # of buckets
		- Each bucket is linked list to records (RAM) or blocks (disk)
		- Roughly Record # / records per bucket * 1.2 = # of buckets
- Spacial data indexing:
	- create decision tree: check left/right to narrow search
	- k-d tree: boundary lines that are eliminated every time
	- quadtrees, octrees, ball trees (spheres instead of rectangles), and r-trees used for spatial querying