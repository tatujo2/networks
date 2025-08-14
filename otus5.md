# Домашнее задание PBR
## Цель: 
## Настроить политику маршрутизации в офисе Чокурдах
## Распределить трафик между 2 линками
## Задачи:
### 1.	Настроите политику маршрутизации для сетей офиса.
### 2.	Распределите трафик между двумя линками с провайдером.
### 3.	Настроите отслеживание линка через технологию IP SLA.(только для IPv4)
### 4.	Настройте для офиса Лабытнанги маршрут по-умолчанию.


Описание/Пошаговая инструкция выполнения домашнего задания:
В этой самостоятельной работе мы ожидаем, что вы самостоятельно:
### 1.	Настроите политику маршрутизации для сетей офиса.

    Сеть: 
    VLAN 60 10.2.60.0/24 - для VPC30
    VLAN 70 10.2.70.0/24 – для VPC31
    VLAN 99 10.2.99.0./24 – сеть для управления коммутатором

### 2.	Распределите трафик между двумя линками с провайдером.

    Router R28(config)# access-list 1 permit 10.2.60.0 0.0.0.255

    Router R28(config)# access-list 2 permit 10.2.70.0 0.0.0.255

    Router R28(config)# route-map PBR_60 permit 10

    Router R28(config-route-map)# match ip address 1

Router R28(config-route-map)# set ip next-hop 10.2.0.21

Router R28(config-route-map)# route-map PBR_70 permit 20

Router R28(config-route-map)# match ip address 2

Router R28(config-route-map)# set ip next-hop 10.2.0.25

Router R28(config)# interface Ethernet 0/2.60

Router R28(config-if)# ip policy route-map PBR_60

Router R28(config-if)# interface Ethernet 0/2.70

Router R28(config-if)# ip policy route-map PBR_70
3.	Настроите отслеживание линка через технологию IP SLA.(только для IPv4)

ip sla 1
icmp-echo 10.2.0.21  source-interface Eth 0/0
threshold 10000
ip sla schedule 1 life forever start-time now
track 1 ip sla 1 reachability

ip sla 2
icmp-echo 10.2.0.25  source-interface Eth 0/1
threshold 2500
ip sla schedule 2 life forever start-time now
track 2 ip sla 2 reachability

R28#sh ip sla summary
IPSLAs Latest Operation Summary
Codes: * active, ^ inactive, ~ pending

ID           Type        Destination       Stats       Return      Last
                                           (ms)        Code        Run
-----------------------------------------------------------------------
*1           icmp-echo   10.2.0.21         RTT=1       OK          7 seconds ago




*2           icmp-echo   10.2.0.25         RTT=1       OK          8 seconds ago


4.	Настройте для офиса Лабытнанги маршрут по-умолчанию.
R28(config)#do sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 10.2.0.21 to network 0.0.0.0

S*    0.0.0.0/0 [1/0] via 10.2.0.21
      10.0.0.0/8 is variably subnetted, 10 subnets, 3 masks
C        10.2.0.20/30 is directly connected, Ethernet0/0
L        10.2.0.22/32 is directly connected, Ethernet0/0
C        10.2.0.24/30 is directly connected, Ethernet0/1
L        10.2.0.26/32 is directly connected, Ethernet0/1
C        10.2.60.0/24 is directly connected, Ethernet0/2.60
L        10.2.60.1/32 is directly connected, Ethernet0/2.60
C        10.2.70.0/24 is directly connected, Ethernet0/2.70
L        10.2.70.1/32 is directly connected, Ethernet0/2.70
5.	План работы и изменения зафиксированы в документации .


