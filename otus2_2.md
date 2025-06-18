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
|Trunk|Trunk| s1: eth0/0 <br> s1: eth0/1 <br> s1: eth0/3 <br> s1: eth0/3 <br> s2: eth0/0 <br> s2: eth0/1 <br> s2: eth0/3 <br> s2: eth0/3 <br> s3: eth0/0 <br> s3: eth0/1 <br> s3: eth0/2 <br> s3: eth0/3 | 

## Часть 1:	Определение корневого моста

switch1#show spanning-tree

VLAN0001

      Spanning tree enabled protocol ieee
  
      Root ID    Priority    32769
  
             Address     aabb.cc00.2000
             
             Cost        100
             
             Port        2 (Ethernet0/1)
             
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             
      Bridge ID  Priority    40961  (priority 40960 sys-id-ext 1)
  
             Address     aabb.cc00.1000
             
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             
             Aging Time  300 sec

      Interface           Role Sts Cost      Prio.Nbr Type

      ------------------- ---- --- --------- -------- --------------------------------

      Et0/1               Root FWD 100       128.2    Shr

      Et0/3               Altn BLK 100       128.4    Shr


      !!!!

switch2#sh spanning-tree

      VLAN0001

      Spanning tree enabled protocol ieee
  
      Root ID    Priority    32769
  
             Address     aabb.cc00.2000
             
             This bridge is the root
             
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

      Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
      
             Address     aabb.cc00.2000
             
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             
             Aging Time  15  sec

      Interface           Role Sts Cost      Prio.Nbr Type

      ------------------- ---- --- --------- -------- --------------------------------

      Et0/1               Desg FWD 100       128.2    Shr

      Et0/3               Desg FWD 100       128.4    Shr

      !!!!

switch3#sh spanning-tree

      VLAN0001

      Spanning tree enabled protocol ieee
  
      Root ID    Priority    32769
  
             Address     aabb.cc00.2000
             
             Cost        100
             
             Port        2 (Ethernet0/1)
             
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

      Bridge ID  Priority    36865  (priority 36864 sys-id-ext 1)
  
             Address     aabb.cc00.3000
             
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             
             Aging Time  300 sec

      Interface           Role Sts Cost      Prio.Nbr Type

      ------------------- ---- --- --------- -------- --------------------------------

      Et0/1               Root FWD 100       128.2    Shr

      Et0/3               Desg FWD 100       128.4    Shr

      Корневой мост явлется switch2, так как у него Root ID Priority меньше  32769

      На коммутаторе являются корневыми портами: s1: et0/1, s3: et0/1

      На коммутаторе являются назначенными портами: s1: et0/3; s3: et0/3; s2: et0/1, et0/3

      В качестве альтернативного и в настоящее время заблокирован, так как Bridge ID  Priority  40961 : S1: et0/3

### Часть 2:	Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов

      Изменение стоимости порта:
      
      Switch1(config)# Et0/1
      
      Switch1(config-if)# spanning-tree cost 99

switch1#show spanning-tree

      VLAN0001
      
        Spanning tree enabled protocol ieee
        
        Root ID    Priority    32769
        
             Address     aabb.cc00.2000
             
             Cost        99
             
             Port        2 (Ethernet0/1)
             
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

        Bridge ID  Priority    40961  (priority 40960 sys-id-ext 1)
        
             Address     aabb.cc00.1000
             
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             
             Aging Time  15  sec

      Interface           Role Sts Cost      Prio.Nbr Type
      
      ------------------- ---- --- --------- -------- --------------------------------
      
      Et0/1               Root FWD 99        128.2    Shr
      
      Et0/3               Desg FWD 100       128.4    Shr


!!!!

switch3#show spanning-tree

      VLAN0001
      
        Spanning tree enabled protocol ieee
        
        Root ID    Priority    32769
        
             Address     aabb.cc00.2000
             
             Cost        100
             
             Port        2 (Ethernet0/1)
             
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

        Bridge ID  Priority    36865  (priority 36864 sys-id-ext 1)
        
             Address     aabb.cc00.3000
             
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             
             Aging Time  300 sec
             
            Interface           Role Sts Cost      Prio.Nbr Type
            
            ------------------- ---- --- --------- -------- --------------------------------
            
            Et0/1               Root FWD 100       128.2    Shr
            
            Et0/3               Altn BLK 100       128.4    Shr

Протокол spanning-tree заменяет ранее заблокированный порт на назначенный порт и блокирует порт, который был назначенным
портом на другом коммутаторе потому, что уменьшается стоимость пути до корневого свича через switch1.



### Часть 3: Наблюдение за процессом выбора протоколом STP порта, исходя приоритетов портов.

Включаем порты на всех комутаторах Et0/0 Et0/2


switch1#sh spanning-tree

        VLAN0001
      
        Spanning tree enabled protocol ieee
      
        Root ID    Priority    32769
        
             Address     aabb.cc00.2000
             
             Cost        100
             
             Port        1 (Ethernet0/0)
             
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

        Bridge ID  Priority    40961  (priority 40960 sys-id-ext 1)
        
             Address     aabb.cc00.1000
             
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             
             Aging Time  300 sec

      Interface           Role Sts Cost      Prio.Nbr Type
      
      ------------------- ---- --- --------- -------- --------------------------------
      
      Et0/0               Root FWD 100       128.1    Shr
      
      Et0/1               Altn BLK 100       128.2    Shr
      
      Et0/2               Altn BLK 100       128.3    Shr
      
      Et0/3               Altn BLK 100       128.4    Shr

      !!!!

switch3#sh spanning-tree

        VLAN0001
      
        Spanning tree enabled protocol ieee
        
        Root ID    Priority    32769
        
             Address     aabb.cc00.2000
             
             Cost        100
             
             Port        1 (Ethernet0/0)
             
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

        Bridge ID  Priority    36865  (priority 36864 sys-id-ext 1)
        
             Address     aabb.cc00.3000
             
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             
             Aging Time  300 sec

      Interface           Role Sts Cost      Prio.Nbr Type
      
      ------------------- ---- --- --------- -------- --------------------------------
      
      Et0/0               Root FWD 100       128.1    Shr
      
      Et0/1               Altn BLK 100       128.2    Shr
      
      Et0/2               Desg FWD 100       128.3    Shr
      
      Et0/3               Desg FWD 100       128.4    Shr


Корневые порты на коммутаторах: s1: Et0/0, s2: Et0/0, выбраны эти порты, так как имеют самый наименьшую номирацию.






