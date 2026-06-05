Тут в качестве качестве интерфейса gigabitEthernet нужно ставить тот интерфейс, который связывает маршрутизатор с ISP
# HQ-RTR
```
ip firewall mode stateless

nat destination
  pool HQ_SRV
    ip address 192.168.100.2
  exit
  ruleset DNAT
    from interface gigabitethernet 1/0/3
    rule 1
      match protocol tcp
      match destination-port port-range 2026
      action destination-nat pool HQ_SRV
      enable
    exit
    rule 2
      match protocol tcp
      match destination-port port-range 8080
      action destination-nat pool HQ_SRV
      enable
    exit
  exit
exit
```
# BR-RTR

```
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
```