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
- `COALESCE(expression, replacement)`
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

