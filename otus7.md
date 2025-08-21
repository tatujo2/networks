# Домашнее задание "IS-IS"

## Цель:  Настроить IS-IS офисе Триада
### 1. R23 и R25 находятся в зоне 2222.
### 2. R24 находится в зоне 24.
### 3. R26 находится в зоне 26.


### 1. R23 и R25 находятся в зоне 2222.

  R23
  !
  router isis 1
   net 49.2222.0100.0200.0001.00
  !
  interface Loopback0
   ip address 10.2.1.23 255.255.255.255
   ip router isis 1
   isis circuit-type level-2-only
  !
  interface Ethernet0/1
   ip address 10.2.0.1 255.255.255.252
   ip router isis 1
   isis circuit-type level-2-only
  !
  interface Ethernet0/2
   ip address 10.2.0.5 255.255.255.252
   ip router isis 1
   isis circuit-type level-2-only
   
  R25
  !
  router isis 1
   net 49.2222.0100.0200.0002.00
  !
  interface Loopback0
   ip address 10.2.1.25 255.255.255.255
   ip router isis 1
   isis circuit-type level-2-only
  !
  interface Ethernet0/0
   ip address 10.2.0.2 255.255.255.252
   ip router isis 1
   isis circuit-type level-2-only
  !
  interface Ethernet0/2
   ip address 10.2.0.14 255.255.255.252
   ip router isis 1
   isis circuit-type level-2-only

### 2. R24 находится в зоне 24.

  R24
  !
  router isis 1
   net 49.0024.0100.0200.1024.00
  !
  interface Loopback0
   ip address 10.2.1.24 255.255.255.255
   ip router isis 1
   isis circuit-type level-2-only
  !
  interface Ethernet0/1
   ip address 10.2.0.9 255.255.255.252
   ip router isis 1
   isis circuit-type level-2-only
  !
  interface Ethernet0/2
   ip address 10.2.0.6 255.255.255.252
   ip router isis 1
   isis circuit-type level-2-only

### 3. R26 находится в зоне 26.

  R26
  !
  router isis 1
   net 49.0026.0100.0200.1026.00
  !
  interface Loopback0
   ip address 10.2.1.26 255.255.255.255
   ip router isis 1
   isis circuit-type level-2-only
  !         
  interface Ethernet0/0
   ip address 10.2.0.10 255.255.255.252
   ip router isis 1
   isis circuit-type level-2-only
  !
  interface Ethernet0/2
   ip address 10.2.0.13 255.255.255.252
   ip router isis 1
   isis circuit-type level-2-only
 
 
 
