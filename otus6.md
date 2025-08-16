    
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

### 5.	Настройка для IPv6 повторяет логику IPv4.
### 6.	План работы и изменения зафиксированы в документации.
