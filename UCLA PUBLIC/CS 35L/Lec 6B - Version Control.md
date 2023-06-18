## Git Configurations
- git config options come from:
	- git installation global defaults
	- git project specifically (project-specific prefs)
		- `.git/config`
	- git personal prefs
		- `~/.gitconfig`
- `git config`
	- `-l` list current config

## Git reset
- `git reset --soft`:
	- Move master, HEAD to commit, push changes into staging area
	- Keeps index and current files intact
	- git commit to save changes after the current commit
- `git reset --mixed` (default)
	- Move master, HEAD to commit, push changes into working directory
	- Modifies index, keeps current files intaxt
	- needs `git add` before committing
- `git reset --hard`
	- Completely resets working directory
	- Modifies index, modifies current files
## Git blame
- `git blame FILE`
	- Gives copy of content of file but for each line in file, shows commit that created that line + author
- `git blame commit -- FILE`
	- Get blame as of commit in file FILE
## Git bisect
- binary search against commits to find commit that first introduced a bug
```bash
git bisect start
git bisect bad # mark currently checked out commit as bad
git bisect bad bad-tag
git bisect good good-version-tag
git bisect run ./test
```
or
- `git bisect start BADCOMMIT GOODCOMMIT`
- `git bisect run COMMAND`
	- Checks command's exit status
	- Example: `sh -c "cmd1 && cmd2"`
	- At end, at commit that is oldest bad commit
- `git bisect skip`: don't consider current commit when looking
## Remotes
- `git fetch`: brings you up to date with remote repository, makes no changes to anything other than remote cache
- `git pull`: `git fetch` + merge changes into commits + working files
- `git remote`: lists all remotes, `-v` to list urls
	- `origin` is default remote when git clone
## Branches
- lightweight movable pointer to a commit, points to "end" of link of commits to keep track of newest child
- Why branches? different versions with different tolerance for changes, or competing visions, or subteams working on different features
- Abilities: 
	- Cherry-pick changes from one branch to another
	- Merge commits that have multiple older versions
- `git branch -a` list all branches
- `git branch -d branch` delete branch, `-D` to force
- git history is an acyclic directed graph
## Merging branches
- `git merge branch`: merge (or move if linear) to another branch state
	- Often need to resolve merge conflicts