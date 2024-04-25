- Mostly focus on relational database management systems (RDBMS)
## Purpose
- Abstracts away how data is stored, maintained, and processed
- stores and indexes data using advanced data structures
	- B+ tree, hash index
- CRUD operations: create, read, update, delete ops w/o worrying about files/integrity
- Provide one single location even if distributed
- Big data: too much data of wrong type = bad
### Forms
- Blockchain is similar to a distributed database, with one major difference
	- note: w/ centralized databases, admins have power, blockchain, each participant has data and has a consensus mechanist
		- "verifiable data structure"
- Git: "stupid content tracker"
	- Content-addressable filesystem, key-value store
- Both use merkle trees
### Three Layers of Abstraction
1. Physical
	- How it works under the hood
2. Logical
	- abstracting a structured type
	- Has data and links to other data
3. View
	- Filter to certain columns for certain use cases
### Schema
- Schema: design of db
	- Structure of the data, organization, auth, etc
- Data Model: define databases or interactions
## Types of Data Models
- 2 types:
	- Implementation (hard-coded)
	- Design Mechanism (ways to store code)
- Relational
	- Rows represent tuples
	- Relation is a set, unordered (table is ordered)
- Entity-Relationship (ER)
	- Ch 6 in text
	- Treats objects as entities and has relationships to **attributes**
	- **Included on final**
	- Relate objects: 1-to-1, 1-to-many, many-to-many
		- Relationships are fixed and defined
- Object Oriented
	- Originally was an implementation, but now design mechanist
	- Data is considered instance of class
	- CS 241A covers
	- Used by ORMs (object relational mappers)
- Object-relational:
	- Postgres allows creating own types and using them, making it object relational
- Document (semi-structured)
	- Each document can be anything (e.g. library items have title and author, but could have publishers, issues, musical instruments, etc)
	- JSON (js), XML, usually called NoSQL
	- MongoDB, Firebase, Dynamo, etc
- Network/Hierarchical/Graph
	- Data records are nodes, and relationships between records as edges, both have labels
	- neo4j, orientdb, neptune
	- How people think
- Vector (Time Series)
	- Lot of hype
	- Generally store data as vector of numbers
	- runs nearest neighbors (kd-tree), find vectors close to each other
	- Import for LLM
	- ElasticSearch
- Key-Value
	- Stored just as key/value pairs
	- Hash tables!
	- Typically stored in memory buffer
	- Redis, Memcached
- Hybrid/multi-model
	- ArrangoDB
## Database Languages
- Data Definition Language (DDL)
- Data Manipulation Language (DML)
	- Both SQL
	- JS can also used like
### DML
- Procedural (C++, C, Python, etc)
	- Specify what data and how to get it
- Declarative (Prolog, SQL, Kubernetes)
	- Specifies data, but not how to get it
- Query is a written expression to retrieve data
	- Cannot do easliy:
		- Sequential/iterative computations
		- anything that specifies how to perform
- Instead: write ETL job
	- Extract, transform, load
### DDL
- Specifies schema
	- Domain constraints (tiny int, enum)
	- Referential integrity (e.g. if instructor name listed, should exist in instructors)
	- Assertions (business rules: cap on class, prereq)
	- Authorization (permissions)
## Other parts
- Storage manager: keeping on disk
	- Too much to keep in RAM
	- Must be done efficiently
	- hashmap, trie (e.g. store phone numbers), 
- Query manager
	- optimize queries
# Intro to Relational model
- Collection of relations = table
- tuple = row/record
- attribute = column/field
- each record has attributes
	- Each tuple contains exact same attributes in the exact same order
	- unique name, particular data type with a domain, null is special
