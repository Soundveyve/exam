# ISP

```
sudo setenforce 0
sudo iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE
sudo ip route add 192.168.100.0/27 via 172.16.1.2
sudo ip route add 192.168.200.0/27 via 172.16.1.2
sudo ip route add 192.168.20.0/27 via 172.16.2.2
```

enp0s3 - интерфейс, который ведёт до интернета в ISP

------------------------------------------------------------------------------------------
# ОБЯЗАТЕЛЬНО ПИШЕМ НА HQ-SRV ПРИ ПЕРЕЗАПУСКЕ

```
sudo setenforce 0
sudo systemctl enable --now nfs-server.service
sudo systemctl enable httpd --now
sudo systemctl enable mariadb --now
sudo systemctl restart nfs-server.service
sudo systemctl restart httpd.service
sudo systemctl restart httpd
sudo systemctl restart sshd.service
```

# HQ-SRV и BR-SRV чтобы был доступ по SSH
```
sudo setenforce 0
sudo systemctl restart sshd.service
```