### Проверка

    R23#sh ip route 
    Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
           D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
           N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
           E1 - OSPF external type 1, E2 - OSPF external type 2
           i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
           ia - IS-IS inter area, * - candidate default, U - per-user static route
           o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
           a - application route
           + - replicated route, % - next hop override
    
    Gateway of last resort is not set
    
          10.0.0.0/8 is variably subnetted, 10 subnets, 2 masks
    C        10.2.0.0/30 is directly connected, Ethernet0/1
    L        10.2.0.1/32 is directly connected, Ethernet0/1
    C        10.2.0.4/30 is directly connected, Ethernet0/2
    L        10.2.0.5/32 is directly connected, Ethernet0/2
    i L2     10.2.0.8/30 [115/20] via 10.2.0.6, 00:02:50, Ethernet0/2
    i L2     10.2.0.12/30 [115/20] via 10.2.0.2, 00:02:50, Ethernet0/1
    C        10.2.1.23/32 is directly connected, Loopback0
    i L2     10.2.1.24/32 [115/20] via 10.2.0.6, 00:02:50, Ethernet0/2
    i L2     10.2.1.25/32 [115/20] via 10.2.0.2, 00:02:50, Ethernet0/1
    i L2     10.2.1.26/32 [115/30] via 10.2.0.6, 00:02:50, Ethernet0/2
                          [115/30] via 10.2.0.2, 00:02:50, Ethernet0/1
          172.16.0.0/16 is variably subnetted, 2 subnets, 2 masks
    C        172.16.3.0/30 is directly connected, Ethernet0/0
    L        172.16.3.2/32 is directly connected, Ethernet0/0
    
    
    R24#sh ip route          
    Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
           D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
           N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
           E1 - OSPF external type 1, E2 - OSPF external type 2
           i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
           ia - IS-IS inter area, * - candidate default, U - per-user static route
           o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
           a - application route
           + - replicated route, % - next hop override
    
    Gateway of last resort is not set
    
          10.0.0.0/8 is variably subnetted, 10 subnets, 2 masks
    i L2     10.2.0.0/30 [115/20] via 10.2.0.5, 00:00:32, Ethernet0/2
    C        10.2.0.4/30 is directly connected, Ethernet0/2
    L        10.2.0.6/32 is directly connected, Ethernet0/2
    C        10.2.0.8/30 is directly connected, Ethernet0/1
    L        10.2.0.9/32 is directly connected, Ethernet0/1
    i L2     10.2.0.12/30 [115/20] via 10.2.0.10, 00:04:41, Ethernet0/1
    i L2     10.2.1.23/32 [115/20] via 10.2.0.5, 00:00:32, Ethernet0/2
    C        10.2.1.24/32 is directly connected, Loopback0
    i L2     10.2.1.25/32 [115/30] via 10.2.0.10, 00:02:24, Ethernet0/1
                          [115/30] via 10.2.0.5, 00:02:24, Ethernet0/2
    i L2     10.2.1.26/32 [115/20] via 10.2.0.10, 00:04:41, Ethernet0/1
          172.16.0.0/16 is variably subnetted, 4 subnets, 2 masks
    C        172.16.4.0/30 is directly connected, Ethernet0/0
    L        172.16.4.2/32 is directly connected, Ethernet0/0
    C        172.16.5.0/30 is directly connected, Ethernet0/3
    L        172.16.5.1/32 is directly connected, Ethernet0/3
    
    
    R25#sh ip route
    Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
           D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
           N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
           E1 - OSPF external type 1, E2 - OSPF external type 2
           i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
           ia - IS-IS inter area, * - candidate default, U - per-user static route
           o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
           a - application route
           + - replicated route, % - next hop override
    
    Gateway of last resort is not set
    
          10.0.0.0/8 is variably subnetted, 14 subnets, 2 masks
    C        10.2.0.0/30 is directly connected, Ethernet0/0
    L        10.2.0.2/32 is directly connected, Ethernet0/0
    i L2     10.2.0.4/30 [115/20] via 10.2.0.1, 00:04:42, Ethernet0/0
    i L2     10.2.0.8/30 [115/20] via 10.2.0.13, 00:08:56, Ethernet0/2
    C        10.2.0.12/30 is directly connected, Ethernet0/2
    L        10.2.0.14/32 is directly connected, Ethernet0/2
    C        10.2.0.16/30 is directly connected, Ethernet0/1
    L        10.2.0.17/32 is directly connected, Ethernet0/1
    C        10.2.0.24/30 is directly connected, Ethernet0/3
    L        10.2.0.25/32 is directly connected, Ethernet0/3
    i L2     10.2.1.23/32 [115/20] via 10.2.0.1, 00:04:42, Ethernet0/0
    i L2     10.2.1.24/32 [115/30] via 10.2.0.13, 00:06:34, Ethernet0/2
                          [115/30] via 10.2.0.1, 00:06:34, Ethernet0/0
    C        10.2.1.25/32 is directly connected, Loopback0
    i L2     10.2.1.26/32 [115/20] via 10.2.0.13, 00:08:56, Ethernet0/2
    
    
    R26#sh ip route
    Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
           D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
           N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
           E1 - OSPF external type 1, E2 - OSPF external type 2
           i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
           ia - IS-IS inter area, * - candidate default, U - per-user static route
           o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
           a - application route
           + - replicated route, % - next hop override
    
    Gateway of last resort is not set
    
          10.0.0.0/8 is variably subnetted, 12 subnets, 2 masks
    i L2     10.2.0.0/30 [115/20] via 10.2.0.14, 00:14:03, Ethernet0/2
    i L2     10.2.0.4/30 [115/20] via 10.2.0.9, 00:10:19, Ethernet0/0
    C        10.2.0.8/30 is directly connected, Ethernet0/0
    L        10.2.0.10/32 is directly connected, Ethernet0/0
    C        10.2.0.12/30 is directly connected, Ethernet0/2
    L        10.2.0.13/32 is directly connected, Ethernet0/2
    C        10.2.0.20/30 is directly connected, Ethernet0/1
    L        10.2.0.21/32 is directly connected, Ethernet0/1
    i L2     10.2.1.23/32 [115/30] via 10.2.0.14, 00:05:35, Ethernet0/2
                          [115/30] via 10.2.0.9, 00:05:35, Ethernet0/0
    i L2     10.2.1.24/32 [115/20] via 10.2.0.9, 00:10:19, Ethernet0/0
    i L2     10.2.1.25/32 [115/20] via 10.2.0.14, 00:14:03, Ethernet0/2
    C        10.2.1.26/32 is directly connected, Loopback0
          172.16.0.0/16 is variably subnetted, 2 subnets, 2 masks
    C        172.16.6.0/30 is directly connected, Ethernet0/3
    L        172.16.6.1/32 is directly connected, Ethernet0/3
