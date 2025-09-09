# Set Up Git Repository on Storage Server


### Utilize yum to install the git package on the Storage Server
```bash
dnf install git -y
```

### Create a bare repository named /opt/apps.git
```bash
mkdir /opt/apps.git

cd /opt/apps.git

git init --bare
```