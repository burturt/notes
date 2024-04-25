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