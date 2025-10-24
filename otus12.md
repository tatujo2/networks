# Домашнее задание Основные протоколы сети интернет

## Цель:
##Настроить DHCP в офисе Москва
##Настроить синхронизацию времени в офисе Москва
##Настроить NAT в офисе Москва, C.-Перетбруг и Чокурдах



### Задачи:
### 1. Настроите NAT(PAT) на R14 и R15. Трансляция должна осуществляться в адрес автономной системы AS1001.
### 2. Настроите NAT(PAT) на R18. Трансляция должна осуществляться в пул из 5 адресов автономной системы AS2042.
### 3. Настроите статический NAT для R20.
### 4. Настроите NAT так, чтобы R19 был доступен с любого узла для удаленного управления.
### 5*Настроите статический NAT(PAT) для офиса Чокурдах.
### 5. Настроите для IPv4 DHCP сервер в офисе Москва на маршрутизаторах R12 и R13. VPC1 и VPC7 должны получать сетевые настройки по DHCP.
### 6. Настроите NTP сервер на R12 и R13. Все устройства в офисе Москва должны синхронизировать время с R12 и R13.
### 7. Все офисы в лабораторной работе должны иметь IP связность.


### 1. Настроите NAT(PAT) на R14 и R15. Трансляция должна осуществляться в адрес автономной системы AS1001.

			R14
			ip nat inside source list 1 interface Ethernet0/2 overload

			access-list 1 permit 10.0.0.0 0.0.255.255

			interface Ethernet0/0
			 ip address 10.0.0.5 255.255.255.252
			 ip nat inside
			 ip virtual-reassembly in
			 ip ospf 1 area 10
			!
			interface Ethernet0/1
			 ip address 10.0.0.9 255.255.255.252
			 ip nat inside
			 ip virtual-reassembly in
			 ip ospf 1 area 10
			!
			interface Ethernet0/2
			 ip address 172.16.0.1 255.255.255.252
			 ip nat outside
			 ip virtual-reassembly in
			!
			interface Ethernet0/3
			 ip address 10.0.0.2 255.255.255.252
			 ip nat inside
			 ip virtual-reassembly in
			 ip ospf 1 area 101
			!         
			interface Ethernet1/0
			 ip address 10.0.0.41 255.255.255.252
			 ip nat inside
			 ip virtual-reassembly in
			 ip ospf 1 area 0
			 
			 
			 R15
			 
			 ip nat inside source list 1 interface Ethernet0/2 overload
			 
			 access-list 1 permit 10.0.0.0 0.0.255.255
			 
			 interface Ethernet0/0
			 ip address 10.0.0.25 255.255.255.252
			 ip nat inside
			 ip virtual-reassembly in
			 ip ospf 1 area 10
			!
			interface Ethernet0/1
			 ip address 10.0.0.13 255.255.255.252
			 ip nat inside
			 ip virtual-reassembly in
			 ip ospf 1 area 10
			!
			interface Ethernet0/2
			 ip address 172.16.1.1 255.255.255.252
			 ip nat outside
			 ip virtual-reassembly in
			!
			interface Ethernet0/3
			 ip address 10.0.0.30 255.255.255.252
			 ip nat inside
			 ip virtual-reassembly in
			 ip ospf 1 area 102
			!
			interface Ethernet1/0
			 ip address 10.0.0.42 255.255.255.252
			 ip nat inside
			 ip virtual-reassembly in
			 ip ospf 1 area 0
			!

			2. Настроите NAT(PAT) на R18. Трансляция должна осуществляться в пул из 5 адресов автономной системы AS2042.



			ip nat pool AS520-2 172.16.6.3 172.16.6.8 netmask 255.255.255.0
			ip nat pool AS520-1 172.16.5.3 172.16.5.8 netmask 255.255.255.0
			ip nat inside source list 1 pool AS520-1
			ip nat inside source list 2 pool AS520-2

			access-list 1 permit 10.1.0.0 0.0.255.255
			access-list 2 permit 10.1.0.0 0.0.255.255