- **Superkey**: set of attributes that uniquely identifies a tuple
### Identifying keys:
- Instance: find the keys based on *only* the rows that are given to you
- Context: use the context of the problem
#### Algorithm
- Compute all subsets of attributes. Let n = 1
- Test each subset s containing n attributes, if s ia s uperkey, retain it, and all supersets of s
- repeat n = n + 1, keep going until all subsets have been checked
#### Notes
- At most 2^n-1 superkeys
- superkey + any other attribute = another (trivial) superkey
### Candidate key
- minimal superkey: no subset of its attributes form a superkey itself
	- Candidate keys can have null, but the null must uniquely identify a tuple (e.g. treat NULL as just another value)
- Primary key: single candidate key chosen to enforce uniqueness
	- Usually dev controls or maintains, usually shorter candidate keys
	- MUST be unique, NOT NULL
### Foreign Key
- Set of attributes that link 2 attributes together
- Foreign key: is a set of attributes of R that is the primary key of some other relation R'
	- Foreign key does not uniquely identify a tuple in R, but does in R'
	- e.g. video_id in youtube_comment is a foreign key to youtube_video in youtube_video
- Referential integrity
	- Dealing with deletion: either error (restrict) or delete referring elements (cascade)
- Foreign keys **can** be null
# Relational Algebra
- Operations: CRUD (create, read, update, delete)
![Screenshot 2024-01-17 at 9.47.27 PM.png](../../_resources/Screenshot%202024-01-17%20at%209.47.27%20PM.png)
- filtering tuples --> selection
	- only filter tuples! Same tuple length input/output
- Extract subsets of attributes --> projection
- Forming all possible pairs --> cartesian product
- Merge tuples together --> join
## Selection
![Screenshot 2024-01-21 at 10.55.24 AM.png](../../_resources/Screenshot%202024-01-21%20at%2010.55.24%20AM.png)
![Screenshot 2024-01-17 at 9.49.03 PM.png](../../_resources/Screenshot%202024-01-17%20at%209.49.03%20PM.png)
![Screenshot 2024-01-17 at 9.48.59 PM.png](../../_resources/Screenshot%202024-01-17%20at%209.48.59%20PM.png)
- USE ONLY LISTED HERE
- WHERE class with SELECT *
## Projection
![Screenshot 2024-01-21 at 10.55.51 AM.png](../../_resources/Screenshot%202024-01-21%20at%2010.55.51%20AM.png)
![Screenshot 2024-01-19 at 6.52.28 PM.png](../../_resources/Screenshot%202024-01-19%20at%206.52.28%20PM.png)
- Removes duplicates
- projection is equal to SELECT DISTINCT
## Cartesian Product
![Screenshot 2024-01-21 at 10.57.10 AM.png](../../_resources/Screenshot%202024-01-21%20at%2010.57.10%20AM.png)
## Joins
- equijoin: join that uses equality
- Default join: remove non-matched rows
- Join key: attribute used to join
	- Has NOTHING to do with enforcing uniqueness
### Natural Join
- Just ⨝
- RDBMS automatically picks keys to join on
- ![Screenshot 2024-01-21 at 10.58.17 AM.png](../../_resources/Screenshot%202024-01-21%20at%2010.58.17%20AM.png)
- Natural join w/ no overlapping attributes: ends up as cartesian product
- Equijoin
- Can be inner or outer
### Theta Join
- Select own join keys/expression
- Cartesian product then filter on expression
- By default, contains all elements of both relations even if equated (e.g. copies of join key)
- ![Screenshot 2024-01-21 at 10.59.28 AM.png](../../_resources/Screenshot%202024-01-21%20at%2010.59.28%20AM.png)
## Aggregation
- Extension to linear algebra
![Screenshot 2024-01-25 at 1.54.08 PM.png](../../_resources/Screenshot%202024-01-25%20at%201.54.08%20PM.png)


## Rename
![Screenshot 2024-01-25 at 1.56.11 PM.png](../../_resources/Screenshot%202024-01-25%20at%201.56.11%20PM.png)
- Use to rename attributes for unions
## Others
![Screenshot 2024-01-27 at 2.06.46 PM.png](../../_resources/Screenshot%202024-01-27%20at%202.06.46%20PM.png)

