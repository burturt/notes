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