## Tagging
- Type of object: points to a commit
- `git tag -a v10 abc123`
	- Includes message, using `-m`
- `git tag` list all tags
	- `git tag -l 'v10*'` to filter
- can then just use `v10` in replacement of commit id, for like `git diff v10 HEAD`
- Static: never changes, not movable
## Branch
- Lightweight movable pointer
- `git branch NAME COMMIT`
- more common: `git checkout -b NAME COMMIT`
- `git branch`: list all
	- `git branch -l 'main*'` create branches
	- `git branch -d patch-1`
		- Git warns if deleting branch causes commit to become orphaned
		- `git branch -D patch-1`: force delete
			- Git will eventually clean up orphaned commits, `git gc`
	- `git branch -m patch-1 new-patch-1` rename branch, often like rename main to something else then merge
- `git branch --track main origin/main` makes main follow origin/main
### Motivation
- Feature branch: allow work in isolation
- Maintenance branch: branch an older version that is stabler than bleeding edge
- fork: have 2 different versions that are kept separately, no/little attempt for keeping in sync
## Merge
- Merge commit: new commit that has multiple parents
- `diff3`: compare 3 versions
	- `diff3 B A C`, A oldest, B/C alternatives
		- internally: `diff B A` then `diff A C`
		- If conflict, pick "minority" option (one of three)
	- `diff3 -A` show full file
- Preserves history, but makes it more complex
## Rebase
- Apply changes from source branch against target branch, changing commits
- `git rebase -i`: allow choosing which commits to apply
- Simpler history (more linear), but changes history
- `git rebase --ignore-date`
	- When rebasing, uses current date
	- If not, commit dates will be not in order 
## Git stash
- `patch -p1 - mychanges.diff`
	- `patch FILE_TO_EDIT -i PATCH.patch`
- `git stash push`: add changes to index
- `git stash list` show stash
- `git stash apply`
# Git commit Philosophy
- Break large commits into smaller ones
- Don't break the build, e.g. every commit should still compile
# Git bisect Algorithm
- Assign each node with value: number of ancestors + 1
- average number between min good note and max bad node, or maximize both current node and highest node number - current node 
- Be careful about merge commits in bisects
# Git submodules
- Allows gluing 2 projects together
- `git submodule add GIT_URL/b`
- Like clone but cloning b under a
	- every a commit is linked to a specific b commit
- `git submodule foreach COMMAND`: run command for each submodule
- MUST add new commit on a when updating b
## submodule when cloning
- When doing `git clone`, by default does NOT also download submodules, must individually go into submodules and `git submodule init`