# I. Setup réseau initial

🌞 **`ping` d'un client du  `LAN1` vers un client du `LAN 2`**

~~~
PC1> show ip

NAME        : PC1[1]
IP/MASK     : 10.3.1.1/24
GATEWAY     : 10.3.1.254
DNS         :
MAC         : 00:50:79:66:68:00
LPORT       : 20026
RHOST:PORT  : 127.0.0.1:20027
MTU         : 1500

PC1> ping 10.3.2.2

84 bytes from 10.3.2.2 icmp_seq=1 ttl=62 time=38.487 ms
84 bytes from 10.3.2.2 icmp_seq=2 ttl=62 time=43.189 ms
84 bytes from 10.3.2.2 icmp_seq=3 ttl=62 time=29.490 ms
84 bytes from 10.3.2.2 icmp_seq=4 ttl=62 time=26.010 ms
~~~

🌞 **Capture Wireshark `ping_partie1`**

[Ping partie 1](ping_partie1.pcapng)

🌞 **Afficher les adresses MAC des routeurs**

~~~
R1#show arp
Protocol  Address          Age (min)  Hardware Addr   Type   Interface
Internet  10.3.12.1               -   c401.0579.0001  ARPA   FastEthernet0/1
Internet  10.3.12.2              66   c402.059a.0000  ARPA   FastEthernet0/1
Internet  10.3.1.1               12   0050.7966.6800  ARPA   FastEthernet1/0
Internet  10.3.1.2               12   0050.7966.6801  ARPA   FastEthernet1/0
Internet  192.168.122.1          17   5254.0023.04c2  ARPA   FastEthernet0/0
Internet  192.168.122.44          -   c401.0579.0000  ARPA   FastEthernet0/0
Internet  10.3.1.254              -   c401.0579.0010  ARPA   FastEthernet1/0
~~~

🌞 **Prouvez que vous avez déjà un accès internet sur `r1`**

~~~
R1#ping 8.8.8.8

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 8.8.8.8, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 92/94/104 ms
~~~

🌞 **Accès internet `LAN1`**

~~~
R1#conf t

R1#(config)# interface FastEthernet0/0
R1#(config-if)# ip nat outside
R1#(config-if)# exit

R1#(config)# interface FastEthernet0/1
R1#(config-if)# ip nat inside
R1#(config-if)# exit

R1#(config)# interface FastEthernet1/0
R1#(config-if)# ip nat inside
R1#(config-if)# exit

R1#(config)# access-list 1 permit any

R1#(config)# ip nat inside source list 1 interface FastEthernet0/0 overload

PC1> show ip

NAME        : PC1[1]
IP/MASK     : 10.3.1.1/24
GATEWAY     : 10.3.1.254
DNS         :
MAC         : 00:50:79:66:68:00
LPORT       : 20026
RHOST:PORT  : 127.0.0.1:20027
MTU         : 1500

PC1> ping 8.8.8.8

84 bytes from 8.8.8.8 icmp_seq=1 ttl=114 time=39.819 ms
84 bytes from 8.8.8.8 icmp_seq=2 ttl=114 time=38.927 ms
84 bytes from 8.8.8.8 icmp_seq=3 ttl=114 time=39.852 ms
84 bytes from 8.8.8.8 icmp_seq=4 ttl=114 time=41.928 ms
84 bytes from 8.8.8.8 icmp_seq=5 ttl=114 time=43.542 ms
~~~


🌞 **Accès internet `LAN2`**

~~~
R2#conf t
R2(config)#ip route 0.0.0.0 0.0.0.0 10.3.12.1

PC3> show ip

NAME        : PC3[1]
IP/MASK     : 10.3.2.1/24
GATEWAY     : 10.3.2.254
DNS         :
MAC         : 00:50:79:66:68:02
LPORT       : 20030
RHOST:PORT  : 127.0.0.1:20031
MTU         : 1500

PC3> ping 8.8.8.8

84 bytes from 8.8.8.8 icmp_seq=1 ttl=113 time=52.858 ms
84 bytes from 8.8.8.8 icmp_seq=2 ttl=113 time=50.707 ms
84 bytes from 8.8.8.8 icmp_seq=3 ttl=113 time=60.669 ms
84 bytes from 8.8.8.8 icmp_seq=4 ttl=113 time=62.792 ms
84 bytes from 8.8.8.8 icmp_seq=5 ttl=113 time=59.672 ms
~~~

# Partie II. Router-on-a-stick

### A. VLANs

➜ **Déclarer les VLANs**