### Проверка

			R18#ping 172.16.0.1
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.0.1, timeout is 2 seconds:
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 2/2/4 ms
			R18#ping 172.16.0.1 so
			R18#ping 172.16.0.1 source lo0
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.0.1, timeout is 2 seconds:
			Packet sent with a source address of 10.1.1.18 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 2/2/4 ms
			R18#ping 172.16.0.1 sourc     
			R18#ping 172.16.0.1 source et
			R18#ping 172.16.0.1 source ethernet 0/0
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.0.1, timeout is 2 seconds:
			Packet sent with a source address of 10.1.0.5 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 2/2/4 ms
			R18#ping 172.16.0.1 source ethernet 0/1
			Type escape sequence to abort.
			Sending 5, 100-byte ICMP Echos to 172.16.0.1, timeout is 2 seconds:
			Packet sent with a source address of 10.1.0.1 
			!!!!!
			Success rate is 100 percent (5/5), round-trip min/avg/max = 2/3/5 ms
			R18#sh ip nat
			R18#sh ip nat t
			R18#sh ip nat translations 
			Pro Inside global      Inside local       Outside local      Outside global
			icmp 172.16.5.5:11     10.1.0.1:11        172.16.0.1:11      172.16.0.1:11
			--- 172.16.5.5         10.1.0.1           ---                ---
			icmp 172.16.5.4:10     10.1.0.5:10        172.16.0.1:10      172.16.0.1:10
			--- 172.16.5.4         10.1.0.5           ---                ---
			icmp 172.16.5.3:9      10.1.1.18:9        172.16.0.1:9       172.16.0.1:9
			--- 172.16.5.3         10.1.1.18          ---                ---



### 3. Настроите статический NAT для R20.

			R15
			ip nat inside source static 10.0.0.29 172.16.1.10


			Проверка

			R18#telnet 172.16.1.10
			Trying 172.16.1.10 ... Open
			User Access Verification
			Username: cisco 
			Password: 
			R20#


			R15#sh ip nat translations 
			Pro Inside global      Inside local       Outside local      Outside global
			tcp 172.16.1.10:23     10.0.0.29:23       172.16.5.2:58854   172.16.5.2:58854


### 4. Настроите NAT так, чтобы R19 был доступен с любого узла для удаленного управления.


			R14
			ip nat inside source static tcp 10.0.0.1 22 172.16.0.1 22 extendable


### Проверка

			R18

			R18#ssh -l cisco 172.16.0.1
			Password: 

			R19#






### 5*Настроите статический NAT(PAT) для офиса Чокурдах.


			interface Ethernet0/0
			 ip address 10.2.0.22 255.255.255.252
			 ip nat outside
			 ip virtual-reassembly in
			!
			interface Ethernet0/1
			 ip address 10.2.0.26 255.255.255.252
			 ip nat outside
			 ip virtual-reassembly in
			!
			interface Ethernet0/2.60
			 encapsulation dot1Q 60
			 ip address 10.2.60.1 255.255.255.0
			 ip nat inside
			 ip virtual-reassembly in
			 ip policy route-map PBR_60
			!
			interface Ethernet0/2.70
			 encapsulation dot1Q 70
			 ip address 10.2.70.1 255.255.255.0
			 ip nat inside
			 ip virtual-reassembly in
			 ip policy route-map PBR_70
			 
			ip nat inside source static 10.2.60.2 10.2.0.22
			ip nat inside source static 10.2.70.2 10.2.0.26 


### Проверка

			R28#sh ip nat translations 
			Pro Inside global      Inside local       Outside local      Outside global
			icmp 10.2.0.22:70      10.2.60.2:70       10.2.0.21:70       10.2.0.21:70
			--- 10.2.0.22          10.2.60.2          ---                ---
			icmp 10.2.0.26:69      10.2.70.2:69       10.2.0.25:69       10.2.0.25:69
			--- 10.2.0.26          10.2.70.2          ---                ---
			R28#sh ip nat st           
			R28#sh ip nat statistics 
			Total active translations: 4 (2 static, 2 dynamic; 2 extended)
			Peak translations: 19, occurred 00:03:20 ago
			Outside interfaces:
			  Ethernet0/0, Ethernet0/1
			Inside interfaces: 
			  Ethernet0/2.60, Ethernet0/2.70
			Hits: 114  Misses: 0
			CEF Translated packets: 109, CEF Punted packets: 5
			Expired translations: 61
			Dynamic mappings:

			Total doors: 0
			Appl doors: 0
			Normal doors: 0
			Queued Packets: 0
			R28#





### 5. Настроите для IPv4 DHCP сервер в офисе Москва на маршрутизаторах R12 и R13. VPC1 и VPC7 должны получать сетевые настройки по DHCP.


			SW4

			interface Vlan10
			 ip address 10.0.10.1 255.255.255.0
			 ip helper-address 10.0.0.21
			!
			interface Vlan20
			 ip address 10.0.20.2 255.255.255.0
			 ip helper-address 10.0.0.33


			SW5

			interface Vlan10
			 ip address 10.0.10.2 255.255.255.0
			 ip helper-address 10.0.0.21
			!
			interface Vlan20
			 ip address 10.0.20.1 255.255.255.0
			 ip helper-address 10.0.0.33
			 


			R12

			ip dhcp excluded-address 10.0.10.2
			!
			ip dhcp pool LAN-POOL-1
			 network 10.0.10.0 255.255.255.0
			 default-router 10.0.10.1 
			 dns-server 10.0.10.1 
			 domain-name otus.com
			 
			R13

			ip dhcp excluded-address 10.0.20.2
			!
			ip dhcp pool LAN-POOL-2
			 network 10.0.20.0 255.255.255.0
			 default-router 10.0.20.1 
			 dns-server 10.0.20.1 
			 domain-name otus.ru




### Поверка

			VPC1> sh ip  

			NAME        : VPC1[1]
			IP/MASK     : 10.0.10.3/24
			GATEWAY     : 10.0.10.1
			DNS         : 10.0.10.1  
			DHCP SERVER : 10.0.0.21
			DHCP LEASE  : 86396, 86400/43200/75600
			DOMAIN NAME : otus.com
			MAC         : 00:50:79:66:68:01
			LPORT       : 20000
			RHOST:PORT  : 127.0.0.1:30000
			MTU         : 1500

			VPC7> sh ip          

			NAME        : VPC7[1]
			IP/MASK     : 10.0.20.3/24
			GATEWAY     : 10.0.20.1
			DNS         : 10.0.20.1  
			DHCP SERVER : 10.0.0.37
			DHCP LEASE  : 86341, 86400/43200/75600
			DOMAIN NAME : otus.ru
			MAC         : 00:50:79:66:68:07
			LPORT       : 20000
			RHOST:PORT  : 127.0.0.1:30000
			MTU         : 1500





### 6. Настроите NTP сервер на R12 и R13. Все устройства в офисе Москва должны синхронизировать время с R12 и R13.


			R12
			ntp master 3
			ntp update-calendar

			R13
			ntp master 4
			ntp update-calendar
			ntp server 10.0.1.12 


			R14
			ntp server 10.0.1.12 prefer
			ntp server 10.0.1.13

			Аналогично настраиваем: R15, R14, SW4, SW5, SW3, SW2



### Проверка

			SW4#sh ntp status 
			Clock is synchronized, stratum 4, reference is 10.0.1.12      
			nominal freq is 250.0000 Hz, actual freq is 250.0000 Hz, precision is 2**10
			ntp uptime is 160000 (1/100 of seconds), resolution is 4000
			reference time is ECA5A13A.126E97C0 (06:54:50.072 UTC Fri Oct 24 2025)
			clock offset is 0.5000 msec, root delay is 1.00 msec
			root dispersion is 7.68 msec, peer dispersion is 2.86 msec
			loopfilter state is 'CTRL' (Normal Controlled Loop), drift is 0.000000011 s/s
			system poll interval is 128, last update was 73 sec ago.
