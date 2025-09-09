# Git Manage Remotes

### In /usr/src/kodekloudrepos/official repo add a new remote dev_official and point it to /opt/xfusioncorp_official.git repository.

```bash
cd /usr/src/kodekloudrepos/official/

git remote add dev_official /opt/xfusioncorp_official.git

git remote -v
```

### There is a file `/tmp/index.html` on same server; copy this file to the repo and add/commit to master branch.

```bash
cp /tmp/index.html .

git branch

git add .

git commit -m 'add index.html file'
```


### Finally push master branch to this new remote origin.

```bash
git push dev_official master
```