~~~
IOU1#show vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et0/0, Et0/1, Et0/2, Et0/3
                                                Et1/0, Et1/1, Et1/2, Et1/3
                                                Et2/0, Et2/1, Et2/2, Et2/3
                                                Et3/0, Et3/1, Et3/2, Et3/3
10   admins                           active
20   guests                           active
30   others                           active
1002 fddi-default                     act/unsup
1003 token-ring-default               act/unsup
1004 fddinet-default                  act/unsup
1005 trnet-default                    act/unsup

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------
1    enet  100001     1500  -      -      -        -    -        0      0
10   enet  100010     1500  -      -      -        -    -        0      0
20   enet  100020     1500  -      -      -        -    -        0      0
30   enet  100030     1500  -      -      -        -    -        0      0
1002 fddi  101002     1500  -      -      -        -    -        0      0
1003 tr    101003     1500  -      -      -        -    -        0      0
 --More--
~~~

~~~
IOU2#show vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et0/0, Et0/1, Et0/2, Et0/3
                                                Et1/0, Et1/1, Et1/2, Et1/3
                                                Et2/0, Et2/1, Et2/2, Et2/3
                                                Et3/0, Et3/1, Et3/2, Et3/3
10   admins                           active
20   guests                           active
30   others                           active
1002 fddi-default                     act/unsup
1003 token-ring-default               act/unsup
1004 fddinet-default                  act/unsup
1005 trnet-default                    act/unsup

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------
1    enet  100001     1500  -      -      -        -    -        0      0
10   enet  100010     1500  -      -      -        -    -        0      0
20   enet  100020     1500  -      -      -        -    -        0      0
30   enet  100030     1500  -      -      -        -    -        0      0
1002 fddi  101002     1500  -      -      -        -    -        0      0
1003 tr    101003     1500  -      -      -        -    -        0      0
 --More--
~~~

➜ **Définir les ports `access`**

~~~
IOU1#show vlan br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et0/1, Et1/0, Et1/1, Et1/2
                                                Et1/3, Et2/0, Et2/1, Et2/2
                                                Et2/3, Et3/0, Et3/1, Et3/2
                                                Et3/3
10   admins                           active    Et0/2
20   guests                           active    Et0/3
30   others                           active
1002 fddi-default                     act/unsup
1003 token-ring-default               act/unsup
1004 fddinet-default                  act/unsup
1005 trnet-default                    act/unsup
~~~

~~~
IOU2#show vlan br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et0/0, Et1/2, Et1/3, Et2/0
                                                Et2/1, Et2/2, Et2/3, Et3/0
                                                Et3/1, Et3/2, Et3/3
10   admins                           active    Et0/1
20   guests                           active    Et0/2, Et0/3
30   others                           active    Et1/0, Et1/1
1002 fddi-default                     act/unsup
1003 token-ring-default               act/unsup
1004 fddinet-default                  act/unsup
1005 trnet-default                    act/unsup
~~~

➜ **Définir les ports `trunk`**

~~~
IOU1#show interfaces trunk

Port        Mode             Encapsulation  Status        Native vlan
Et0/0       on               802.1q         trunking      1
Et0/1       on               802.1q         trunking      1

Port        Vlans allowed on trunk
Et0/0       1-4094
Et0/1       1-4094

Port        Vlans allowed and active in management domain
Et0/0       1,10,20,30
Et0/1       1,10,20,30

Port        Vlans in spanning tree forwarding state and not pruned
Et0/0       1,10,20,30
Et0/1       1,10,20,30
~~~

~~~
IOU2#show interfaces trunk

Port        Mode             Encapsulation  Status        Native vlan
Et0/0       on               802.1q         trunking      1

Port        Vlans allowed on trunk
Et0/0       1-4094

Port        Vlans allowed and active in management domain
Et0/0       1,10,20,30

Port        Vlans in spanning tree forwarding state and not pruned
Et0/0       1,10,20,30
~~~

🌞 **Tests de `ping`**

~~~
PC1> ping 10.3.1.2

84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=3.895 ms
84 bytes from 10.3.1.2 icmp_seq=2 ttl=64 time=2.634 ms
84 bytes from 10.3.1.2 icmp_seq=3 ttl=64 time=1.955 ms
84 bytes from 10.3.1.2 icmp_seq=4 ttl=64 time=3.640 ms
84 bytes from 10.3.1.2 icmp_seq=5 ttl=64 time=3.683 ms
~~~

### B. Routeur

➜ **On passe sur le routeur**

