# 5 . CONFIGURATION DU ROUTAGE OSPF MONO-AIRE

## 5.1  Vérification de la configuration de votre routeur R101

```
r101#sh version
Cisco IOS Software, 3700 Software (C3725-ADVENTERPRISEK9-M), Version 12.4(15)T14, RELEASE SOFTWARE (fc2)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2010 by Cisco Systems, Inc.
Compiled Tue 17-Aug-10 12:08 by prod_rel_team

ROM: ROMMON Emulation Microcode
ROM: 3700 Software (C3725-ADVENTERPRISEK9-M), Version 12.4(15)T14, RELEASE SOFTWARE (fc2)

r101 uptime is 3 minutes
System returned to ROM by unknown reload cause - suspect boot_data[BOOT_COUNT] 0x0, BOOT_COUNT 0, BOOTDATA 19
System image file is "tftp://255.255.255.255/unknown"


This product contains cryptographic features and is subject to United
States and local country laws governing import, export, transfer and
use. Delivery of Cisco cryptographic products does not imply
third-party authority to import, export, distribute or use encryption.
Importers, exporters, distributors and users are responsible for
compliance with U.S. and local country laws. By using this product you
agree to comply with applicable laws and regulations. If you are unable
to comply with U.S. and local laws, return this product immediately.

A summary of U.S. laws governing Cisco cryptographic products may be found at:
http://www.cisco.com/wwl/export/crypto/tool/stqrg.html

If you require further assistance please contact us by sending email to
export@cisco.com.

Cisco 3725 (R7000) processor (revision 0.1) with 124928K/6144K bytes of memory.
Processor board ID FTX0945W0MY
R7000 CPU at 240MHz, Implementation 39, Rev 2.1, 256KB L2, 512KB L3 Cache
4 FastEthernet interfaces
4 Serial(sync/async) interfaces
DRAM configuration is 64 bits wide with parity enabled.
55K bytes of NVRAM.

Configuration register is 0x2102

r101#
```

**Question 1**

- Quel type de routeur devez-vous paramétrer ?
  * Un routeur Cisco 3725 (R7000)
* Quelle version d’IOS ?
  * Version 12.4(15)T14

## 5.2  Configuration minimale de votre routeur

```
r101#
r101#conf t
Enter configuration commands, one per line. End with CNTL/Z.
r101 (config) #hostname r101
r101 (config)#enable password cisco
r101 (config) #line vty © 4
r101 (config-line) #password netman
r101 (config-line)#^Z
r101#
00:11:52: %SYS-5-CONFIG I: Configured from console by console
r101#wr
Building configuration.
[OK]
r101#
```

## 5.3  Attribution des adresses IP

```
r101#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
r101(config)#interf
r101(config)#interface fa
r101(config)#interface fastEthernet 
r101(config)#interface fastEthernet 0/0
r101(config-if)#ip address 10.1.100.101 255.255.255.0
r101(config-if)#no shut
r101(config-if)#interface fastEthernet 0/1           
r101(config-if)#ip address 10.1.101.1 255.255.255.128
r101(config-if)#no shut
r101(config-if)#interface se                         
r101(config-if)#interface ser
r101(config-if)#interface ser 0/0
r101(config-if)#ip address 10.1.50.1 255.255.255.252 
r101(config-if)#no shut
r101(config-if)#interface lo                        
r101(config-if)#interface loo
r101(config-if)#interface loo 0
r101(config-if)#ip address 172.16.101.101 255.255.255.255
r101(config-if)#exit
r101(config)#^Z
r101#
00:20:13: %SYS-5-CONFIG_I: Configured from console by console
r101#
```

```
r101#show ip int brief
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            10.1.100.101    YES NVRAM  up                    up      
Serial0/0                  10.1.50.1       YES NVRAM  up                    up      
FastEthernet0/1            10.1.101.1      YES NVRAM  up                    up      
Serial0/1                  unassigned      YES NVRAM  administratively down down    
Serial0/2                  unassigned      YES NVRAM  administratively down down    
Serial0/3                  unassigned      YES NVRAM  administratively down down    
FastEthernet1/0            unassigned      YES NVRAM  administratively down down    
FastEthernet2/0            unassigned      YES NVRAM  administratively down down    
Loopback0                  172.16.101.101  YES NVRAM  up                    up   
```

chacune des interfaces des routeurs voisins répondent au ping :  

```
r101#ping 10.1.100.100

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.100.100, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 16/18/20 ms
r101#ping 10.1.100.102

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.100.102, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 8/15/20 ms
r101#ping 10.1.50.2   

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.50.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/4 ms
r101#
```

## 5.4  Etat des interfaces VPC

```
PC101> show

NAME   IP/MASK              GATEWAY           MAC                LPORT  RHOST:PORT
PC101  10.1.101.126/25      10.1.101.1        00:50:79:66:68:01  10072  127.0.0.1:10073
       fe80::250:79ff:fe66:6801/64

PC101> save
Saving startup configuration to startup.vpc
.  done

PC101> ping 10.1.101.1

84 bytes from 10.1.101.1 icmp_seq=1 ttl=255 time=9.219 ms
84 bytes from 10.1.101.1 icmp_seq=2 ttl=255 time=10.570 ms
84 bytes from 10.1.101.1 icmp_seq=3 ttl=255 time=1.170 ms
84 bytes from 10.1.101.1 icmp_seq=4 ttl=255 time=6.923 ms
84 bytes from 10.1.101.1 icmp_seq=5 ttl=255 time=3.950 ms
```

```
r101#show interfaces fastEthernet 0/1
FastEthernet0/1 is up, line protocol is up 
  Hardware is Gt96k FE, address is c203.0deb.0001 (bia c203.0deb.0001)
  Description: LAN 101
  Internet address is 10.1.101.1/25
  MTU 1500 bytes, BW 10000 Kbit/sec, DLY 1000 usec, 
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  Keepalive set (10 sec)
  Half-duplex, 10Mb/s, 100BaseTX/FX
  ARP type: ARPA, ARP Timeout 04:00:00
  Last input 00:03:54, output 00:00:06, output hang never
  Last clearing of "show interface" counters never
  Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue: 0/40 (size/max)
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     6 packets input, 554 bytes
     Received 1 broadcasts, 0 runts, 0 giants, 0 throttles
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
     0 watchdog
     0 input packets with dribble condition detected
     289 packets output, 30079 bytes, 0 underruns
     0 output errors, 0 collisions, 0 interface resets
     0 unknown protocol drops
     0 babbles, 0 late collision, 0 deferred
     0 lost carrier, 0 no carrier
     0 output buffer failures, 0 output buffers swapped out
r101#
```

**Question 2** 

* Depuis les VPCs quelles sont les interfaces que vous pouvez atteindre?
  (Le ping doit réussir en local, et sur quelques autres adresses IP, lesquelles?)
  
  * Depuis le PC101
    On peut atteindre toutes les interfaces de R101
    En revanche toutes les interfaces au delà vers R100 et R102 renvoient un timeout
  
  * Depuis le PC 102
    
    On peut atteindre toutes les interfaces de R102
    En revanche toutes les interfaces au delà vers R100 et R101 renvoient un timeout

* Comment expliquez-vous cela ?
  
  * dans le cas du PC101
    Le routeur R101 n'a pas de OSPF activé, il est donc le seul qui dispose des routes retour pour que le message ICMP Reply soit retourné au PC101. 
    R100 et R102 reçoivent bien le message ICMP Request sur leurs interfaces, en revanche, en l'absence d'OSPF sur R101, ils ne disposent pas de la route adéquat pour émettre le ICMP Reply. 

```
R100#show ip route 
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     172.16.0.0/32 is subnetted, 6 subnets
O       172.16.201.201 [110/21] via 10.0.0.200, 00:55:45, FastEthernet0/1
O       172.16.200.200 [110/11] via 10.0.0.200, 00:55:45, FastEthernet0/1
O       172.16.101.101 [110/11] via 10.1.100.101, 00:55:45, FastEthernet0/0
C       172.16.100.100 is directly connected, Loopback0
O       172.16.201.202 [110/21] via 10.0.0.200, 00:55:45, FastEthernet0/1
O       172.16.101.102 [110/11] via 10.1.100.102, 00:55:55, FastEthernet0/0
     10.0.0.0/8 is variably subnetted, 10 subnets, 3 masks
C       10.0.0.0/24 is directly connected, FastEthernet0/1
O       10.2.60.0/30 [110/70] via 10.0.0.200, 00:55:47, FastEthernet0/1
O       10.1.50.0/30 [110/60] via 10.1.100.102, 00:55:57, FastEthernet0/0
                     [110/60] via 10.1.100.101, 00:55:47, FastEthernet0/0
O       10.2.201.128/25 [110/30] via 10.0.0.200, 00:55:47, FastEthernet0/1
C       10.1.100.0/24 is directly connected, FastEthernet0/0
O       10.2.200.0/24 [110/20] via 10.0.0.200, 00:55:48, FastEthernet0/1
O       10.2.201.0/25 [110/30] via 10.0.0.200, 00:55:48, FastEthernet0/1
O       10.1.101.128/25 [110/20] via 10.1.100.102, 00:55:58, FastEthernet0/0
C       10.0.255.4/30 is directly connected, Serial0/1
C       10.0.255.0/30 is directly connected, Serial0/0
R100#
```

```
r101#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     172.16.0.0/32 is subnetted, 6 subnets
O       172.16.201.201 [110/31] via 10.1.100.100, 00:57:17, FastEthernet0/0
O       172.16.200.200 [110/21] via 10.1.100.100, 00:57:17, FastEthernet0/0
C       172.16.101.101 is directly connected, Loopback0
O       172.16.100.100 [110/11] via 10.1.100.100, 00:57:17, FastEthernet0/0
O       172.16.201.202 [110/31] via 10.1.100.100, 00:57:17, FastEthernet0/0
O       172.16.101.102 [110/11] via 10.1.100.102, 00:57:17, FastEthernet0/0
     10.0.0.0/8 is variably subnetted, 11 subnets, 3 masks
O       10.0.0.0/24 [110/20] via 10.1.100.100, 00:57:18, FastEthernet0/0
O       10.2.60.0/30 [110/80] via 10.1.100.100, 00:57:18, FastEthernet0/0
C       10.1.50.0/30 is directly connected, Serial0/0
O       10.2.201.128/25 [110/40] via 10.1.100.100, 00:57:18, FastEthernet0/0
C       10.1.101.0/25 is directly connected, FastEthernet0/1
C       10.1.100.0/24 is directly connected, FastEthernet0/0
O       10.2.200.0/24 [110/30] via 10.1.100.100, 00:57:20, FastEthernet0/0
O       10.2.201.0/25 [110/40] via 10.1.100.100, 00:57:20, FastEthernet0/0
O       10.1.101.128/25 [110/20] via 10.1.100.102, 00:57:20, FastEthernet0/0
O       10.0.255.4/30 [110/74] via 10.1.100.100, 00:57:20, FastEthernet0/0
O       10.0.255.0/30 [110/60] via 10.1.100.100, 00:57:20, FastEthernet0/0
r101#
```

```
r102#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     172.16.0.0/32 is subnetted, 6 subnets
O       172.16.201.201 [110/31] via 10.1.100.100, 00:57:55, FastEthernet0/0
O       172.16.200.200 [110/21] via 10.1.100.100, 00:57:55, FastEthernet0/0
O       172.16.101.101 [110/11] via 10.1.100.101, 00:57:55, FastEthernet0/0
O       172.16.100.100 [110/11] via 10.1.100.100, 00:58:05, FastEthernet0/0
O       172.16.201.202 [110/31] via 10.1.100.100, 00:57:55, FastEthernet0/0
C       172.16.101.102 is directly connected, Loopback0
     10.0.0.0/8 is variably subnetted, 10 subnets, 3 masks
O       10.0.0.0/24 [110/20] via 10.1.100.100, 00:57:57, FastEthernet0/0
O       10.2.60.0/30 [110/80] via 10.1.100.100, 00:57:57, FastEthernet0/0
C       10.1.50.0/30 is directly connected, Serial0/0
O       10.2.201.128/25 [110/40] via 10.1.100.100, 00:57:57, FastEthernet0/0
C       10.1.100.0/24 is directly connected, FastEthernet0/0
O       10.2.200.0/24 [110/30] via 10.1.100.100, 00:57:57, FastEthernet0/0
O       10.2.201.0/25 [110/40] via 10.1.100.100, 00:57:57, FastEthernet0/0
C       10.1.101.128/25 is directly connected, FastEthernet0/1
O       10.0.255.4/30 [110/74] via 10.1.100.100, 00:58:07, FastEthernet0/0
O       10.0.255.0/30 [110/60] via 10.1.100.100, 00:58:07, FastEthernet0/0
r102#
```

