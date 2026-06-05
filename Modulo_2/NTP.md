# На ISP

Закомментировать 4 сервера и изменить значение `localstratum` и `allow`

sudo nano /etc/chrony.conf
```
#server ntp1.vniiftri.ru iburst
#server ntp2.vniiftri.ru iburst
#server ntp3.vniiftri.ru iburst
server ntp.vniiftri.ru iburst
local stratum 5
allow 0.0.0.0/0
```

```
sudo systemctl restart chronyd
sudo chronyc sources
```

------------------------------------------------------------------------------------------
# На остальных Lunux'aх

sudo nano /etc/chrony.conf
```
# Для HQ-сегмента
server 172.16.1.1 iburst

# Для BR-сегмента
server 172.16.2.1 iburst
```

```
sudo systemctl restart chronyd
sudo chronyc sources
```