- on a un soucis : le routeur a une seule interface qui doit servir de passerelle pour tous les LANs
- il doit donc avoir plusieurs adresses IP sur une seule interface !
- on fait ça avec des sous-interfaces :
  - on "découpe" l'interface en plusieurs sous-interfaces
  - chacune des sous-interfaces a une adresse IP
  - chaque sous-interface est définie comme étant dans un VLAN particulier

➜ **Configuration IP du routeur**

- adresse IP DHCP côté nuage
- adresses IP sur les sous-interfaces qui pointent vers le LAN
- n'oubliez pas d'allumer les interfaces

🌞 **Tests de `ping`**

~~~
R1#ping 10.3.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 32/46/60 ms
R1#ping 10.3.2.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.2.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 32/49/68 ms

R1#ping 10.3.1.2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.1.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/19/56 ms

~~~

~~~
PC1> show ip

NAME        : PC1[1]
IP/MASK     : 10.3.1.1/24
GATEWAY     : 10.3.1.254
DNS         :
MAC         : 00:50:79:66:68:00
LPORT       : 20018
RHOST:PORT  : 127.0.0.1:20019
MTU         : 1500

PC2> show ip

NAME        : PC2[1]
IP/MASK     : 10.3.2.1/24
GATEWAY     : 10.3.2.254
DNS         :
MAC         : 00:50:79:66:68:01
LPORT       : 20020
RHOST:PORT  : 127.0.0.1:20021
MTU         : 1500

PC1> ping 10.3.2.1

84 bytes from 10.3.2.1 icmp_seq=1 ttl=63 time=24.251 ms
84 bytes from 10.3.2.1 icmp_seq=2 ttl=63 time=12.037 ms
84 bytes from 10.3.2.1 icmp_seq=3 ttl=63 time=17.665 ms
84 bytes from 10.3.2.1 icmp_seq=4 ttl=63 time=14.225 ms
84 bytes from 10.3.2.1 icmp_seq=5 ttl=63 time=19.360 ms
~~~

🌞 **Tests de `ping`**

~~~
PC2> ping 8.8.8.8

84 bytes from 8.8.8.8 icmp_seq=1 ttl=114 time=39.129 ms
84 bytes from 8.8.8.8 icmp_seq=2 ttl=114 time=39.355 ms
84 bytes from 8.8.8.8 icmp_seq=3 ttl=114 time=33.988 ms
84 bytes from 8.8.8.8 icmp_seq=4 ttl=114 time=49.530 ms
84 bytes from 8.8.8.8 icmp_seq=5 ttl=114 time=50.876 ms
~~~

# III. Services dans le LAN

## 1. DHCP

🌞 **Prouvez avec un VPCS**

~~~
PC4> ip dhcp
DORA IP 10.3.2.10/24 GW 10.3.2.254

PC4> show ip

NAME        : PC4[1]
IP/MASK     : 10.3.2.10/24
GATEWAY     : 10.3.2.254
DNS         : 1.1.1.1
DHCP SERVER : 10.3.2.253
DHCP LEASE  : 43000, 43007/21503/37631
MAC         : 00:50:79:66:68:04
LPORT       : 20030
RHOST:PORT  : 127.0.0.1:20031
MTU         : 1500

PC4> ping efrei.fr
efrei.fr resolved to 51.255.68.208

84 bytes from 51.255.68.208 icmp_seq=1 ttl=53 time=41.181 ms
84 bytes from 51.255.68.208 icmp_seq=2 ttl=53 time=38.672 ms
84 bytes from 51.255.68.208 icmp_seq=3 ttl=53 time=43.866 ms
84 bytes from 51.255.68.208 icmp_seq=4 ttl=53 time=39.918 ms
84 bytes from 51.255.68.208 icmp_seq=5 ttl=53 time=42.464 ms
~~~

## 2. DNS

➜ **Modifier la configuration de `dhcp.tp2.b3`**

~~~
[root@localhost ~]# cat /etc/dhcp/dhcpd.conf
subnet 10.3.2.0 netmask 255.255.255.0 {
    range 10.3.2.10 10.3.2.50;
    option broadcast-address 10.3.2.1;
    option routers 10.3.2.254;
    option subnet-mask 255.255.255.0;
    option domain-name-servers 10.3.3.1;
}
~~~

🌞 **Tests résolutions DNS**

~~~
PC4> dhcp
DORA IP 10.3.2.45/24 GW 10.3.2.254

PC4> show ip