# Schema
## SQL Data Types
- DDL/Data Definition Language
	- Schema, types of attributes, integrity contraints (pk, fk, uk, check contraint), indices, security and auth info, physical storage
### Numbers
- int(eger)
- smallint
- numeric(p, d), p digits, d after decimal
- real, double precision
- float(n), precision of at least n digits
### Strings
- char(n) (char array len n)
- varchar(n) (max length n, variable length)
### Dates
- date
- time
- timestamp
- TZ are implementation-specific
### NULL
- unspecified or missing
- postgres represented either as blank or null, MySQL NULL
## PostgreSQL types
- smallint (2 bytes)
- integer/int (4 bytes)
- bigint (8 bytes)
- decimal(p, d) p precision digits total, d number of decimal digits
- NaN: NaN == NaN and NaN > all non-NaN and non-infinity values
	- Operations on NaN result in NaN
- text: unbounded text length, indexable
- real
- double
- char(n) - n is actual char count, even if multi byte
- bytea: blob
- boolean (very liberal, case intensitive)
- bit(n) or bit varying(n): bitstring, e.g. `B'101'`
- timestamp, **timestamptz**, date, time, timetz, interval
	- `CURRENT_{DATE|TIME|TIMESTAMP}, LOCAL{TIME|TIMESTAMP}`
- enum
	- `CREATE TYPE mood AS ENUM ('sad', 'happy')`
- Space
	- see if point is in polygon
	- see if polygons overlap
	- see where point is on line
	- find points in radius
	- point, line, lseg, box, path, polygon, circle
- JSON, XML
## SQL Queries!
```sql
CREATE TABLE IF NOT EXIST youtube_video (
	-- this is a comment
	video_id character(11),
	title varchar(100) NOT NULL,
	post_date timestamptz NOT NULL DEFAULT CURRENT_TIMESTAMP,
	PRIMARY KEY (video_id),
	FOREIGN KEY (channel) REFERENCES youtube_channel(channel_id) ON DELETE CASCADE ON UPDATE CASCADE;
);
-- If no cascade, will error out instead
-- Can use RESTRICT (error), CASCADE (delete/update others), SET NULL
-- SET NULL: on update will set null
```
# Basic Queries
![Screenshot 2024-02-01 at 11.21.34 PM.png](../../_resources/Screenshot%202024-02-01%20at%2011.21.34%20PM.png)
- database: physical partitions, cannot be joined across
- schema: collection of tables/relations
	- public is the default schema
## Changing a Table's Schema
1. Promote, never delete
2. Don't delete columns - just abandon and allow nulls
3. Never add columns, instead create json extra col or create another table w/ join key
- Change via ALTER TABLE
```sql
ALTER TABLE youtube_video
	DROP cat_id
	ALTER COLUMN video_id TYPE char(12) USING UPPER(video_id)
	-- add 12th charfr om 11, convert to uppercase
	ADD flagged bit,
	ADD rowno int,
	DROP CONSTRAINT youtube_video_pkey
	-- name of constraint relationname_pkey
	-- name of constraint relationname_attribute_fkey
	ADD PRIMARY KEY video_id;
ALTER TABLE youtube_video RENAME COLUMN video_id TO id;
ALTER TABLE youtube_video RENAME TO youtube_video;
```
## Drop stuff
```sql
DROP TABLE youtube_videos; DROP TABLE IF EXISTS;
DROP SCHEMA schema;
DROP DATABASE db;
TRUNCATE youtube_videos; -- clear all data out
```
# SELECT
- SELECT FROM JOINs WHERE GROUP BY HAVING ORDER BY LIMIT OFFSET
- always ephemeral unless `SELECT INFO`
- `SELECT col1, col2 as new_col2, FROM r WHERE conditions;`
- Can do arithmetic/rename/cast
```sql
SELECT video_id, title,
	(views / 1000)::float as thousand_views,
	(likes / (likes + dislikes))::float AS pct_liked
	FROM youtube_video;
```
## Where clause
- normal, `!=`, `IN`, `NOT IN`
- `= 'NaN'`, `IS NULL`, CANNOT equal null
- `views BETWEEN 500 AND 1000;`
- Acts on values in columns, and transformation function applied on rows, NOT aggregate
## ORDER BY
- `ORDER BY gpa DESC, last, first;`: sort by last names first, then first if ties
	- `NULLS FIRST` or `NULLS LAST` at end of order statement
