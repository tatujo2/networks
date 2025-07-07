
# Lab - Configure DHCPv6

# Topology

![](https://github.com/tatujo2/networks/blob/main/screenshots/1.png)

## Addressing Table

| Device    | Interface | IP Address		 | 
|-----------|-----------|------------------------|
|R1	    | Eth0/0    | 2001:db8:acad:2::1 /64 |		
|	    |      	|	fe80::1          |		
|	    | Eth0/1 	| 2001:db8:acad:1::1/64	 | 
|	    |  		|  	fe80::1		 |
|R2 	    | Eth0/0	| 2001:db8:acad:2::2/64  |
|           |           |	fe80::2          | 
|	    | Eth0/1	| 2001:db8:acad:3::1/64  |
|           |           |	fe80::1          | 
|PC1	    |NIC1 	|DHCP   		 |
|PC2	    |NIC1       |DHCP           	 |


# Build the Network and Configure Basic Device Settings

## Router1

		interface Ethernet0/0
		no ip address
		ipv6 address FE80::1 link-local
		ipv6 address 2001:DB8:ACAD:2::1/64
		ipv6 enable
		!
		interface Ethernet0/1
		no ip address
		ipv6 address FE80::1 link-local
		ipv6 address 2001:DB8:ACAD:1::1/64
		ipv6 enable

		!
		## Router2

		interface Ethernet0/0
		 no ip address
		 ipv6 address FE80::2 link-local
		 ipv6 address 2001:DB8:ACAD:2::2/64
		 ipv6 enable
		!
		interface Ethernet0/1
		 no ip address
		 ipv6 address FE80::1 link-local
		 ipv6 address 2001:DB8:ACAD:3::1/64
		 ipv6 enable

# Configure a stateful DHCPv6 server on R1

## Router1

		ipv6 dhcp pool DHCPv6
		 address prefix 2001:DB8:ACAD:1::/64
		 dns-server 2001:DB8:ACAD::8888
		 domain-name google
		 
		ipv6 dhcp pool DHCPv6_2
		 address prefix 2001:DB8:ACAD:3::/64
		 dns-server 2001:DB8:ACAD::7777
		 domain-name ya.ru
		 
		interface Ethernet0/1
		 ipv6 nd managed-config-flag
		 ipv6 nd other-config-flag
		 ipv6 dhcp server DHCPv6

# Configure and verify DHCPv6 relay on R2.

## Router2

		ipv6 dhcp relay destination  2001:DB8:ACAD:2::1
 
# Test connectivity by pinging R1’s G0/0/1 interface IP address.


		Router>ping 2001:db8:acad:3:2050:79ff:fe66:6806
		Type escape sequence to abort.
		Sending 5, 100-byte ICMP Echos to 2001:DB8:ACAD:3:2050:79FF:FE66:6806, timeout is 2 seconds:
		!!!!!
		Success rate is 100 percent (5/5), round-trip min/avg/max = 2/5/15 ms


