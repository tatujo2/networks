# Домашнее задание EIGRP

## Цель: Настроить EIGRP в С.-Петербург; Использовать named EIGRP

## Задачи: 
### 1. R32 получает только маршрут по умолчанию.
###	2. R16-17 анонсируют только суммарные префиксы.
### 3. Использовать EIGRP named-mode для настройки сети.


## 1. R32 получает только маршрут по умолчанию.(EIGRP named-mode)

### R16
		router eigrp SPB
		 !
		 address-family ipv4 unicast autonomous-system 1
		  !
		  af-interface Ethernet0/3
		   summary-address 0.0.0.0 0.0.0.0
		  exit-af-interface
		  !
		 exit-address-family
		 
 ## Проверка
 
 ### R32
 
		 R32#sh run | s ei
		router eigrp SPB
		 !
		 address-family ipv4 unicast autonomous-system 1
		  !
		  topology base
		   redistribute connected
		  exit-af-topology
		  network 10.1.0.28 0.0.0.3
		 exit-address-family
		 
		 
		 R32#sh ip route
		Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
			   D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
			   N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
			   E1 - OSPF external type 1, E2 - OSPF external type 2
			   i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
			   ia - IS-IS inter area, * - candidate default, U - per-user static route
			   o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
			   a - application route
			   + - replicated route, % - next hop override

		Gateway of last resort is 10.1.0.29 to network 0.0.0.0

		D*    0.0.0.0/0 [90/1024640] via 10.1.0.29, 00:53:37, Ethernet0/0
			  10.0.0.0/8 is variably subnetted, 3 subnets, 2 masks
		C        10.1.0.28/30 is directly connected, Ethernet0/0
		L        10.1.0.30/32 is directly connected, Ethernet0/0
		C        10.1.1.32/32 is directly connected, Loopback0
		
		
## 2. R16-17 анонсируют только суммарные префиксы.(EIGRP named-mode)

### R16
		R16#sh run | s ei       
		router eigrp SPB
		 !
		 address-family ipv4 unicast autonomous-system 1
		  !
		  af-interface Ethernet0/1
		   summary-address 10.1.0.0 255.255.0.0
		  exit-af-interface
		  !
		  af-interface Ethernet0/3
		   summary-address 0.0.0.0 0.0.0.0
		  exit-af-interface
		  !
		  topology base
		   redistribute connected
		  exit-af-topology
		  network 10.1.0.4 0.0.0.3
		  network 10.1.0.28 0.0.0.3
		  network 10.1.40.0 0.0.0.255
		  network 10.1.50.0 0.0.0.255
		 exit-address-family
 
 ### R17
		 R17#sh run | s ei
		router eigrp SPB
		 !
		 address-family ipv4 unicast autonomous-system 1
		  !
		  af-interface Ethernet0/1
		   summary-address 10.1.0.0 255.255.0.0
		  exit-af-interface
		  !
		  topology base
		   redistribute connected
		  exit-af-topology
		  network 10.1.0.0 0.0.0.3
		  network 10.1.40.0 0.0.0.255
		  network 10.1.50.0 0.0.0.255
		  network 10.1.99.0 0.0.0.255
		 exit-address-family

## Проверка

		R18#sh ip route
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

			  10.0.0.0/8 is variably subnetted, 6 subnets, 3 masks
		D        10.1.0.0/16 [90/1024640] via 10.1.0.6, 01:56:41, Ethernet0/0
							 [90/1024640] via 10.1.0.2, 01:56:41, Ethernet0/1
		C        10.1.0.0/30 is directly connected, Ethernet0/1
		L        10.1.0.1/32 is directly connected, Ethernet0/1
		C        10.1.0.4/30 is directly connected, Ethernet0/0
		L        10.1.0.5/32 is directly connected, Ethernet0/0
		C        10.1.1.18/32 is directly connected, Loopback0
			  172.16.0.0/16 is variably subnetted, 4 subnets, 2 masks
		C        172.16.5.0/30 is directly connected, Ethernet0/2
		L        172.16.5.2/32 is directly connected, Ethernet0/2
		C        172.16.6.0/30 is directly connected, Ethernet0/3
		L        172.16.6.2/32 is directly connected, Ethernet0/3
		R18#
