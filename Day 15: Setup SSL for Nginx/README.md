# Setup SSL for Nginx

```bash
openssl x509 -in /tmp/nautilus.crt --noout --text

dnf install nginx -y

mkdir /etc/pki/tls/nginx

cp /tmp/nautilus.* /etc/pki/tls/nginx/

ls /etc/pki/tls/nginx/

```

### `vi /etc/nginx/nginx.conf`
```conf
server {
    listen              443 ssl;
    server_name         stapp03;
    ssl_certificate     /etc/pki/tls/nginx/nautilus.crt;
    ssl_certificate_key /etc/pki/tls/nginx/nautilus.key;
    ssl_protocols       TLSv1.2 TLSv1.3;
    ssl_ciphers         HIGH:!aNULL:!MD5;
    root /usr/share/nginx/html;
    index index.html;
}
```

```bash
echo "Welcome!" > /usr/share/nginx/html/index.html

nginx -t

systemctl enable --now nginx

```

### `curl -Ik https://stapp03`
```
HTTP/1.1 200 OK
Server: nginx/1.20.1
Date: Tue, 09 Sep 2025 04:25:17 GMT
Content-Type: text/html
Content-Length: 9
Last-Modified: Tue, 09 Sep 2025 04:24:13 GMT
Connection: keep-alive
ETag: "68bfabed-9"
Accept-Ranges: bytes
```

### `curl -k https://stapp03`
```
Welcome!
```