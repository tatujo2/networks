# Домашнее задание BGP. Фильтрация



## Цель:

### 1. Настроить фильтрацию в офисе Москва так, чтобы не появилось транзитного трафика(As-path).
### 2. Настроить фильтрацию в офисе С.-Петербург так, чтобы не появилось транзитного трафика(Prefix-list).
### 3. Настроить провайдера Киторн так, чтобы в офис Москва отдавался только маршрут по умолчанию.
### 4. Настроить провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по умолчанию и префикс офиса С.-Петербург.
### 5. Все сети в лабораторной работе должны иметь IP связность.



## Схема:

![](https://github.com/tatujo2/networks/blob/main/screenshots/otus10.PNG)



### 1. Настроить фильтрацию в офисе Москва так, чтобы не появилось транзитного трафика(As-path).

		R14#sh run | s bgp   
		router bgp 1001
		 bgp log-neighbor-changes
		 network 172.16.0.0 mask 255.255.255.252
		 neighbor 10.0.1.15 remote-as 1001
		 neighbor 10.0.1.15 update-source Loopback0
		 neighbor 10.0.1.15 soft-reconfiguration inbound
		 neighbor 172.16.0.2 remote-as 101
		 neighbor 172.16.0.2 soft-reconfiguration inbound
		 neighbor 172.16.0.2 filter-list 1 out
		 
		ip as-path access-list 1 permit ^$

		R15#sh run | s bgp
		router bgp 1001
		 bgp log-neighbor-changes
		 bgp default local-preference 700
		 network 172.16.1.0 mask 255.255.255.252
		 neighbor 10.0.1.14 remote-as 1001
		 neighbor 10.0.1.14 update-source Loopback0
		 neighbor 10.0.1.14 soft-reconfiguration inbound
		 neighbor 172.16.1.2 remote-as 301
		 neighbor 172.16.1.2 soft-reconfiguration inbound
		 neighbor 172.16.1.2 filter-list 1 out
		 
		ip as-path access-list 1 permit ^$


### Проверка:

		R22#sh ip bgp neighbors 172.16.0.1 received-routes 
		BGP table version is 13, local router ID is 172.16.3.1
		Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
					  r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
					  x best-external, a additional-path, c RIB-compressed, 
		Origin codes: i - IGP, e - EGP, ? - incomplete
		RPKI validation codes: V valid, I invalid, N Not found

			 Network          Next Hop            Metric LocPrf Weight Path
		 *   172.16.0.0/30    172.16.0.1               0             0 1001 i
		 *   172.16.1.0/30    172.16.0.1                             0 1001 i
		 
		R21#sh ip bgp neighbors 172.16.1.1 received-routes 
		BGP table version is 13, local router ID is 172.16.4.1
		Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
					  r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
					  x best-external, a additional-path, c RIB-compressed, 
		Origin codes: i - IGP, e - EGP, ? - incomplete
		RPKI validation codes: V valid, I invalid, N Not found

			 Network          Next Hop            Metric LocPrf Weight Path
		 *   172.16.1.0/30    172.16.1.1               0             0 1001 i
 
 
 
### 2. Настроить фильтрацию в офисе С.-Петербург так, чтобы не появилось транзитного трафика(Prefix-list).
 
		R18#
		 neighbor 172.16.5.1 prefix-list R18->R24R26 out
		 neighbor 172.16.6.1 prefix-list R18->R24R26 out
		 
		 ip prefix-list R18->R24R26 seq 5 permit 172.16.6.0/30
		 ip prefix-list R18->R24R26 seq 10 permit 172.16.5.0/30
 
 
### Проверка:

		R18#sh ip bgp neighbors 172.16.5.1 advertised-routes 
		BGP table version is 17, local router ID is 10.1.1.18
		Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
					  r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
					  x best-external, a additional-path, c RIB-compressed, 
		Origin codes: i - IGP, e - EGP, ? - incomplete
		RPKI validation codes: V valid, I invalid, N Not found

			 Network          Next Hop            Metric LocPrf Weight Path
		 r>  172.16.5.0/30    172.16.5.1               0             0 520 i
		 r>  172.16.6.0/30    172.16.5.1                             0 520 i

		Total number of prefixes 2 

		R18#sh ip bgp neighbors 172.16.6.1 advertised-routes 
		BGP table version is 17, local router ID is 10.1.1.18
		Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
					  r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
					  x best-external, a additional-path, c RIB-compressed, 
		Origin codes: i - IGP, e - EGP, ? - incomplete
		RPKI validation codes: V valid, I invalid, N Not found

			 Network          Next Hop            Metric LocPrf Weight Path
		 r>  172.16.5.0/30    172.16.5.1               0             0 520 i
		 r>  172.16.6.0/30    172.16.5.1                             0 520 i

		Total number of prefixes 2 

 
 
### 3. Настроить провайдера Киторн так, чтобы в офис Москва отдавался только маршрут по умолчанию.

		R22#
		 neighbor 172.16.0.1 remote-as 1001
		 neighbor 172.16.0.1 description Moskva
		 neighbor 172.16.0.1 default-originate
		 neighbor 172.16.0.1 route-map ROUTE_DEFAULT out
		 
		 ip route 0.0.0.0 0.0.0.0 Null0 permanent
		 
		 ip prefix-list ROUTE_DEFAULT seq 5 permit 0.0.0.0/0
		 
		 route-map ROUTE_DEFAULT permit 10
		 match ip address prefix-list ROUTE_DEFAULT

		 route-map ROUTE_DEFAULT deny 20
 
 
### Проверка:

		R14#sh ip bgp neighbors 172.16.0.2 received-routes 
		BGP table version is 7, local router ID is 10.0.1.14
		Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
					  r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
					  x best-external, a additional-path, c RIB-compressed, 
		Origin codes: i - IGP, e - EGP, ? - incomplete
		RPKI validation codes: V valid, I invalid, N Not found

			 Network          Next Hop            Metric LocPrf Weight Path
		 *   0.0.0.0          172.16.0.2                             0 101 i

		Total number of prefixes 1 



### 4. Настроить провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по умолчанию и префикс офиса С.-Петербург.

		R21#
		 neighbor 172.16.1.1 route-map ROUTE_DEFAULT out
		 
		 ip prefix-list ROUTE_DEFAULT seq 5 permit 0.0.0.0/0
		 ip prefix-list ROUTE_DEFAULT seq 10 permit 172.16.5.0/30
		 ip prefix-list ROUTE_DEFAULT seq 15 permit 172.16.6.0/30

		 route-map ROUTE_DEFAULT permit 10
		  match ip address prefix-list ROUTE_DEFAULT
  
  
### Проверка:

		R15#sh ip bgp neighbors 172.16.1.2 received-routes 
		BGP table version is 7, local router ID is 10.0.1.15
		Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
					  r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
					  x best-external, a additional-path, c RIB-compressed, 
		Origin codes: i - IGP, e - EGP, ? - incomplete
		RPKI validation codes: V valid, I invalid, N Not found

			 Network          Next Hop            Metric LocPrf Weight Path
		 *>  0.0.0.0          172.16.1.2                             0 301 101 i
		 *>  172.16.5.0/30    172.16.1.2                             0 301 520 i
		 *>  172.16.6.0/30    172.16.1.2                             0 301 520 i



# 5. Все сети в лабораторной работе должны иметь IP связность.

		R15#ping 172.16.0.1
		Type escape sequence to abort.
		Sending 5, 100-byte ICMP Echos to 172.16.0.1, timeout is 2 seconds:
		!!!!!
		Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
		R15#ping 172.16.1.1
		Type escape sequence to abort.
		Sending 5, 100-byte ICMP Echos to 172.16.1.1, timeout is 2 seconds:
		!!!!!
		Success rate is 100 percent (5/5), round-trip min/avg/max = 4/4/5 ms
		R15#ping 172.16.2.1
		Type escape sequence to abort.
		Sending 5, 100-byte ICMP Echos to 172.16.2.1, timeout is 2 seconds:
		!!!!!
		Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
		R15#ping 172.16.3.1
		Type escape sequence to abort.
		Sending 5, 100-byte ICMP Echos to 172.16.3.1, timeout is 2 seconds:
		!!!!!
		Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
		R15#ping 172.16.4.1
		Type escape sequence to abort.
		Sending 5, 100-byte ICMP Echos to 172.16.4.1, timeout is 2 seconds:
		!!!!!
		Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
		R15#ping 172.16.5.1
		Type escape sequence to abort.
		Sending 5, 100-byte ICMP Echos to 172.16.5.1, timeout is 2 seconds:
		!!!!!
		Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/4 ms
		R15#ping 172.16.6.1
		Type escape sequence to abort.
		Sending 5, 100-byte ICMP Echos to 172.16.6.1, timeout is 2 seconds:
		!!!!!
		Success rate is 100 percent (5/5), round-trip min/avg/max = 1/2/5 ms

		R14#ping 172.16.0.2 source et0/2
		Type escape sequence to abort.
		Sending 5, 100-byte ICMP Echos to 172.16.0.2, timeout is 2 seconds:
		Packet sent with a source address of 172.16.0.1 
		!!!!!
		Success rate is 100 percent (5/5), round-trip min/avg/max = 1/2/3 ms
		R14#ping 172.16.1.2 source et0/2
		Type escape sequence to abort.
		Sending 5, 100-byte ICMP Echos to 172.16.1.2, timeout is 2 seconds:
		Packet sent with a source address of 172.16.0.1 
		!!!!!
		Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/5 ms
		R14#ping 172.16.2.2 source et0/2
		Type escape sequence to abort.
		Sending 5, 100-byte ICMP Echos to 172.16.2.2, timeout is 2 seconds:
		Packet sent with a source address of 172.16.0.1 
		!!!!!
		Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/3 ms
		R14#ping 172.16.3.2 source et0/2
		Type escape sequence to abort.
		Sending 5, 100-byte ICMP Echos to 172.16.3.2, timeout is 2 seconds:
		Packet sent with a source address of 172.16.0.1 
		!!!!!
		Success rate is 100 percent (5/5), round-trip min/avg/max = 2/2/4 ms
		R14#ping 172.16.4.2 source et0/2
		Type escape sequence to abort.
		Sending 5, 100-byte ICMP Echos to 172.16.4.2, timeout is 2 seconds:
		Packet sent with a source address of 172.16.0.1 
		!!!!!
		Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
		R14#ping 172.16.5.2 source et0/2
		Type escape sequence to abort.
		Sending 5, 100-byte ICMP Echos to 172.16.5.2, timeout is 2 seconds:
		Packet sent with a source address of 172.16.0.1 
		!!!!!
		Success rate is 100 percent (5/5), round-trip min/avg/max = 2/2/3 ms
		R14#ping 172.16.6.2 source et0/2
		Type escape sequence to abort.
		Sending 5, 100-byte ICMP Echos to 172.16.6.2, timeout is 2 seconds:
		Packet sent with a source address of 172.16.0.1 
		!!!!!
		Success rate is 100 percent (5/5), round-trip min/avg/max = 2/2/4 ms