## 5.5  Mise en place d’OSPF

```
r101#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
r101(config)#router ospf 1
```

**Question 3** 

* Dans quel but entrez-vous le numéro de votre routeur dans la commande OSPF 
  * Ce numéro correspond au PID. La signification de ce numéro est locale au routeur. Cela permet de lancer plusieurs processus OSPF concomitants sur le routeur.

---

```
r101(config-router)#network 10.1.50.0 0.0.0.3 area 0
r101(config-router)#network 10.1.100.0 0.0.0.255 area 0
r101(config-router)#network 10.1.101.0 0.0.0.127 area 0
r101(config-router)#network 172.16.101.101 0.0.0.0 area 0
r101(config-router)#^Z
r101#
01:26:30: %SYS-5-CONFIG_I: Configured from console by console
r101#wr mem
Building configuration...
[OK]
r101#show ip protocols 
Routing Protocol is "ospf 1"
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  Router ID 172.16.101.101
  Number of areas in this router is 1. 1 normal 0 stub 0 nssa
  Maximum path: 4
  Routing for Networks:
    10.1.50.0 0.0.0.3 area 0
    10.1.100.0 0.0.0.255 area 0
    10.1.101.0 0.0.0.127 area 0
    10.1.101.128 0.0.0.127 area 0
    172.16.101.101 0.0.0.0 area 0
 Reference bandwidth unit is 100 mbps
  Routing Information Sources:
    Gateway         Distance      Last Update
    172.16.201.201       110      00:00:43
    172.16.100.100       110      01:25:39
    172.16.200.200       110      01:25:39
    172.16.201.202       110      01:25:39
    172.16.101.102       110      01:25:39
  Distance: (default is 110)

r101#
```

**Question 4** 

* À quelle interface l’adresse affichée comme router ID appartient-elle ?
  
  * A l'interface de loopback

* Quelle est la valeur de Maximum Path ?
  
  * elle est à 4

## 5.6  Observation de la table de routage

```
r101#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     172.16.0.0/32 is subnetted, 6 subnets
O       172.16.201.201 [110/31] via 10.1.100.100, 01:28:59, FastEthernet0/0
O       172.16.200.200 [110/21] via 10.1.100.100, 01:28:59, FastEthernet0/0
C       172.16.101.101 is directly connected, Loopback0
O       172.16.100.100 [110/11] via 10.1.100.100, 01:28:59, FastEthernet0/0
O       172.16.201.202 [110/31] via 10.1.100.100, 01:28:59, FastEthernet0/0
O       172.16.101.102 [110/11] via 10.1.100.102, 01:28:59, FastEthernet0/0
     10.0.0.0/8 is variably subnetted, 11 subnets, 3 masks
O       10.0.0.0/24 [110/20] via 10.1.100.100, 01:29:00, FastEthernet0/0
O       10.2.60.0/30 [110/80] via 10.1.100.100, 00:04:04, FastEthernet0/0
C       10.1.50.0/30 is directly connected, Serial0/0
O       10.2.201.128/25 [110/40] via 10.1.100.100, 01:29:00, FastEthernet0/0
C       10.1.101.0/25 is directly connected, FastEthernet0/1
C       10.1.100.0/24 is directly connected, FastEthernet0/0
O       10.2.200.0/24 [110/30] via 10.1.100.100, 01:29:02, FastEthernet0/0
O       10.2.201.0/25 [110/40] via 10.1.100.100, 01:29:02, FastEthernet0/0
O       10.1.101.128/25 [110/20] via 10.1.100.102, 01:29:02, FastEthernet0/0
O       10.0.255.4/30 [110/74] via 10.1.100.100, 01:29:02, FastEthernet0/0
O       10.0.255.0/30 [110/60] via 10.1.100.100, 01:29:02, FastEthernet0/0
r101#
```

**Question 5**

* Quels sont les indicateurs OSPF dans cet affichage ?
  
  * O - OSPF
  
  * IA - OSPF inter area 
  
  * N1 - OSPF NSSA external type 1
  
  * N2 - OSPF NSSA external type 2
  
  * E1 - OSPF external type 1 
  
  * E2 - OSPF external type 2

* Lequel apparait dans votre table ? 
  
  * O - OSPF

---

```
r101#show ip interface fa 0/0
FastEthernet0/0 is up, line protocol is up
  Internet address is 10.1.100.101/24
  Broadcast address is 255.255.255.255
  Address determined by non-volatile memory
  MTU is 1500 bytes
  Helper address is not set
  Directed broadcast forwarding is disabled
  Multicast reserved groups joined: 224.0.0.5 224.0.0.6
  Outgoing access list is not set
  Inbound  access list is not set
  Proxy ARP is enabled
  Local Proxy ARP is disabled
  Security level is default
  Split horizon is enabled
  ICMP redirects are always sent
  ICMP unreachables are always sent
  ICMP mask replies are never sent
  IP fast switching is enabled
  IP fast switching on the same interface is disabled
  IP Flow switching is disabled
  IP CEF switching is enabled
  IP CEF Fast switching turbo vector
  IP multicast fast switching is enabled
  IP multicast distributed fast switching is disabled
  IP route-cache flags are Fast, CEF
  Router Discovery is disabled
  IP output packet accounting is disabled
  IP access violation accounting is disabled
  TCP/IP header compression is disabled
  RTP/IP header compression is disabled
  Policy routing is disabled
  Network address translation is disabled
  BGP Policy Mapping is disabled
  WCCP Redirect outbound is disabled
  WCCP Redirect inbound is disabled
  WCCP Redirect exclude is disabled
r101# 
```

**Question 6**

* Quels sont les groupes multicast que votre routeur a rejoint ?
  
  * OSPFIGP OSPFIGP All Routers
  
  * OSPFIGP OSPFIGP Designated Routers

* Quelles adresses apparaissent dans la liste des groupes réservés ?
  
  * 224.0.0.5
  
  * 224.0.0.6

