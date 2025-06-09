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
|Vlan7|ParkingLot|switch1: E0/3 <br> switch1: E0/4 <br> switch2: E0/3 <br> switch2: E0/4|
|Vlan8|Naitive|N/A|


### 1. Create VLANs and Assign Switch Ports
Switch1, commands line:
<br> interface Ethernet0/0
 <br> switchport trunk encapsulation dot1q
 <br> switchport nonegotiate
 <br> switchport mode trunk
!
<br> interface Ethernet0/1
 <br> switchport trunk encapsulation dot1q
 <br> switchport nonegotiate
 <br> switchport mode trunk
!
<br> interface Ethernet0/2
  <br> switchport access vlan 3
  <br> switchport nonegotiate
  <br> switchport mode access


Switch2, commands line:
