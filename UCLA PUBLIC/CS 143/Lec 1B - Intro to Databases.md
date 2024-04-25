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
	- JS can also used
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