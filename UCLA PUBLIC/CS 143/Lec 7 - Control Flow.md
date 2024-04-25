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