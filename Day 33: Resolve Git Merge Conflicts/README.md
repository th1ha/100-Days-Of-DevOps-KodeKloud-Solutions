# Resolve Git Merge Conflicts

```bash
git push origin master

#Username for 'http://git.stratos.xfusioncorp.com': max
#Password for 'http://max@git.stratos.xfusioncorp.com': 
#To http://git.stratos.xfusioncorp.com/sarah/story-blog.git
# ! [rejected]        master -> master (fetch first

git pull origin master

#CONFLICT (add/add): Merge conflict in story-index.txt

vi story-index.txt
# (Keep all 4 story titles and remove conflict markers,,<replace Mooose with Mouse save and exit)

git add .

git commit -m "Resolve conflit"

git push origin master
 ```