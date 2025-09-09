#  Git Stash

### Look for the stashed changes under /usr/src/kodekloudrepos/demo git repository, and restore the stash with stash@{1} identifier. Further, commit and push your changes to the origin.

```bash
cd /usr/src/kodekloudrepos/demo/

git stash list

#stash@{0}: WIP on master: 47e1f1e initial commit
#stash@{1}: WIP on master: 47e1f1e initial commit

git stash apply stash@{1}

git commit -am 'restore from stash'

git push origin master
```