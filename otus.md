# Задание № 1
Цели занятия
рассмотреть протоколы VLAN, DTP;
исследовать расширенные виртуальные локальные сети;
рассмотреть технологии маршрутизации между VLAN;
поиск и устранение неполадок в сетях VLAN.

![](https://github.com/tatujo2/networks/blob/main/screenshots/1.1.png)

## _Addressing Table_ 

| Device | Interface  | IP Address   |  Subnet Mask  | Default Gateway |
|--------|------------|--------------|---------------|-----------------|
|router1 | E0/1.3     | 192.168.3.1  | 255.255.255.0 |       N/A       |
|router1 | E0/1.4     | 192.168.4.1  | 255.255.255.0 |       N/A       |
|switch1 | Vlan 3     | 192.168.3.11 | 255.255.255.0 |192.168.3.1      |
|switch2 | Vlan 3     | 192.168.3.12 | 255.255.255.0 |192.168.3.1      |
|PC1     | NIC        | 192.168.3.3  | 255.255.255.0 |192.168.3.1      |
|PC2     | NIC        | 192.168.4.3  | 255.255.255.0 |192.168.4.1      |

## _VLAN Table_ 
|Vlan | Name | Interface Assigned|
|-----|------|-------------------|
|Vlan3|Management|switch1: vlan3 <br> switch1: E0/2 <br> switch2: vlan3|
|Vlan4|Operations|switch2 E0/2|
|Vlan7|ParkingLot|switch1: E0/3 <br> switch2: E0/3 |
|Vlan8|Naitive|N/A|


### 1. Create VLANs and Assign Switch Ports

#### Switch1, commands line:
!
<br> interface Ethernet0/2

      switchport access vlan 3
      
      switchport nonegotiate
  
      switchport mode access

<br> interface Ethernet0/3

      switchport access vlan 7
      
      shutdown



#### Switch2, commands line:
!
<br> interface Ethernet0/2

       switchport access vlan 4
       
       switchport nonegotiate
       
       switchport mode access

<br> interface Ethernet0/3

      switchport access vlan 7
      
      shutdown

<br> interface Ethernet0/1

      switchport access vlan 7
      
      shutdown

### 2. Configure an 802.1Q Trunk Between the Switches

#### Switch1, commands line:
!
<br> interface Ethernet0/0
      
      switchport trunk encapsulation dot1q
      
      switchport nonegotiate
      
      switchport mode trunk

#### Switch2, commands line:
!
<br> interface Ethernet0/0

       switchport trunk encapsulation dot1q
       
       switchport nonegotiate
       
       switchport mode trunk

### 3. Configure Inter-VLAN Routing on the Router 

#### Switch1, commands line:
!
<br> interface Ethernet0/1

      switchport trunk encapsulation dot1q
      
      switchport nonegotiate
      
      switchport mode trunk

#### Roter1, commands line:

interface Ethernet0/1.3

      encapsulation dot1Q 3

!
interface Ethernet0/1.4

      encapsulation dot1Q 4

### 3. Verify Inter-VLAN Routing is Working 

#### Ping from PC1 to its default gateway
![ Ping from PC1 to its default gateway ](https://github.com/tatujo2/networks/blob/main/screenshots/ping_from_pc1_to_gateway.png)

#### Ping from PC1 to PC2
![ Ping from PC1 to PC2 ](https://github.com/tatujo2/networks/blob/main/screenshots/ping_from_pc1_to_pc2.png)

#### Ping from PC1 to switch2
![ Ping from PC1 to switch2 ](https://github.com/tatujo2/networks/blob/main/screenshots/ping_from_pc1_to_switch2.png)

#### From the command prompt on PC2, issue the tracert command to the address of PC1.
![ Trace from PC2 to PC1 ](https://github.com/tatujo2/networks/blob/main/screenshots/screenshots/tracert_from_pc1_to_pc2.png)
