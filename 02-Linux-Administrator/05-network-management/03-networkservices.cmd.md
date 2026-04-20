<!-- Displaying and Set/Configuring IP Addresses -->

[root@c71f0fe92f3c ~]# ip addr show [display ip address of the machine]

    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
        valid_lft forever preferred_lft forever
    2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
        link/ether 0a:ff:fe:20:37:c3 brd ff:ff:ff:ff:ff:ff
        inet 172.31.103.147/20 brd 172.31.111.255 scope global noprefixroute dynamic ens5
        valid_lft 3106sec preferred_lft 3106sec
        inet6 2600:1f18:502:2f01:c27b:23ad:c1ba:fddf/128 scope global noprefixroute dynamic 
        valid_lft 419sec preferred_lft 109sec
        inet6 fe80::8ff:feff:fe20:37c3/64 scope link noprefixroute 
        valid_lft forever preferred_lft forever

[root@c71f0fe92f3c ~]# ip a s [shorthand syntax for 'ip addr show' above]

    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
        valid_lft forever preferred_lft forever
    2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
        link/ether 0a:ff:fe:20:37:c3 brd ff:ff:ff:ff:ff:ff
        inet 172.31.103.147/20 brd 172.31.111.255 scope global noprefixroute dynamic ens5
        valid_lft 2626sec preferred_lft 2626sec
        inet6 2600:1f18:502:2f01:c27b:23ad:c1ba:fddf/128 scope global noprefixroute dynamic 
        valid_lft 429sec preferred_lft 119sec
        inet6 fe80::8ff:feff:fe20:37c3/64 scope link noprefixroute 
        valid_lft forever preferred_lft forever        

[root@c71f0fe92f3c ~]# ip a [shorthand syntax to display ip address of the machine]

    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
        valid_lft forever preferred_lft forever
    2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
        link/ether 0a:ff:fe:20:37:c3 brd ff:ff:ff:ff:ff:ff
        inet 172.31.103.147/20 brd 172.31.111.255 scope global noprefixroute dynamic ens5
        valid_lft 3101sec preferred_lft 3101sec
        inet6 2600:1f18:502:2f01:c27b:23ad:c1ba:fddf/128 scope global noprefixroute dynamic 
        valid_lft 415sec preferred_lft 105sec
        inet6 fe80::8ff:feff:fe20:37c3/64 scope link noprefixroute 
        valid_lft forever preferred_lft forever

[root@c71f0fe92f3c ~]# ip -4 a  [display only ipv4 address]

    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
    2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
        inet 172.31.103.147/20 brd 172.31.111.255 scope global noprefixroute dynamic ens5
        valid_lft 2952sec preferred_lft 2952sec        

[root@c71f0fe92f3c ~]# ip -6 a  [display only ipv6 address]

    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 state UNKNOWN qlen 1000
        inet6 ::1/128 scope host 
        valid_lft forever preferred_lft forever
    2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 state UP qlen 1000
        inet6 2600:1f18:502:2f01:c27b:23ad:c1ba:fddf/128 scope global noprefixroute dynamic 
        valid_lft 383sec preferred_lft 73sec
        inet6 fe80::8ff:feff:fe20:37c3/64 scope link noprefixroute 
        valid_lft forever preferred_lft forever        

[root@c71f0fe92f3c ~]# ip -4 a s ens5  [displays ipv4 info for the network interface ens5 only]

    2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
        inet 172.31.103.147/20 brd 172.31.111.255 scope global noprefixroute dynamic ens5
        valid_lft 2728sec preferred_lft 2728sec        

[root@c71f0fe92f3c ~]# ip a s ens5  [displays ipv4/ipv6 address info for the network interface ens5 only]

    2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
        link/ether 0a:ff:fe:20:37:c3 brd ff:ff:ff:ff:ff:ff
        inet 172.31.103.147/20 brd 172.31.111.255 scope global noprefixroute dynamic ens5
        valid_lft 2485sec preferred_lft 2485sec
        inet6 2600:1f18:502:2f01:c27b:23ad:c1ba:fddf/128 scope global noprefixroute dynamic 
        valid_lft 430sec preferred_lft 120sec
        inet6 fe80::8ff:feff:fe20:37c3/64 scope link noprefixroute 
        valid_lft forever preferred_lft forever   

