dnf install -y iptables-services iptables-utils

systemctl --now enable iptables

iptables -F

iptables -A INPUT -p tcp --dport 8086 -s 172.16.238.14 -j ACCEPT
iptables -A INPUT -p tcp --dport 8086 -j DROP

iptables -L INPUT -n --line-numbers

service iptables save

