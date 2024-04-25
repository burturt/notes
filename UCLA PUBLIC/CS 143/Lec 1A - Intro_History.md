## Originally: flat file
- Data stored in individual flat files
- Must update manually/via scripts to edit records (add new students, compute GPAs, class rosters, etc)
### Drawbacks
- Non-standard format: could mess things up
- **Duplicated data**, which adds higher storage costs and issues with inconsistency
- **Poor data integrity**
- **Sysadmin is the bottleneck**/human hours needed for new data or report needed
	- Must submit and wait for any new data
- Bad integrity on constraints, really hard when multiple files are involved
- **Lack of atomicity** (bank, enrollment exchange)
	- Always in consistent state: complete success or complete failure
- **Concurrent** access issues
- **Security**: imposing security constraints is difficult