* Quels sont les noms de ces adresses multicast et à quoi servent-elles ?    
  
  * Il s'agit d'adresses appartenant au  "IPv4 Multicast Address Space Registry"
    La [fiche de l'IANA](https://www.iana.org/assignments/multicast-addresses/multicast-addresses.txt) décrit très bien leur utilité : 
    *"the range of addresses between 224.0.0.0 and 224.0.0.255, inclusive,
     is reserved for the use of routing protocols and other low-level
     topology discovery or maintenance protocols, such as gateway discovery
     and group membership reporting."* 

---

```
r101#show ip ospf interface
FastEthernet0/1 is up, line protocol is up 
  Internet Address 10.1.101.1/25, Area 0 
  Process ID 1, Router ID 172.16.101.101, Network Type BROADCAST, Cost: 10
  Transmit Delay is 1 sec, State DR, Priority 1
  Designated Router (ID) 172.16.101.101, Interface address 10.1.101.1
  No backup designated router on this network
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
    oob-resync timeout 40
    Hello due in 00:00:03
  Supports Link-local Signaling (LLS)
  Cisco NSF helper support enabled
  IETF NSF helper support enabled
  Index 4/4, flood queue length 0
  Next 0x0(0)/0x0(0)
  Last flood scan length is 0, maximum is 0
  Last flood scan time is 0 msec, maximum is 0 msec
  Neighbor Count is 0, Adjacent neighbor count is 0 
  Suppress hello for 0 neighbor(s)
Loopback0 is up, line protocol is up 
  Internet Address 172.16.101.101/32, Area 0 
  Process ID 1, Router ID 172.16.101.101, Network Type LOOPBACK, Cost: 1
  Loopback interface is treated as a stub Host
FastEthernet0/0 is up, line protocol is up 
  Internet Address 10.1.100.101/24, Area 0 
  Process ID 1, Router ID 172.16.101.101, Network Type BROADCAST, Cost: 10
  Transmit Delay is 1 sec, State BDR, Priority 1
  Designated Router (ID) 172.16.101.102, Interface address 10.1.100.102
  Backup Designated router (ID) 172.16.101.101, Interface address 10.1.100.101
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
    oob-resync timeout 40
    Hello due in 00:00:08
  Supports Link-local Signaling (LLS)
  Cisco NSF helper support enabled
  IETF NSF helper support enabled
  Index 2/2, flood queue length 0
  Next 0x0(0)/0x0(0)
  Last flood scan length is 1, maximum is 1
  Last flood scan time is 0 msec, maximum is 4 msec
  Neighbor Count is 2, Adjacent neighbor count is 2 
    Adjacent with neighbor 172.16.100.100
    Adjacent with neighbor 172.16.101.102  (Designated Router)
  Suppress hello for 0 neighbor(s)
Serial0/0 is up, line protocol is up 
  Internet Address 10.1.50.1/30, Area 0 
  Process ID 1, Router ID 172.16.101.101, Network Type POINT_TO_POINT, Cost: 50
  Transmit Delay is 1 sec, State POINT_TO_POINT
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
    oob-resync timeout 40
    Hello due in 00:00:03
  Supports Link-local Signaling (LLS)
  Cisco NSF helper support enabled
  IETF NSF helper support enabled
  Index 1/1, flood queue length 0
  Next 0x0(0)/0x0(0)
  Last flood scan length is 1, maximum is 1
  Last flood scan time is 0 msec, maximum is 4 msec
  Neighbor Count is 1, Adjacent neighbor count is 1 
    Adjacent with neighbor 172.16.101.102
  Suppress hello for 0 neighbor(s)
r101#
```

| Interface | Type Réseau    | Coût | Priorité | Numéro du DR        | Numéro du BDR                               |
|:---------:|:--------------:|:----:|:--------:|:-------------------:|:-------------------------------------------:|
| **Lo0**   | LOOPBACK       | 1    | /        | /                   | /                                           |
| **Fa0/0** | BROADCAST      | 10   | 1        | R102-172.16.101.102 | R101-172.16.101.101                         |
| **Fa0/1** | BROADCAST      | 10   | 1        | 172.16.101.101      | No backup designated router on this network |
| **S0/0**  | POINT_TO_POINT | 50   | /        | /                   | /                                           |

* Calculez le coût OSPF du chemin pour atteindre l'interface loopback de R202 depuis R101
  * R101-Fa0/0(10) + R100-fa0/1(10) + R200-Fa0/0(10) + R202-Lo0(1) = 31

---

```
r101#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     172.16.0.0/32 is subnetted, 6 subnets
O       172.16.201.201 [110/31] via 10.1.100.100, 01:37:34, FastEthernet0/0
O       172.16.200.200 [110/21] via 10.1.100.100, 01:37:34, FastEthernet0/0
C       172.16.101.101 is directly connected, Loopback0
O       172.16.100.100 [110/11] via 10.1.100.100, 01:37:34, FastEthernet0/0
O       172.16.201.202 [110/31] via 10.1.100.100, 01:37:34, FastEthernet0/0
O       172.16.101.102 [110/11] via 10.1.100.102, 01:37:34, FastEthernet0/0
     10.0.0.0/8 is variably subnetted, 11 subnets, 3 masks
O       10.0.0.0/24 [110/20] via 10.1.100.100, 01:37:35, FastEthernet0/0
O       10.2.60.0/30 [110/80] via 10.1.100.100, 00:12:39, FastEthernet0/0
C       10.1.50.0/30 is directly connected, Serial0/0
O       10.2.201.128/25 [110/40] via 10.1.100.100, 01:37:35, FastEthernet0/0
C       10.1.101.0/25 is directly connected, FastEthernet0/1
C       10.1.100.0/24 is directly connected, FastEthernet0/0
O       10.2.200.0/24 [110/30] via 10.1.100.100, 01:37:37, FastEthernet0/0
O       10.2.201.0/25 [110/40] via 10.1.100.100, 01:37:37, FastEthernet0/0
O       10.1.101.128/25 [110/20] via 10.1.100.102, 01:37:37, FastEthernet0/0
O       10.0.255.4/30 [110/74] via 10.1.100.100, 01:37:37, FastEthernet0/0
O       10.0.255.0/30 [110/60] via 10.1.100.100, 01:37:37, FastEthernet0/0
r101#
```

**Question 7**

* Est-ce que le coût figurant dans la table de routage est le même que celui
  que vous avez calculé ? 
  
  * Oui : 172.16.201.202 [110/**31**]

* Voyez-vous des numéros d’aires dans la table de routage ? 
  
  * Non

---

```
r101#show ip ospf database

            OSPF Router with ID (172.16.101.101) (Process ID 1)

                Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
172.16.100.100  172.16.100.100  1940        0x80000006 0x007A1B 7
172.16.101.101  172.16.101.101  835         0x80000006 0x00F251 5
172.16.101.102  172.16.101.102  1961        0x80000005 0x00813F 5
172.16.200.200  172.16.200.200  1930        0x80000005 0x006271 7
172.16.201.201  172.16.201.201  1966        0x80000005 0x00F41E 5
172.16.201.202  172.16.201.202  1934        0x80000004 0x002669 5

                Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.0.0.200      172.16.200.200  1930        0x80000003 0x005FE3
10.1.100.102    172.16.101.102  1961        0x80000004 0x008CB2
10.2.200.202    172.16.201.202  1933        0x80000004 0x00EE63
r101#
```

**Question 8**

* Voyez-vous le numéro d’aire dans le résultat ?
  
  * Oui : Area 0

**Question 9**

* Combien de routeurs ont injecté des informations dans la base de données ?
  * 6

**Question 10**

* Quels sont les routeurs qui injectent des routes pour les réseaux
  FastEthernet (100 et 200) ?
  * Les Designated Routers

---

```
r101#show ip ospf neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
172.16.100.100    1   FULL/DROTHER    00:00:34    10.1.100.100    FastEthernet0/0
172.16.101.102    1   FULL/DR         00:00:36    10.1.100.102    FastEthernet0/0
172.16.101.102    0   FULL/  -        00:00:31    10.1.50.2       Serial0/0
r101#
```

**Question 11**

* Quelle commande garantirait qu'il ne devienne pas BDR si le DR tombait en
  panne ?
  * *ip ospf priority 0*

# 6. OSPF EN AIRES MULTIPLES

## 6.2  Schéma maquette

Utilisez la commande suivante pour revoir la configuration OSPF de votre routeur :

```
r101#show running-config | begin router
router ospf 101
 log-adjacency-changes
 network 10.1.50.0 0.0.0.3 area 0
 network 10.1.100.0 0.0.0.255 area 0
 network 10.1.101.0 0.0.0.127 area 0
 network 172.16.101.101 0.0.0.0 area 0
!
ip forward-protocol nd
!
!
ip http server
no ip http secure-server
!
no cdp log mismatch duplex
!
!
!
!
!
!
control-plane
!
!    

r101#
```

Revenez en mode configuration et entrez dans la section routeur OSPF :

```
r101#conf t                            
Enter configuration commands, one per line.  End with CNTL/Z.
r101(config)#router ospf 101
r101(config-router)#
```

Supprimez l’instruction network qui affecte votre interface fa 0/1 à l’aire 0.

```
r101(config-router)#f0/1 no network 10.1.101.0 0.0.0.127 area 0
r101(config-router)#s0/0 no network 10.1.50.0 0.0.0.3 area 0
r101(config-router)#loo0 no network 172.16.101.101 0.0.0.0 area 0
r101(config-router)#
00:07:43: %OSPF-5-ADJCHG: Process 101, Nbr 172.16.101.102 on Serial0/0 from FULL to DOWN, Neighbor Down: Interface down or detached
r101(config-router)#
```

Redéfinir l’Id de vos 6 routeurs OSPF (exemple pour r202):

```
r202#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
r202(config)#router ospf 202
r202(config-router)#router-id 202.202.202.202
Reload or use "clear ip ospf process" command, for this to take effect
r202#clear ip ospf process
Reset ALL OSPF processes? [no]: YES
r202#
*Mar  1 00:16:00.979: %OSPF-5-ADJCHG: Process 202, Nbr 200.200.200.200 on FastEthernet0/0 from FULL to DOWN, Neighbor Down: Interface down or detached
*Mar  1 00:16:00.979: %OSPF-5-ADJCHG: Process 202, Nbr 201.201.201.201 on FastEthernet0/0 from FULL to DOWN, Neighbor Down: Interface down or detached
*Mar  1 00:16:00.979: %OSPF-5-ADJCHG: Process 202, Nbr 201.201.201.201 on Serial0/0 from FULL to DOWN, Neighbor Down: Interface down or detached
*Mar  1 00:16:01.007: %OSPF-5-ADJCHG: Process 202, Nbr 201.201.201.201 on Serial0/0 from LOADING to FULL, Loading Done
r202#show ip protocols  
Routing Protocol is "ospf 202"
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  Router ID 202.202.202.202
  Number of areas in this router is 1. 1 normal 0 stub 0 nssa
  Maximum path: 4
  Routing for Networks:
    10.2.60.0 0.0.0.3 area 0
    10.2.200.0 0.0.0.255 area 0
    10.2.201.128 0.0.0.127 area 0
    172.16.201.202 0.0.0.0 area 0
 Reference bandwidth unit is 100 mbps
  Routing Information Sources:
    Gateway         Distance      Last Update
    201.201.201.201      110      00:00:08
    200.200.200.200      110      00:02:41
    102.102.102.102      110      00:02:00
    172.16.100.100       110      00:02:00
    172.16.101.101       110      00:13:40
    172.16.200.200       110      00:15:12
    172.16.201.201       110      00:15:12
    172.16.101.102       110      00:13:22
  Distance: (default is 110)

r202#
```

Revenir dans la partie routeur de votre configuration et utilisez la syntaxe suivante pour ajouter ces trois interfaces à votre nouvelle aire (utilisez l’historique des commandes pour éviter de tout retaper) ; Exemple pour r101 :

```
r101#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
r101(config)#router ospf 101
r101(config-router)#network 10.1.101.0 0.0.0.127 area 101     
r101(config-router)#network 10.1.50.0 0.0.0.3 area 101   
r101(config-router)#network 172.16.101.101 0.0.0.0 area 101   
r101#show ip protocols                 
Routing Protocol is "ospf 101"
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  Router ID 101.101.101.101
  It is an area border router
  Number of areas in this router is 2. 2 normal 0 stub 0 nssa
  Maximum path: 4
  Routing for Networks:
    10.1.50.0 0.0.0.3 area 101
    10.1.100.0 0.0.0.255 area 0
    10.1.101.0 0.0.0.127 area 101
    172.16.101.101 0.0.0.0 area 101
 Reference bandwidth unit is 100 mbps
  Routing Information Sources:
    Gateway         Distance      Last Update
    202.202.202.202      110      00:00:39
    201.201.201.201      110      00:00:14
    200.200.200.200      110      00:00:39
    102.102.102.102      110      00:00:34
    172.16.201.201       110      00:07:55
    172.16.100.100       110      00:00:39
    172.16.200.200       110      00:10:51
    172.16.201.202       110      00:06:36

r101#show running-config | begin router
router ospf 101
 router-id 101.101.101.101
 log-adjacency-changes
 network 10.1.50.0 0.0.0.3 area 101
 network 10.1.100.0 0.0.0.255 area 0
 network 10.1.101.0 0.0.0.127 area 101
 network 172.16.101.101 0.0.0.0 area 101
!
ip forward-protocol nd
!
!
ip http server
no ip http secure-server
!
no cdp log mismatch duplex
!
!
!
!
!
!
control-plane
!

r101#
```

| Interface        | Numéro d'aire |
| ---------------- |:-------------:|
| FastEthernet 0/0 | 0             |
| FastEthernet 0/1 | 101           |
| Serial 0/0       | 101           |
| Loopback 0       | 101           |

---

Exemple pour r101 : 

```
r101#clear ip ospf process 
Reset ALL OSPF processes? [no]: yes
r101#
00:51:32: %OSPF-5-ADJCHG: Process 101, Nbr 102.102.102.102 on FastEthernet0/0 from FULL to DOWN, Neighbor Down: Interface down or detached
00:51:32: %OSPF-5-ADJCHG: Process 101, Nbr 172.16.100.100 on FastEthernet0/0 from EXSTART to DOWN, Neighbor Down: Interface down or detached
00:51:32: %OSPF-5-ADJCHG: Process 101, Nbr 102.102.102.102 on Serial0/0 from FULL to DOWN, Neighbor Down: Interface down or detached
00:51:32: %OSPF-5-ADJCHG: Process 101, Nbr 102.102.102.102 on Serial0/0 from LOADING to FULL, Loading Done
r101#
00:51:42: %OSPF-5-ADJCHG: Process 101, Nbr 102.102.102.102 on Serial0/0 from LOADING to FULL, Loading Done
r101#
00:52:15: %OSPF-5-ADJCHG: Process 101, Nbr 172.16.100.100 on FastEthernet0/0 from LOADING to FULL, Loading Done
r101#
00:52:22: %OSPF-5-ADJCHG: Process 101, Nbr 102.102.102.102 on FastEthernet0/0 from LOADING to FULL, Loading Done
r101#show ip protocols
Routing Protocol is "ospf 101"
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  Router ID 101.101.101.101
  It is an area border router
  Number of areas in this router is 2. 2 normal 0 stub 0 nssa
  Maximum path: 4
  Routing for Networks:
    10.1.50.0 0.0.0.3 area 101
    10.1.100.0 0.0.0.255 area 0
    10.1.101.0 0.0.0.127 area 101
    172.16.101.101 0.0.0.0 area 101
 Reference bandwidth unit is 100 mbps
  Routing Information Sources:
    Gateway         Distance      Last Update
    202.202.202.202      110      00:03:05
    201.201.201.201      110      00:04:18
    200.200.200.200      110      00:00:17
    102.102.102.102      110      00:00:41
    172.16.201.201       110      00:38:25
    172.16.100.100       110      00:00:17
    172.16.200.200       110      00:41:21
    172.16.201.202       110      00:37:06
    Gateway         Distance      Last Update
    172.16.101.102       110      00:45:22
  Distance: (default is 110)

r101#
```

```
r101#show ip route 
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     172.16.0.0/32 is subnetted, 6 subnets
O IA    172.16.201.201 [110/31] via 10.1.100.100, 00:04:48, FastEthernet0/0
O       172.16.200.200 [110/21] via 10.1.100.100, 00:05:38, FastEthernet0/0
C       172.16.101.101 is directly connected, Loopback0
O       172.16.100.100 [110/11] via 10.1.100.100, 00:05:38, FastEthernet0/0
O IA    172.16.201.202 [110/31] via 10.1.100.100, 00:04:48, FastEthernet0/0
O       172.16.101.102 [110/51] via 10.1.50.2, 00:06:01, Serial0/0
     10.0.0.0/8 is variably subnetted, 11 subnets, 3 masks
O       10.0.0.0/24 [110/20] via 10.1.100.100, 00:05:39, FastEthernet0/0
O IA    10.2.60.0/30 [110/80] via 10.1.100.100, 00:04:49, FastEthernet0/0
C       10.1.50.0/30 is directly connected, Serial0/0
O IA    10.2.201.128/25 [110/40] via 10.1.100.100, 00:04:49, FastEthernet0/0
C       10.1.101.0/25 is directly connected, FastEthernet0/1
C       10.1.100.0/24 is directly connected, FastEthernet0/0
O       10.2.200.0/24 [110/30] via 10.1.100.100, 00:05:09, FastEthernet0/0
O IA    10.2.201.0/25 [110/40] via 10.1.100.100, 00:04:59, FastEthernet0/0
O       10.1.101.128/25 [110/60] via 10.1.50.2, 00:06:12, Serial0/0
O       10.0.255.4/30 [110/74] via 10.1.100.100, 00:05:49, FastEthernet0/0
O       10.0.255.0/30 [110/60] via 10.1.100.100, 00:05:49, FastEthernet0/0
r101#
```

```
r102#show ip route 
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     172.16.0.0/32 is subnetted, 6 subnets
O IA    172.16.201.201 [110/31] via 10.1.100.100, 00:05:26, FastEthernet0/0
O       172.16.200.200 [110/21] via 10.1.100.100, 00:05:59, FastEthernet0/0
O       172.16.101.101 [110/51] via 10.1.50.1, 00:06:49, Serial0/0
O       172.16.100.100 [110/11] via 10.1.100.100, 00:05:59, FastEthernet0/0
O IA    172.16.201.202 [110/31] via 10.1.100.100, 00:05:26, FastEthernet0/0
C       172.16.101.102 is directly connected, Loopback0
     10.0.0.0/8 is variably subnetted, 11 subnets, 3 masks
O       10.0.0.0/24 [110/20] via 10.1.100.100, 00:06:01, FastEthernet0/0
O IA    10.2.60.0/30 [110/80] via 10.1.100.100, 00:05:27, FastEthernet0/0
C       10.1.50.0/30 is directly connected, Serial0/0
O IA    10.2.201.128/25 [110/40] via 10.1.100.100, 00:05:27, FastEthernet0/0
O       10.1.101.0/25 [110/60] via 10.1.50.1, 00:06:51, Serial0/0
C       10.1.100.0/24 is directly connected, FastEthernet0/0
O       10.2.200.0/24 [110/30] via 10.1.100.100, 00:05:37, FastEthernet0/0
O IA    10.2.201.0/25 [110/40] via 10.1.100.100, 00:05:27, FastEthernet0/0
C       10.1.101.128/25 is directly connected, FastEthernet0/1
O       10.0.255.4/30 [110/74] via 10.1.100.100, 00:06:01, FastEthernet0/0
O       10.0.255.0/30 [110/60] via 10.1.100.100, 00:06:01, FastEthernet0/0
r102#
```

**Question 12**

* Quelle est la différence entre les routes marquées *O IA* et les routes marquées seulement *O* ou *C* ?
  * O IA : Routes qui ont pour origine une autre aire
  * O : Routes générées à l'intérieur d'une area 
  * C : Route directement connectée au routeur 

---

```
r101#show ip ospf database 

            OSPF Router with ID (101.101.101.101) (Process ID 101)

                Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
101.101.101.101 101.101.101.101 526         0x80000006 0x004E02 1
102.102.102.102 102.102.102.102 520         0x80000008 0x000C39 1
100.100.100.100 100.100.100.100 532         0x8000000B 0x000D40 7
200.200.200.200 200.200.200.200 492         0x80000008 0x005B30 7
201.201.201.201 201.201.201.201 494         0x80000002 0x00BAE0 1
202.202.202.202 202.202.202.202 492         0x80000002 0x007C16 1

                Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.0.0.100      100.100.100.100 557         0x80000001 0x006934
10.1.100.100    100.100.100.100 522         0x80000002 0x001017
10.2.200.202    202.202.202.202 493         0x80000001 0x00E80D

                Summary Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.50.0       101.101.101.101 569         0x80000001 0x00D466
10.1.50.0       102.102.102.102 1421        0x80000001 0x00B680
10.1.101.0      101.101.101.101 571         0x80000001 0x002587
10.1.101.0      102.102.102.102 1421        0x80000001 0x00FC79
10.1.101.128    101.101.101.101 551         0x80000001 0x0016E3
10.1.101.128    102.102.102.102 1411        0x80000001 0x000226
10.2.60.0       201.201.201.201 547         0x80000001 0x009608
10.2.60.0       202.202.202.202 531         0x80000001 0x007822
10.2.201.0      201.201.201.201 547         0x80000001 0x0005B0
10.2.201.128    202.202.202.202 531         0x80000001 0x00E14F
172.16.101.101  101.101.101.101 571         0x80000001 0x00DA44
172.16.101.101  102.102.102.102 1421        0x80000001 0x00B236
172.16.101.102  101.101.101.101 551         0x80000001 0x00C625
172.16.101.102  102.102.102.102 1394        0x80000001 0x00B267
172.16.201.201  201.201.201.201 547         0x80000001 0x00DAE9
172.16.201.202  202.202.202.202 531         0x80000001 0x00B20D

                Router Link States (Area 101)

Link ID         ADV Router      Age         Seq#       Checksum Link count
101.101.101.101 101.101.101.101 568         0x80000005 0x004E8C 4
102.102.102.102 102.102.102.102 559         0x80000004 0x00AAAA 4

                Summary Net Link States (Area 101)

Link ID         ADV Router      Age         Seq#       Checksum
10.0.0.0        101.101.101.101 529         0x80000001 0x00ED9B
10.0.0.0        102.102.102.102 515         0x80000001 0x00CFB5
10.0.255.0      101.101.101.101 531         0x80000001 0x006DF6
10.0.255.0      102.102.102.102 515         0x80000001 0x004F11
10.0.255.4      101.101.101.101 531         0x80000001 0x00D180
10.0.255.4      102.102.102.102 515         0x80000001 0x00B39A
10.1.100.0      101.101.101.101 574         0x80000001 0x002D01
10.1.100.0      102.102.102.102 565         0x80000001 0x000F1B
10.2.60.0       101.101.101.101 481         0x80000001 0x00878A
10.2.60.0       102.102.102.102 482         0x80000001 0x0069A4
10.2.200.0      101.101.101.101 531         0x80000001 0x00991B
10.2.200.0      102.102.102.102 515         0x80000001 0x007B35
10.2.201.0      101.101.101.101 481         0x80000001 0x00F533
10.2.201.0      102.102.102.102 482         0x80000001 0x00D74D
10.2.201.128    101.101.101.101 482         0x80000001 0x00F0B7
10.2.201.128    102.102.102.102 483         0x80000001 0x00D2D1
172.16.100.100  101.101.101.101 532         0x80000001 0x0054C2
172.16.100.100  102.102.102.102 516         0x80000001 0x0036DC
172.16.200.200  101.101.101.101 532         0x80000001 0x007CC7
172.16.200.200  102.102.102.102 516         0x80000001 0x005EE1
172.16.201.201  101.101.101.101 482         0x80000001 0x00CB6C
172.16.201.201  102.102.102.102 484         0x80000001 0x00AD86
172.16.201.202  101.101.101.101 484         0x80000001 0x00C175
172.16.201.202  102.102.102.102 485         0x80000001 0x00A38F
r101# 
```

| Type de LSA       | Nombre dans l'aire 0 | Nombre dans votre aire |
|:----------------- |:--------------------:|:----------------------:|
| 1 Router          | 6                    | 2                      |
| 2 Network         | 3                    | 0                      |
| 3 Summary Network | 16                   | 24                     |
| 4 ASBR Summary    | 0                    | 0                      |
| 5 External        | 0                    | 0                      |

## 6.3  Configurer l’agrégation des routes

Exemple pour R101

| Interface           | F0/1          | S0/0            | Lo0            |
| ------------------- | ------------- | --------------- | -------------- |
| Votre adresse       | 10.1.101.101  | 10.1.50.1       | 172.16.101.101 |
| Adresse des voisins | 10.1.101.129  | 10.1.50.2       |                |
| <range-address>     | 10.1.101.0    | 10.1.50.0       | 172.16.101.0   |
| <range-mask>        | 255.255.255.0 | 255.255.255.252 | 255.255.255.0  |

```
r101#show running-config | begin router
router ospf 101
 router-id 101.101.101.101
 log-adjacency-changes
 network 10.1.50.0 0.0.0.3 area 101
 network 10.1.100.0 0.0.0.255 area 0
 network 10.1.101.0 0.0.0.127 area 101
 network 172.16.101.101 0.0.0.0 area 101
!
ip forward-protocol nd
!
!
ip http server
no ip http secure-server
!
no cdp log mismatch duplex
!
!
!
!
!
!
control-plane
!

r101#
r101#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
r101(config)#router ospf 101
r101(config-router)#area 101 range 10.1.101.0 255.255.255.0
r101(config-router)#area 101 range 10.1.50.0 255.255.255.252
r101(config-router)#area 101 range 172.16.101.0 255.255.255.0 
r101(config-router)#
r101#
01:55:48: %SYS-5-CONFIG_I: Configured from console by console
r101#wr
Building configuration...
[OK]
r101#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     172.16.0.0/16 is variably subnetted, 7 subnets, 2 masks
O IA    172.16.201.201/32 [110/31] via 10.1.100.100, 00:01:14, FastEthernet0/0
O       172.16.200.200/32 [110/21] via 10.1.100.100, 00:01:14, FastEthernet0/0
C       172.16.101.101/32 is directly connected, Loopback0
O       172.16.100.100/32 [110/11] via 10.1.100.100, 00:01:14, FastEthernet0/0
O IA    172.16.201.202/32 [110/31] via 10.1.100.100, 00:01:14, FastEthernet0/0
O       172.16.101.102/32 [110/51] via 10.1.50.2, 00:01:14, Serial0/0
O       172.16.101.0/24 is a summary, 00:01:15, Null0
     10.0.0.0/8 is variably subnetted, 12 subnets, 3 masks
O       10.0.0.0/24 [110/20] via 10.1.100.100, 00:01:15, FastEthernet0/0
O IA    10.2.60.0/30 [110/80] via 10.1.100.100, 00:01:15, FastEthernet0/0
C       10.1.50.0/30 is directly connected, Serial0/0
O IA    10.2.201.128/25 [110/40] via 10.1.100.100, 00:01:15, FastEthernet0/0
C       10.1.101.0/25 is directly connected, FastEthernet0/1
O       10.1.101.0/24 is a summary, 00:01:17, Null0
C       10.1.100.0/24 is directly connected, FastEthernet0/0
O       10.2.200.0/24 [110/30] via 10.1.100.100, 00:01:17, FastEthernet0/0
O IA    10.2.201.0/25 [110/40] via 10.1.100.100, 00:01:17, FastEthernet0/0
O       10.1.101.128/25 [110/60] via 10.1.50.2, 00:01:17, Serial0/0
O       10.0.255.4/30 [110/74] via 10.1.100.100, 00:01:17, FastEthernet0/0
O       10.0.255.0/30 [110/60] via 10.1.100.100, 00:01:17, FastEthernet0/0
r101#
r101#show ip ospf database

            OSPF Router with ID (101.101.101.101) (Process ID 101)

                Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
100.100.100.100 100.100.100.100 1129        0x80000002 0x0013C4 7
101.101.101.101 101.101.101.101 961         0x80000008 0x005EED 1
102.102.102.102 102.102.102.102 1149        0x8000000A 0x001C25 1
200.200.200.200 200.200.200.200 964         0x8000000B 0x0032D9 7
201.201.201.201 201.201.201.201 1911        0x80000003 0x00B8E1 1
202.202.202.202 202.202.202.202 1976        0x80000003 0x007A17 1

                Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.0.0.200      200.200.200.200 964         0x80000002 0x00028C
10.1.100.102    102.102.102.102 1149        0x80000002 0x0020E4
10.2.200.202    202.202.202.202 1976        0x80000002 0x00E60E

                Summary Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.50.0       101.101.101.101 1971        0x80000002 0x00D267
10.1.50.0       102.102.102.102 1908        0x80000002 0x00B481
10.1.101.0      101.101.101.101 210         0x80000003 0x001E0D
10.1.101.0      102.102.102.102 1908        0x80000002 0x00FA7A
10.1.101.128    102.102.102.102 1908        0x80000002 0x00FF27
10.2.60.0       201.201.201.201 1913        0x80000002 0x009409
10.2.60.0       202.202.202.202 1978        0x80000002 0x007623
10.2.201.0      201.201.201.201 1913        0x80000002 0x0003B1
10.2.201.128    202.202.202.202 1978        0x80000002 0x00DF50
172.16.101.0    101.101.101.101 115         0x80000001 0x00D0B3
172.16.101.101  102.102.102.102 1908        0x80000002 0x00B037
172.16.101.102  102.102.102.102 1909        0x80000002 0x00B068
172.16.201.201  201.201.201.201 1913        0x80000002 0x00D8EA
172.16.201.202  202.202.202.202 1978        0x80000002 0x00B00E

                Router Link States (Area 101)

Link ID         ADV Router      Age         Seq#       Checksum Link count
101.101.101.101 101.101.101.101 1973        0x80000006 0x004C8D 4
102.102.102.102 102.102.102.102 1910        0x80000005 0x00A8AB 4

                Summary Net Link States (Area 101)

Link ID         ADV Router      Age         Seq#       Checksum
10.0.0.0        101.101.101.101 964         0x80000002 0x00EB9C
10.0.0.0        102.102.102.102 1152        0x80000002 0x00CDB6
10.0.255.0      101.101.101.101 964         0x80000002 0x006BF7
10.0.255.0      102.102.102.102 1152        0x80000002 0x004D12
10.0.255.4      101.101.101.101 964         0x80000002 0x00CF81
10.0.255.4      102.102.102.102 1152        0x80000002 0x00B19B
10.1.100.0      101.101.101.101 964         0x80000002 0x002B02
10.1.100.0      102.102.102.102 1152        0x80000002 0x000D1C
10.1.101.0      102.102.102.102 213         0x80000001 0x0068B6
10.2.60.0       101.101.101.101 964         0x80000002 0x00858B
10.2.60.0       102.102.102.102 1152        0x80000002 0x0067A5
10.2.200.0      101.101.101.101 966         0x80000002 0x00971C
10.2.200.0      102.102.102.102 1153        0x80000002 0x007936
10.2.201.0      101.101.101.101 966         0x80000002 0x00F334
10.2.201.0      102.102.102.102 1153        0x80000002 0x00D54E
10.2.201.128    101.101.101.101 966         0x80000002 0x00EEB8
10.2.201.128    102.102.102.102 1153        0x80000002 0x00D0D2
172.16.100.100  101.101.101.101 966         0x80000002 0x0052C3
172.16.100.100  102.102.102.102 1153        0x80000002 0x0034DD
172.16.101.0    102.102.102.102 119         0x80000001 0x00175F
172.16.200.200  101.101.101.101 966         0x80000002 0x007AC8
172.16.200.200  102.102.102.102 1153        0x80000002 0x005CE2
172.16.201.201  101.101.101.101 966         0x80000002 0x00C96D
172.16.201.201  102.102.102.102 1153        0x80000002 0x00AB87
172.16.201.202  101.101.101.101 966         0x80000002 0x00BF76
172.16.201.202  102.102.102.102 1153        0x80000002 0x00A190
r101#  
```

```
r102#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
r102(config)#router ospf 102
r102(config-router)#area 101 range 10.1.101.0 255.255.255.0
r102(config-router)#area 101 range 10.1.50.0 255.255.255.252
r102(config-router)#area 101 range 172.16.101.0 255.255.255.0
r102(config-router)#  
r102#
02:00:22: %SYS-5-CONFIG_I: Configured from console by console
r102#wr
Building configuration...
[OK]
r102#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     172.16.0.0/16 is variably subnetted, 7 subnets, 2 masks
O IA    172.16.201.201/32 [110/31] via 10.1.100.100, 00:00:35, FastEthernet0/0
O       172.16.200.200/32 [110/21] via 10.1.100.100, 00:00:35, FastEthernet0/0
O       172.16.101.101/32 [110/51] via 10.1.50.1, 00:00:35, Serial0/0
O       172.16.100.100/32 [110/11] via 10.1.100.100, 00:00:35, FastEthernet0/0
O IA    172.16.201.202/32 [110/31] via 10.1.100.100, 00:00:35, FastEthernet0/0
C       172.16.101.102/32 is directly connected, Loopback0
O       172.16.101.0/24 is a summary, 00:00:36, Null0
     10.0.0.0/8 is variably subnetted, 12 subnets, 3 masks
O       10.0.0.0/24 [110/20] via 10.1.100.100, 00:00:36, FastEthernet0/0
O IA    10.2.60.0/30 [110/80] via 10.1.100.100, 00:00:36, FastEthernet0/0
C       10.1.50.0/30 is directly connected, Serial0/0
O IA    10.2.201.128/25 [110/40] via 10.1.100.100, 00:00:36, FastEthernet0/0
O       10.1.101.0/25 [110/60] via 10.1.50.1, 00:00:36, Serial0/0
O       10.1.101.0/24 is a summary, 00:00:36, Null0
C       10.1.100.0/24 is directly connected, FastEthernet0/0
O       10.2.200.0/24 [110/30] via 10.1.100.100, 00:00:36, FastEthernet0/0
O IA    10.2.201.0/25 [110/40] via 10.1.100.100, 00:00:36, FastEthernet0/0
C       10.1.101.128/25 is directly connected, FastEthernet0/1
O       10.0.255.4/30 [110/74] via 10.1.100.100, 00:00:36, FastEthernet0/0
O       10.0.255.0/30 [110/60] via 10.1.100.100, 00:00:36, FastEthernet0/0
r102#show ip ospf database

            OSPF Router with ID (10.1.101.129) (Process ID 101)

            OSPF Router with ID (102.102.102.102) (Process ID 102)

                Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
100.100.100.100 100.100.100.100 1372        0x80000002 0x0013C4 7
101.101.101.101 101.101.101.101 1205        0x80000008 0x005EED 1
102.102.102.102 102.102.102.102 1391        0x8000000A 0x001C25 1
200.200.200.200 200.200.200.200 1207        0x8000000B 0x0032D9 7
201.201.201.201 201.201.201.201 112         0x80000004 0x00B6E2 1
202.202.202.202 202.202.202.202 224         0x80000004 0x007818 1

                Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.0.0.200      200.200.200.200 1207        0x80000002 0x00028C
10.1.100.102    102.102.102.102 1391        0x80000002 0x0020E4
10.2.200.202    202.202.202.202 224         0x80000003 0x00E40F

                Summary Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.50.0       101.101.101.101 184         0x80000003 0x00D068
10.1.50.0       102.102.102.102 105         0x80000003 0x00B282
10.1.101.0      101.101.101.101 186         0x80000004 0x001C0E
10.1.101.0      102.102.102.102 100         0x80000004 0x00FD28
10.2.60.0       201.201.201.201 114         0x80000003 0x00920A
10.2.60.0       202.202.202.202 226         0x80000003 0x007424
10.2.201.0      201.201.201.201 114         0x80000003 0x0001B2
10.2.201.128    202.202.202.202 226         0x80000003 0x00DD51
172.16.101.0    101.101.101.101 359         0x80000001 0x00D0B3
172.16.101.0    102.102.102.102 49          0x80000001 0x00B2CD
172.16.201.201  201.201.201.201 114         0x80000003 0x00D6EB
172.16.201.202  202.202.202.202 226         0x80000003 0x00AE0F

                Router Link States (Area 101)

Link ID         ADV Router      Age         Seq#       Checksum Link count
101.101.101.101 101.101.101.101 186         0x80000007 0x004A8E 4
102.102.102.102 102.102.102.102 106         0x80000006 0x00A6AC 4

                Summary Net Link States (Area 101)

Link ID         ADV Router      Age         Seq#       Checksum
10.0.0.0        101.101.101.101 1209        0x80000002 0x00EB9C
10.0.0.0        102.102.102.102 1395        0x80000002 0x00CDB6
10.0.255.0      101.101.101.101 1210        0x80000002 0x006BF7
10.0.255.0      102.102.102.102 1395        0x80000002 0x004D12
10.0.255.4      101.101.101.101 1210        0x80000002 0x00CF81
10.0.255.4      102.102.102.102 1395        0x80000002 0x00B19B
10.1.100.0      101.101.101.101 1210        0x80000002 0x002B02
10.1.100.0      102.102.102.102 1395        0x80000002 0x000D1C
10.1.101.0      102.102.102.102 456         0x80000001 0x0068B6
10.2.60.0       101.101.101.101 1210        0x80000002 0x00858B
10.2.60.0       102.102.102.102 1395        0x80000002 0x0067A5
10.2.200.0      101.101.101.101 1210        0x80000002 0x00971C
10.2.200.0      102.102.102.102 1395        0x80000002 0x007936
10.2.201.0      101.101.101.101 1210        0x80000002 0x00F334
10.2.201.0      102.102.102.102 1395        0x80000002 0x00D54E
10.2.201.128    101.101.101.101 1211        0x80000002 0x00EEB8
10.2.201.128    102.102.102.102 1396        0x80000002 0x00D0D2
172.16.100.100  101.101.101.101 1211        0x80000002 0x0052C3
172.16.100.100  102.102.102.102 1397        0x80000002 0x0034DD
172.16.101.0    102.102.102.102 362         0x80000001 0x00175F
172.16.200.200  101.101.101.101 1211        0x80000002 0x007AC8
172.16.200.200  102.102.102.102 1397        0x80000002 0x005CE2
172.16.201.201  101.101.101.101 1211        0x80000002 0x00C96D
172.16.201.201  102.102.102.102 1398        0x80000002 0x00AB87
172.16.201.202  101.101.101.101 1212        0x80000002 0x00BF76
172.16.201.202  102.102.102.102 1398        0x80000002 0x00A190
r102#
```

**Question 13**

* Le nombre de LSA de type 3 pour votre aire a-t-il diminué depuis la dernière fois que vous avez consulté la base ?
  * Non,  cela dit j'estime que cela aurait du être le cas.
    J'associe ce comportement au fait que soit je n'ai pas laissé assez de temps entre les deux consultations, soit j'ai commis une erreur dans ma configuration.

## 6.4  Créer une aire terminale

```
r202#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
r202(config)#interf
r202(config)#interface fast
r202(config)#interface fastEthernet 0/0
r202(config-if)#shut
r202(config-if)#
*Mar  1 02:15:25.551: %OSPF-5-ADJCHG: Process 202, Nbr 200.200.200.200 on FastEthernet0/0 from FULL to DOWN, Neighbor Down: Interface down or detached
*Mar  1 02:15:25.555: %OSPF-5-ADJCHG: Process 202, Nbr 201.201.201.201 on FastEthernet0/0 from FULL to DOWN, Neighbor Down: Interface down or detached
r202(config-if)#
*Mar  1 02:15:27.535: %LINK-5-CHANGED: Interface FastEthernet0/0, changed state to administratively down
*Mar  1 02:15:28.535: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/0, changed state to down
r202(config-if)#exit
r202(config)#router ospf 202
r202(config-router)#no network 10.2.200.0 0.0.0.255 area 0
r202(config-router)#
r202#
*Mar  1 02:16:13.687: %SYS-5-CONFIG_I: Configured from console by console
r202#show ip route 
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     172.16.0.0/32 is subnetted, 1 subnets
C       172.16.201.202 is directly connected, Loopback0
     10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C       10.2.60.0/30 is directly connected, Serial0/0
C       10.2.201.128/25 is directly connected, FastEthernet0/1
```

**Question 14**

* En quoi ces routes diffèrent-elles des routes OSPF ?
  * Elles diffèrent dans le sens où elles sont directement connectées et donc plus taguées OSPF

---

```
r202#show ip protocol
Routing Protocol is "ospf 202"
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  Router ID 202.202.202.202
  Number of areas in this router is 1. 1 normal 0 stub 0 nssa
  Maximum path: 4
  Routing for Networks:
    10.2.60.0 0.0.0.3 area 201
    10.2.201.128 0.0.0.127 area 201
    172.16.201.202 0.0.0.0 area 201
 Reference bandwidth unit is 100 mbps
  Routing Information Sources:
    Gateway         Distance      Last Update
    100.100.100.100      110      01:11:11
    101.101.101.101      110      00:09:26
    201.201.201.201      110      01:24:56
    200.200.200.200      110      01:11:11
    102.102.102.102      110      00:17:59
    172.16.100.100       110      01:24:56
    172.16.101.101       110      02:15:23
    172.16.200.200       110      02:16:55
    172.16.201.201       110      02:16:55
    172.16.101.102       110      02:15:05
  Distance: (default is 110)

r202#
```

```
r201#show ip protocol
Routing Protocol is "ospf 201"
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  Router ID 201.201.201.201
  It is an area border router
  Number of areas in this router is 2. 1 normal 1 stub 0 nssa
  Maximum path: 4
  Routing for Networks:
    10.2.60.0 0.0.0.3 area 201
    10.2.200.0 0.0.0.255 area 0
    10.2.201.0 0.0.0.127 area 201
    172.16.201.201 0.0.0.0 area 201
 Reference bandwidth unit is 100 mbps
  Routing Information Sources:
    Gateway         Distance      Last Update
    100.100.100.100      110      01:11:31
    101.101.101.101      110      00:09:46
    202.202.202.202      110      01:25:16
    200.200.200.200      110      01:11:31
    102.102.102.102      110      00:18:19
    172.16.100.100       110      01:25:06
    172.16.101.101       110      02:15:44
    172.16.200.200       110      02:17:15
    Gateway         Distance      Last Update
    172.16.101.102       110      02:15:27
    172.16.201.202       110      02:02:47
  Distance: (default is 110)

r201#
```

**Question 15**

* Quelle est la différence majeure entre les deux bases de données ?
  
  Le routeur pair n'a plus de routes vers l'area 0

---

```
r101#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
r101(config)#router ospf 101
r101(config-router)#area 101 stub 
r101(config-router)#
02:19:32: %OSPF-5-ADJCHG: Process 101, Nbr 102.102.102.102 on Serial0/0 from FULL to DOWN, Neighbor Down: Adjacency forced to reset
r101(config-router)#
r101#
02:19:34: %SYS-5-CONFIG_I: Configured from console by console
r101#
```

```
r101#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     172.16.0.0/16 is variably subnetted, 7 subnets, 2 masks
O IA    172.16.201.201/32 [110/31] via 10.1.100.100, 00:03:41, FastEthernet0/0
O       172.16.200.200/32 [110/21] via 10.1.100.100, 00:03:41, FastEthernet0/0
C       172.16.101.101/32 is directly connected, Loopback0
O       172.16.100.100/32 [110/11] via 10.1.100.100, 00:03:41, FastEthernet0/0
O IA    172.16.201.202/32 [110/81] via 10.1.100.100, 00:00:59, FastEthernet0/0
O       172.16.101.102/32 [110/51] via 10.1.50.2, 00:02:40, Serial0/0
O       172.16.101.0/24 is a summary, 00:03:42, Null0
     10.0.0.0/8 is variably subnetted, 12 subnets, 3 masks
O       10.0.0.0/24 [110/20] via 10.1.100.100, 00:03:42, FastEthernet0/0
O IA    10.2.60.0/30 [110/80] via 10.1.100.100, 00:03:42, FastEthernet0/0
C       10.1.50.0/30 is directly connected, Serial0/0
O IA    10.2.201.128/25 [110/90] via 10.1.100.100, 00:01:01, FastEthernet0/0
C       10.1.101.0/25 is directly connected, FastEthernet0/1
O       10.1.101.0/24 is a summary, 00:03:42, Null0
C       10.1.100.0/24 is directly connected, FastEthernet0/0
O       10.2.200.0/24 [110/30] via 10.1.100.100, 00:03:42, FastEthernet0/0
O IA    10.2.201.0/25 [110/40] via 10.1.100.100, 00:03:42, FastEthernet0/0
O       10.1.101.128/25 [110/60] via 10.1.50.2, 00:02:42, Serial0/0
O       10.0.255.4/30 [110/74] via 10.1.100.100, 00:03:42, FastEthernet0/0
O       10.0.255.0/30 [110/60] via 10.1.100.100, 00:03:42, FastEthernet0/0
r101#
```

```
r102#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is 10.1.50.1 to network 0.0.0.0

     172.16.0.0/32 is subnetted, 6 subnets
O IA    172.16.201.201 [110/81] via 10.1.50.1, 00:03:08, Serial0/0
O IA    172.16.200.200 [110/71] via 10.1.50.1, 00:03:08, Serial0/0
O       172.16.101.101 [110/51] via 10.1.50.1, 00:03:08, Serial0/0
O IA    172.16.100.100 [110/61] via 10.1.50.1, 00:03:08, Serial0/0
O IA    172.16.201.202 [110/131] via 10.1.50.1, 00:01:24, Serial0/0
C       172.16.101.102 is directly connected, Loopback0
     10.0.0.0/8 is variably subnetted, 11 subnets, 3 masks
O IA    10.0.0.0/24 [110/70] via 10.1.50.1, 00:03:09, Serial0/0
O IA    10.2.60.0/30 [110/130] via 10.1.50.1, 00:03:09, Serial0/0
C       10.1.50.0/30 is directly connected, Serial0/0
O IA    10.2.201.128/25 [110/140] via 10.1.50.1, 00:01:25, Serial0/0
O       10.1.101.0/25 [110/60] via 10.1.50.1, 00:03:09, Serial0/0
O IA    10.1.100.0/24 [110/60] via 10.1.50.1, 00:03:09, Serial0/0
O IA    10.2.200.0/24 [110/80] via 10.1.50.1, 00:03:09, Serial0/0
O IA    10.2.201.0/25 [110/90] via 10.1.50.1, 00:03:09, Serial0/0
C       10.1.101.128/25 is directly connected, FastEthernet0/1
O IA    10.0.255.4/30 [110/124] via 10.1.50.1, 00:03:09, Serial0/0
O IA    10.0.255.0/30 [110/110] via 10.1.50.1, 00:03:09, Serial0/0
O*IA 0.0.0.0/0 [110/51] via 10.1.50.1, 00:03:09, Serial0/0
r102#
```

## 6.5  Configurer une aire totalement terminale

**Question 16**

* Qu'est-ce qui a changé dans la table de routage des deux routeurs ?
  
  * Sur R101, il n'y a plus de routes vers R202 et sur R201, il n'y a plus de routes vers R102

* Y a-t-il des entrées O IA dans la table de routage intérieure ?
  
  * Non

* Quelle modification de la table de routage intérieure indique maintenant une
  aire totalement terminale ?
  
  * Il y a maintenant une route par défaut O*IA

# ROUTAGE IPV6 AVEC OSPFV3

## 7.2  Attribution des adresses IPv6

Exemple de configuration des interface sur le routeur 101

```
r101#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
r101(config)#ipv6 unicast-routing
r101(config)#interface fastEthernet 0/0
r101(config-if)#ipv6 address fd00:fa:d0:100::101/64
r101(config-if)#ipv6 enable
r101(config-if)#exit
r101(config)#interface fastEthernet 0/1
r101(config-if)#ipv6 address fd00:fa:d0:101::1/64
r101(config-if)#ipv6 enable
r101(config-if)#exit  
r101(config)#interface serial 0/0
r101(config-if)#ipv6 address fd00:fa:d0:50::1/64          
r101(config-if)#ipv6 enable 
r101(config-if)#^Z
r101#
00:12:14: %SYS-5-CONFIG_I: Configured from console by console
r101#wr
Building configuration...
[OK]
```

Résultat des commandes *show ipv6 interface* & *show ipv6 interface brief*

```
r100#show ipv6 interface      
FastEthernet0/0 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::C001:DFF:FECD:0 
  No Virtual link-local address(es):
  Description: SW100_e0/0
  Global unicast address(es):
    FD00:FA:D0:100::100, subnet is FD00:FA:D0:100::/64 
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::5
    FF02::6
    FF02::1:FF00:100
    FF02::1:FFCD:0
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  ND advertised reachable time is 0 milliseconds
  ND advertised retransmit interval is 0 milliseconds
  ND router advertisements are sent every 200 seconds
  ND router advertisements live for 1800 seconds
  ND advertised default router preference is Medium
  Hosts use stateless autoconfig for addresses.
Serial0/0 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::C001:DFF:FECD:0 
  No Virtual link-local address(es):
  Description: R200_ser0/0
  Global unicast address(es):
    FD00:FA:D0:120::100, subnet is FD00:FA:D0:120::/64 
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::5
    FF02::1:FF00:100
    FF02::1:FFCD:0
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  Hosts use stateless autoconfig for addresses.
FastEthernet0/1 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::C001:DFF:FECD:1 
  No Virtual link-local address(es):
  Description: R200_fa0/1
  Global unicast address(es):
    FD00:FA:D0::100, subnet is FD00:FA:D0::/64 
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::5
    FF02::6
    FF02::1:FF00:100
    FF02::1:FFCD:1
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  ND advertised reachable time is 0 milliseconds
  ND advertised retransmit interval is 0 milliseconds
  ND router advertisements are sent every 200 seconds
  ND router advertisements live for 1800 seconds
  ND advertised default router preference is Medium
  Hosts use stateless autoconfig for addresses.
Serial0/1 is administratively down, line protocol is down
  IPv6 is enabled, link-local address is FE80::C001:DFF:FECD:0 [TEN]
  No Virtual link-local address(es):
  Description: R200_ser0/1
  Global unicast address(es):
    FD00:FA:D0:121::100, subnet is FD00:FA:D0:121::/64 [TEN]
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::5
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  Hosts use stateless autoconfig for addresses.
r100#
r100#show ipv6 interface brief
FastEthernet0/0            [up/up]
    FE80::C001:DFF:FECD:0
    FD00:FA:D0:100::100
Serial0/0                  [up/up]
    FE80::C001:DFF:FECD:0
    FD00:FA:D0:120::100
FastEthernet0/1            [up/up]
    FE80::C001:DFF:FECD:1
    FD00:FA:D0::100
Serial0/1                  [administratively down/down]
    FE80::C001:DFF:FECD:0
    FD00:FA:D0:121::100
Serial0/2                  [administratively down/down]
Serial0/3                  [administratively down/down]
FastEthernet1/0            [administratively down/down]
FastEthernet2/0            [administratively down/down]
Loopback0                  [up/up]
r100#
```

```
r101#show ipv6 interface 
FastEthernet0/0 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::C003:DFF:FEEB:0 
  No Virtual link-local address(es):
  Description: SW100_e0/1
  Global unicast address(es):
    FD00:FA:D0:100::101, subnet is FD00:FA:D0:100::/64 
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::1:FF00:101
    FF02::1:FFEB:0
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  ND advertised reachable time is 0 milliseconds
  ND advertised retransmit interval is 0 milliseconds
  ND router advertisements are sent every 200 seconds
  ND router advertisements live for 1800 seconds
  ND advertised default router preference is Medium
  Hosts use stateless autoconfig for addresses.
Serial0/0 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::C003:DFF:FEEB:0 
  No Virtual link-local address(es):
  Description: R101_ser0/0
  Global unicast address(es):
    FD00:FA:D0:50::1, subnet is FD00:FA:D0:50::/64 
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::1:FF00:1
    FF02::1:FFEB:0
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  Hosts use stateless autoconfig for addresses.
FastEthernet0/1 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::C003:DFF:FEEB:1 
  No Virtual link-local address(es):
  Description: LAN 101
  Global unicast address(es):
    FD00:FA:D0:101::1, subnet is FD00:FA:D0:101::/64 
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::1:FF00:1
    FF02::1:FFEB:1
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  ND advertised reachable time is 0 milliseconds
  ND advertised retransmit interval is 0 milliseconds
  ND router advertisements are sent every 200 seconds
  ND router advertisements live for 1800 seconds
  ND advertised default router preference is Medium
  Hosts use stateless autoconfig for addresses.
r101#
r101#show ipv6 interface brief 
FastEthernet0/0            [up/up]
    FE80::C003:DFF:FEEB:0
    FD00:FA:D0:100::101
Serial0/0                  [up/up]
    FE80::C003:DFF:FEEB:0
    FD00:FA:D0:50::1
FastEthernet0/1            [up/up]
    FE80::C003:DFF:FEEB:1
    FD00:FA:D0:101::1
Serial0/1                  [administratively down/down]
Serial0/2                  [administratively down/down]
Serial0/3                  [administratively down/down]
FastEthernet1/0            [administratively down/down]
FastEthernet2/0            [administratively down/down]
Loopback0                  [up/up]
r101#
```

```
r102#show ipv6 interface      
FastEthernet0/0 is administratively down, line protocol is down
  IPv6 is enabled, link-local address is FE80::C004:DFF:FEFA:0 [TEN]
  No Virtual link-local address(es):
  Description: SW100_e0/2
  Global unicast address(es):
    FD00:FA:D0:100::102, subnet is FD00:FA:D0:100::/64 [TEN]
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::5
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  ND advertised reachable time is 0 milliseconds
  ND advertised retransmit interval is 0 milliseconds
  ND router advertisements are sent every 200 seconds
  ND router advertisements live for 1800 seconds
  ND advertised default router preference is Medium
  Hosts use stateless autoconfig for addresses.
Serial0/0 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::C004:DFF:FEFA:0 
  No Virtual link-local address(es):
  Description: R101_ser0/0
  Global unicast address(es):
    FD00:FA:D0:50::2, subnet is FD00:FA:D0:50::/64 
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::5
    FF02::1:FF00:2
    FF02::1:FFFA:0
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  Hosts use stateless autoconfig for addresses.
FastEthernet0/1 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::C004:DFF:FEFA:1 
  No Virtual link-local address(es):
  Description: LAN 102
  Global unicast address(es):
    FD00:FA:D0:102::1, subnet is FD00:FA:D0:102::/64 
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::5
    FF02::6
    FF02::1:FF00:1
    FF02::1:FFFA:1
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  ND advertised reachable time is 0 milliseconds
  ND advertised retransmit interval is 0 milliseconds
  ND router advertisements are sent every 200 seconds
  ND router advertisements live for 1800 seconds
  ND advertised default router preference is Medium
  Hosts use stateless autoconfig for addresses.
r102# 
r102#show ipv6 interface brief
FastEthernet0/0            [administratively down/down]
    FE80::C004:DFF:FEFA:0
    FD00:FA:D0:100::102
Serial0/0                  [up/up]
    FE80::C004:DFF:FEFA:0
    FD00:FA:D0:50::2
FastEthernet0/1            [up/up]
    FE80::C004:DFF:FEFA:1
    FD00:FA:D0:102::1
Serial0/1                  [administratively down/down]
Serial0/2                  [administratively down/down]
Serial0/3                  [administratively down/down]
FastEthernet1/0            [administratively down/down]
FastEthernet2/0            [administratively down/down]
Loopback0                  [up/up]
r102#
```

```
r200#show ipv6 interface
FastEthernet0/0 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::C002:DFF:FEDC:0 
  No Virtual link-local address(es):
  Description: SW200_e0/0
  Global unicast address(es):
    FD00:FA:D0:200::200, subnet is FD00:FA:D0:200::/64 
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::5
    FF02::6
    FF02::1:FF00:200
    FF02::1:FFDC:0
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  ND advertised reachable time is 0 milliseconds
  ND advertised retransmit interval is 0 milliseconds
  ND router advertisements are sent every 200 seconds
  ND router advertisements live for 1800 seconds
  ND advertised default router preference is Medium
  Hosts use stateless autoconfig for addresses.
Serial0/0 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::C002:DFF:FEDC:0 
  No Virtual link-local address(es):
  Description: R100_ser0/0
  Global unicast address(es):
    FD00:FA:D0:120::200, subnet is FD00:FA:D0:120::/64 
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::5
    FF02::1:FF00:200
    FF02::1:FFDC:0
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  Hosts use stateless autoconfig for addresses.
FastEthernet0/1 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::C002:DFF:FEDC:1 
  No Virtual link-local address(es):
  Description: R100_fa0/1
  Global unicast address(es):
    FD00:FA:D0::200, subnet is FD00:FA:D0::/64 
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::5
    FF02::6
    FF02::1:FF00:200
    FF02::1:FFDC:1
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  ND advertised reachable time is 0 milliseconds
  ND advertised retransmit interval is 0 milliseconds
  ND router advertisements are sent every 200 seconds
  ND router advertisements live for 1800 seconds
  ND advertised default router preference is Medium
  Hosts use stateless autoconfig for addresses.
Serial0/1 is administratively down, line protocol is down
  IPv6 is enabled, link-local address is FE80::C002:DFF:FEDC:0 [TEN]
  No Virtual link-local address(es):
  Description: R100_ser0/1
  Global unicast address(es):
    FD00:FA:D0:121::200, subnet is FD00:FA:D0:121::/64 [TEN]
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::5
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  Hosts use stateless autoconfig for addresses.
r200#
r200#show ipv6 interface brief 
FastEthernet0/0            [up/up]
    FE80::C002:DFF:FEDC:0
    FD00:FA:D0:200::200
Serial0/0                  [up/up]
    FE80::C002:DFF:FEDC:0
    FD00:FA:D0:120::200
FastEthernet0/1            [up/up]
    FE80::C002:DFF:FEDC:1
    FD00:FA:D0::200
Serial0/1                  [administratively down/down]
    FE80::C002:DFF:FEDC:0
    FD00:FA:D0:121::200
Serial0/2                  [administratively down/down]
Serial0/3                  [administratively down/down]
FastEthernet1/0            [administratively down/down]
FastEthernet2/0            [administratively down/down]
Loopback0                  [up/up]
r200#
```

```
r201#show ipv6 interface
FastEthernet0/0 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::C005:EFF:FE09:0 
  No Virtual link-local address(es):
  Description: SW200_e0/1
  Global unicast address(es):
    FD00:FA:D0:200::201, subnet is FD00:FA:D0:200::/64 
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::1:FF00:201
    FF02::1:FF09:0
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  ND advertised reachable time is 0 milliseconds
  ND advertised retransmit interval is 0 milliseconds
  ND router advertisements are sent every 200 seconds
  ND router advertisements live for 1800 seconds
  ND advertised default router preference is Medium
  Hosts use stateless autoconfig for addresses.
Serial0/0 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::C005:EFF:FE09:0 
  No Virtual link-local address(es):
  Description: R202_ser0/0
  Global unicast address(es):
    FD00:FA:D0:60::1, subnet is FD00:FA:D0:60::/64 
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::1:FF00:1
    FF02::1:FF09:0
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  Hosts use stateless autoconfig for addresses.
FastEthernet0/1 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::C005:EFF:FE09:1 
  No Virtual link-local address(es):
  Description: LAN 201
  Global unicast address(es):
    FD00:FA:D0:201::1, subnet is FD00:FA:D0:201::/64 
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::1:FF00:1
    FF02::1:FF09:1
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  ND advertised reachable time is 0 milliseconds
  ND advertised retransmit interval is 0 milliseconds
  ND router advertisements are sent every 200 seconds
  ND router advertisements live for 1800 seconds
  ND advertised default router preference is Medium
  Hosts use stateless autoconfig for addresses.
r201#show ipv6 interface brief
FastEthernet0/0            [up/up]
    FE80::C005:EFF:FE09:0
    FD00:FA:D0:200::201
Serial0/0                  [up/up]
    FE80::C005:EFF:FE09:0
    FD00:FA:D0:60::1
FastEthernet0/1            [up/up]
    FE80::C005:EFF:FE09:1
    FD00:FA:D0:201::1
Serial0/1                  [administratively down/down]
Serial0/2                  [administratively down/down]
Serial0/3                  [administratively down/down]
FastEthernet1/0            [administratively down/down]
FastEthernet2/0            [administratively down/down]
Loopback0                  [up/up]
r201#
```

```
r202#show ipv6 interface
FastEthernet0/0 is administratively down, line protocol is down
  IPv6 is enabled, link-local address is FE80::C006:EFF:FE18:0 [TEN]
  No Virtual link-local address(es):
  Description: SW200_e0/2
  Global unicast address(es):
    FD00:FA:D0:200::202, subnet is FD00:FA:D0:200::/64 [TEN]
  Joined group address(es):
    FF02::1
    FF02::2
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  ND advertised reachable time is 0 milliseconds
  ND advertised retransmit interval is 0 milliseconds
  ND router advertisements are sent every 200 seconds
  ND router advertisements live for 1800 seconds
  ND advertised default router preference is Medium
  Hosts use stateless autoconfig for addresses.
Serial0/0 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::C006:EFF:FE18:0 
  No Virtual link-local address(es):
  Description: R201_ser0/0
  Global unicast address(es):
    FD00:FA:D0:60::2, subnet is FD00:FA:D0:60::/64 
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::1:FF00:2
    FF02::1:FF18:0
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  Hosts use stateless autoconfig for addresses.
FastEthernet0/1 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::C006:EFF:FE18:1 
  No Virtual link-local address(es):
  Description: LAN 202
  Global unicast address(es):
    FD00:FA:D0:202::1, subnet is FD00:FA:D0:202::/64 
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::1:FF00:1
    FF02::1:FF18:1
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  ND advertised reachable time is 0 milliseconds
  ND advertised retransmit interval is 0 milliseconds
  ND router advertisements are sent every 200 seconds
  ND router advertisements live for 1800 seconds
  ND advertised default router preference is Medium
  Hosts use stateless autoconfig for addresses.
r202#show ipv6 interface brief
FastEthernet0/0            [administratively down/down]
    FE80::C006:EFF:FE18:0
    FD00:FA:D0:200::202
Serial0/0                  [up/up]
    FE80::C006:EFF:FE18:0
    FD00:FA:D0:60::2
FastEthernet0/1            [up/up]
    FE80::C006:EFF:FE18:1
    FD00:FA:D0:202::1
Serial0/1                  [administratively down/down]
Serial0/2                  [administratively down/down]
Serial0/3                  [administratively down/down]
FastEthernet1/0            [administratively down/down]
FastEthernet2/0            [administratively down/down]
Loopback0                  [up/up]
r202# 
```

## 7.3  Mise en œuvre du routage OSPFv3

Exemple pour le routeur R101

```
r101#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
r101(config)#ipv6 router ospf 6
r101(config-rtr)#router-id 101.101.101.101
r101(config-rtr)#auto-cost reference-bandwidth 10000
% OSPFv3: Reference bandwidth is changed. 
        Please ensure reference bandwidth is consistent across all routers.
r101(config-rtr)#exit
r101(config)#interface fa 0/0
r101(config-if)#ipv6 ospf 6 area 0
r101(config-if)#exit
r101(config)#interface fa 0/1
r101(config-if)#ipv6 ospf 6 area 101
r101(config-if)#exit
r101(config)#interface ser 0/0
r101(config-if)#ipv6 ospf 6 area 101
r101(config-if)#exit
r101(config)#exit
```

Pour vérifier que la configuration est bien appliquée, je privilégie la commande *show ipv6 protocol* à la place de *show ipv6 int brief*, je vois ainsi mieux si les interfaces ont bien été distribuées dans les différentes areas

```
r100#show ipv6 protocol
IPv6 Routing Protocol is "connected"
IPv6 Routing Protocol is "static"
IPv6 Routing Protocol is "ospf 6"
  Interfaces (Area 0):
    Serial0/1
    FastEthernet0/1
    Serial0/0
    FastEthernet0/0
  Redistribution:
    None
r100#
```

```
r101#show ipv6 protocol
IPv6 Routing Protocol is "connected"
IPv6 Routing Protocol is "static"
IPv6 Routing Protocol is "ospf 6"
  Interfaces (Area 0):
    FastEthernet0/0
  Interfaces (Area 101):
    FastEthernet0/1
    Serial0/0
  Redistribution:
    None
r101#
```

```
r102#show ipv6 protocol
IPv6 Routing Protocol is "connected"
IPv6 Routing Protocol is "static"
IPv6 Routing Protocol is "ospf 6"
  Interfaces (Area 0):
    FastEthernet0/0
  Interfaces (Area 101):
    FastEthernet0/1
    Serial0/0
  Redistribution:
    None
r102#
```

```
r200#show ipv6 protocol
IPv6 Routing Protocol is "connected"
IPv6 Routing Protocol is "static"
IPv6 Routing Protocol is "ospf 6"
  Interfaces (Area 0):
    Serial0/1
    FastEthernet0/1
    Serial0/0
    FastEthernet0/0
  Redistribution:
    None
r200#
```

```
r201#show ipv6 protocol
IPv6 Routing Protocol is "connected"
IPv6 Routing Protocol is "static"
IPv6 Routing Protocol is "ospf 6"
  Interfaces (Area 0):
    FastEthernet0/0
  Interfaces (Area 201):
    FastEthernet0/1
    Serial0/0
  Redistribution:
    None
r201#
```

```
r202#show ipv6 protocol
IPv6 Routing Protocol is "connected"
IPv6 Routing Protocol is "static"
IPv6 Routing Protocol is "ospf 6"
  Interfaces (Area 0):
    FastEthernet0/0
  Interfaces (Area 201):
    FastEthernet0/1
    Serial0/0
  Redistribution:
    None
r202#
```

Test de ping de R101 jusqu'à l'interface f0/1 de R102 

```
r101#ping fd00:fa:d0:100::100

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to FD00:FA:D0:100::100, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/11/20 ms
r101#
00:00:49: %OSPFv3-5-ADJCHG: Process 6, Nbr 100.100.100.100 on FastEthernet0/0 from LOADING to FULL, Loading Done
00:00:49: %OSPF-5-ADJCHG: Process 101, Nbr 100.100.100.100 on FastEthernet0/0 from LOADING to FULL, Loading Done
r101#ping fd00:fa:d0:120::100

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to FD00:FA:D0:120::100, timeout is 2 seconds:
..!!!
Success rate is 60 percent (3/5), round-trip min/avg/max = 8/17/32 ms
r101#ping fd00:fa:d0:120::200     

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to FD00:FA:D0:120::200, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/11/20 ms
r101#ping fd00:fa:d0:200::201

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to FD00:FA:D0:200::201, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/34/44 ms
r101#ping fd00:fa:d0:60::2   

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to FD00:FA:D0:60::2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 16/36/56 ms
r101#ping fd00:fa:d0:202::1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to FD00:FA:D0:202::1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/31/40 ms
r101#
```

## 7.4  Observation du routage OSPFv3

Exemple sur R101

```
r101#sh ipv6 route ospf
IPv6 Routing Table - 14 entries
Codes: C - Connected, L - Local, S - Static, R - RIP, B - BGP
       U - Per-user Static route, M - MIPv6
       I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea, IS - ISIS summary
       O - OSPF intra, OI - OSPF inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2
       D - EIGRP, EX - EIGRP external
O   FD00:FA:D0::/64 [110/2000]
     via FE80::C001:DFF:FECD:0, FastEthernet0/0
OI  FD00:FA:D0:60::/64 [110/8000]
     via FE80::C001:DFF:FECD:0, FastEthernet0/0
O   FD00:FA:D0:102::/64 [110/6000]
     via FE80::C004:DFF:FEFA:0, Serial0/0
O   FD00:FA:D0:120::/64 [110/6000]
     via FE80::C001:DFF:FECD:0, FastEthernet0/0
O   FD00:FA:D0:200::/64 [110/3000]
     via FE80::C001:DFF:FECD:0, FastEthernet0/0
OI  FD00:FA:D0:201::/64 [110/4000]
     via FE80::C001:DFF:FECD:0, FastEthernet0/0
OI  FD00:FA:D0:202::/64 [110/9000]
     via FE80::C001:DFF:FECD:0, FastEthernet0/0
r101#
r101#sh ipv6 ospf data

            OSPFv3 Router with ID (101.101.101.101) (Process ID 6)

                Router Link States (Area 0)

ADV Router      Age         Seq#        Fragment ID  Link count  Bits
100.100.100.100 67          0x80000005  0            3           None
101.101.101.101 66          0x80000005  0            1           B
200.200.200.200 61          0x80000006  0            3           None
201.201.201.201 69          0x80000005  0            1           B

                Net Link States (Area 0)

ADV Router      Age         Seq#        Link ID    Rtr count
101.101.101.101 72          0x80000001  4          2
200.200.200.200 78          0x80000001  5          2
201.201.201.201 76          0x80000001  4          2

                Inter Area Prefix Link States (Area 0)

ADV Router      Age         Seq#        Prefix
101.101.101.101 108         0x80000001  FD00:FA:D0:101::/64
101.101.101.101 108         0x80000001  FD00:FA:D0:50::/64
101.101.101.101 108         0x80000001  FD00:FA:D0:102::/64
201.201.201.201 112         0x80000001  FD00:FA:D0:201::/64
201.201.201.201 112         0x80000001  FD00:FA:D0:60::/64
201.201.201.201 112         0x80000001  FD00:FA:D0:202::/64

                Link (Type-8) Link States (Area 0)

ADV Router      Age         Seq#        Link ID    Interface
100.100.100.100 114         0x80000002  4          Fa0/0
101.101.101.101 120         0x80000001  4          Fa0/0

                Intra Area Prefix Link States (Area 0)

ADV Router      Age         Seq#        Link ID    Ref-lstype  Ref-LSID
100.100.100.100 74          0x80000004  0          0x2001      0
101.101.101.101 74          0x80000001  4096       0x2002      4
200.200.200.200 75          0x80000004  0          0x2001      0
200.200.200.200 80          0x80000001  5120       0x2002      5
201.201.201.201 78          0x80000001  4096       0x2002      4

                Router Link States (Area 101)

ADV Router      Age         Seq#        Fragment ID  Link count  Bits
101.101.101.101 80          0x80000003  0            1           B
102.102.102.102 84          0x80000003  0            1           None

                Inter Area Prefix Link States (Area 101)

ADV Router      Age         Seq#        Prefix
101.101.101.101 112         0x80000001  FD00:FA:D0:100::/64
101.101.101.101 72          0x80000001  FD00:FA:D0:120::/64
101.101.101.101 72          0x80000001  FD00:FA:D0:200::/64
101.101.101.101 72          0x80000001  FD00:FA:D0::/64
101.101.101.101 72          0x80000001  FD00:FA:D0:202::/64
101.101.101.101 72          0x80000001  FD00:FA:D0:60::/64
101.101.101.101 72          0x80000001  FD00:FA:D0:201::/64

                Link (Type-8) Link States (Area 101)

ADV Router      Age         Seq#        Link ID    Interface
101.101.101.101 116         0x80000002  5          Fa0/1
101.101.101.101 116         0x80000002  6          Se0/0
102.102.102.102 118         0x80000002  6          Se0/0

                Intra Area Prefix Link States (Area 101)

ADV Router      Age         Seq#        Link ID    Ref-lstype  Ref-LSID
101.101.101.101 116         0x80000002  0          0x2001      0
102.102.102.102 119         0x80000002  0          0x2001      0
r101#
r101#sh ipv6 ospf int fa 0/0
FastEthernet0/0 is up, line protocol is up 
  Link Local Address FE80::C003:DFF:FEEB:0, Interface ID 4
  Area 0, Process ID 6, Instance ID 0, Router ID 101.101.101.101
  Network Type BROADCAST, Cost: 1000
  Transmit Delay is 1 sec, State DR, Priority 1 
  Designated Router (ID) 101.101.101.101, local address FE80::C003:DFF:FEEB:0
  Backup Designated router (ID) 100.100.100.100, local address FE80::C001:DFF:FECD:0
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
    Hello due in 00:00:07
  Index 1/1/1, flood queue length 0
  Next 0x0(0)/0x0(0)/0x0(0)
  Last flood scan length is 0, maximum is 14
  Last flood scan time is 0 msec, maximum is 0 msec
  Neighbor Count is 1, Adjacent neighbor count is 1 
    Adjacent with neighbor 100.100.100.100  (Backup Designated Router)
  Suppress hello for 0 neighbor(s)
r101#
r101#sh ipv6 ospf neighbor

Neighbor ID     Pri   State           Dead Time   Interface ID    Interface
100.100.100.100   1   FULL/BDR        00:00:31    4               FastEthernet0/0
102.102.102.102   1   FULL/  -        00:00:39    6               Serial0/0
r101#
r101#debug ipv6 ospf events
  OSPFv3 events debugging is on
r101#
r101#debug ipv6 ospf hello          
  OSPFv3 hello events debugging is on
r101#
00:05:12: OSPFv3: Rcv hello from 100.100.100.100 area 0 from FastEthernet0/0 FE80::C001:DFF:FECD:0 interface ID 4
00:05:12: OSPFv3: End of hello processing
00:05:12: OSPFv3: Send hello to FF02::5 area 101 on FastEthernet0/1 from FE80::C003:DFF:FEEB:1 interface ID 5
00:05:13: OSPFv3: Send hello to FF02::5 area 0 on FastEthernet0/0 from FE80::C003:DFF:FEEB:0 interface ID 4
r101#
00:05:14: OSPFv3: Rcv hello from 102.102.102.102 area 101 from Serial0/0 FE80::C004:DFF:FEFA:0 interface ID 6
00:05:14: OSPFv3: End of hello processing
00:05:15: OSPFv3: Send hello to FF02::5 area 101 on Serial0/0 from FE80::C003:DFF:FEEB:0 interface ID 6
r101#
00:05:22: OSPFv3: Send hello to FF02::5 area 101 on FastEthernet0/1 from FE80::C003:DFF:FEEB:1 interface ID 5
00:05:22: OSPFv3: Rcv hello from 100.100.100.100 area 0 from FastEthernet0/0 FE80::C001:DFF:FECD:0 interface ID 4
00:05:22: OSPFv3: End of hello processing
00:05:23: OSPFv3: Send hello to FF02::5 area 0 on FastEthernet0/0 from FE80::C003:DFF:FEEB:0 interface ID 4
r101#
00:05:24: OSPFv3: Send hello to FF02::5 area 101 on Serial0/0 from FE80::C003:DFF:FEEB:0 interface ID 6
00:05:24: OSPFv3: Rcv hello from 102.102.102.102 area 101 from Serial0/0 FE80::C004:DFF:FEFA:0 interface ID 6
00:05:24: OSPFv3: End of hello processing
r101#
00:05:32: OSPFv3: Rcv hello from 100.100.100.100 area 0 from FastEthernet0/0 FE80::C001:DFF:FECD:0 interface ID 4
00:05:32: OSPFv3: End of hello processing
00:05:32: OSPFv3: Send hello to FF02::5 area 101 on FastEthernet0/1 from FE80::C003:DFF:FEEB:1 interface ID 5
00:05:32: OSPFv3: Send hello to FF02::5 area 0 on FastEthernet0/0 from FE80::C003:DFF:FEEB:0 interface ID 4
r101#
00:05:33: OSPFv3: Send hello to FF02::5 area 101 on Serial0/0 from FE80::C003:DFF:FEEB:0 interface ID 6
00:05:33: OSPFv3: Rcv hello from 102.102.102.102 area 101 from Serial0/0 FE80::C004:DFF:FEFA:0 interface ID 6
00:05:33: OSPFv3: End of hello processing
r101#
```