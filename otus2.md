# Лабораторная работа. Развертывание коммутируемой сети с резервными каналами
![](https://github.com/tatujo2/networks/blob/main/screenshots/2.1.png)
## Adressing table 
| Device | Interface | IP Address |  Subnet Mask | Default Gateway|
|--------|-----------|------------|--------------|----------------|
| Switch1| Vlan1     | 192.168.1.1| 255.255.255.0|       N/A      |
| Switch2| Vlan1     | 192.168.1.2| 255.255.255.0|       N/A      |
| Switch3| Vlan1     | 192.168.1.3| 255.255.255.0|       N/A      |

## Vlan table
|Vlan|Name|Assigned Enterface|
|-|-|-|
|Trunk|Trunk| s1: eth0/1 <br> s1: eth0/2 <br> s2: eth0/1 <br> s2: eth0/2 <br> s3: eth0/1 <br> s3: eth0/2 <br>| 
