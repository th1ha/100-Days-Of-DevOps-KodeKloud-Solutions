# Install and Configure Web Application


### Install httpd package and dependencies on `app server 1`
```bash
dnf install -y httpd
```

### Apache should serve on port `3002`
```bash
vi /etc/httpd/conf/httpd.conf

# Listen 3002

systemctl enable --now httpd

ss -tulpan | grep 3002
```

### There are two website's backups `/home/thor/ecommerce` and `/home/thor/apps` on `jump_host`. Set them up on Apache in a way that ecommerce should work on the link `http://localhost:3002/ecommerce/` and apps should work on link `http://localhost:3002/apps/` on the mentioned app server.

#### cat `/var/www/html/apps/index.html`

```html
<!DOCTYPE html>
<html>
<body>

<h1>KodeKloud</h1>

<p>This is a sample page for our apps website</p>

</body>
</html>
```

#### cat `/var/www/html/ecommerce/index.html`
```html
<!DOCTYPE html>
<html>
<body>

<h1>KodeKloud</h1>

<p>This is a sample page for our ecommerce website</p>

</body>
</html>
```

#### cat `/etc/httpd/conf/kodekloud.conf`
```html
<VirtualHost *:3002>
    DocumentRoot "/var/www/html"
    Alias /ecommerce "/var/www/html/ecommerce"
    <Directory "/var/www/html/ecommerce">
        AllowOverride All
        Require all granted
    </Directory>

    Alias /apps "/var/www/html/apps"
    <Directory "/var/www/html/apps">
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

### Verify
```bash
curl localhost:3002/ecommerce/

curl localhost:3002/apps/
```