# Домашнее задание iBGP


## Цель:

### 1. Настроите iBGP в офисом Москва между маршрутизаторами R14 и R15.
### 2. Настроите iBGP в провайдере Триада, с использованием RR.
### 3. Настройте офиса Москва так, чтобы приоритетным провайдером стал Ламас.
### 4. Настройте офиса С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно.
### 5. Все сети в лабораторной работе должны иметь IP связность.


## Схема: 

![](https://github.com/tatujo2/networks/blob/main/screenshots/otus10.PNG)


### 1. Настроите iBGP в офисом Москва между маршрутизаторами R14 и R15.
 
			R14
			router bgp 1001
			 bgp log-neighbor-changes
			 neighbor 10.0.1.15 remote-as 1001
			 neighbor 10.0.1.15 update-source Loopback0

			R15
			router bgp 1001
			 bgp log-neighbor-changes
			 neighbor 10.0.1.14 remote-as 1001
			 neighbor 10.0.1.14 update-source Loopback0
 
 
### Проверка

			R14#sh ip bgp summary 
			BGP router identifier 10.0.1.14, local AS number 1001
			BGP table version is 3, main routing table version 3
			2 network entries using 280 bytes of memory
			2 path entries using 160 bytes of memory
			2/2 BGP path/bestpath attribute entries using 288 bytes of memory
			0 BGP route-map cache entries using 0 bytes of memory
			0 BGP filter-list cache entries using 0 bytes of memory
			BGP using 728 total bytes of memory
			BGP activity 2/0 prefixes, 2/0 paths, scan interval 60 secs

			Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
			10.0.1.15       4         1001      11      11        3    0    0 00:07:15        1

			R15# sh ip bgp summary 
			BGP router identifier 10.0.1.15, local AS number 1001
			BGP table version is 3, main routing table version 3
			2 network entries using 280 bytes of memory
			2 path entries using 160 bytes of memory
			2/2 BGP path/bestpath attribute entries using 288 bytes of memory
			0 BGP route-map cache entries using 0 bytes of memory
			0 BGP filter-list cache entries using 0 bytes of memory
			BGP using 728 total bytes of memory
			BGP activity 2/0 prefixes, 2/0 paths, scan interval 60 secs

			Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
			10.0.1.14       4         1001      12      12        3    0    0 00:08:04        1
 



### 2. Настроите iBGP в провайдере Триада, с использованием RR.

			R23#sh run | s bgp
			router bgp 520
			 bgp log-neighbor-changes
			 neighbor 10.2.1.24 remote-as 520
			 neighbor 10.2.1.24 update-source Loopback0
			 neighbor 10.2.1.24 route-reflector-client
			 neighbor 10.2.1.25 remote-as 520
			 neighbor 10.2.1.25 update-source Loopback0
			 neighbor 10.2.1.25 route-reflector-client
			 neighbor 10.2.1.26 remote-as 520
			 neighbor 10.2.1.26 update-source Loopback0
			 neighbor 10.2.1.26 route-reflector-client
			 
			 
			R24#sh run | s bgp
			router bgp 520
			 bgp log-neighbor-changes
			 neighbor 10.2.1.23 remote-as 520
			 neighbor 10.2.1.23 update-source Loopback0
			 
			 
			R25#sh run | s bg
			router bgp 520
			 bgp log-neighbor-changes
			 neighbor 10.2.1.23 remote-as 520
			 neighbor 10.2.1.23 update-source Loopback0
			 
			R26#sh run | s bgp
			router bgp 520
			 bgp log-neighbor-changes
			 neighbor 10.2.1.23 remote-as 520
			 neighbor 10.2.1.23 update-source Loopback0


### Проверка:

			R23#sh ip bgp summary 
			BGP router identifier 10.2.1.23, local AS number 520
			BGP table version is 4, main routing table version 4
			3 network entries using 420 bytes of memory
			3 path entries using 240 bytes of memory
			1/1 BGP path/bestpath attribute entries using 144 bytes of memory
			0 BGP route-map cache entries using 0 bytes of memory
			0 BGP filter-list cache entries using 0 bytes of memory
			BGP using 804 total bytes of memory
			BGP activity 3/0 prefixes, 3/0 paths, scan interval 60 secs

			Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
			10.2.1.24       4          520      11      12        4    0    0 00:06:44        2
			10.2.1.25       4          520      10      12        4    0    0 00:06:43        0
			10.2.1.26       4          520      11      12        4    0    0 00:06:39        1


			R24#sh ip bgp summary 
			BGP router identifier 10.2.1.24, local AS number 520
			BGP table version is 4, main routing table version 4
			3 network entries using 420 bytes of memory
			3 path entries using 240 bytes of memory
			2/2 BGP path/bestpath attribute entries using 288 bytes of memory
			1 BGP rrinfo entries using 24 bytes of memory
			0 BGP route-map cache entries using 0 bytes of memory
			0 BGP filter-list cache entries using 0 bytes of memory
			BGP using 972 total bytes of memory
			BGP activity 3/0 prefixes, 3/0 paths, scan interval 60 secs

			Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
			10.2.1.23       4          520      12      11        4    0    0 00:07:31        1


			R25#sh ip bgp summary 
			BGP router identifier 10.2.1.25, local AS number 520
			BGP table version is 4, main routing table version 4
			3 network entries using 420 bytes of memory
			3 path entries using 240 bytes of memory
			1/1 BGP path/bestpath attribute entries using 144 bytes of memory
			2 BGP rrinfo entries using 48 bytes of memory
			0 BGP route-map cache entries using 0 bytes of memory
			0 BGP filter-list cache entries using 0 bytes of memory
			BGP using 852 total bytes of memory
			BGP activity 3/0 prefixes, 3/0 paths, scan interval 60 secs

			Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
			10.2.1.23       4          520      13      11        4    0    0 00:08:02        3

			R26#sh ip bgp summary 
			BGP router identifier 10.2.1.26, local AS number 520
			BGP table version is 4, main routing table version 4
			3 network entries using 420 bytes of memory
			3 path entries using 240 bytes of memory
			2/2 BGP path/bestpath attribute entries using 288 bytes of memory
			1 BGP rrinfo entries using 24 bytes of memory
			0 BGP route-map cache entries using 0 bytes of memory
			0 BGP filter-list cache entries using 0 bytes of memory
			BGP using 972 total bytes of memory
			BGP activity 3/0 prefixes, 3/0 paths, scan interval 60 secs

			Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
			10.2.1.23       4          520      14      13        4    0    0 00:08:23        2



### 3. Настройте офиса Москва так, чтобы приоритетным провайдером стал Ламас.

			R15#sh run | s bgp
			router bgp 1001
			 bgp log-neighbor-changes
			 bgp default local-preference 700
			 

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
			B        172.16.2.0/30 [200/0] via 172.16.1.2, 00:02:07
			B        172.16.3.0/30 [200/0] via 172.16.1.2, 00:02:07
			B        172.16.4.0/30 [200/0] via 172.16.1.2, 00:02:07
			B        172.16.5.0/30 [200/0] via 172.16.1.2, 00:02:07
			B        172.16.6.0/30 [200/0] via 172.16.1.2, 00:02:07


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
			B        172.16.0.0/30 [20/0] via 172.16.1.2, 00:32:15
			B        172.16.2.0/30 [20/0] via 172.16.1.2, 00:32:44
			B        172.16.3.0/30 [20/0] via 172.16.1.2, 00:32:15
			B        172.16.4.0/30 [20/0] via 172.16.1.2, 00:32:44
			B        172.16.5.0/30 [20/0] via 172.16.1.2, 00:32:44
			B        172.16.6.0/30 [20/0] via 172.16.1.2, 00:32:44



### 4. Настройте офиса С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно.

			R18#sh run | s bgp
			router bgp 2042
			 bgp log-neighbor-changes
			 neighbor 172.16.5.1 remote-as 520
			 neighbor 172.16.6.1 remote-as 520
			 maximum-paths 2
 
 
### Проверка:

			R18#sh ip bgp neighbors 172.16.4.2
			% No such neighbor
			R18#sh ip bgp 172.16.4.2          
			BGP routing table entry for 172.16.4.0/30, version 2
			Paths: (2 available, best #2, table default)
			Multipath: eBGP
			  Advertised to update-groups:
				 1         
			  Refresh Epoch 1
			  520
				172.16.6.1 from 172.16.6.1 (10.2.1.26)
				  Origin IGP, localpref 100, valid, external, multipath(oldest)
				  rx pathid: 0, tx pathid: 0
			  Refresh Epoch 1
			  520
				172.16.5.1 from 172.16.5.1 (10.2.1.24)
				  Origin IGP, metric 0, localpref 100, valid, external, multipath, best
				  rx pathid: 0, tx pathid: 0x0
				  


			R18#traceroute 172.16.4.2
			Type escape sequence to abort.
			Tracing the route to 172.16.4.2
			VRF info: (vrf in name/id, vrf out name/id)
			  1 172.16.6.1 [AS 520] 4 msec
				172.16.5.1 [AS 520] 4 msec
				172.16.6.1 [AS 520] 3 msec
	

### 5. Все сети в лабораторной работе должны иметь IP связность.


			R26#ping 172.16.0.1 source et0/3
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.0.1, timeout is 2 seconds:
			Packet sent with a source address of 172.16.6.1 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 1/2/3 ms
			R26#ping 172.16.2.1 source et0/3
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.2.1, timeout is 2 seconds:
			Packet sent with a source address of 172.16.6.1 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
			R26#ping 172.16.3.1 source et0/3 
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.3.1, timeout is 2 seconds:
			Packet sent with a source address of 172.16.6.1 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 1/2/4 ms
			R26#ping 172.16.4.1 source et0/3
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.4.1, timeout is 2 seconds:
			Packet sent with a source address of 172.16.6.1 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/4 ms
			R26#ping 172.16.5.1 source et0/3
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.5.1, timeout is 2 seconds:
			Packet sent with a source address of 172.16.6.1 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
			R26#ping 172.16.6.1 source et0/3
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.6.1, timeout is 2 seconds:
			Packet sent with a source address of 172.16.6.1 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 4/4/5 ms


			R14#ping 172.16.0.1 source et0/2
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.0.1, timeout is 2 seconds:
			Packet sent with a source address of 172.16.0.1 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 4/4/5 ms
			R14#ping 172.16.2.1 source et0/2
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.2.1, timeout is 2 seconds:
			Packet sent with a source address of 172.16.0.1 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
			R14#ping 172.16.3.1 source et0/2
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.3.1, timeout is 2 seconds:
			Packet sent with a source address of 172.16.0.1 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/4 ms
			R14#ping 172.16.4.1 source et0/2
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.4.1, timeout is 2 seconds:
			Packet sent with a source address of 172.16.0.1 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/3 ms
			R14#ping 172.16.5.1 source et0/2
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.5.1, timeout is 2 seconds:
			Packet sent with a source address of 172.16.0.1 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 2/4/7 ms
			R14#ping 172.16.6.1 source et0/2
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.6.1, timeout is 2 seconds:
			Packet sent with a source address of 172.16.0.1 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 2/3/4 ms


			R15#ping 172.16.0.2 source et0/2
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.0.2, timeout is 2 seconds:
			Packet sent with a source address of 172.16.1.1 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
			R15#ping 172.16.1.2 source et0/2
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.1.2, timeout is 2 seconds:
			Packet sent with a source address of 172.16.1.1 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/3 ms
			R15#ping 172.16.2.2 source et0/2
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.2.2, timeout is 2 seconds:
			Packet sent with a source address of 172.16.1.1 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
			R15#ping 172.16.3.2 source et0/2
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.3.2, timeout is 2 seconds:
			Packet sent with a source address of 172.16.1.1 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 1/2/4 ms
			R15#ping 172.16.4.2 source et0/2
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.4.2, timeout is 2 seconds:
			Packet sent with a source address of 172.16.1.1 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
			R15#ping 172.16.5.2 source et0/2
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.5.2, timeout is 2 seconds:
			Packet sent with a source address of 172.16.1.1 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 2/2/3 ms
			R15#ping 172.16.6.2 source et0/2
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.6.2, timeout is 2 seconds:
			Packet sent with a source address of 172.16.1.1 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 2/2/2 ms
