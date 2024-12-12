# TP1 : Remise dans le bain

# I. Most simplest LAN

🌞 **Déterminer l'adresse MAC de vos deux machines**

~~~
node1> show ip

NAME        : node1[1]
IP/MASK     : 10.1.1.1/24
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:00
LPORT       : 20004
RHOST:PORT  : 127.0.0.1:20005
MTU         : 1500
~~~
~~~
node2> show ip

NAME        : node2[1]
IP/MASK     : 10.1.1.2/24
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:01
LPORT       : 20002
RHOST:PORT  : 127.0.0.1:20003
MTU         : 1500
~~~

🌞 **Définir une IP statique sur les deux machines**

~~~
node1> ip 10.1.1.1/24
Checking for duplicate address...
node1 : 10.1.1.1 255.255.255.0

node1> save
Saving startup configuration to startup.vpc
.  done

node1> show ip

NAME        : node1[1]
IP/MASK     : 10.1.1.1/24
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:00
LPORT       : 20004
RHOST:PORT  : 127.0.0.1:20005
MTU         : 1500
~~~
~~~
node2> ip 10.1.1.2/24
Checking for duplicate address...
node2 : 10.1.1.2 255.255.255.0

node2> save
Saving startup configuration to startup.vpc
.  done

node2> show ip

NAME        : node2[1]
IP/MASK     : 10.1.1.2/24
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:01
LPORT       : 20002
RHOST:PORT  : 127.0.0.1:20003
MTU         : 1500
~~~

🌞 **Effectuer un `ping` d'une machine à l'autre**

~~~
node1> ping 10.1.1.2

84 bytes from 10.1.1.2 icmp_seq=1 ttl=64 time=0.814 ms
84 bytes from 10.1.1.2 icmp_seq=2 ttl=64 time=1.380 ms
84 bytes from 10.1.1.2 icmp_seq=3 ttl=64 time=2.204 ms
84 bytes from 10.1.1.2 icmp_seq=4 ttl=64 time=1.270 ms
84 bytes from 10.1.1.2 icmp_seq=5 ttl=64 time=2.020 ms
~~~
~~~
node2> ping 10.1.1.1

84 bytes from 10.1.1.1 icmp_seq=1 ttl=64 time=0.847 ms
84 bytes from 10.1.1.1 icmp_seq=2 ttl=64 time=1.593 ms
84 bytes from 10.1.1.1 icmp_seq=3 ttl=64 time=0.380 ms
84 bytes from 10.1.1.1 icmp_seq=4 ttl=64 time=0.579 ms
84 bytes from 10.1.1.1 icmp_seq=5 ttl=64 time=1.689 ms
~~~

🌞 **Wireshark !**

cf ping_node1A2.pcapng / Protocol : ICMP

🌞 **ARP**

~~~
node1> ping 10.1.1.2

84 bytes from 10.1.1.2 icmp_seq=1 ttl=64 time=1.247 ms
84 bytes from 10.1.1.2 icmp_seq=2 ttl=64 time=0.831 ms
84 bytes from 10.1.1.2 icmp_seq=3 ttl=64 time=2.469 ms
84 bytes from 10.1.1.2 icmp_seq=4 ttl=64 time=1.798 ms
84 bytes from 10.1.1.2 icmp_seq=5 ttl=64 time=1.619 ms

node1> show arp

00:50:79:66:68:01  10.1.1.2 expires in 107 seconds
~~~

# II. Ajoutons un switch

🌞 **Déterminer l'adresse MAC de vos trois machines**

~~~
node1> show ip

NAME        : node1[1]
IP/MASK     : 10.1.1.1/24
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:00
LPORT       : 20004
RHOST:PORT  : 127.0.0.1:20005
MTU         : 1500
~~~
~~~
node2> show ip

NAME        : node2[1]
IP/MASK     : 10.1.1.2/24
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:01
LPORT       : 20002
RHOST:PORT  : 127.0.0.1:20003
MTU         : 1500
~~~
~~~
node3> show ip

