1. Parse SQL
	- Convert SQL to relational algebra representation
2. Optimize, query-optimizing transformations
3. Evaluation, using stats to be efficient
	- Optimize using:
		- Block transfers + seeks estimated
		- # of tuples
		- current CPU/RAM state (postgres)
		- Data transfer speed
		- Remaining disk space
		- network transfer speed
		- time

![Screenshot 2024-03-14 at 8.55.14 PM.png](../../_resources/Screenshot%202024-03-14%20at%208.55.14%20PM.png)
## Select
- SELECT * from r WHERE p
- b~r~ = num blocks in file
- t~t~ = time to transfer one block
- t~s~ = seek time
- To read full file: t~s~ + b~r~ * t~t~ (even if we search on a key if contiguous)
- To find one record by PK in B+ tree
	- h(t~s~ + t~t~) to traverse B+ tree
	- 0 to search for record pointer in block
	- t~s~ + t~t~ to retrieve record
## Join
- nested-loop join: O(N^2^), try every element in A against B
	- No index required, no join condition restriction
	- VERY slow
- block nested-loop: loop through blocks instead of records to lower seeks/block transfers
- Merge join: sort both relations, then check against using 2 pointers
- Hash join
## Explain
EXPLAIN ANALYZE SELECT ...
- Show details of query plan
# Spark Join Algorithms
- Broadcast based joins (hash + nested loop)
	- Broadcast hash join:
		- smaller table is broadcasted to every executor if can fit in RAM (default 10MB)
		- Divide up large table into partitions
		- Cannot do outer join (can't determine which rows in smaller table was used)
	- Broadcast nested loop:
		- process join locally after broadcasting R 
		- Least performant
- Shuffle based joins (hash/sort-merge)
	- each row of R and S are shuffled across network
	- All rows with same value on join key ends up in same partition
	- Shuffle hash join: doesn't require keys to be sortable, but building hash table uses more RAM, time, only equi, no outer join, and shuffling very expensive
- Choose join by size of tables, join condition, confi values, hints
![Screenshot 2024-03-17 at 3.54.24 PM.png](../../_resources/Screenshot%202024-03-17%20at%203.54.24%20PM.png)

