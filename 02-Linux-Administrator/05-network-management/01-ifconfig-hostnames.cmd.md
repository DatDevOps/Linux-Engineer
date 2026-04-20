<!-- IP and Hostnames -->

# ip VS ifconfig
'ifconfig' is a dated, obsolete, way for checking system ip configurations while the 'ip' command is the newer way with shorthand syntax

sh-4.2$ ifconfig  [display interface/s ip configuration and used for interface ip configurations]

    eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 9001
            inet 172.19.160.40  netmask 255.255.255.128  broadcast 172.19.160.127
            inet6 fe80::a4:90ff:fe94:8261  prefixlen 64  scopeid 0x20<link>
            ether 02:a4:90:94:82:61  txqueuelen 1000  (Ethernet)
            RX packets 209729  bytes 228307127 (217.7 MiB)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 202056  bytes 40358972 (38.4 MiB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

    lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
            inet 127.0.0.1  netmask 255.0.0.0
            inet6 ::1  prefixlen 128  scopeid 0x10<host>
            loop  txqueuelen 1000  (Local Loopback)
            RX packets 4  bytes 190 (190.0 B)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 4  bytes 190 (190.0 B)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

sh-4.2$ ifconfig eth0  [display ip configuration for just the interface eth0]

    eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 9001
            inet 172.19.160.40  netmask 255.255.255.128  broadcast 172.19.160.127
            inet6 fe80::a4:90ff:fe94:8261  prefixlen 64  scopeid 0x20<link>
            ether 02:a4:90:94:82:61  txqueuelen 1000  (Ethernet)
            RX packets 209792  bytes 228320345 (217.7 MiB)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 202123  bytes 40375299 (38.5 MiB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

sh-4.2$ ip addr  [returns ip configurations for all interfaces]

    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
    2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
        link/ether 02:a4:90:94:82:61 brd ff:ff:ff:ff:ff:ff
        inet 172.19.160.40/25 brd 172.19.160.127 scope global dynamic eth0
        valid_lft 3020sec preferred_lft 3020sec
        inet6 fe80::a4:90ff:fe94:8261/64 scope link
        valid_lft forever preferred_lft forever

sh-4.2$ ip a   [shorthand symbol for above]

    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
    2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
        link/ether 02:a4:90:94:82:61 brd ff:ff:ff:ff:ff:ff
        inet 172.19.160.40/25 brd 172.19.160.127 scope global dynamic eth0
        valid_lft 3008sec preferred_lft 3008sec
        inet6 fe80::a4:90ff:fe94:8261/64 scope link
        valid_lft forever preferred_lft forever

sh-4.2$ ip r  [shorthand for 'ip route']

    default via 172.19.160.1 dev eth0                                   <=== Traffic to any unknown network. Goes via gateway 172.19.160.1 and Uses interface eth0
    169.254.169.254 dev eth0                                            <=== DHCP fallback. Internal kernel networking. Usually ignored in normal traffic
    172.19.160.0/25 dev eth0 proto kernel scope link src 172.19.160.40      <=== Directly reachable local subnet. No gateway required. Source IP is 172.19.160.40

sh-4.2$ ip -4 r  [returns only ipv4 address]

    default via 172.19.160.1 dev eth0
    169.254.169.254 dev eth0
    172.19.160.0/25 dev eth0 proto kernel scope link src 172.19.160.40

sh-4.2$ ip -6 r  [returns only ipv6 address if they are configured or reachable]

    unreachable ::/96 dev lo metric 1024 error 4294967183 pref medium
    unreachable ::ffff:0.0.0.0/96 dev lo metric 1024 error 4294967183 pref medium


'ip n' Linux command used to show and manage the neighbor table, which is how the system maps IP addresses to MAC addresses on the local network. Displays the neighbor cache
It shows the following:
    - IP address
    - MAC address
    - Network interface
    - Neighbor state (REACHABLE, STALE, etc.)
This is how Linux knows which MAC address to send packets to for a given IP.

sh-4.2$ ip n []

    172.19.160.2 dev eth0 lladdr 02:d0:0b:1e:7b:bc STALE
    172.19.160.1 dev eth0 lladdr 02:d0:0b:1e:7b:bc REACHABLE
    169.254.169.254 dev eth0 lladdr 02:d0:0b:1e:7b:bc STALE

sh-4.2$ ip netns  [returns ip namespace if one is configured otherwise returns nothing]

sh-4.2$ sudo ip netns add development  [creating a new ip namespace]

sh-4.2$ ip netns  [returns the ip namepace we just created and any other on the machine]

    development

# Hostnames    

<!-- view hostnames -->

sh-4.2$ hostname  [displays hostname]

    ip-172-19-160-40.ca-central-1.compute.internal

sh-4.2$ hostname -f  [displays hostname]

    ip-172-19-160-40.ca-central-1.compute.internal

sh-4.2$  uname -n

    ip-172-19-160-40.ca-central-1.compute.internal

sh-4.2$ hostnamectl  [displays hostname and used for configuring system hostname]

    Static hostname: ip-172-19-160-40.ca-central-1.compute.internal
            Icon name: computer-vm
            Chassis: vm
            Machine ID: ec29042fbe8caa598760cb6d7c3d2667
            Boot ID: 6d58b9d776de4ae59a7c781d8d7b4179
        Virtualization: amazon
    Operating System: Amazon Linux 2
        CPE OS Name: cpe:2.3:o:amazon:amazon_linux:2
            Kernel: Linux 4.14.355-281.714.amzn2.x86_64
      Architecture: x86-64

<!-- configuring hostname -->

sh-4.2$ sudo su -

    Last login: Sun Apr  5 18:10:53 CDT 2026 on pts/1

[root@ip-172-19-160-40 ~]# hostname  [returns machine hostname]

    ip-172-19-160-40.ca-central-1.compute.internal

[root@ip-172-19-160-40 ~]# hostname chukky-lab [renames hostname to chukky-lab but not persisted]

[root@ip-172-19-160-40 ~]# hostname  [displays new hostname but still not persisted]

    chukky-lab

[root@ip-172-19-160-40 ~]# hostnamectl  [shows old and new hostnames as static and transient hostnames respectively]

    Static hostname: ip-172-19-160-40.ca-central-1.compute.internal
    Transient hostname: chukky-lab                                  <==== hostname is transient because it has not yet been persisted
            Icon name: computer-vm
            Chassis: vm
            Machine ID: ec29042fbe8caa598760cb6d7c3d2667
            Boot ID: 6d58b9d776de4ae59a7c781d8d7b4179
        Virtualization: amazon
    Operating System: Amazon Linux 2
        CPE OS Name: cpe:2.3:o:amazon:amazon_linux:2
                Kernel: Linux 4.14.355-281.714.amzn2.x86_64
        Architecture: x86-64

[root@ip-172-19-160-40 ~]# cat /etc/hostname  [shows that our set hostname above is not persisted]

    ip-172-19-160-40.ca-central-1.compute.internal   

[root@ip-172-19-160-40 ~]# bash  [starting a new shell]

[root@chukky-lab ~]# cat /etc/hostname [new terminal has new hostname but hostname is still not persisted]

    ip-172-19-160-40.ca-central-1.compute.internal

[root@chukky-lab ~]# exit  [exit the new shell]

    exit

[root@ip-172-19-160-40 ~]# hostnamectl set-hostname chukky-lab.example.com  [set new hostname and persist using hostnamectl]

[root@ip-172-19-160-40 ~]# bash  [new terminal]

[root@chukky-lab ~]# cat /etc/hostname  [new terminal has new hostname and it is persisted in /etc/hostname file]

    chukky-lab.example.com    

<!-- lets set a pretty hostname -->
Pretty hostname has illegal characters not accpted by static hostnames

[root@chukky-lab ~]# hostnamectl set-hostname "chukky-'lab.example.com"  [note the illegal character apostrophe (')]

[root@chukky-lab ~]# hostnamectl

    hostnamectl
    Static hostname: chukky-lab.example.com                             <== static hostname stayed same
    Pretty hostname: chukky-'lab.example.com                            <== new pretty hostname
            Icon name: computer-vm
            Chassis: vm
            Machine ID: ec29042fbe8caa598760cb6d7c3d2667
            Boot ID: 6d58b9d776de4ae59a7c781d8d7b4179
        Virtualization: amazon
    Operating System: Amazon Linux 2
        CPE OS Name: cpe:2.3:o:amazon:amazon_linux:2
                Kernel: Linux 4.14.355-281.714.amzn2.x86_64
        Architecture: x86-64

[root@chukky-lab ~]# cat /etc/hostname  [static hostname is same]

    chukky-lab.example.com

[root@chukky-lab ~]# cat /etc/machine-i  [type and double tab to see available machine-info file because we set pretty hostname]

    machine-id    machine-info

[root@chukky-lab ~]# cat /etc/machine-info   [contains our pretty hostname]

    PRETTY_HOSTNAME="chukky-'lab.example.com"    
    

<!-- Local host and DNS resolution -->

NB:I SWITCHED TO USING 3 SERVERS ON PLURALSIGHT FOR THE DEMO


Run th below on SERVER #2 and note the hostname and ip

[cloud_user@c71f0fe92f1c ~]$ cat /etc/hostname   [Note the hostname of server2]

    c71f0fe92f1c.mylabserver.com

[cloud_user@c71f0fe92f1c ~]$ ip a   [Note the ip address of server2]

    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
        valid_lft forever preferred_lft forever
    2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
        link/ether 0a:ff:eb:15:08:c7 brd ff:ff:ff:ff:ff:ff
        inet 172.31.107.201/20 brd 172.31.111.255 scope global noprefixroute dynamic ens5
        valid_lft 3179sec preferred_lft 3179sec
        inet6 2600:1f18:502:2f01:53bd:7cc7:aa65:9c54/128 scope global noprefixroute dynamic 
        valid_lft 382sec preferred_lft 72sec
        inet6 fe80::8ff:ebff:fe15:8c7/64 scope link noprefixroute 
        valid_lft forever preferred_lft forever


Run th below on SERVER #3 and note the hostname and ip      

[cloud_user@c71f0fe92f2c ~]$ cat /etc/hostname   [Note the hostname of server3]

    c71f0fe92f2c.mylabserver.com

[cloud_user@c71f0fe92f2c ~]$ ip a    [Note the ip address of server3]

    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
        valid_lft forever preferred_lft forever
    2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
        link/ether 0a:ff:d1:3d:5c:f3 brd ff:ff:ff:ff:ff:ff
        inet 172.31.99.202/20 brd 172.31.111.255 scope global noprefixroute dynamic ens5
        valid_lft 3303sec preferred_lft 3303sec
        inet6 2600:1f18:502:2f01:cd32:18d2:4c71:f315/128 scope global noprefixroute dynamic 
        valid_lft 436sec preferred_lft 126sec
        inet6 fe80::8ff:d1ff:fe3d:5cf3/64 scope link noprefixroute 
        valid_lft forever preferred_lft forever



Now lets configure the hostnames of server 2/3 with aliases on server1

[cloud_user@c71f0fe92f3c ~]$ cat /etc/hostname    [Note the hostname of server1]

    c71f0fe92f3c.mylabserver.com

[cloud_user@c71f0fe92f3c ~]$ ip a  [Note the ip address of server3]

    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
        valid_lft forever preferred_lft forever
    2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
        link/ether 0a:ff:fe:20:37:c3 brd ff:ff:ff:ff:ff:ff
        inet 172.31.103.147/20 brd 172.31.111.255 scope global noprefixroute dynamic ens5
        valid_lft 3272sec preferred_lft 3272sec
        inet6 2600:1f18:502:2f01:c27b:23ad:c1ba:fddf/128 scope global noprefixroute dynamic 
        valid_lft 405sec preferred_lft 95sec
        inet6 fe80::8ff:feff:fe20:37c3/64 scope link noprefixroute 
        valid_lft forever preferred_lft forever


[cloud_user@c71f0fe92f3c ~]$ cat /etc/hosts  [server1 configured host.]

    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
    ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
    # Cloud Server Hostname mapping
    172.31.103.147   c71f0fe92f3c.mylabserver.com  

Lets now add server 2/3 to the host  file of server1


[root@c71f0fe92f3c ~]# vi /etc/hosts [append server2/3 ip and hostname to file with alias. You can have as many space delimited alias as you want]

    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
    ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
    # Cloud Server Hostname mapping
    172.31.103.147   c71f0fe92f3c.mylabserver.com   s1 p1             <===== [added just two alias s1 and p1]
    172.31.107.201   c71f0fe92f1c.mylabserver.com   s2 p2             <===== [added ip address, hostname, and two alias s2 and p2 for server2]
    172.31.99.202    c71f0fe92f2c.mylabserver.com   s3 p3             <===== [added ip address, hostname, and two alias s3 and p3 for server3]


Still on server1 ping the itself and other host using the aliases

[root@c71f0fe92f3c ~]# ping s1 -c3

    PING c71f0fe92f3c.mylabserver.com (172.31.103.147) 56(84) bytes of data.
    64 bytes from c71f0fe92f3c.mylabserver.com (172.31.103.147): icmp_seq=1 ttl=64 time=0.020 ms
    64 bytes from c71f0fe92f3c.mylabserver.com (172.31.103.147): icmp_seq=2 ttl=64 time=0.030 ms
    64 bytes from c71f0fe92f3c.mylabserver.com (172.31.103.147): icmp_seq=3 ttl=64 time=0.031 ms

    --- c71f0fe92f3c.mylabserver.com ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 1999ms
    rtt min/avg/max/mdev = 0.020/0.027/0.031/0.005 ms

[root@c71f0fe92f3c ~]# ping s2 -c3

    PING c71f0fe92f1c.mylabserver.com (172.31.107.201) 56(84) bytes of data.
    64 bytes from c71f0fe92f1c.mylabserver.com (172.31.107.201): icmp_seq=1 ttl=64 time=0.892 ms
    64 bytes from c71f0fe92f1c.mylabserver.com (172.31.107.201): icmp_seq=2 ttl=64 time=0.473 ms
    64 bytes from c71f0fe92f1c.mylabserver.com (172.31.107.201): icmp_seq=3 ttl=64 time=0.312 ms

    --- c71f0fe92f1c.mylabserver.com ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 2001ms
    rtt min/avg/max/mdev = 0.312/0.559/0.892/0.244 ms

[root@c71f0fe92f3c ~]# ping s3 -c3

    PING c71f0fe92f2c.mylabserver.com (172.31.99.202) 56(84) bytes of data.
    64 bytes from c71f0fe92f2c.mylabserver.com (172.31.99.202): icmp_seq=1 ttl=64 time=0.880 ms
    64 bytes from c71f0fe92f2c.mylabserver.com (172.31.99.202): icmp_seq=2 ttl=64 time=0.393 ms
    64 bytes from c71f0fe92f2c.mylabserver.com (172.31.99.202): icmp_seq=3 ttl=64 time=0.382 ms

    --- c71f0fe92f2c.mylabserver.com ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 2001ms
    rtt min/avg/max/mdev = 0.382/0.551/0.880/0.233 ms

[root@c71f0fe92f3c ~]# ping p3 -c3

    PING c71f0fe92f2c.mylabserver.com (172.31.99.202) 56(84) bytes of data.
    64 bytes from c71f0fe92f2c.mylabserver.com (172.31.99.202): icmp_seq=1 ttl=64 time=0.466 ms
    64 bytes from c71f0fe92f2c.mylabserver.com (172.31.99.202): icmp_seq=2 ttl=64 time=0.382 ms
    64 bytes from c71f0fe92f2c.mylabserver.com (172.31.99.202): icmp_seq=3 ttl=64 time=0.407 ms

    --- c71f0fe92f2c.mylabserver.com ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 2000ms
    rtt min/avg/max/mdev = 0.382/0.418/0.466/0.038 ms

[root@c71f0fe92f3c ~]# ping p2 -c3

    PING c71f0fe92f1c.mylabserver.com (172.31.107.201) 56(84) bytes of data.
    64 bytes from c71f0fe92f1c.mylabserver.com (172.31.107.201): icmp_seq=1 ttl=64 time=0.348 ms
    64 bytes from c71f0fe92f1c.mylabserver.com (172.31.107.201): icmp_seq=2 ttl=64 time=0.644 ms
    64 bytes from c71f0fe92f1c.mylabserver.com (172.31.107.201): icmp_seq=3 ttl=64 time=0.292 ms

    --- c71f0fe92f1c.mylabserver.com ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 2000ms
    rtt min/avg/max/mdev = 0.292/0.428/0.644/0.154 ms

[root@c71f0fe92f3c ~]# ping p1 -c3

    PING c71f0fe92f3c.mylabserver.com (172.31.103.147) 56(84) bytes of data.
    64 bytes from c71f0fe92f3c.mylabserver.com (172.31.103.147): icmp_seq=1 ttl=64 time=0.022 ms
    64 bytes from c71f0fe92f3c.mylabserver.com (172.31.103.147): icmp_seq=2 ttl=64 time=0.033 ms
    64 bytes from c71f0fe92f3c.mylabserver.com (172.31.103.147): icmp_seq=3 ttl=64 time=0.031 ms

    --- c71f0fe92f3c.mylabserver.com ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 1999ms
    rtt min/avg/max/mdev = 0.022/0.028/0.033/0.007 ms


Still on server1 ping the itself and other host using the hostnames

[root@c71f0fe92f3c ~]# ping c71f0fe92f3c.mylabserver.com -c3   [ping to server1]

    PING c71f0fe92f3c.mylabserver.com (172.31.103.147) 56(84) bytes of data.
    64 bytes from c71f0fe92f3c.mylabserver.com (172.31.103.147): icmp_seq=1 ttl=64 time=0.022 ms
    64 bytes from c71f0fe92f3c.mylabserver.com (172.31.103.147): icmp_seq=2 ttl=64 time=0.034 ms
    64 bytes from c71f0fe92f3c.mylabserver.com (172.31.103.147): icmp_seq=3 ttl=64 time=0.029 ms

    --- c71f0fe92f3c.mylabserver.com ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 2000ms
    rtt min/avg/max/mdev = 0.022/0.028/0.034/0.006 ms

[root@c71f0fe92f3c ~]# ping c71f0fe92f1c.mylabserver.com -c3   [ping to server2]

    PING c71f0fe92f1c.mylabserver.com (172.31.107.201) 56(84) bytes of data.
    64 bytes from c71f0fe92f1c.mylabserver.com (172.31.107.201): icmp_seq=1 ttl=64 time=0.314 ms
    64 bytes from c71f0fe92f1c.mylabserver.com (172.31.107.201): icmp_seq=2 ttl=64 time=0.301 ms
    64 bytes from c71f0fe92f1c.mylabserver.com (172.31.107.201): icmp_seq=3 ttl=64 time=0.287 ms

    --- c71f0fe92f1c.mylabserver.com ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 2000ms
    rtt min/avg/max/mdev = 0.287/0.300/0.314/0.022 ms

[root@c71f0fe92f3c ~]# ping c71f0fe92f1c.mylabserver.com -c3  [ping to server3]

    PING c71f0fe92f1c.mylabserver.com (172.31.107.201) 56(84) bytes of data.
    64 bytes from c71f0fe92f1c.mylabserver.com (172.31.107.201): icmp_seq=1 ttl=64 time=0.288 ms
    64 bytes from c71f0fe92f1c.mylabserver.com (172.31.107.201): icmp_seq=2 ttl=64 time=0.293 ms
    64 bytes from c71f0fe92f1c.mylabserver.com (172.31.107.201): icmp_seq=3 ttl=64 time=0.271 ms

    --- c71f0fe92f1c.mylabserver.com ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 2000ms
    rtt min/avg/max/mdev = 0.271/0.284/0.293/0.009 ms

<!-- DNS Name Resolution -->

[root@c71f0fe92f3c ~]# yum info avahi  [Linux package for local DNS hostname resolution. Also called 'no config' or 'zero config' because it comes as a plug and play package]

[root@c71f0fe92f3c ~]# yum info avahi

    Loaded plugins: fastestmirror
    Loading mirror speeds from cached hostfile
    * epel: d2lzkl7pfhq30w.cloudfront.net
    * nux-dextop: li.nux.ro
    Installed Packages
    Name        : avahi
    Arch        : x86_64
    Version     : 0.6.31
    Release     : 20.el7
    ....
    ....
    ....
    Repo        : base
    Summary     : Local network service discovery           <======= Avahi used for Local DNS resolution
    URL         : http://avahi.org
    ....
    ....


[root@c71f0fe92f3c ~]# getent hosts

    127.0.0.1       localhost localhost.localdomain localhost4 localhost4.localdomain4
    127.0.0.1       localhost localhost.localdomain localhost6 localhost6.localdomain6
    172.31.103.147  c71f0fe92f3c.mylabserver.com s1 p1
    172.31.107.201  c71f0fe92f1c.mylabserver.com s2 p2
    172.31.99.202   c71f0fe92f2c.mylabserver.com s3 p3

[root@c71f0fe92f3c ~]# grep hosts /etc/nsswitch.conf

    #hosts:     db files nisplus nis dns
    hosts:      files dns myhostname

In the above output, the system will first check local files (like /etc/hosts) for hostname resolution, then query DNS servers, and finally check the 'myhostname' module which resolves the local hostname.
The Name Service Switch config file tells the system which sources to query (and in what order) when resolving various types of information like hostnames, users, groups, etc.

- #hosts: db files nisplus nis dns	A commented-out line — it's inactive. It shows a legacy example using NIS/NIS+ (older network directory services).
- hosts: files dns myhostname	The active rule for hostname resolution.

Active rule: files dns myhostname
The system resolves hostnames by trying these sources left to right, stopping at the first success:
1. files — Check /etc/hosts first (local static entries)
2. dns — Query DNS servers (from /etc/resolv.conf) if not found locally
3. myhostname — Fall back to resolving the machine's own hostname if DNS also fails (provided by systemd-resolved)

The DNS server used for point 2 above query can is shown below
[root@c71f0fe92f3c ~]# cat /etc/resolv.conf   [returns the namesrver of our network]

    # Generated by NetworkManager
    search ec2.internal mylabserver.com
    nameserver 172.31.0.2    

If you run the below command and 'dig' is not installed, run the next 2 commands to install and this 'dig' command again
'dig' (Domain Information Groper) is a DNS lookup tool used to query DNS servers and inspect the results.
[root@c71f0fe92f3c ~]# dig

    ; <<>> DiG 9.11.4-P2-RedHat-9.11.4-26.P2.el7_9.16 <<>>
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 9985
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 13, AUTHORITY: 0, ADDITIONAL: 1

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 4096
    ;; QUESTION SECTION:
    ;.                              IN      NS

    ;; ANSWER SECTION:
    .                       5       IN      NS      c.root-servers.net.
    .                       5       IN      NS      d.root-servers.net.
    .                       5       IN      NS      e.root-servers.net.
    .                       5       IN      NS      f.root-servers.net.
    .                       5       IN      NS      g.root-servers.net.
    .                       5       IN      NS      h.root-servers.net.
    .                       5       IN      NS      i.root-servers.net.
    .                       5       IN      NS      j.root-servers.net.
    .                       5       IN      NS      k.root-servers.net.
    .                       5       IN      NS      l.root-servers.net.
    .                       5       IN      NS      m.root-servers.net.
    .                       5       IN      NS      a.root-servers.net.
    .                       5       IN      NS      b.root-servers.net.

    ;; Query time: 0 msec
    ;; SERVER: 172.31.0.2#53(172.31.0.2)
    ;; WHEN: Mon Apr 13 15:36:40 UTC 2026
    ;; MSG SIZE  rcvd: 239   

If 'dig' is not installed, run the next 2 commands to install and then run the above again
[root@c71f0fe92f3c ~]# dig
    bash:dig: command not found

[cloud_user@c71f0fe92f3c ~]$ dig www.pluralsight.com

    ; <<>> DiG 9.11.4-P2-RedHat-9.11.4-26.P2.el7_9.16 <<>> www.pluralsight.com
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 17446
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 4096
    ;; QUESTION SECTION:
    ;www.pluralsight.com.           IN      A

    ;; ANSWER SECTION:
    www.pluralsight.com.    300     IN      A       104.17.36.85
    www.pluralsight.com.    300     IN      A       104.17.35.85

    ;; Query time: 4 msec
    ;; SERVER: 172.31.0.2#53(172.31.0.2)        <===== DNS SERVER [uses Local dns of pluralsight ]
    ;; WHEN: Mon Apr 13 19:14:11 UTC 2026
    ;; MSG SIZE  rcvd: 80

[cloud_user@c71f0fe92f3c ~]$ dig www.pluralsight.com +short

    104.17.35.85
    104.17.36.85




Useful dig Flags and meaning

- dig www.pluralsight.com +short       # Just the IP(s), no fluff
- dig www.pluralsight.com MX           # Query for mail records
- dig www.pluralsight.com AAAA         # Query for IPv6
- dig @8.8.8.8 www.pluralsight.com     # Use a specific DNS server (Google's)
- dig www.pluralsight.com +trace       # Trace the full resolution path
