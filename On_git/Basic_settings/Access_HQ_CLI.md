# На ISP

Через `nmtui` настраиваем адреса на сетевом интерфейсе, который связывает ISP с HQ-RTR

Адрес: 172.16.1.1/28
Сервер DNS: 8.8.8.8

На этом же интерфейсе, который связывает ISP и HQ-RTR, через nmtui прописываем маршруты:
192.168.100.0/27    172.16.1.2
192.168.200.0/28    172.16.1.2

Настройка NAT для проксирования запросов клиентов из внутренних сетей наружу и отключение Selinux:

```sh
sudo setenforce 0
ip -br a

# Вместо enp0s3 указываем название адаптера, который ведёт в интернет
# Он будет уже с ip-шником
sudo iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE
```

/etc/sysctl.conf
```
net.ipv4.ip_forward=1
```

/proc/sys/net/ipv4/ip_forward`
```
1
```
Как только записали, проверьте ещё раз, что там теперь стоит единичка.

Статические маршруты командами (сбрасываются при перезагрузке и рестарте интерфейса через nmtui):
```sh
sudo ip route add 192.168.100.0/27 via 172.16.1.2
sudo ip route add 192.168.200.0/27 via 172.16.1.2
sudo ip route add 192.168.20.0/27 via 172.16.2.2
```

`172.16.1.2` и `172.16.2.2` - адреса маршрутизаторов. Статические маршруты лучше вписать через nmtui. 

------------------------------------------------------------------------------------------
# HQ-RTR

```
configure
	do sh int status
```

1/0/3 - интерфейс в сторону ISP
1/0/2 - интерфейс в сторону HQ-CLI

Замените их на те, которые будут у вас вести к ISP и HQ-CLI. Ориентируйтесь по MAC-адресам и номерам мостов во вкладке Proxmox'a и оборудовании виртуалки. 

```
configure
hostname hq-rtr.sirius-exam.org

object-group network LOCAL
  ip address-range 192.168.100.1-192.168.100.12
  ip address-range 192.168.200.1-192.168.200.12
exit

object-group network PUBLIC
  ip address-range 172.16.1.3-172.16.1.7
exit

interface gigabitethernet 1/0/2
  ip firewall disable
  ip address 192.168.200.1/28
exit

interface gigabitethernet 1/0/3
  ip firewall disable
  ip address 172.16.1.2/28
  ip nat proxy-arp PUBLIC
exit

nat source
  pool TRANSLATE
    ip address-range 172.16.1.3-172.16.1.7
  exit
  ruleset SNAT
    to interface gigabitethernet 1/0/3
    rule 1
      match source-address object-group LOCAL
      action source-nat pool TRANSLATE
    exit
  exit
exit

ip route 0.0.0.0/0 172.16.1.1
```

Не забываем закомитить

```
do commit
do confirm
```

Следите за выводом команды commit. Если она начинается с 
"Can't ..." то значит что то вписали не так и нужно откатывать изменения через.

Как только сохранили, на маршрутизаторе делаем команду 
```
do ping 8.8.8.8
```
пинги обязаны пройти. Если нет, то повторяем все действия заново.

------------------------------------------------------------------------------------------
# HQ-CLI

Через nmtui задаём

Адрес: 192.168.200.2/28
Шлюз: 192.168.200.1
DNS1: 8.8.8.8
DNS2: 77.88.8.8

Проверяем что адрес задался:
```
ip -br a
```

После этого можете пользоваться Яндексом, ссылками и доками