NAME        : node3[1]
IP/MASK     : 0.0.0.0/0
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:02
LPORT       : 20010
RHOST:PORT  : 127.0.0.1:20011
MTU         : 1500
~~~

🌞 **Définir une IP statique sur les trois machines**

~~~
node1> ip 10.1.1.1/24
Checking for duplicate address...
node1 : 10.1.1.1 255.255.255.0

node1> save
Saving startup configuration to startup.vpc
.  done

node1> show ip

NAME        : node1[1]
IP/MASK     : 10.1.1.1/24
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:00
LPORT       : 20004
RHOST:PORT  : 127.0.0.1:20005
MTU         : 1500
~~~
~~~
node2> ip 10.1.1.2/24
Checking for duplicate address...
node2 : 10.1.1.2 255.255.255.0

node2> save
Saving startup configuration to startup.vpc
.  done

node2> show ip

NAME        : node2[1]
IP/MASK     : 10.1.1.2/24
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:01
LPORT       : 20002
RHOST:PORT  : 127.0.0.1:20003
MTU         : 1500
~~~
~~~
node3> ip 10.1.1.3/24
Checking for duplicate address...
node3 : 10.1.1.3 255.255.255.0

node3> save
Saving startup configuration to startup.vpc
.  done

node3> show ip

NAME        : node3[1]
IP/MASK     : 10.1.1.3/24
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:02
LPORT       : 20010
RHOST:PORT  : 127.0.0.1:20011
MTU         : 1500
~~~

🌞 **Effectuer des `ping` d'une machine à l'autre**

~~~
node1> ping 10.1.1.2

84 bytes from 10.1.1.2 icmp_seq=1 ttl=64 time=0.814 ms
84 bytes from 10.1.1.2 icmp_seq=2 ttl=64 time=1.380 ms
84 bytes from 10.1.1.2 icmp_seq=3 ttl=64 time=2.204 ms
84 bytes from 10.1.1.2 icmp_seq=4 ttl=64 time=1.270 ms
84 bytes from 10.1.1.2 icmp_seq=5 ttl=64 time=2.020 ms
~~~
~~~
node2> ping 10.1.1.3

84 bytes from 10.1.1.3 icmp_seq=1 ttl=64 time=1.280 ms
84 bytes from 10.1.1.3 icmp_seq=2 ttl=64 time=2.171 ms
84 bytes from 10.1.1.3 icmp_seq=3 ttl=64 time=1.776 ms
84 bytes from 10.1.1.3 icmp_seq=4 ttl=64 time=1.451 ms
84 bytes from 10.1.1.3 icmp_seq=5 ttl=64 time=1.804 ms
~~~
~~~
node1> ping 10.1.1.3

84 bytes from 10.1.1.3 icmp_seq=1 ttl=64 time=1.071 ms
84 bytes from 10.1.1.3 icmp_seq=2 ttl=64 time=0.178 ms
84 bytes from 10.1.1.3 icmp_seq=3 ttl=64 time=0.704 ms
84 bytes from 10.1.1.3 icmp_seq=4 ttl=64 time=0.184 ms
84 bytes from 10.1.1.3 icmp_seq=5 ttl=64 time=1.896 ms
~~~
~~~
node3> ping 10.1.1.1

84 bytes from 10.1.1.1 icmp_seq=1 ttl=64 time=0.959 ms
84 bytes from 10.1.1.1 icmp_seq=2 ttl=64 time=2.355 ms
84 bytes from 10.1.1.1 icmp_seq=3 ttl=64 time=1.166 ms
84 bytes from 10.1.1.1 icmp_seq=4 ttl=64 time=2.358 ms
84 bytes from 10.1.1.1 icmp_seq=5 ttl=64 time=2.428 ms
~~~

# III. Serveur DHCP

## 1. Legit

