# Clone Git Repository on Storage Server

### The repository to be cloned is located at `/opt/blog.git`


### Clone this Git repository to the `/usr/src/kodekloudrepos` directory. Perform this task using the `natasha` user, and ensure that no modifications are made to the repository or existing directories, such as changing permissions or making unauthorized alterations.

```bash
cd /usr/src/kodekloudrepos/

git clone /opt/blog.git
```