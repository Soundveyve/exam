Инструкция на случай если не будет выставляться статический IP-шник на виртуалке с Linux
Ошибка будет звучать так: 
"Не удалось активировать подключение:  Сбой активации: Не удалось зарезервировать конфигурацию IP (отсутствует свободный адрес, истекло время ожидания и т.д)"

Удалите этот интерфейс:
```
sudo nmcli connection delete Название_поключения
```

А потом добавьте (всё будет добиваться TAB):
```
sudo nmcli connection add type ethernet ifname НАЗВАНИЕ_ИНТЕРФЕЙСА ipv4.method manual ipv4.address АДРЕС_КОТОРЫЙ_НАЗНАЧИМ/МАСКА ipv4.gateway ШЛЮЗ ipv4.dns 8.8.8.8 ipv6.method disabled
```

Пример удаления и добавления с нужными параметрами интерфейса на BR-SRV
```
sudo nmcli connection delete Проводное\ подключение\ 1

sudo nmcli connection add type ethernet ifname enp7s1 ipv4.method manual ipv4.address 192.168.20.2/28 ipv4.gateway 192.168.20.1 ipv4.dns 8.8.8.8 ipv6.method disabled
```