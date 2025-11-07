# VPN. GRE. DmVPN

## Цель:

### 1. Настроите GRE между офисами Москва и С.-Петербург.
### 2. Настроите DMVMN между Москва и Чокурдах, Лабытнанги.


## Схема:

### 1. Настроите GRE между офисами Москва и С.-Петербург.


			R18

			interface Tunnel0
			 ip address 192.168.30.2 255.255.255.252
			 ip mtu 1400
			 ip tcp adjust-mss 1360
			 tunnel source 172.16.5.2
			 tunnel destination 172.16.1.1
			 
			ip route 10.0.0.0 255.255.0.0 192.168.30.1


			R15

			interface Tunnel0
			 ip address 192.168.30.1 255.255.255.252
			 ip mtu 1400
			 ip tcp adjust-mss 1360
			 tunnel source 172.16.1.1
			 tunnel destination 172.16.5.2

			ip route 10.1.0.0 255.255.0.0 192.168.30.2

### Проверка

			VPC8> ping 10.0.10.3 

			84 bytes from 10.0.10.3 icmp_seq=1 ttl=59 time=5.066 ms
			84 bytes from 10.0.10.3 icmp_seq=2 ttl=59 time=4.768 ms
			84 bytes from 10.0.10.3 icmp_seq=3 ttl=59 time=5.975 ms
			84 bytes from 10.0.10.3 icmp_seq=4 ttl=59 time=7.420 ms
			84 bytes from 10.0.10.3 icmp_seq=5 ttl=59 time=5.126 ms

			VPC8> trace 10.0.10.3
			trace to 10.0.10.3, 8 hops max, press Ctrl+C to stop
			 1   10.1.40.1   1.106 ms  0.961 ms  0.787 ms
			 2   10.1.0.1   1.848 ms  1.467 ms  1.550 ms
			 3   192.168.30.1   4.676 ms  5.098 ms  13.636 ms
			 4   10.0.0.14   5.618 ms  8.004 ms  51.562 ms
			 5   10.0.0.22   20.769 ms  3.261 ms  2.959 ms
			 6   *10.0.10.3   9.350 ms (ICMP type:3, code:3, Destination port unreachable)

			VPC1> ping 10.1.40.3

			84 bytes from 10.1.40.3 icmp_seq=1 ttl=59 time=6.844 ms
			84 bytes from 10.1.40.3 icmp_seq=2 ttl=59 time=5.090 ms
			84 bytes from 10.1.40.3 icmp_seq=3 ttl=59 time=4.208 ms
			84 bytes from 10.1.40.3 icmp_seq=4 ttl=59 time=4.050 ms
			84 bytes from 10.1.40.3 icmp_seq=5 ttl=59 time=4.600 ms

			VPC1> trace 10.1.40.3
			trace to 10.1.40.3, 8 hops max, press Ctrl+C to stop
			 1   10.0.10.1   0.933 ms  0.756 ms  0.766 ms
			 2   10.0.0.37   1.429 ms  1.172 ms  1.453 ms
			 3   10.0.0.25   1.965 ms  1.596 ms  1.548 ms
			 4   192.168.30.2   12.344 ms  8.450 ms  7.216 ms
			 5   10.1.0.6   5.786 ms  3.554 ms  8.485 ms
			 6   *10.1.40.3   4.715 ms (ICMP type:3, code:3, Destination port unreachable)



### 2. Настроите DMVMN между Москва и Чокурдах, Лабытнанги.


			R14
			interface Tunnel0
			 ip address 172.16.55.1 255.255.255.0
			 no ip redirects
			 ip mtu 1400
			 ip nhrp map multicast dynamic
			 ip nhrp network-id 999
			 ip tcp adjust-mss 1360
			 tunnel source Ethernet0/2
			 tunnel mode gre multipoint
			 tunnel key 999
			 
			R27
			interface Tunnel0
			 ip address 172.16.55.2 255.255.255.0
			 no ip redirects
			 ip nhrp map 172.16.55.1 172.16.0.1
			 ip nhrp map multicast 172.16.0.1
			 ip nhrp network-id 999
			 ip nhrp nhs 172.16.55.1
			 tunnel source Ethernet0/0
			 tunnel mode gre multipoint
			 tunnel key 999
			 
			R28
			interface Tunnel0
			 ip address 172.16.55.3 255.255.255.0
			 no ip redirects
			 ip nhrp map 172.16.55.1 172.16.0.1
			 ip nhrp map multicast 172.16.0.1
			 ip nhrp network-id 999
			 ip nhrp nhs 172.16.55.1
			 tunnel source Ethernet0/0
			 tunnel mode gre multipoint
			 tunnel key 999

### Проверка


			R14#ping 172.16.55.2
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.55.2, timeout is 2 seconds:
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 2/2/4 ms

			R14#ping 172.16.55.3
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.55.3, timeout is 2 seconds:
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 3/3/5 ms

			R14#traceroute 172.16.55.2
			Type escape sequence to abort.
			Tracing the route to 172.16.55.2
			VRF info: (vrf in name/id, vrf out name/id)
			  1 172.16.55.2 [AS 101] 10 msec *  9 msec

			R14#traceroute 172.16.55.3
			Type escape sequence to abort.
			Tracing the route to 172.16.55.3
			VRF info: (vrf in name/id, vrf out name/id)
			  1 172.16.55.3 [AS 101] 9 msec *  7 msec
			R14#
