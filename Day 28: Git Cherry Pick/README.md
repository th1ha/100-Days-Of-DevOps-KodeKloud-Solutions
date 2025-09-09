# Git Cherry Pick

```bash
git log feature --oneline

#7f75e5e (HEAD -> feature, origin/feature) Update welcome.txt
#a99edf3 Update info.txt

git log master --oneline

git branch

git checkout master

git cherry-pick a99edf3

git log master --oneline

git push origin master
```