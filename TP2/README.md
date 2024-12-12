# TP2 : Routage, DHCP et DNS

# I. Routage

🌞 **Configuration de `router.tp2.efrei`**

- Internet OK
~~~
[root@localhost ~]# ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=109 time=41.3 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=109 time=41.5 ms
--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1004ms
rtt min/avg/max/mdev = 41.288/41.399/41.511/0.111 ms
~~~

- IP static
~~~
[root@localhost ~]# sudo vi /etc/sysconfig/network-scripts/ifcfg-enp0s8
[root@localhost ~]# cat /etc/sysconfig/network-scripts/ifcfg-enp0s8
DEVICE=enp0s8
NAME=lan

ONBOOT=yes
BOOTPROTO=static

IPADDR=10.2.1.254
NETMASK=255.255.255.0

sudo nmcli connection reload
sudo nmcli connection up lan
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/6)
~~~

- Bo ip a
~~~
[root@localhost ~]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:f6:4d:c0 brd ff:ff:ff:ff:ff:ff
    inet 192.168.122.106/24 brd 192.168.122.255 scope global dynamic noprefixroute enp0s3
       valid_lft 2754sec preferred_lft 2754sec
    inet6 fe80::a00:27ff:fef6:4dc0/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: enp0s8: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc fq_codel state DOWN group default qlen 1000
    link/ether 08:00:27:32:ea:db brd ff:ff:ff:ff:ff:ff
    inet 10.2.1.254/24 brd 10.2.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
4: enp0s9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:6e:d4:de brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.10/24 brd 192.168.56.255 scope global noprefixroute enp0s9
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe6e:d4de/64 scope link
       valid_lft forever preferred_lft forever
~~~


~~~
[root@localhost ~]# sudo firewall-cmd --add-masquerade
success
[root@localhost ~]# sudo firewall-cmd --add-masquerade --permanent
success
[root@localhost ~]# sudo firewall-cmd --reload
success
~~~

🌞 **Configuration de `node1.tp2.efrei`**

~~~
node1> ip 10.2.1.1/24
Checking for duplicate address...
node1 : 10.2.1.1 255.255.255.0

node1> show ip

NAME        : node1[1]
IP/MASK     : 10.2.1.1/24
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:00
LPORT       : 20004
RHOST:PORT  : 127.0.0.1:20005
MTU         : 1500

node1> ping 10.2.1.254
84 bytes from 10.2.1.254 icmp_seq=1 ttl=64 time=1.828 ms
84 bytes from 10.2.1.254 icmp_seq=2 ttl=64 time=2.484 ms
~~~

~~~
node1> ip 10.2.1.1 255.255.255.0 10.2.1.254
Checking for duplicate address...
node1 : 10.2.1.1 255.255.255.0 gateway 10.2.1.254

node1> show ip

NAME        : node1[1]
IP/MASK     : 10.2.1.1/24
GATEWAY     : 10.2.1.254
DNS         :
MAC         : 00:50:79:66:68:00
LPORT       : 20004
RHOST:PORT  : 127.0.0.1:20005
MTU         : 1500

node1> ping 8.8.8.8

84 bytes from 8.8.8.8 icmp_seq=1 ttl=107 time=52.360 ms
84 bytes from 8.8.8.8 icmp_seq=2 ttl=107 time=39.132 ms
84 bytes from 8.8.8.8 icmp_seq=3 ttl=107 time=56.524 ms

node1> trace 10.2.1.254
trace to 10.2.1.254, 8 hops max, press Ctrl+C to stop
 1   *10.2.1.254   2.385 ms (ICMP type:3, code:13, Communication administratively prohibited)
~~~

🌞 **Afficher la CAM Table du switch**

~~~
Switch#show mac address-table
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0050.7966.6800    DYNAMIC     Et0/0
   1    0800.2732.eadb    DYNAMIC     Et2/1
Total Mac Addresses for this criterion: 2
~~~