- Can add `LIMIT 3;`, can use OFFSET but bad performance
## Aggregation
- Default: if no group by, aggregates over everything
	- One row per unique value in group by statemenet
- If you group by, put aggregation function last in select _
- If using aggregation or group by, every column must be aggregation function or part of group by
## Filtering on aggregation
- MUST USE `HAVING` to filter on aggregations
- `GROUP BY MAJOR HAVING AVG(gpa) < 3.95;`
- Cannot use alias
## DB execution order
- FROM ON JOIN (read data)
	- Can create table aliases
- WHERE GROUP BY HAVING SELECT DICTINCT ORDER BY
# Joins
## Types of joins
1. Handle rows in R not matching S:
	- ignore: inner join. Keep some? outer + null
2. Operator: `=` is equi, anything else is non-equi
3. Join table with self: selfjoin
	- Data for graph, tree, network, DAG
	- Compute X product, determine theta, determine if cycle is good (A --> B --> A) and filter
5. natural join
6. joins with no/incomplete join conditions
- Inner join: return only when in both
```sql
SELECT instructor_name, course_name
FROM instructor
INNER JOIN course -- inner optional
ON instructor.ID = course.ID
INNER JOIN course_offering
ON course.course = course_offering.course;
```
- Can alias: `FROM instructor r` or `FROM instructor AS r`
- Can use table aliases everywhere
- Can join on attributes no in select
- `CROSS JOIN` does exist for cartesian product
	- `LEFT OUTER JOIN`, `RIGHT OUTER JOIN`, `FULL JOIN`
- `COALESCE(expression, replacement)`, `NULLIF(1, 2)`  if 1=2, return Null, otherwise return 1
## Subquery
```sql
SELECT * FROM (SELECT * FROM t) sq JOIN t ON t.one = sq.two;
```
- Usually same, but in big data, can be faster than join
- Must have a derivated table name, can use `_`, use in JOIN or FROM 
- Can also have subquery resolve to a **scalar** (one value), and doesn't need a alias
### IN
```sql
SELECT flights.* FROM flights WHERE (flights.tail, _) IN (
	SELECT tail FROM airtran_aircraft, _
);
```
# Data manipulation
```sql
INSERT INTO relation VALUES ('val11', 'val12'), ('va21', 'val22'); -- must follow schema and order
INSERT INTO relation (col1name, col2name) VALUES ('val11', 'val12'), ('va21', 'val22');
UPDATE relation SET column = new_value WHERE condition;
```

# Constraints
- In schema (check constraint):
```sql
CONSTRAINT Valid_Enrolldate CHECK (enrolldate > birthdate),
```
- Pros: data integrity, set syntax, no trust app dev, changes in app doesn't break integrity
- Cons: Limited in functionality/less flexible, more cpu load, harder to parse/handle errors, not portable across db migrations
- Business logic: in app, data integrity, in db
# Control Flow
```sql
CASE level
	WHEN 'URF' THEN 'Freshmen'
	WHEN 'USO' THEN 'Sohpomore'
	ELSE 'N/A'
END AS new_level

CASE -- comparison is by enum
	WHEN level <= 'USR' THEN 'Undergrade'
	WHEN level <= 'GD2' THEN 'Graduate'
	ELSE 'N/A'
END AS new_career
```
# Accessing
## Connecting
- Open connection w/ driver
- Construct query
- Execute query with cursor
- Iterate over result set
	- fetchone() or fetchall() for records (maintained on db server)
	- Cursor: don't use to page b/c connection must stay open and online
- Close connection
	- Must also close cursor
```python
import psycopg2
conn = psycopg2.connect(user="some_user", password="p2ssw40d", host="localhost", port="5432", database="my_db")
connection.autocommit = True # queries only run when cur.commit, in a transaction
with connection.cursor() as cur:
	cur.execute("""
	SELECT uid, last, first FROM bruinbase
	WHERE major=%(major_input)s;
	""". {
		'major_input': request_args['major']
	})
	rows = cur.fetchall()
	for result in rows:
		uid, last, first = result
connection.close()
```
# Normalization
- Why normalize?
	- UPDATE/DELETE anomolies
- Functional Dependency: X -> Y if and only if each value of X is associated with exactly one value of Y
	- "uniquely determines, defines, maps to, identifys"
### Functional Dependencies
A -> A, AB -> A (reflexive/trivial)
A -> B, then AY -> BY (augmentation)
A -> B, B -> C (transitivity)
A -> B and X -> Y, then AX -> BY (composition)
A -> BY, then A -> B and A -> Y (decomposition)
A -> B, BC -> Y, then BA -> Y (pseudotransitivty)
### Reducing (canonical cover)
- Include trivial CK relations!
- Decompose the right hand side: A->BC becomes A->B and A->C
- Remove extraneous attributes, e.g. AB -> C but A -> C already exists, so eliminate
- Remove inferred/redundant FDs:
	- transitivity
- Cleanup using union rule
### Normal Forms
#### Dependency Preservation
- Dependency preserved: all functional deps have a relation containing all attributes OR follow algorithm
- ![Screenshot 2024-02-09 at 12.38.36 AM.png](../../_resources/Screenshot%202024-02-09%20at%2012.38.36%20AM.png)
- Guaranteed up to 3NF 
#### 1NF
- Attributes are atomic, indivisible
- No repeated groups (e.g. no arrays/lists, or unordered repeated columns)
- **Unique key (PK exists)**
- No null values
- Rows are not ordered
#### 2NF
- in 1NF
- Every attribute is in candidate key or depends on the **entirety** of **all** CKs (transitivity ok)
- Automatically in 2NF if no composite keys and 1NF
#### 3NF
- 2NF and attributes must ONLY directly depend on all candidates keys and NOTHING ELSE OR must be part of a candidate key
	- No transitivity
- Other def: all functional dependencies are trivial, start in full superkey, or end in part of candidate key (all must meet one for 3NF)
#### BCNF
- all functional dependencies are trivial OR start in **full** superkey
- Decompose into BCNF: take violating dependency, create relation, then create another relation containing source + remaining attributes
![Screenshot 2024-03-16 at 7.57.35 PM.png](../../_resources/Screenshot%202024-03-16%20at%207.57.35%20PM.png)
- ^ superkey *of that relation*
### Lossless joins
- Check R1 ⨝ R2 = R OR
- R1 U R2 = R
- R1 ∩ R2 not empty set (e.g. should have join key)
- R1 ∩ R2: is superkey of R1 or R2
	- Check if closure is R1 or R2
- up to 4NF lossless
# Data Warehouses and Lakes
- OLTP: Online Transaction Processing
	- Designed frequent interactive use, random reads + writes w/ low latency
	- Optimized for quick reads and writes
	- Transactions w/ rows, minimized redundancy and uses joins
	- Production ready
- OLAP (Online Analytical processing)/Data Warehousing: read-only, fast low latency reads, writes all at once overnight, internal
	- Usually still use SQL
	- Optimized for low latency reads (SELECT) of aggregated or precomputed data
	- Use cases: reporting, dashboarding
	- Automatically calculates aggregates by columns, specified in advanced or all possible combos, OR exploded tables (precomputed joins)
	- cubes
	- Take time to process, reads of aggregates/exploded tables fast, slightly out-of-date info, append-only, read-only
