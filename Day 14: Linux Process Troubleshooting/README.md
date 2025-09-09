# Solutions

```bash
systemctl status httpd

ss -tulpan | grep 8089
# tcp    LISTEN     0      10     127.0.0.1:8089                  *:*                   users:(("sendmail",pid=614,fd=4))

systemctl stop sendmail

systemctl disable sendmail

ss -tulpan | grep 8089

systemctl restart httpd

systemctl status httpd
```