# Configure Nginx + PHP-FPM Using Unix Sock

### Install nginx on app server 2 , configure it to use port 8098 and its document root should be `/var/www/html`
```bash
dnf install nginx -y
```

### Install `php-fpm` version 8.3 on app server 2, it must use the unix socket `/var/run/php-fpm/default.sock` (create the parent directories if don't exist).
```bash
dnf install https://rpms.remirepo.net/enterprise/remi-release-9.rpm -y
dnf module reset php -y
dnf module list php
dnf module enable php:remi-8.3 -y
dnf module list php
dnf install -y php-fpm php-cli php-mysqlnd

php-fpm -v
```

### Configure php-fpm and nginx to work together.

#### vi `/etc/php-fpm.d/www.conf`
```
listen = /var/run/php-fpm/default.sock
listen.owner = nginx
listen.group = nginx
listen.mode = 0660
```

#### vi `/etc/nginx/conf.d/app.conf`
```bash
server {
    listen 8098;

    root /var/www/html;
    index index.php index.html;

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass unix:/var/run/php-fpm/default.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```

#### enable service
```bash
systemctl enable --now nginx php-fpm
```

### Once configured correctly, you can test the website using curl `http://stapp02:8098/index.php` command from jump host.

```bash
curl http://stapp02:8098/index.php
```