- ETL jobs:
	- Run jobs against replica, or on each write to primary db, write to both prod and replica and run ETL on replica
- MapReduce:
	- 	1 Input
	- 	2 MAP
	- 	3 Optional Combiner
	- 	4 Partition
	- 	5 Shuffle: Sort
	- 	6 REDUCE
	- 	7 Output
## OLAP operations
- Slice: select one dimension/value from a cube and return subcube (e.g. product category = wireless mouse)
	- Return rectangular rep of data across all locations and time
- Dice: select multiple values from multiple dimensions
- Rollup: compute aggregates across all levels of hierarchial attribute
	- ROLLUP(year, month, day) (start with least granular) return summary by year, month, day
- Drill down: extract aggregates at finer level of granularity
- **Pivot**: convert long to wide formats (e.g. wide hw1, hw2, hw3 vs 3 separate entries in long)
- fact table: quantitative data, usually denormalized
- Dimension table: data of attributes of each fact
## Data Warehouse Schemas
- Star: single fact table, multiple dimension tables that must be joined
- Snowflake: central fact table and several dimension tables that may link to each other
- Galaxy: multiple fact tables share dimensions 
## Examples
- DuckDB: OLAP Analytical Data Store 
	- In memory/standalone
	- Optimized for analytics via columnar data model
	- no server necessary
	- allows auto importing, used in many cases, can be used w/o copy/import in pandas, integration into R and python, flexible typewise, queries not relational, portable, free
```sql
SELECT AVG(trip_distance) FROM 's3://yellow_tripdata_20[12]*.parquet'
SELECT * FROM '~/local/files/file.parquet'
SELECT * FROM dataframe
SELECT * FROM
'https://shell.duckdb.org/data/tpch/0_01/parquet/lineitem.parquet'
```
# Data Lakes
- Single system that contains raw, unprocessed data, no schema, and contents are ingested by machines for specific use cases
- Tends to bloat, more like a model of how to store raw data, not necessarily tech itself
- Hadoop Distributed File System is a data lake
	- Allows multiple nodes to read data
# Streaming Data
- Message broker: send/read to message broker, holds a message queue
	- Durability handled by broker, broker handles state
	- if too big, delete either oldest or random
- Windows:
	- tumbling window: non-overlapping windows of time
	- hopping window: fixed length period, but time periods overlap
	- sliding window: based on events, window +- a time around each event
	- session window: arbitrary window from first to last event
- Stragglers:
	- events arriving late
	- Can ignore, keep window open for a while to capture stragglers, sometimes can correct computation, or include in current window metric
- Potential problems:
	- Lost messages between P and B, or B and C
	- Lock ACKs
	- Offline producers, consumers, or brokers
	- queue overflows
	- producer can't trust broker or consumers
- Minimize message loss
	- Load balancing
	- Acknowledgements
	- Retransmission
	- Deleting messages using TTLs, or at random
	- Reassigning consumer duties from one consumer to another
	- Adding new consumers on the fly
## Bloom Filter
- Compute each hash function, h~1~(e), h~2~(e), h~3~(e), ...
- Each hash number mod length of the bit array is a position in the array
- Check the bits in those positions: if all 1, then *may* have seen e before, otherwise definitely never seen
- **HyperLogLog**: treat bitstring as binary number, and use run lengths and probability to estimate number of distinct objects
	- count approx 4 billion distinct elements with 5 bits: log2 log2 (2^32) = 5

![Screenshot 2024-03-17 at 8.43.47 PM.png](../../_resources/Screenshot%202024-03-17%20at%208.43.47%20PM.png)

# NoSQL
- **CAP Theorem**:
	- Consistency (every read receives most recent write), availability, and partition tolerance (if messages lost or delayed by network) can't all be satisfied in distributed system when a network partition or failure occurs
	- Partition tolerance must always be true
