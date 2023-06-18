- Version control: dev part of software
- Configuration control: ops part of software
- Common tools for both:
	- Tools for recording/developing variants (near clones) of existing systems
	- Keep track of history:
		- Revert in case of bugs/current version is wrong
		- See which version you have
		- Document compatibility issues
		- Learn lessons from past development
	- Keep track of future:
		- Plans for future, different directors to head
		- manage changes in different areas
		- Merging 2 separate projects into 1
		- Project reviewers 
	- Make connections between history-history and history-future
		- Example: bug reports
# Git
- Originally developed for linux kernel
- 3 Big ideas:
	- Object database: records variants of history of project
		- Sample object: commit (project state + metadata (time, author, etc))
	- Index: records your planned changes for next commit
		- Focus of day-to-day stuff; keep track of the planned future
	- Distributed database: Have copies that are not the same but are part of the same project
		- Some security controls
		- Not exactly the same
- `.gitignore`: ignore certain files and leave outside version control
	- glob-like: start `/` --> starts working directory
		- name w/o slash start will match name in any subdirectory
		- slash at end matches only directories
### Commit arithmetic
- `HEAD`: latest commit you can see
- `C^`: commit just before C, can put multiple
- `COMMIT^!` shorthand `COMMIT^..COMMIT` (give just that commit)
## Commands
### exploring current state
- `git clone URL`: Clones repository from remote, e.g. `https://git.savannah.gnu.org/git/diffutils.git`
	- Compressed: lists number of variants as # of objects
	- Deltas: differences between 2 versions, and resolving it allows reading all versions
	- Creates subdirectory for project, including copy of history
- `.git` folder
	- contains history and copy of repo
	- If cloning locally, uses hard links to link to files
- `git checkout FILE`: Restore file from index
- `git status`: gives status of project: branch (variant), sync status of upstream (but doesn't necessarily sync local copy of upstream), lists changes
	- Also useful while in middle of merge, rebase, or bisect: show conflicts
- `git log`: shows history of current system
```
commit 6351be27c38642916e394cbf331ecc2cbb25f4fe
Author: Paul Eggert <eggert@cs.ucla.edu>
Date:   Fri Feb 10 15:44:19 2023 -0800

	MSG
```
- commit hash calculated using checksum of contents, cryptographically secure (SHA-1). "Content-addressible memory"
- commit msg: metacomment, big deal as important for reviewers, developers, etc
	- convention: 1st line is key, 50 characters or less, 2nd line empty, rest describe motivation for change, include files changed
- `git log --pretty=fuller`: shows committer and author difference
	- Author date: when commit was created, commit date: date actually added to repository
- `git log commit`: give log as of that commit
- `git log commit1..commit2` commits between 1 and 2 and including 2
## Git exploration
- `git log --pretty=oneline`: commit + first line
- `git ls-files` list all working files
	- `git grep`: lists working file names and greps
- `git diff` default diff between index (staging) and working files
	- `git diff VAR1..VAR2` changes between 2 versions
	- `git diff VAR` changes between state and working version
	- `-w`: ignore changes that are only whitespace
	- `git diff --cached`: commit (default HEAD) and index (staging)
- Git contains index: plans for next commit
- `git add FILE`: git add file and stages file for next commit
	- Copy files into index
- `git commit`: creates commit object in repository
	- `-a`: add all files and create commit
	- `-m`: give message w/o editor
- `git show COMMIT`
	- output of `git log` + `git diff` for specified commit
	- `COMMIT:FILE`: show just changes relevant to FILE