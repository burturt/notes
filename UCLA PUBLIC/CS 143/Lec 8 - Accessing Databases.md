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