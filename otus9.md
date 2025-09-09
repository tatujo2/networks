# Домашнее задание. BGP. Основы.

# Цель:  Настроить BGP между автономными системами 
# Организовать доступность между офисами Москва и С.-Петербург

## Задачи:

### 1. Настроите eBGP между офисом Москва и двумя провайдерами - Киторн и Ламас.
### 2. Настроите eBGP между провайдерами Киторн и Ламас.
### 3. Настроите eBGP между Ламас и Триада.
### 4. Настроите eBGP между офисом С.-Петербург и провайдером Триада.
### 5. Организуете IP доступность между пограничным роутерами офисами Москва и С.-Петербург.




## Схема: 
![](https://github.com/tatujo2/networks/blob/main/screenshots/otus9.PNG)



### 1. Настроите eBGP между офисом Москва и двумя провайдерами - Киторн и Ламас.

		R14#
		router bgp 1001
		 bgp log-neighbor-changes
		 neighbor 172.16.0.2 remote-as 101
		 
		 
		R15#
		router bgp 1001
		 bgp log-neighbor-changes
		 neighbor 172.16.1.2 remote-as 301
		 
		 
		R22#                         
		router bgp 101
		 bgp log-neighbor-changes
		 neighbor 172.16.0.1 remote-as 1001
		 
		 
		R21#
		router bgp 301
		 bgp log-neighbor-changes
		 neighbor 172.16.1.1 remote-as 1001
		
		
### Проверка:

		R15# sh ip bgp summary 
		BGP router identifier 172.16.1.1, local AS number 1001
		BGP table version is 10, main routing table version 10
		7 network entries using 980 bytes of memory
		8 path entries using 640 bytes of memory
		5/5 BGP path/bestpath attribute entries using 720 bytes of memory
		4 BGP AS-PATH entries using 96 bytes of memory
		0 BGP route-map cache entries using 0 bytes of memory
		0 BGP filter-list cache entries using 0 bytes of memory
		BGP using 2436 total bytes of memory
		BGP activity 7/0 prefixes, 9/1 paths, scan interval 60 secs

		Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
		172.16.1.2      4          301      75      71       10    0    0 01:01:28        7
		
		
		R14#sh ip bgp summary 
		BGP router identifier 172.16.0.1, local AS number 1001
		BGP table version is 28, main routing table version 28
		7 network entries using 980 bytes of memory
		8 path entries using 640 bytes of memory
		5/5 BGP path/bestpath attribute entries using 720 bytes of memory
		4 BGP AS-PATH entries using 112 bytes of memory
		0 BGP route-map cache entries using 0 bytes of memory
		0 BGP filter-list cache entries using 0 bytes of memory
		BGP using 2452 total bytes of memory
		BGP activity 17/10 prefixes, 18/10 paths, scan interval 60 secs

		Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
		172.16.0.2      4          101      81      74       28    0    0 01:03:08        7
		
		
		R22#sh ip bgp summary 
		BGP router identifier 172.16.3.1, local AS number 101
		BGP table version is 38, main routing table version 38
		7 network entries using 980 bytes of memory
		9 path entries using 720 bytes of memory
		5/4 BGP path/bestpath attribute entries using 720 bytes of memory
		4 BGP AS-PATH entries using 96 bytes of memory
		0 BGP route-map cache entries using 0 bytes of memory
		0 BGP filter-list cache entries using 0 bytes of memory
		BGP using 2516 total bytes of memory
		BGP activity 12/5 prefixes, 22/13 paths, scan interval 60 secs

		Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
		172.16.0.1      4         1001      65      72       38    0    0 00:55:04        1


		R21#sh ip bgp summary 
		BGP router identifier 172.16.4.1, local AS number 301
		BGP table version is 10, main routing table version 10
		7 network entries using 980 bytes of memory
		10 path entries using 800 bytes of memory
		5/4 BGP path/bestpath attribute entries using 720 bytes of memory
		4 BGP AS-PATH entries using 96 bytes of memory
		0 BGP route-map cache entries using 0 bytes of memory
		0 BGP filter-list cache entries using 0 bytes of memory
		BGP using 2596 total bytes of memory
		BGP activity 7/0 prefixes, 11/1 paths, scan interval 60 secs

		Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
		172.16.1.1      4         1001      74      78       10    0    0 01:04:09        1
		



### 2. Настроите eBGP между провайдерами Киторн и Ламас.


R22#   
		router bgp 101
		 bgp log-neighbor-changes
		 neighbor 172.16.2.2 remote-as 301
		 
		 
		R21#
		router bgp 301
		 bgp log-neighbor-changes
		 neighbor 172.16.2.1 remote-as 101



### Проверка:


		R21#sh ip bgp summary        
		BGP router identifier 172.16.4.1, local AS number 301
		BGP table version is 10, main routing table version 10
		7 network entries using 980 bytes of memory
		10 path entries using 800 bytes of memory
		5/4 BGP path/bestpath attribute entries using 720 bytes of memory
		4 BGP AS-PATH entries using 96 bytes of memory
		0 BGP route-map cache entries using 0 bytes of memory
		0 BGP filter-list cache entries using 0 bytes of memory
		BGP using 2596 total bytes of memory
		BGP activity 7/0 prefixes, 11/1 paths, scan interval 60 secs

		Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
		172.16.2.1      4          101      63      63       10    0    0 00:47:45        3

		
		R22#sh ip bgp summary 
		BGP router identifier 172.16.3.1, local AS number 101
		BGP table version is 38, main routing table version 38
		7 network entries using 980 bytes of memory
		9 path entries using 720 bytes of memory
		5/4 BGP path/bestpath attribute entries using 720 bytes of memory
		4 BGP AS-PATH entries using 96 bytes of memory
		0 BGP route-map cache entries using 0 bytes of memory
		0 BGP filter-list cache entries using 0 bytes of memory
		BGP using 2516 total bytes of memory
		BGP activity 12/5 prefixes, 22/13 paths, scan interval 60 secs

		Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
		172.16.2.2      4          301      65      65       38    0    0 00:49:53        5




### 3. Настроите eBGP между Ламас и Триада.

		R21#
		router bgp 301
		 bgp log-neighbor-changes
		 neighbor 172.16.4.2 remote-as 520
		 
		 
		R24#
		router bgp 520
		 bgp log-neighbor-changes
		 neighbor 172.16.4.1 remote-as 301
		

		
### Проверка:

		R21#
		BGP router identifier 172.16.4.1, local AS number 301
		BGP table version is 10, main routing table version 10
		7 network entries using 980 bytes of memory
		10 path entries using 800 bytes of memory
		5/4 BGP path/bestpath attribute entries using 720 bytes of memory
		4 BGP AS-PATH entries using 96 bytes of memory
		0 BGP route-map cache entries using 0 bytes of memory
		0 BGP filter-list cache entries using 0 bytes of memory
		BGP using 2596 total bytes of memory
		BGP activity 7/0 prefixes, 11/1 paths, scan interval 60 secs

		Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
		172.16.4.2      4          520      79      78       10    0    0 01:04:08        3


		R24#
		BGP router identifier 10.2.1.24, local AS number 520
		BGP table version is 16, main routing table version 16
		7 network entries using 980 bytes of memory
		9 path entries using 720 bytes of memory
		4/4 BGP path/bestpath attribute entries using 576 bytes of memory
		3 BGP AS-PATH entries using 72 bytes of memory
		0 BGP route-map cache entries using 0 bytes of memory
		0 BGP filter-list cache entries using 0 bytes of memory
		BGP using 2348 total bytes of memory
		BGP activity 10/3 prefixes, 14/5 paths, scan interval 60 secs

		Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
		172.16.4.1      4          301      83      85       16    0    0 01:09:30        5




### 4. Настроите eBGP между офисом С.-Петербург и провайдером Триада.

		R24#
		router bgp 520
		 bgp log-neighbor-changes
		 neighbor 172.16.5.2 remote-as 2042
		 
		R26#
		router bgp 520
		 bgp log-neighbor-changes
		 neighbor 172.16.6.2 remote-as 2042
		 
		R18#
		router bgp 2042
		 bgp log-neighbor-changes
		 neighbor 172.16.5.1 remote-as 520
		 neighbor 172.16.6.1 remote-as 520



### Проверка:

		R24#sh ip bgp summary 
		BGP router identifier 10.2.1.24, local AS number 520
		BGP table version is 16, main routing table version 16
		7 network entries using 980 bytes of memory
		9 path entries using 720 bytes of memory
		4/4 BGP path/bestpath attribute entries using 576 bytes of memory
		3 BGP AS-PATH entries using 72 bytes of memory
		0 BGP route-map cache entries using 0 bytes of memory
		0 BGP filter-list cache entries using 0 bytes of memory
		BGP using 2348 total bytes of memory
		BGP activity 10/3 prefixes, 14/5 paths, scan interval 60 secs

		Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
		172.16.5.2      4         2042      97     104       16    0    0 01:20:11        2


		R26#sh ip bgp summary 
		BGP router identifier 10.2.1.26, local AS number 520
		BGP table version is 3, main routing table version 3
		2 network entries using 280 bytes of memory
		3 path entries using 240 bytes of memory
		2/2 BGP path/bestpath attribute entries using 288 bytes of memory
		1 BGP AS-PATH entries using 24 bytes of memory
		0 BGP route-map cache entries using 0 bytes of memory
		0 BGP filter-list cache entries using 0 bytes of memory
		BGP using 832 total bytes of memory
		BGP activity 2/0 prefixes, 3/0 paths, scan interval 60 secs

		Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
		172.16.6.2      4         2042     103      97        3    0    0 01:24:03        2


		R18#sh ip bgp summary 
		BGP router identifier 10.1.1.18, local AS number 2042
		BGP table version is 16, main routing table version 16
		7 network entries using 980 bytes of memory
		9 path entries using 720 bytes of memory
		4/4 BGP path/bestpath attribute entries using 576 bytes of memory
		3 BGP AS-PATH entries using 72 bytes of memory
		0 BGP route-map cache entries using 0 bytes of memory
		0 BGP filter-list cache entries using 0 bytes of memory
		BGP using 2348 total bytes of memory
		BGP activity 11/4 prefixes, 13/4 paths, scan interval 60 secs

		Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
		172.16.5.1      4          520     111     104       16    0    0 01:26:29        6
		172.16.6.1      4          520      99     105       16    0    0 01:26:23        1




### 5. Организуете IP доступность между пограничным роутерами офисами Москва и С.-Петербург.
		
		Анонсирум сети:
		
		R15# sh run | s bgp
		router bgp 1001
		 bgp log-neighbor-changes
		 network 172.16.1.0 mask 255.255.255.252
		 neighbor 172.16.1.2 remote-as 301
		 
		 
		R14#sh run | s bgp
		router bgp 1001
		 bgp log-neighbor-changes
		 network 172.16.0.0 mask 255.255.255.252
		 neighbor 172.16.0.2 remote-as 101
		 
		 
		R22#sh run | s bgp
		router bgp 101
		 bgp log-neighbor-changes
		 network 172.16.0.0 mask 255.255.255.252
		 network 172.16.2.0 mask 255.255.255.252
		 network 172.16.3.0 mask 255.255.255.252
		 neighbor 172.16.0.1 remote-as 1001
		 neighbor 172.16.2.2 remote-as 301
		 
		 
		R21#sh run | s bgp
		router bgp 301
		 bgp log-neighbor-changes
		 network 172.16.1.0 mask 255.255.255.252
		 network 172.16.2.0 mask 255.255.255.252
		 network 172.16.4.0 mask 255.255.255.252
		 neighbor 172.16.1.1 remote-as 1001
		 neighbor 172.16.2.1 remote-as 101
		 neighbor 172.16.4.2 remote-as 520


		R24#sh run | s bgp
		router bgp 520
		 bgp log-neighbor-changes
		 network 172.16.4.0 mask 255.255.255.252
		 network 172.16.5.0 mask 255.255.255.252
		 neighbor 172.16.4.1 remote-as 301
		 neighbor 172.16.5.2 remote-as 2042
		 
		 
		R26#sh run | s bgp
		router bgp 520
		 bgp log-neighbor-changes
		 network 172.16.6.0 mask 255.255.255.252
		 neighbor 172.16.6.2 remote-as 2042
		 
		 
		R18#sh run | s bgp
		router bgp 2042
		 bgp log-neighbor-changes
		 network 172.16.5.0 mask 255.255.255.252
		 network 172.16.6.0 mask 255.255.255.252
		 neighbor 172.16.5.1 remote-as 520
		 neighbor 172.16.6.1 remote-as 520
		 
		 
		 
		 
### Проверка:

		R14#sh ip route bgp 
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

			  172.16.0.0/16 is variably subnetted, 8 subnets, 2 masks
		B        172.16.1.0/30 [20/0] via 172.16.0.2, 01:18:07
		B        172.16.2.0/30 [20/0] via 172.16.0.2, 01:18:07
		B        172.16.3.0/30 [20/0] via 172.16.0.2, 01:18:07
		B        172.16.4.0/30 [20/0] via 172.16.0.2, 01:18:07
		B        172.16.5.0/30 [20/0] via 172.16.0.2, 01:18:07
		B        172.16.6.0/30 [20/0] via 172.16.0.2, 01:18:07

		R14#ping 172.16.6.2
		Type escape sequence to abort.
		Sending 5, 100-byte ICMP Echos to 172.16.6.2, timeout is 2 seconds:
		!!!!!
		Success rate is 100 percent (5/5), round-trip min/avg/max = 2/2/2 ms
		R14#ping 172.16.5.2
		Type escape sequence to abort.
		Sending 5, 100-byte ICMP Echos to 172.16.5.2, timeout is 2 seconds:
		!!!!!
		Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/3 ms


		R15#sh ip route bgp 
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

			  172.16.0.0/16 is variably subnetted, 8 subnets, 2 masks
		B        172.16.0.0/30 [20/0] via 172.16.1.2, 01:19:51
		B        172.16.2.0/30 [20/0] via 172.16.1.2, 01:23:22
		B        172.16.3.0/30 [20/0] via 172.16.1.2, 01:19:51
		B        172.16.4.0/30 [20/0] via 172.16.1.2, 01:23:22
		B        172.16.5.0/30 [20/0] via 172.16.1.2, 01:23:22
		B        172.16.6.0/30 [20/0] via 172.16.1.2, 01:23:22


		R15#ping 172.16.6.2
		Type escape sequence to abort.
		Sending 5, 100-byte ICMP Echos to 172.16.6.2, timeout is 2 seconds:
		!!!!!
		Success rate is 100 percent (5/5), round-trip min/avg/max = 1/3/4 ms
		R15#ping 172.16.5.2
		Type escape sequence to abort.
		Sending 5, 100-byte ICMP Echos to 172.16.5.2, timeout is 2 seconds:
		!!!!!
		Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms


		R18#sh ip route bgp 
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

			  172.16.0.0/16 is variably subnetted, 9 subnets, 2 masks
		B        172.16.0.0/30 [20/0] via 172.16.5.1, 01:21:37
		B        172.16.1.0/30 [20/0] via 172.16.5.1, 01:25:08
		B        172.16.2.0/30 [20/0] via 172.16.5.1, 01:25:08
		B        172.16.3.0/30 [20/0] via 172.16.5.1, 01:21:37
		B        172.16.4.0/30 [20/0] via 172.16.5.1, 01:35:58


		R18#ping 172.16.0.1
		Type escape sequence to abort.
		Sending 5, 100-byte ICMP Echos to 172.16.0.1, timeout is 2 seconds:
		!!!!!
		Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/3 ms
		R18#ping 172.16.1.1
		Type escape sequence to abort.
		Sending 5, 100-byte ICMP Echos to 172.16.1.1, timeout is 2 seconds:
		!!!!!
		Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms

