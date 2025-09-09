# Install and Configure Nginx as an LBR

```bash
dnf install nginx -y

```

### `vi /etc/nginx/nginx.conf`
```conf
http {
    upstream app_servers {
        server stapp01:5002;
        server stapp02:5002;
        server stapp03:5002;
    }

    server {
        location / {
                      proxy_pass http://app_servers;
                      proxy_set_header Host $host;
                      proxy_set_header X-Real-IP $remote_addr;
                      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                      proxy_set_header X-Forwarded-Proto $scheme;
                  }
    }

}
```

```bash
nginx -t

systemctl enable --now nginx

```

### `curl stlb01`
```
Welcome to xFusionCorp Industries!
```