# II. Serveur DHCP

🌞 **Install et conf du serveur DHCP** sur `dhcp.tp2.efrei`

- DHCP du TP1 je change juste l'ip et ajoute la gateway
~~~
[root@localhost dhcp]# cat /etc/sysconfig/network-scripts/ifcfg-enp0s3
DEVICE=enp0s3
NAME=lan

ONBOOT=yes
BOOTPROTO=static

IPADDR=10.2.1.253
NETMASK=255.255.255.0
GATEWAY=10.2.1.254

[root@localhost dhcp]# sudo nmcli connection reload
[root@localhost dhcp]# sudo nmcli connection up lan
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/29)
~~~

🌞 **Test du DHCP** sur `node1.tp2.efrei`

~~~
node1> dhcp
DORA IP 10.2.1.10/24 GW 10.2.1.254

node1> ping 1.1.1.1

84 bytes from 1.1.1.1 icmp_seq=1 ttl=47 time=105.151 ms
84 bytes from 1.1.1.1 icmp_seq=2 ttl=47 time=70.195 ms
~~~

🌟 **BONUS**

~~~
[root@localhost ~]# cat /etc/dhcp/dhcpd.conf
subnet 10.2.1.0 netmask 255.255.255.0 {
    range 10.2.1.10 10.2.1.50;
    option broadcast-address 10.2.1.1;
    option routers 10.2.1.254;
    option subnet-mask 255.255.255.0;
    option domain-name-servers 1.1.1.1;
}


node1> show ip

NAME        : node1[1]
IP/MASK     : 10.2.1.10/24
GATEWAY     : 10.2.1.254
DNS         : 1.1.1.1
DHCP SERVER : 10.2.1.253
DHCP LEASE  : 42027, 42087/21043/36826
MAC         : 00:50:79:66:68:00
LPORT       : 20004
RHOST:PORT  : 127.0.0.1:20005
MTU         : 1500
~~~

🌞 **Wireshark it !**

[DORA](/TP2/DORA_TP2.pcap)

# III. ARP

## 1. Les tables ARP

🌞 **Affichez la table ARP de `router.tp2.efrei`**

~~~
[root@localhost ~]# ip n s
10.2.1.253 dev enp0s8 lladdr 08:00:27:81:3f:9c STALE
10.2.1.10 dev enp0s8 lladdr 00:50:79:66:68:00 STALE
~~~

🌞 **Capturez l'échange ARP avec Wireshark**

[ARP](/TP2/ARP.pcap)

## 2. ARP poisoning

🌞 **Envoyer une trame ARP arbitraire**

~~~
(cmeyer© kali)-[~]
sudo arping 10.2.1.10
[sudo] password for cmeyer:
ARPING 10.2.1.10
60 bytes from 00:50:79:66:68:00 (10.2.1.10): index=0 time=3.089 msec
60 bytes from 00:50:79:66:68:00 (10.2.1.10): index=1 time=3.125 msec
60 bytes from 00:50:79:66:68:00 (10.2.1.10): index=2 time=2.756 msec
60 bytes from 00:50:79:66:68:00 (10.2.1.10): index=3 time=2.779 msec
60 bytes from 00:50:79:66:68:00 (10.2.1.10): index=4 time=2.854 msec
60 bytes from 00:50:79:66:68:00 (10.2.1.10): index=5 time=3.590 msec
— 10.2.1.10 statistics -
3 packets transmitted, 6 packets received, 0% unanswered (3 extra)
rtt min/avg/max/std-dev = 2.756/3.032/3.590/0.287 ms
~~~

🌞 **Mettre en place un ARP MITM**

~~~
(cmeyer© kali)-[~]
sudo arpspoof -r -t 10.2.1.10 10.2.1.254
~~~

🌞 **Capture Wireshark `arp_mitm.pcap`**

![ARP mitm](TP2/arp_mitm.pcap)

🌞 **Réaliser la même attaque avec Scapy**

