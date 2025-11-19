# IPSec over DmVPN

## Цель:
### Настроить GRE поверх IPSec между офисами Москва и С.-Петербург
### Настроить DMVPN поверх IPSec между офисами Москва и Чокурдах, Лабытнанги

## Схема:



### 1.Настроите GRE поверх IPSec между офисами Москва и С.-Петербург.

			Создание CA сервера (R22)

			R22
			crypto pki server R22
			 database level complete
			 no database archive
			 lifetime crl 0 2
			!
			crypto pki trustpoint R22
			 revocation-check crl
			 rsakeypair R22
			 
			ip http server

			ip domain name otus.ru



			R15
			crypto ikev2 proposal PHASE1 
			 encryption aes-cbc-128
			 integrity md5
			 group 2
			!
			crypto ikev2 policy IKEV2 
			 proposal PHASE1
			!
			!
			crypto ikev2 profile PROFILE1
			 match address local interface Tunnel0
			 match identity remote address 192.168.30.2 255.255.255.252 
			 authentication remote rsa-sig
			 authentication local rsa-sig
			 pki trustpoint R22
			!
			!
			!
			crypto ipsec transform-set IPSEC_TS esp-null esp-sha-hmac 
			 mode tunnel
			!
			!
			!         
			crypto map IPSEC 1 ipsec-isakmp 
			 set peer 192.168.30.2
			 set transform-set IPSEC_TS 
			 set pfs group5
			 set ikev2-profile PROFILE1
			 match address R15_to_R18

			interface Tunnel0
			 crypto map IPSEC

			ip route 10.1.0.0 255.255.0.0 192.168.30.2
			!
			ip access-list extended R15_to_R18
			 permit ip host 192.168.30.1 host 192.168.30.2
			 
			Аналогично настроен R18

### Проверка:

			R22#sh crypto pki certificates 
			CA Certificate
			  Status: Available
			  Certificate Serial Number (hex): 01
			  Certificate Usage: Signature
			  Issuer: 
				cn=R22
			  Subject: 
				cn=R22
			  Validity Date: 
				start date: 15:26:50 UTC Nov 16 2025
				end   date: 15:26:50 UTC Nov 15 2028
			  Associated Trustpoints: R22 
			  Storage: nvram:R22#1CA.cer
			  
			R22#sh crypto pki server R22 certificates 
			Serial Issued date              Expire date               Subject Name
			1       15:26:50 UTC Nov 16 2025 15:26:50 UTC Nov 15 2028  cn=R22
			2       15:46:18 UTC Nov 16 2025 15:46:18 UTC Nov 16 2026  serialNumber=67109152+ipaddress=172.16.5.2+hostname=R18.otus.ru
			3       15:46:18 UTC Nov 16 2025 15:46:18 UTC Nov 16 2026  serialNumber=67109104+ipaddress=172.16.1.1+hostname=R15.otus.ru
			4       17:34:04 UTC Nov 16 2025 17:34:04 UTC Nov 16 2026  serialNumber=67109088+ipaddress=172.16.0.1+hostname=R14.otus.ru
			5       17:34:04 UTC Nov 16 2025 17:34:04 UTC Nov 16 2026  serialNumber=67109296+ipaddress=172.16.9.2+hostname=R27.otus.ru
			6       18:28:24 UTC Nov 16 2025 18:28:24 UTC Nov 16 2026  serialNumber=67109312+ipaddress=172.16.7.2+hostname=R28.otus.ru



			R15#traceroute  10.1.1.18
			Type escape sequence to abort.
			Tracing the route to 10.1.1.18
			VRF info: (vrf in name/id, vrf out name/id)
			  1 
			*Nov 16 18:52:22.448: %CRYPTO-6-IKMP_NO_ID_CERT_ADDR_MATCH: ID of 192.168.30.2 (type 1) and certificate addr with 172.16.5.2
			*Nov 16 18:52:22.448: %CRYPTO-6-IKMP_NO_ID_CERT_ADDR_MATCH: ID of 192.168.30.2 (type 1) and certificate addr with 172.16.5.2 * 
				192.168.30.2 [AS 101] 2 msec 3 msec
			R15#sh crypto ipsec sa   

			interface: Tunnel0
				Crypto map tag: IPSEC, local addr 192.168.30.1

			   protected vrf: (none)
			   local  ident (addr/mask/prot/port): (192.168.30.1/255.255.255.255/0/0)
			   remote ident (addr/mask/prot/port): (192.168.30.2/255.255.255.255/0/0)
			   current_peer 192.168.30.2 port 500
				 PERMIT, flags={origin_is_acl,}
				#pkts encaps: 0, #pkts encrypt: 0, #pkts digest: 0
				#pkts decaps: 2, #pkts decrypt: 2, #pkts verify: 2
				#pkts compressed: 0, #pkts decompressed: 0
				#pkts not compressed: 0, #pkts compr. failed: 0
				#pkts not decompressed: 0, #pkts decompress failed: 0
				#send errors 0, #recv errors 0

				 local crypto endpt.: 192.168.30.1, remote crypto endpt.: 192.168.30.2
				 plaintext mtu 1358, path mtu 1400, ip mtu 1400, ip mtu idb Tunnel0
				 current outbound spi: 0x1E19BF56(505003862)
				 PFS (Y/N): N, DH group: none

				 inbound esp sas:
				  spi: 0x8C26B0CD(2351345869)
					transform: esp-null esp-sha-hmac ,
					in use settings ={Tunnel, }
					conn id: 1, flow_id: SW:1, sibling_flags 80000040, crypto map: IPSEC
					sa timing: remaining key lifetime (k/sec): (4232043/3581)
					IV size: 0 bytes
					replay detection support: Y
					Status: ACTIVE(ACTIVE)

				 inbound ah sas:

				 inbound pcp sas:

				 outbound esp sas:
				  spi: 0x1E19BF56(505003862)
					transform: esp-null esp-sha-hmac ,
					in use settings ={Tunnel, }
					conn id: 2, flow_id: SW:2, sibling_flags 80000040, crypto map: IPSEC
					sa timing: remaining key lifetime (k/sec): (4232044/3581)
					IV size: 0 bytes
					replay detection support: Y
					Status: ACTIVE(ACTIVE)

				 outbound ah sas:
					  
				 outbound pcp sas:



### 2.Настроите DMVPN поверх IPSec между Москва и Чокурдах, Лабытнанги.

			R14
			crypto pki trustpoint R22
			 enrollment url http://R22:80
			 serial-number
			 ip-address 172.16.0.1
			 revocation-check crl
			 
			ip domain name otus.ru
			ip host R22 172.16.2.1

			crypto ikev2 proposal PHASE1 
			 encryption aes-cbc-128
			 integrity md5
			 group 2
			!
			crypto ikev2 policy IKEV2 
			 proposal PHASE1
			!
			!
			crypto ikev2 profile PROFILE1
			 match identity remote fqdn domain otus.ru
			 match identity remote address 172.16.9.2 255.255.255.255 
			 match identity remote address 172.16.7.2 255.255.255.255 
			 authentication remote rsa-sig
			 authentication local rsa-sig
			 pki trustpoint R22
			!
			!
			!
			crypto ipsec transform-set IPSEC_TS esp-null esp-sha-hmac 
			 mode tunnel
			!         
			crypto ipsec profile IPSEC_TS_1
			 set transform-set IPSEC_TS 
			 set ikev2-profile PROFILE1
			!
			interface Tunnel0
			 tunnel protection ipsec profile IPSEC_TS_1
			 
			Аналогично образом настроен R27 и R28

### Проверка:

			R14#show crypto ipsec sa 

			interface: Tunnel0
				Crypto map tag: Tunnel0-head-0, local addr 172.16.0.1

			   protected vrf: (none)
			   local  ident (addr/mask/prot/port): (172.16.0.1/255.255.255.255/47/0)
			   remote ident (addr/mask/prot/port): (172.16.9.2/255.255.255.255/47/0)
			   current_peer 172.16.9.2 port 500
				 PERMIT, flags={origin_is_acl,}
				#pkts encaps: 27, #pkts encrypt: 27, #pkts digest: 27
				#pkts decaps: 27, #pkts decrypt: 27, #pkts verify: 27
				#pkts compressed: 0, #pkts decompressed: 0
				#pkts not compressed: 0, #pkts compr. failed: 0
				#pkts not decompressed: 0, #pkts decompress failed: 0
				#send errors 0, #recv errors 0

				 local crypto endpt.: 172.16.0.1, remote crypto endpt.: 172.16.9.2
				 plaintext mtu 1454, path mtu 1500, ip mtu 1500, ip mtu idb (none)
				 current outbound spi: 0x2A8EA05(44624389)
				 PFS (Y/N): N, DH group: none

				 inbound esp sas:
				  spi: 0x8B4D0EF9(2337083129)
					transform: esp-null esp-sha-hmac ,
					in use settings ={Tunnel, }
					conn id: 2, flow_id: SW:2, sibling_flags 80000040, crypto map: Tunnel0-head-0
					sa timing: remaining key lifetime (k/sec): (4256685/579)
					IV size: 0 bytes
					replay detection support: Y
					Status: ACTIVE(ACTIVE)

				 inbound ah sas:

				 inbound pcp sas:

