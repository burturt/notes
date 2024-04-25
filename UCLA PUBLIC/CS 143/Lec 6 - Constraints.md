- In schema (check constraint):
```sql
CONSTRAINT Valid_Enrolldate CHECK (enrolldate > birthdate),
```
- Pros: data integrity, set syntax, no trust app dev, changes in app doesn't break integrity
- Cons: Limited in functionality/less flexible, more cpu load, harder to parse/handle errors, not portable across db migrations
- Business logic: in app, data integrity, in db