🌞 **Donner un accès Internet à la machine `dhcp.tp1.efrei`**

~~~
[root@localhost ~]# ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=116 time=24.5 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=116 time=37.0 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=116 time=25.0 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=116 time=30.3 ms
^C
--- 8.8.8.8 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3007ms
rtt min/avg/max/mdev = 24.523/29.202/37.010/5.047 ms
~~~

🌞 **Installer et configurer un serveur DHCP**

- Install :
~~~
dnf install dhcp-server
~~~

~~~
[root@localhost ~]# ping 8.8.8.8
ping: connect: Network is unreachable
~~~

- IP statique :
~~~
[root@localhost dhcp]# sudo vi /etc/sysconfig/network-scripts/ifcfg-enp0s3
[root@localhost dhcp]# cat /etc/sysconfig/network-scripts/ifcfg-enp0s3
DEVICE=enp0s3
NAME=lan

ONBOOT=yes
BOOTPROTO=static

IPADDR=10.1.1.10
NETMASK=255.255.255.0

[root@localhost dhcp]# sudo nmcli connection reload
[root@localhost dhcp]# sudo nmcli connection up lan
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/29)

[root@localhost dhcp]# sudo nmcli connection down lan
Connection 'lan' successfully deactivated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/29)

[root@localhost dhcp]# sudo nmcli connection up lan
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/31)

[root@localhost dhcp]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:3f:61:2b brd ff:ff:ff:ff:ff:ff
    inet 10.1.1.10/24 brd 10.1.1.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe3f:612b/64 scope link
       valid_lft forever preferred_lft forever
~~~

- DHCP dans le firewall :
~~~
[root@localhost ~]# firewall-cmd --add-service=dhcp
success
[root@localhost ~]# firewall-cmd --runtime-to-permanent
success
~~~

- Config DHCP :
~~~
[root@localhost ~]# sudo cp /etc/dhcp/dhcpd.conf /etc/dhcp/dhcpd.conf.bak
[root@localhost ~]# sudo vi /etc/dhcp/dhcpd.conf
[root@localhost ~]# cat /etc/dhcp/dhcpd.conf
subnet 10.1.1.0
netmask 255.255.255.0 {
    range 10.1.1.10 10.1.1.50;
    option broadcast-address 10.1.1.1;
    option routers 10.1.1.1;
    option subnet-mask 255.255.255.0;
}

[root@localhost ~]# sudo systemctl enable --now dhcpd
[root@localhost ~]# sudo systemctl status dhcpd
● dhcpd.service - DHCPv4 Server Daemon
     Loaded: loaded (/usr/lib/systemd/system/dhcpd.service; enabled; preset: disabled)
     Active: active (running) since Wed 2024-12-04 17:55:48 CET; 11s ago
       Docs: man:dhcpd(8)
             man:dhcpd.conf(5)
   Main PID: 800 (dhcpd)
     Status: "Dispatching packets..."
      Tasks: 1 (limit: 23142)
     Memory: 7.1M
        CPU: 31ms
     CGroup: /system.slice/dhcpd.service
             └─1551 /usr/sbin/dhcpd -f -cf /etc/dhcp/dhcpd.conf -user dhcpd -group dhcpd --no-pid

Dec 04 18:23:02 localhost. localdomain dhcpd[800]: Config file: /etc/dhcp/dhcpd.conf
Dec 04 18:23:02 localhost. localdomain dhcpd[80B]: Database file: /var/lib/dhcpd/dhcpd.leases
Dec 04 18:23:02 localhost. localdomain dhcpd[80B]: PID file: /var/run/dhcpd.pid
Dec 04 18:23:02 localhost. localdomain dhcpd[800]: Source compiled to use binary-leases
Dec 04 18:23:02 localhost. localdomain dhcpd[800]: Wrote 0 leases to leases file.
Dec 04 18:23:02 localhost. localdomain dhcpd[800]: Listening on LPF/enp0s3/08:00:27:3f:9c/10.1.1.0/24 
Dec 04 18:23:02 localhost. localdomain dhcpd[800]: Sending on   LPF/enp0s3/08:00:27:3f:9c/10.1.1.0/24 
Dec 04 18:23:02 localhost. localdomain dhcpd[800]: Sending on   Socket/fallback/fallback-net
Dec 04 18:23:02 localhost. localdomain dhcpd[800]: Server starting service.
Dec 04 18:23:02 localhost. localdomain dhcpd[800]: Started DHCPv4 Server Daemon.
~~~

