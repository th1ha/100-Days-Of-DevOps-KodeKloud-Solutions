# Git Revert Some Changes

### In /usr/src/kodekloudrepos/games git repository, revert the latest commit ( HEAD ) to the previous commit (JFYI the previous commit hash should be with initial commit message ).

```bash
cd /usr/src/kodekloudrepos/games/

git log

git log --oneline

git revert HEAD --no-edit

```

### Use `revert demo` message (please use all small letters for commit message) for the new revert commit.

```bash
# changing the last commit
git commit --amend -m "revert demo"

git log --oneline
```