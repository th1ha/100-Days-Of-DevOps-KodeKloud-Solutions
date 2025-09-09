# Git Merge Branches

### Create a new branch devops in `/usr/src/kodekloudrepos/ecommerce` repo from master and copy the /tmp/index.html file (present on storage server itself) into the repo. Further, add/commit this file in the new branch and merge back that branch into master branch. Finally, push the changes to the origin for both of the branches.

```bash
cd /usr/src/kodekloudrepos/ecommerce/

ls 

git checkout -b devops

cp /tmp/index.html .

git add .

git commit -m 'add index.html file'

git checkout master

git branch  # verify

git merge devops

ls

git push origin master

git push origin devops

```