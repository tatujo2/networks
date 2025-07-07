# Lab - Implement DHCPv4

## Topology
![](https://github.com/tatujo2/networks/blob/main/screenshots/1.png)

## Addressing Table

| Device    | Interface | IP Address| Subnet Mask  | Default Gateway  |
|-----------|-----------|-----------|--------------|------------------|
|R1		    	|Eth0/0     | 10.0.0.1  | 255.255.255.0|		N/a		  |
|		      	|Eth0/1	    |	N/a	    	|		N/a	   |		N/a		  |
|		      	|Eth0/1.100 	|192.168.100.1| 255.255.255.0|		N/a		  |
|	      		|Eth0/1.200   |192.168.200.1|	255.255.255.0		   |		N/a		  |
|		      	|Eth0/1.1000   |N/a        |  N/a		   |		N/a		  |
|R2         |Eth0/0     |Eth0/0     | 10.0.0.2  | 255.255.255.0|		N/a		  |
|		      	|Eth0/1   	|192.168.222.1 |255.255.255.0|		N/a		  |
|S1		    	|VLAN200    |192.168.200.2|255.255.255.0|192.168.200.1 |
|S2		    	|VLAN1      |192.168.222.2|255.255.255.0|192.168.222.1 |
|PC1		    |NIC1 	    |DHCP       |DHCP		   |DHCP			  |
|PC2	    	|NIC1       |DHCP       |DHCP		   |DHCP			  |


## VLAN Table

|Vlan|Name|Assigned Enterface|
|-|-|-|
|1|N/a| s2: eth0/1|
|100|Clients|s1: eth0/1|
|200|Management|s1: vlan200|
|999|ParkingLot|s1: eth0/2 <br> s1: eth0/3|
|1000|Naitive|N/a|


# Build the Network and Configure Basic Device Settings

## Route R1

    interface Ethernet0/0
       ip address 10.0.0.1 255.255.255.0
       !
      interface Ethernet0/1
       no ip address
       !
      interface Ethernet0/1.100
       encapsulation dot1Q 100
        ip address 192.168.100.1 255.255.255.0
       !
      interface Ethernet0/1.200
       encapsulation dot1Q 200
        ip address 192.168.200.1 255.255.255.0
   
      ip route 0.0.0.0 0.0.0.0 10.0.0.2

## Route R2

      interface Ethernet0/0
       ip address 10.0.0.2 255.255.255.0
       !
      interface Ethernet0/1
       ip address 192.168.222.1 255.255.255.0
        ip helper-address 10.0.0.1

      ip route 0.0.0.0 0.0.0.0 10.0.0.2

## Switch S1

    interface Ethernet0/0
     switchport trunk allowed vlan 100,200
     switchport trunk encapsulation dot1q
     switchport mode trunk
    !
    interface Ethernet0/1
     switchport access vlan 100
    !
    interface Ethernet0/2
     switchport access vlan 999
     shutdown
    !
    interface Ethernet0/3
     switchport access vlan 999
     shutdown
    !
    interface Vlan100
     description Clients
     no ip address
    !
    interface Vlan200
     description Mamagement
     ip address 192.168.200.2 255.255.255.0
    !
    interface Vlan999
     description Parking Lot
     no ip address
     shutdown
    !
    interface Vlan1000
     description Native
     no ip address
     shutdown
    !
    ip default-gateway 192.168.200.1


## Switch S2

    interface Ethernet0/0
    !
    interface Ethernet0/1
    !
    interface Ethernet0/2
    !
    interface Ethernet0/3
    !
    interface Vlan1
     ip address 192.168.222.2 255.255.255.0
    !
    ip default-gateway 192.168.222.1

    Configure and verify two DHCPv4 Servers on R1

## Route R1

    !
    ip dhcp pool Client
     network 192.168.100.0 255.255.255.0
     domain-name ccna-lab.com
     default-router 192.168.100.1
     dns-server 8.8.8.8 8.8.8.9
     lease 2 12 30
    !
    ip dhcp pool Users2
     network 192.168.222.0 255.255.255.0
     default-router 192.168.222.1
     domain-name Users2
     dns-server 7.7.7.7 7.7.7.8
     lease 2 12 30

## Route R2

    interface Ethernet0/1
     ip helper-address 10.0.0.1


# Verify the DHCPv4 Server configuration
## a.	Issue the command show ip dhcp pool to examine the pool details.

    Router#show ip dhcp pool

    Pool Client :
     Utilization mark (high/low)    : 100 / 0
     Subnet size (first/next)       : 0 / 0
     Total addresses                : 254
     Leased addresses               : 1
     Pending event                  : none
     1 subnet is currently in the pool :
     Current index        IP address range                    Leased addresses
     192.168.100.3        192.168.100.1    - 192.168.100.254   1

    Pool Users2 :
     Utilization mark (high/low)    : 100 / 0
     Subnet size (first/next)       : 0 / 0
     Total addresses                : 254
     Leased addresses               : 1
     Pending event                  : none
     1 subnet is currently in the pool :
     Current index        IP address range                    Leased addresses
     192.168.222.3        192.168.222.1    - 192.168.222.254   1
    Router#


## b.	Issue the command show ip dhcp bindings to examine established DHCP address assignments.

    Router#show ip dhcp binding
    Bindings from all pools not associated with VRF:
    IP address          Client-ID/              Lease expiration        Type
                        Hardware address/
                        User name
    192.168.100.2       0100.5079.6668.05       Jul 01 2025 10:35 PM    Automatic
    192.168.222.2       0100.5079.6668.06       Jul 01 2025 10:08 PM    Automatic
    Router#


## c.	Issue the command show ip dhcp server statistics to examine DHCP messages.

    Router#show ip dhcp server statistics
    Memory usage         42639
    Address pools        2
    Database agents      0
    Automatic bindings   2
    Manual bindings      0
    Expired bindings     0
    Malformed messages   0
    Secure arp entries   0

    Message              Received
    BOOTREQUEST          0
    DHCPDISCOVER         18
    DHCPREQUEST          4
    DHCPDECLINE          0
    DHCPRELEASE          0
    DHCPINFORM           0

    Message              Sent
    BOOTREPLY            0
    DHCPOFFER            16
    DHCPACK              4
    DHCPNAK              0
    Router#


# TEST PING

    Router#ping 192.168.100.2
    Type escape sequence to abort.
    Sending 5, 100-byte ICMP Echos to 192.168.100.2, timeout is 2 seconds:
    !!!!!
    Success rate is 100 percent (5/5), round-trip min/avg/max = 2/3/6 ms

    Router#ping 192.168.222.2
    Type escape sequence to abort.
    Sending 5, 100-byte ICMP Echos to 192.168.222.2, timeout is 2 seconds:
    !!!!!
    Success rate is 100 percent (5/5), round-trip min/avg/max = 2/5/12 ms