- Key-value, columnar (each row can have different number of columns, adding columns is simple), document store, graph
- Columnar:
	- Each row key has keyspace of key-value pairs of columns
	- unnormalized for fast lookups
	- usable as Data Warehouse
	- Rows are strictly ordered
	- No joins - it's not relational
- MongoDB:
	- Each record is a document, stored in BSON
	- Records organized into collections, which are organized into databases
	- Use `db.coolection.find()`
	- Lots of redundancy, no normalization
	- No joins
	- No schema (BSON blobs)
# B+ Trees/Indexes
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
# Query Plan
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


# Transactions
```sql
START TRANSACTION
DELETE FROM final WHERE uid IN ('222', '333');
UPDATE final SET score = score + 15;
COMMIT;
```
- **ACID**: Atomicity, Consistency, Isolation, and Durability
	- Atomic: happens all or none (roll back if fail, like corrption, constraints, system issues)
	- Consistent: database must be consistent before and after (e.g. no money lost or gained in transaction)
	- Isolation: multiple transactions can occur independently, transparently, without interference
	- Durability: changes of successful transaction apply even in failure
	- Provides high consistency
- NoSQL: **BASE**
	- Basically Available (distributed available nodes) Soft-state (data values may change over time as unknown when system is consistent) Eventually consistent (data may become consistent over time)
	- High availability and eventually consistency
## Schedules
- Serial: execute transactions one after another
- Conflict-serializable: acts just like a serial schedule but is actually interleaved
	- still guarantees consistency
- Swap test:
	- 2 reads can be swapped
	- any writes cannot be swapped
- Precedence graph:
	- Draw from Ti to Tj if A(X) before B(X) where A or B or both is a write
	- if no cycles than conflict serializable
![Screenshot 2024-03-17 at 5.27.17 PM.png](../../_resources/Screenshot%202024-03-17%20at%205.27.17%20PM.png)
## Translation Isolation Levels
- Allowing concurrent allows for improved throughput, and reduced waiting time
	- non-serial schedule may lead to inconsistent state, but conflict serializable still guarantees for consistency 
- Serializable
	- Equivalent to serial
	- Poor performance, max consistency
	- No new data can be read 
- Repeated Read
	- if Ti reads X, no other TXN Tj can update until done
	- default in mysql
	- data read cannot change, but new data in a set can appear (phantom read)
- Read Committed
	- Ti can only read data from Tj that committed
	- Read data was valid at some point; can change midway (non-repeatable/fuzzy reads)
- Read Uncommitted
	- Ti can read data from any other TXN even if not committed
	- Allows dirty read
- All levels prevent dirty writes (all writes are atomic, no in-between states)
# Locking
- Shared and exclusive locks
- Starvation is possible on exclusive lock
	- Prevent: When a transaction requests a lock on data item A in a model M, lock grant if no other transaction holds lock that conflicts, and no other transactino that is waiting for a lock on A and that made its lock request beofre T
## Two-Phase Locking
- Each transaction makes lock + unlock requests in two phases
- Growing phase: acquire but not release
- Shrinking phase: release but cannot acquire
- Improves consistency
## Locking strategies
- Lock requests stored in lock table
- Lock manager prevent deadlines using wait-for graph
- Prevent deadlocks:
	- Acquire all required locks at once
	- acquire lcoks in a certain order
	- use preemption
	- Lock timeouts
- Detection and recovery:
	- Choose a victim (look at time running, data items used, many more data items are needed, how much rollback needed)
	- Rollback
	- Repeat until no deadlock
## Transactions in NoSQL
- Redis:
	- All commands are serialized (isolated)
	- atomic
	- durability if journal persistence used
	- If failure occurs, inconsistency may occur, and must manually run recover
	- use `WATCH` to prevent inconsistencies
- MongoDB
	- Update on single document is atomic
	- ACID transactions recent addition to multi-documents
- neo4j
	- transactions are ACID complient
	- all DB operations are in transaction
	- read committed default isolation
	- user can manually acquire write locks and relationships
	- locks can be acquired for nodes and replationships separately