# ISP

```
sudo setenforce 0
sudo iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE
sudo ip route add 192.168.100.0/27 via 172.16.1.2
sudo ip route add 192.168.200.0/27 via 172.16.1.2
sudo ip route add 192.168.20.0/27 via 172.16.2.2
```

enp0s3 - интерфейс, который ведёт до интернета

------------------------------------------------------------------------------------------
# Службы (mariadb, named и другие)

```
sudo setenforce 0
sudo systemctl restart служба.service
```

# HQ-SRV и BR-SRV чтобы был доступ по SSH
```
sudo setenforce 0
sudo systemctl restart sshd.service
```