[root@c71f0fe92f3c ~]# ip addr add 172.17.67.3/16 dev ens5  [adds an ip address to an interface]

You can also add ip addresses to an interface using the shorthand syntax: 'ip a a 72.17.67.3/16 dev ens5'

[root@c71f0fe92f3c ~]# ip a s [new ip added to the ens5 interface]

    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
        valid_lft forever preferred_lft forever
    2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
        link/ether 0a:ff:fe:20:37:c3 brd ff:ff:ff:ff:ff:ff
        inet 172.31.103.147/20 brd 172.31.111.255 scope global noprefixroute dynamic ens5
        valid_lft 2245sec preferred_lft 2245sec
        inet 172.17.67.3/16 scope global ens5                                                   <======= added ip
        valid_lft forever preferred_lft forever
        inet6 2600:1f18:502:2f01:c27b:23ad:c1ba:fddf/128 scope global noprefixroute dynamic 
        valid_lft 400sec preferred_lft 90sec
        inet6 fe80::8ff:feff:fe20:37c3/64 scope link noprefixroute 
        valid_lft forever preferred_lft forever     

Note that the ip is not persisited because a restart of the network services means the interface will loose the added ip because we have not configured it anywhere on the system. The solution is to use a network manager

<!-- Network Manager -->

[root@c71f0fe92f3c ~]# systemctl status NetworkManager

    ● NetworkManager.service - Network Manager
    Loaded: loaded (/usr/lib/systemd/system/NetworkManager.service; enabled; vendor preset: enabled)
    Active: active (running) since Mon 2026-04-20 11:40:09 UTC; 52min ago
        Docs: man:NetworkManager(8)
        ...
        ...

[root@c71f0fe92f3c ~]# man nmcli [ displays all available options. we are interested in these ones: add, delete, down, edit, help, load, reload, show, up] 

    add delete down edit help load reload show up

[root@c71f0fe92f3c ~]# nmcli connection show [displays available connections with name, uuid, type, and device - system 'interface']

    NAME         UUID                                  TYPE      DEVICE 
    System ens5  8126c120-a964-e959-ff98-ac4973344505  ethernet  ens5   
    System eth0  5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  --  

[root@c71f0fe92f3c ~]# nmcli connection show "System ens5"  [returns a long list info without preyty format using Name of device] 


[root@c71f0fe92f3c ~]# nmcli -pretty connection show "System ens5" [returns same info as above command but with pretty format. You can just use '-p' for shorthand]

[root@c71f0fe92f3c ~]# nmcli -p connection show 8126c120-a964-e959-ff98-ac4973344505  [returns a pretty long list info without preyty format using UUID of device] 

[root@c71f0fe92f3c ~]# nmcli device show ens5 [returns a long list info without using the physical interface name] 

    GENERAL.DEVICE:                         ens5
    GENERAL.TYPE:                           ethernet
    GENERAL.HWADDR:                         0A:FF:FE:20:37:C3
    GENERAL.MTU:                            9001
    GENERAL.STATE:                          100 (connected)
    GENERAL.CONNECTION:                     System ens5
    GENERAL.CON-PATH:                       /org/freedesktop/NetworkManager/ActiveConnection/1
    WIRED-PROPERTIES.CARRIER:               on
    IP4.ADDRESS[1]:                         172.31.103.147/20
    IP4.ADDRESS[2]:                         172.17.67.3/16
    IP4.GATEWAY:                            172.31.96.1
    IP4.ROUTE[1]:                           dst = 0.0.0.0/0, nh = 172.31.96.1, mt = 100
    IP4.ROUTE[2]:                           dst = 172.31.96.0/20, nh = 0.0.0.0, mt = 100
    IP4.ROUTE[3]:                           dst = 172.17.0.0/16, nh = 0.0.0.0, mt = 0
    IP4.ROUTE[4]:                           dst = 172.17.0.0/16, nh = 0.0.0.0, mt = 100
    IP4.DNS[1]:                             172.31.0.2
    IP4.DOMAIN[1]:                          ec2.internal
    IP6.ADDRESS[1]:                         2600:1f18:502:2f01:c27b:23ad:c1ba:fddf/128
    IP6.ADDRESS[2]:                         fe80::8ff:feff:fe20:37c3/64
    IP6.GATEWAY:                            fe80::8d9:3cff:fedf:ff86
    IP6.ROUTE[1]:                           dst = 2600:1f18:502:2f01::/64, nh = ::, mt = 100
    IP6.ROUTE[2]:                           dst = ::/0, nh = fe80::8d9:3cff:fedf:ff86, mt = 100
    IP6.ROUTE[3]:                           dst = ff00::/8, nh = ::, mt = 256, table=255
    IP6.ROUTE[4]:                           dst = fe80::/64, nh = ::, mt = 100
    IP6.ROUTE[5]:                           dst = 2600:1f18:502:2f01:c27b:23ad:c1ba:fddf/128, nh = ::, mt = 100

