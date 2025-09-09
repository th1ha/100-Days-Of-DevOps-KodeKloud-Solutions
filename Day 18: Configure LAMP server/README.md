# Configure LAMP server


### on app servers
```bash
dnf install -y php php-cli php-mysqlnd php-fpm httpd

vi /etc/httpd/conf/httpd.conf

# Listen 3000

systemctl enable --now httpd

ss -tulpan | grep 3000

```

### check the config `cat /var/www/html/index.php`
```
<?php

$dbname = 'kodekloud_db9';
$dbuser = 'kodekloud_roy';
$dbpass = '8FmzjvFU6S';
$dbhost = 'stdb01';

$link = mysqli_connect($dbhost, $dbuser, $dbpass) or die("Unable to Connect to '$dbhost'");
echo "App is able to connect to the database using user $dbuser";
?>
```

### on db server
```bash
dnf install mariadb-server mariadb -y

systemctl enable --now mariadb

CREATE DATABASE kodekloud_db9;
CREATE USER 'kodekloud_roy'@'%' IDENTIFIED BY '8FmzjvFU6S';
GRANT ALL PRIVILEGES ON kodekloud_db9.* TO 'kodekloud_roy'@'%';
FLUSH PRIVILEGES;
```

### Verify
```bash
curl stapp01:3000
```
#### output
```
App is able to connect to the database using user kodekloud_roy
```