# Git hard reset

### In /usr/src/kodekloudrepos/news git repository, reset the git commit history so that there are only two commits in the commit history i.e initial commit and add data.txt file
```bash
cd /usr/src/kodekloudrepos/news

git log --oneline

#be4a61e (HEAD -> master, origin/master) Test Commit10
#421930d Test Commit1
#3ef87d7 add data.txt file
#d937c3c initial commit

git reset --hard 3ef87d7

git log --oneline

#3ef87d7 (HEAD -> master) add data.txt file
#d937c3c initial commit

git push origin master --force

git log --oneline

#3ef87d7 (HEAD -> master, origin/master) add data.txt file
#d937c3c initial commit

```