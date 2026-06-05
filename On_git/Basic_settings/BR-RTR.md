В этом конфиге:
1/0/1 - ведёт к BR-SRV. Посмотрите какой ведёт к BR-SRV у вас и замените
1/0/2 - ведёт к ISP. Тоже поменяйте на тот, который идёт к ISP у вас

```
hostname br-rtr.sirius-exam.org

object-group network LOCAL
  ip address-range 192.168.20.1-192.168.20.12
exit
object-group network PUBLIC
  ip address-range 172.16.2.3-172.16.2.7
exit

router ospf 10
  area 1.1.1.1
    network 10.10.10.0/28
    network 192.168.20.0/28
    enable
  exit
  enable
exit

interface gigabitethernet 1/0/1
  ip firewall disable
  ip address 192.168.20.1/28
exit
interface gigabitethernet 1/0/2
  ip firewall disable
  ip address 172.16.2.2/28
  ip nat proxy-arp PUBLIC
exit

tunnel gre 1
  ttl 225
  ip firewall disable
  local address 172.16.2.2
  remote address 172.16.1.2
  ip address 10.10.10.2/28
  ip ospf instance 10
  ip ospf area 1.1.1.1
  ip ospf authentication key ascii-text encrypted AC94107EA75F5AFF
  ip ospf
  enable
exit

ip firewall mode stateless

nat destination
  pool BR_SRV
    ip address 192.168.20.2
  exit
  ruleset DNAT
    from interface gigabitethernet 1/0/2
    rule 1
      match protocol tcp
      match destination-port port-range 2026
      action destination-nat pool BR_SRV
      enable
    exit
    rule 2
      match protocol tcp
      match destination-port port-range 8080
      action destination-nat pool BR_SRV
      enable
    exit
  exit
exit

nat source
  pool TRANSLATE
    ip address-range 172.16.2.3-172.16.2.7
  exit
  ruleset SNAT
    to interface gigabitethernet 1/0/2
    rule 1
      match source-address object-group LOCAL
      action source-nat pool TRANSLATE
    exit
  exit
exit

ip route 0.0.0.0/0 172.16.2.1

clock timezone gmt +3
```