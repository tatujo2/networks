# Lab - Implement DHCPv4
## Addressing Table

| Device    | Interface | IP Address| Subnet Mask  | Default Gateway  |
|-----------|-----------|-----------|--------------|------------------|
|R1		    	|Eth0/0     | 10.0.0.1  | 255.255.255.0|		N/a		  |
|		      	|Eth0/1	    |	N/a	    	|		N/a	   |		N/a		  |
|		      	|Eth0/1.100 	|192.168.0.1| 255.255.255.0|		N/a		  |
|	      		|Eth0/1.200   |192.168.1.1|	255.255.255.0		   |		N/a		  |
|		      	|Eth0/1.1000   |N/a        |  N/a		   |		N/a		  |
|R2         |Eth0/0     |Eth0/0     | 10.0.0.2  | 255.255.255.0|		N/a		  |
|		      	|Eth0/1   	|N/a        |N/a           |		N/a		  |
|S1		    	|VLAN200    |           |			     |				  |
|S2		    	|VLAN1      |     	    | 			   |				  |
|PC1		    |NIC1 	    |DHCP       |DHCP		   |DHCP			  |
|PC2	    	|NIC1       |DHCP       |DHCP		   |DHCP			  |


## VLAN Table

|Vlan|Name|Assigned Enterface|
|-|-|-|
|1|N/a| s2: eth0/1|
|100|Clients|s1: eth0/1|
|200|Management|s1: vlan200|
|999|ParkingLot|s1: eth0/2 <br> s1: eth0/2|
|1000|Naitive|N/a|
