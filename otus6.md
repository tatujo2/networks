    
# Домашнее задание OSPF
## Цель:
### Настроить OSPF офисе Москва
### Разделить сеть на зоны
### Настроить фильтрацию между зонами

## Задачи:
### 1.	Маршрутизаторы R14-R15 находятся в зоне 0 - backbone.
### 2.	Маршрутизаторы R12-R13 находятся в зоне 10. Дополнительно к маршрутам должны получать маршрут по умолчанию.
### 3.	Маршрутизатор R19 находится в зоне 101 и получает только маршрут по умолчанию.
### 4.	Маршрутизатор R20 находится в зоне 102 и получает все маршруты, кроме маршрутов до сетей зоны 101.
### 5.	Настройка для IPv6 повторяет логику IPv4.
### 6.	План работы и изменения зафиксированы в документации.

## Схема:
![](https://github.com/tatujo2/networks/blob/main/screenshots/otus6.png)

### 1.	Маршрутизаторы R14-R15 находятся в зоне 0 - backbone.
    Для того чтобы маршрутизаторы выдели друга настроен линк между ними.
    R14#
    interface Ethernet1/0
     ip address 10.0.0.41 255.255.255.252
     ip ospf 1 area 0
    R15#
    interface Ethernet1/0
     ip address 10.0.0.42 255.255.255.252
     ip ospf 1 area 0



### 2.	Маршрутизаторы R12-R13 находятся в зоне 10. Дополнительно к маршрутам должны получать маршрут по умолчанию.

    #R12
    interface Ethernet0/0
     ip address 10.0.0.21 255.255.255.252
     ip ospf 1 area 10
    !
      interface Ethernet0/1
     ip address 10.0.0.17 255.255.255.252
     ip ospf 1 area 10
    !
    interface Ethernet0/2
     ip address 10.0.0.6 255.255.255.252
     ip ospf 1 area 10
    !
    interface Ethernet0/3
     ip address 10.0.0.14 255.255.255.252
     ip ospf 1 area 10

    #R13
    interface Ethernet0/0
     ip address 10.0.0.33 255.255.255.252
     ip ospf 1 area 10
    !
    interface Ethernet0/1
     ip address 10.0.0.37 255.255.255.252
     ip ospf 1 area 10
    !
    interface Ethernet0/2
     ip address 10.0.0.26 255.255.255.252
     ip ospf 1 area 10
    !
    interface Ethernet0/3
     ip address 10.0.0.10 255.255.255.252
 ip ospf 1 area 10

    #R14
    router ospf 1
     default-information originate always
    #R15
    router ospf 1
     default-information originate always

### 3.	Маршрутизатор R19 находится в зоне 101 и получает только маршрут по умолчанию.

    #R19
    interface Ethernet0/0
     ip address 10.0.0.1 255.255.255.252
     ip ospf 1 area 101

    #R14
    interface Ethernet0/3
     ip address 10.0.0.2 255.255.255.252
     ip ospf 1 area 101

### 4.	Маршрутизатор R20 находится в зоне 102 и получает все маршруты, кроме маршрутов до сетей зоны 101.

    #R20
    interface Ethernet0/0
     ip address 10.0.0.29 255.255.255.252
     ip ospf 1 area 102

    #R15
    interface Ethernet0/3
     ip address 10.0.0.30 255.255.255.252
     ip ospf 1 area 102

    #R15
    ip prefix-list deny_101 seq 5 deny 10.0.0.0/30
    ip prefix-list deny_101 seq 10 permit 0.0.0.0/0 le 32

    router ospf 1
     area 102 filter-list prefix deny_101 in

### Проверка:

    R20#sh ip route
    Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
           D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
           N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
           E1 - OSPF external type 1, E2 - OSPF external type 2
           i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
           ia - IS-IS inter area, * - candidate default, U - per-user static route
           o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
           a - application route
           + - replicated route, % - next hop override

    Gateway of last resort is 10.0.0.30 to network 0.0.0.0

    O*E2  0.0.0.0/0 [110/1] via 10.0.0.30, 00:37:58, Ethernet0/0
          10.0.0.0/8 is variably subnetted, 11 subnets, 2 masks
    O IA     10.0.0.4/30 [110/30] via 10.0.0.30, 00:37:58, Ethernet0/0
    O IA     10.0.0.8/30 [110/30] via 10.0.0.30, 00:37:58, Ethernet0/0
    O IA     10.0.0.12/30 [110/20] via 10.0.0.30, 00:37:58, Ethernet0/0
    O IA     10.0.0.16/30 [110/30] via 10.0.0.30, 00:37:57, Ethernet0/0
    O IA     10.0.0.20/30 [110/30] via 10.0.0.30, 00:37:57, Ethernet0/0
    O IA     10.0.0.24/30 [110/20] via 10.0.0.30, 00:37:58, Ethernet0/0
    C        10.0.0.28/30 is directly connected, Ethernet0/0
    L        10.0.0.29/32 is directly connected, Ethernet0/0
    O IA     10.0.0.32/30 [110/30] via 10.0.0.30, 00:37:57, Ethernet0/0
    O IA     10.0.0.36/30 [110/30] via 10.0.0.30, 00:37:57, Ethernet0/0
    O IA     10.0.0.40/30 [110/20] via 10.0.0.30, 00:37:58, Ethernet0/0
          172.16.0.0/30 is subnetted, 2 subnets
    O IA     172.16.0.0 [110/30] via 10.0.0.30, 00:37:58, Ethernet0/0
    O IA     172.16.1.0 [110/20] via 10.0.0.30, 00:37:58, Ethernet0/0

     

### 5.	Настройка для IPv6 повторяет логику IPv4.
### 6.	План работы и изменения зафиксированы в документации.
