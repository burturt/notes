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