[root@c71f0fe92f3c ~]# nmcli connection add con-name home ifname ens6 type ethernet ip4 172.17.67.4 gw4 172.31.96.1

    Connection 'home' (c133fd87-e283-4efa-a5dd-71c5fc6e5a43) successfully added.

The above command creates a new NetworkManager connection profile. Here's the breakdown:

- nmcli connection add    ==> adds a new connection profile to NetworkManager
- con-name home           ==> names the connection profile "home" (a friendly label you'll use to reference it)
- ifname ens6             ==> binds it to the physical interface ens6 (doesn't need to exist yet. In our case here it does not exist)
- type ethernet           ==>specifies this is a wired Ethernet connection
- ip4 172.17.67.4         ==> assigns the static IPv4 address 172.17.67.4 to the interface
- gw4 172.31.96.1         ==> sets the default IPv4 gateway to 172.31.96.1

Unlike ip addr add (which you used earlier and is lost on reboot/restart), this connection profile is persisted — NetworkManager saves it as a config file under /etc/sysconfig/network-scripts/ifcfg-home, so it survives service restarts and reboots.

Note: the connection shows DEVICE: -- in nmcli connection show because ens6 doesn't physically exist on this machine. To activate it, you'd run nmcli connection up home, but that would only work if the ens6 interface is present.

[root@c71f0fe92f3c ~]# nmcli connection show

    NAME         UUID                                  TYPE      DEVICE 
    System ens5  8126c120-a964-e959-ff98-ac4973344505  ethernet  ens5   
    home         c133fd87-e283-4efa-a5dd-71c5fc6e5a43  ethernet  --    <====== new connection added 
    System eth0  5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  --     

[root@c71f0fe92f3c ~]# nmcli connection delete home  [delete our new added connection]

    Connection 'home' (c133fd87-e283-4efa-a5dd-71c5fc6e5a43) successfully deleted.

[root@c71f0fe92f3c ~]# nmcli con show  [add conenction no longer  available because we deleted it. here we use the shorthand syntax 'con']

    NAME         UUID                                  TYPE      DEVICE 
    System ens5  8126c120-a964-e959-ff98-ac4973344505  ethernet  ens5   
    System eth0  5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  --     

[root@c71f0fe92f3c ~]# nmcli connection add con-name home ifname ens6 type ethernet ip4 172.17.67.4 gw4 172.31.96.1 [adding new connnection to existing interface]

     Connection 'home' (c233fd87-e283-4efa-a5dd-71c5fc6e5a43) successfully added.

[root@c71f0fe92f3c ~]# nmcli connection show

    NAME         UUID                                  TYPE      DEVICE 
    System ens5  8126c120-a964-e959-ff98-ac4973344505  ethernet  ens5   
    home         c233fd87-e283-4efa-a5dd-71c5fc6e5a43  ethernet  --    <====== new connection added 
    System eth0  5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  --          

[root@c71f0fe92f3c ~]# ip a s ens5  [displays ipv4/ipv6 address info for the network interface ens5 only. The new connection is not among ip's]


[root@c71f0fe92f3c ~]#nmcli connection down "System ens5"  [disables the connection]

[root@c71f0fe92f3c ~]#nmcli connection up "home"  [enables the connection]


[root@c71f0fe92f3c ~]# ip a s ens5  [now shows the new connection ip]

[root@c71f0fe92f3c ~]#nmcli connection down "home"  [disables the connection]

[root@c71f0fe92f3c ~]#nmcli connection up "System ens5"  [enables the connection]

[root@c71f0fe92f3c ~]# ip a s ens5  [now shows the new System ens5 ip]

In this way we can add and remote connections with associated ip addresses

[root@c71f0fe92f3c ~]# nmcli connection delete home  [delete our new added connection]

    Connection 'home' (c233fd87-e283-4efa-a5dd-71c5fc6e5a43) successfully deleted.


Network Manager is pretty cool but more suited for mobile devices and not a stationed server. Better way is to use Network services with configuration files


<!-- Network Services and Configuration files -->

[root@c71f0fe92f3c ~]# ls -l /etc/sysconfig/network-scripts/   [contains configs for interfaces]

    total 236
    -rw-r--r--. 1 root root   199 Mar 20 19:26 ifcfg-ens5
    -rw-r--r--. 1 root root   184 Jul  9  2019 ifcfg-eth0
    -rw-r--r--. 1 root root   254 May 22  2020 ifcfg-lo
    lrwxrwxrwx. 1 root root    24 Mar 12  2021 ifdown -> ../../../usr/sbin/ifdown
    ...
    ...

[root@c71f0fe92f3c ~]# ip a s  [shows ip address and associated interfaces]

    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
        valid_lft forever preferred_lft forever
    2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
        link/ether 0a:ff:fe:20:37:c3 brd ff:ff:ff:ff:ff:ff
        inet 172.31.103.147/20 brd 172.31.111.255 scope global noprefixroute dynamic ens5
        valid_lft 2202sec preferred_lft 2202sec
        inet6 2600:1f18:502:2f01:c27b:23ad:c1ba:fddf/128 scope global noprefixroute dynamic 
        valid_lft 434sec preferred_lft 124sec
        inet6 fe80::8ff:feff:fe20:37c3/64 scope link   

[root@c71f0fe92f3c ~]# cat  /etc/sysconfig/network-scripts/ifcfg-ens5   [shows config for ens5 interface]

    # Created by cloud-init on instance boot automatically, do not edit.
    #
    BOOTPROTO=dhcp
    DEVICE=ens5
    DHCPV6C=yes
    HWADDR=0a:ff:fe:20:37:c3
    IPV6INIT=yes
    ONBOOT=yes
    STARTMODE=auto
    TYPE=Ethernet
    USERCTL=no

[root@c71f0fe92f3c ~]# cd  /etc/sysconfig/network-scripts/

[root@c71f0fe92f3c network-scripts]# cp ifcfg-ens5 ifcfg-ens6  [copy existing config from ens5 to create a new interface config ens6]

[root@c71f0fe92f3c network-scripts]# ls -l ifcfg*  [contains new config]

    -rw-r--r--. 1 root root 199 Mar 20 19:26 ifcfg-ens5
    -rw-r--r--. 1 root root 199 Apr 20 16:45 ifcfg-ens6
    -rw-r--r--. 1 root root 184 Jul  9  2019 ifcfg-eth0
    -rw-r--r--. 1 root root 254 May 22  2020 ifcfg-lo

[root@c71f0fe92f3c network-scripts]# vi  ifcfg-ens6 [add some new for learning purposes]

Add or make the following change:
Device=ens6  (we assume we have an existing ens6 interface)
IPADDR=<ENTER IP HERE>
NETMASK=<ENTER NETMASK HERE>
GATEWAY=<ENTER GATEWAY HERE>
TYPE=Ethernet
NM_CONTROLLED=no                    <==== here we don't want the interface to be controlled network manager

#Note that the rest of the command below could not be executed because my AWS EC2 from PluralSight was shuttin down if I run ifdown. But on try it on virtual box and you should get similar result

[root@c71f0fe92f3c ~] # ifdown ens6

[root@c71f0fe92f3c ~] # ifup ens6

[root@c71f0fe92f3c ~] # systemctl stop NetworkManager

[root@c71f0fe92f3c ~] # systemctl start NetworkManager

[root@c71f0fe92f3c ~] # nmcli con show

[root@c71f0fe92f3c ~] # ip a s [shold now have the new configured interface with ip address]

[root@c71f0fe92f3c ~] # nmcli con show  [should contain the new configured interface]

    NAME         UUID                                  TYPE      DEVICE 
    System ens5  8126c120-a964-e959-ff98-ac4973344505  ethernet  ens5   
    ens6         d233fd87-e283-4efa-a5dd-71c5fc6e5a43  ethernet  ens6   <====== new connection added 
    System eth0  5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  --   

[root@c71f0fe92f3c ~] # ifup ens6
[root@c71f0fe92f3c ~] # ifup ens6



