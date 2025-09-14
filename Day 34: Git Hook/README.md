# Git Hook

### create a `post-update` hook in the git git repository

```bash
vim /opt/demo.git/hooks/post-update
```

<details>
  <summary>post-update</summary>

```bash
#!/bin/bash
for refname in "$@"
do
    branch=$(echo "$refname" | cut -d/ -f3)
    if [ "$branch" == "master" ]; then
        current_date=$(date +"%Y-%m-%d")
        tag_name="release-$current_date"
        
        echo "Git post-update hook triggered for branch: $branch"
        echo "Creating a new tag: $tag_name"
        
        # Get the latest commit hash for the branch
        newrev=$(git rev-parse "$refname")
        
        # Actually create the tag
        git tag "$tag_name" "$newrev"
        
        if [ $? -eq 0 ]; then
            echo "Tag $tag_name created successfully on the bare repository."
        else
            echo "Failed to create tag $tag_name"
        fi
    fi
done
```

</details>

```bash
chmod +x/opt/demo.git/hooks/post-update

ls -l /opt/demo.git/hooks/post-update

#-rwxr-xr-x 1 root root 777 Sep 14 12:59 /opt/demo.git/hooks/post-update

```

### Merge the feature branch into the master branch and pusht it

```bash
git merge feature

git push origin master

```

### verify

```bash
git tag

git ls-remote --tags origin

```