NAME        : PC4[1]
IP/MASK     : 10.3.2.35/24
GATEWAY     : 10.3.2.254
DNS         : 1.1.1.1  10.3.3.1
DHCP SERVER : 10.3.2.253
DHCP LEASE  : 43195, 43200/21600/37800
DOMAIN NAME : dns.tp3.b2
MAC         : 00:50:79:66:68:01
LPORT       : 20028
RHOST:PORT  : 127.0.0.1:20029
MTU         : 1500

PC4> ping efrei.fr
efrei.fr resolved to 51.255.68.208

84 bytes from 51.255.68.208 icmp_seq=1 ttl=53 time=42.133 ms
84 bytes from 51.255.68.208 icmp_seq=2 ttl=53 time=34.988 ms
84 bytes from 51.255.68.208 icmp_seq=3 ttl=53 time=54.636 ms
84 bytes from 51.255.68.208 icmp_seq=4 ttl=53 time=43.894 ms
84 bytes from 51.255.68.208 icmp_seq=5 ttl=53 time=38.440 ms

PC4> ping dns.tp3.b2
dns.tp3.b2 resolved to 10.3.3.1

84 bytes from 10.3.3.1 icmp_seq=1 ttl=63 time=20.827 ms
84 bytes from 10.3.3.1 icmp_seq=2 ttl=63 time=13.156 ms
84 bytes from 10.3.3.1 icmp_seq=3 ttl=63 time=14.921 ms
84 bytes from 10.3.3.1 icmp_seq=4 ttl=63 time=16.363 ms
84 bytes from 10.3.3.1 icmp_seq=5 ttl=63 time=15.913 ms
~~~

🌞 **Capture Wireshark**

[Ping avec nom de domaine](./ping_dns.pcapng)

## 3. HTTP

🌞 **Preuve avec un client**

~~~html
(cmeyer© kali) -[~]
$ curl web.tp3.b2

<!doctype html>
<html>
  <head>
    <meta charset-'utf-8'>
    <meta name='viewport' content='width=device-width, initial-scale-1'>
    <title>HTTP Server Test Page powered by: Rocky Linux</title>
    <style type="text/css">
      /*<![CDATA[*/

      html {
        height: 100%;
        width: 100%;
      }
[...]
~~~

# Partie IV : Attaques sur les services en place

## 1. DNS

🌞 **Requêter l'enregistrement AXFR**

~~~
(cmeyer© kali) -[~]
$ dig @dns.tp3.b2 tp3.b2 AXFR

; <<>> DiG 9.20.3-1-Debian <<>> @dns.tp3.b2 tp3.b2 AXFR
(1 server found)
;; global options: +cmd
tp3.b2. 86400 IN SOA dns.tp3.b2. admin.tp3.b2. 2019061800 3600 1800 604800 86400
tp3.b2. 86400 IN NS dns.tp3.b2. 
coolsite.tp3.b2. 86400 IN A 10.3.3.4 
dns.tp3.b2. 86400 IN A 10.3.3.1 
meow.tp3.b2. 86400 IN A 10.3.3.6 
prout.tp3.b2. 86400 IN A 10.3.3.5 
supersite.tp3.b2. 86400 IN A 10.3.3.3 
web.tp3.b2. 86400 IN A 10.3.3.2 
web2.tp3.b2. 86400 IN A 10.3.3.4 
web3.tp3.b2. 86400 IN A 10.3.3.5 
web4.tp3.b2. 86400 IN A 10.3.3.6
tp3.b2. 86400 IN SOA dns.tp3.b2. admin.tp3.b2. 2019061800 3600 1800 604800 86400
~~~

🌞 **Spoof DNS query**

~~~python
from scapy.all import *

dns_request = sr1(IP(src="10.3.2.10", dst="dns.tp3.b2")/UDP(dport=53)/DNS(rd=1, qd=DNSQR(qname="tp3.b2", qtype="AXFR")),verbose=0)

send(dns_request)
print(answer[DNS].summary())
~~~

cf dns_flood

## 2. TCP

🌞 **Mettre en place une attaque TCP RST**

- il faut une connexion TCP établie dans le LAN
- si vous visitez le site web avec un navigateur, un navigateur maintient la connexion TCP active un moment normalement (contrairement à `curl`)
- vous pouvez aussi faire une connexion SSH entre deux machines par exemple, ce sera encore plus parlant et visible car la connexion SSH va couper d'un coup : c'est le but de l'attaque
- **envoyer un ou plusieurs paquets TCP RST pour terminer une connexion TCP en cours**
  - il faut une connexion TCP en cours
  - il faut repérer les numéros de séquence
  - envoyer un TCP RST à un participant, en spoofant l'identité de l'autre
  - avec les bons numéros à l'intérieur