🌞 **Récupérer une IP automatiquement depuis les 3 nodes**

~~~
node1> dhcp
DDORA IP 10.1.1.11/24 GW 10.1.1.1

node1> show ip

NAME        : node1[1]
IP/MASK     : 10.1.1.11/24
GATEWAY     : 10.1.1.1
DNS         :
DHCP SERVER : 10.1.1.10
DHCP LEASE  : 43180, 43200/21600/37800
MAC         : 00:50:79:66:68:00
LPORT       : 20007
RHOST:PORT  : 127.0.0.1:20008
MTU         : 1500
~~~
~~~
node2> dhcp
DDORA IP 10.1.1.12/24 GW 10.1.1.1

node2> show ip

NAME        : node2[1]
IP/MASK     : 10.1.1.12/24
GATEWAY     : 10.1.1.1
DNS         :
DHCP SERVER : 10.1.1.10
DHCP LEASE  : 43179, 43200/21600/37800
MAC         : 00:50:79:66:68:02
LPORT       : 20009
RHOST:PORT  : 127.0.0.1:20010
MTU         : 1500
~~~
~~~
node3> dhcp
DDORA IP 10.1.1.13/24 GW 10.1.1.1

node3> show ip

NAME        : node3[1]
IP/MASK     : 10.1.1.13/24
GATEWAY     : 10.1.1.1
DNS         :
DHCP SERVER : 10.1.1.10
DHCP LEASE  : 43177, 43200/21600/37800
MAC         : 00:50:79:66:68:01
LPORT       : 20011
RHOST:PORT  : 127.0.0.1:20012
MTU         : 1500
~~~

🌞 **Wireshark !**

cf DORA.pcapng

## 2. DHCP spoofing

**Introduire une nouvelle VM dans la topologie, OS de votre choix**

- VM -> Rocky Linux

🌞 **Configurez dnsmasq**

- Config du dnsmasq
~~~
sudo dnf install dnsmasq -y

sudo vi /etc/dnsmasq.conf
cat /etc/dnsmasq.conf
port=0
dhcp-range=10.1.1.210,10.1.1.250,255.255.255.0,12h
dhcp-authoritative
interface=enp0s3
~~~

- IP statique :
~~~
[root@localhost dhcp]# sudo vi /etc/sysconfig/network-scripts/ifcfg-enp0s3
[root@localhost dhcp]# cat /etc/sysconfig/network-scripts/ifcfg-enp0s3
DEVICE=enp0s3
NAME=lan

ONBOOT=yes
BOOTPROTO=static

IPADDR=10.1.1.210
NETMASK=255.255.255.0
~~~

- DHCP dans le firewall :
~~~
[root@localhost ~]# firewall-cmd --add-service=dhcp
success
[root@localhost ~]# firewall-cmd --runtime-to-permanent
success
~~~

🌞 **Test !**

~~~
node1> dhcp
DDORA IP 10.1.1.245/24 GW 10.1.1.210
~~~

🌞 **Now race !**

~~~
node1> dhcp
DORA IP 10.1.1.11/24 GW 10.1.1.1
~~~
~~~
node2> dhcp
DDORA IP 10.1.1.246/24 GW 10.1.1.210
~~~
~~~
node3> dhcp
DDORA IP 10.1.1.247/24 GW 10.1.1.210
~~~

🌞 **Wireshark !**

cf race.pcapng
