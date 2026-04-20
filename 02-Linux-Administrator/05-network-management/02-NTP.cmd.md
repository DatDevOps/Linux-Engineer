<!-- Network Time Peer -->

We will learn how to sync the hardware time and the system time

# Working with time

[cloud_user@c71f0fe92f3c ~]$ sudo su -

    [sudo] password for cloud_user: 
    Last login: Mon Apr 13 13:23:22 UTC 2026 on pts/0
    Last failed login: Mon Apr 13 16:35:15 UTC 2026 from 47.97.127.96 on ssh:notty
    There were 15 failed login attempts since the last successful login.

[root@c71f0fe92f3c ~]# date  [returns system time]

    Tue Apr 14 12:31:06 UTC 2026

[root@c71f0fe92f3c ~]# hwclock [hardware clock]

    Tue 14 Apr 2026 12:32:15 PM UTC  -1.654684 seconds

The system and hardware clock can be different sometime or not in sysn and we will need to sync them.  
hwclock is a tool for reading and setting the hardware clock (also called the RTC — Real Time Clock), which is a battery-powered clock chip on the motherboard that keeps time even when the machine is off.  
What --systohc does is that it copies the current system time → into the hardware clock.
This is the opposite of --hctosys (hardware → system), which reads the RTC and sets the kernel time from it (typically done at boot).

[root@c71f0fe92f3c ~]# hwclock --systohc  [copies system time to hardware time]

[root@c71f0fe92f3c ~]# date --set="20260414 12:03" [changing the system time]

    Tue Apr 14 12:03:00 UTC 2026

[root@c71f0fe92f3c ~]# date  [system time changed but not hardware time]

    Tue Apr 14 12:03:05 UTC 2026

[root@c71f0fe92f3c ~]# hwclock  [hardware time not in sysn with system time]

    Tue 14 Apr 2026 12:42:39 PM UTC  -1.435213 seconds

[root@c71f0fe92f3c ~]# hwclock --hctosys  [copies hardware time to system time]

[root@c71f0fe92f3c ~]# date

Tue Apr 14 12:45:11 UTC 2026

[root@c71f0fe92f3c ~]# hwclock

    Tue 14 Apr 2026 12:45:18 PM UTC  -1.651926 seconds   
    
Notice that if you change either the system or hardware time, you have to sync both using --systohc or --hctosys

[root@c71f0fe92f3c ~]# timedatectl  [returns system time with more info]

        Local time: Tue 2026-04-14 12:48:52 UTC
    Universal time: Tue 2026-04-14 12:48:52 UTC
            RTC time: Tue 2026-04-14 12:48:52
        Time zone: UTC (UTC, +0000)
        NTP enabled: yes            <===== note NTP set to true/yes
    NTP synchronized: no
    RTC in local TZ: no
        DST active: n/a

[root@c71f0fe92f3c ~]# date --set="20130414 12:03"  [set system time to a wrong time and date]

    Sun Apr 14 12:03:00 UTC 2013

[root@c71f0fe92f3c ~]# hwclock --systohc  [copies wrong time to hardware clock]

[root@c71f0fe92f3c ~]# hwclock  [hardware clock sync with]

    Sun 14 Apr 2013 12:03:27 PM UTC  -1.352549 seconds

[root@c71f0fe92f3c ~]# timedatectl  [shows system time is now the wrong time I set]

        Local time: Sun 2013-04-14 12:03:55 UTC
    Universal time: Sun 2013-04-14 12:03:55 UTC
            RTC time: Sun 2013-04-14 12:03:54
        Time zone: UTC (UTC, +0000)
        NTP enabled: yes            <===== note NTP set to true/yes
    NTP synchronized: no
    RTC in local TZ: no
        DST active: n/a 

[root@c71f0fe92f3c ~]# timedatectl set-time "2026-04-14 08:15:00"   [Failed because NTP is set to true/yes]

    Failed to set time: Automatic time synchronization is enabled

[root@c71f0fe92f3c ~]# timedatectl set-ntp false   [setting NTP to false]

[root@c71f0fe92f3c ~]# timedatectl  []

        Local time: Sun 2013-04-14 12:26:55 UTC
    Universal time: Sun 2013-04-14 12:26:55 UTC
            RTC time: Sun 2013-04-14 12:26:54
        Time zone: UTC (UTC, +0000)
        NTP enabled: no            <===== note NTP set to false/no
    NTP synchronized: no
    RTC in local TZ: no
        DST active: n/a


[root@c71f0fe92f3c ~]# timedatectl set-time "2026-04-15 08:09:00"  [set the correct time/date and sync both hardware/system time using timedatectl]

[root@c71f0fe92f3c ~]# date   [new time/date picked up by stystem  time]

    Wed Apr 15 08:09:05 UTC 2026

[root@c71f0fe92f3c ~]# hwclock    [new time/date picked up by hardware time]

    Wed 15 Apr 2026 08:09:15 AM UTC  -1.972484 seconds

[root@c71f0fe92f3c ~]# timedatectl   [shows date/time with more info]

    Local time: Wed 2026-04-15 08:09:21 UTC
    Universal time: Wed 2026-04-15 08:09:21 UTC
            RTC time: Wed 2026-04-15 08:09:21
        Time zone: UTC (UTC, +0000)
        NTP enabled: no
    NTP synchronized: no
    RTC in local TZ: no
        DST active: n/a

<!-- Time synchronization with Chrony -->

[root@c71f0fe92f3c ~]# yum install -y chrony  [installs chrony package]

[root@c71f0fe92f3c ~]# vi /etc/chrony.conf   [go through to understand the chrony config file]

    # Use public servers from the pool.ntp.org project.
    # Please consider joining the pool (http://www.pool.ntp.org/join.html).
    server 0.centos.pool.ntp.org iburst
    server 1.centos.pool.ntp.org iburst
    server 2.centos.pool.ntp.org iburst
    server 3.centos.pool.ntp.org iburst

    # Record the rate at which the system clock gains/losses time.
    driftfile /var/lib/chrony/drift

    # Allow the system clock to be stepped in the first three updates
    # if its offset is larger than 1 second.
    makestep 1.0 3

    # Enable kernel synchronization of the real-time clock (RTC).
    rtcsync

    # Enable hardware timestamping on all interfaces that support it.
    #hwtimestamp *

    # Increase the minimum number of selectable sources required to adjust
    # the system clock.
    #minsources 2

    # Allow NTP client access from local network.
    #allow 192.168.0.0/16

    # Serve time even if not synchronized to a time source.
    #local stratum 10

    # Specify file containing keys for NTP authentication.
    #keyfile /etc/chrony.keys

    # Specify directory for log files.
    logdir /var/log/chrony

    # Select which information is logged.
    #log measurements statistics tracking

[root@c71f0fe92f3c ~]# systemctl status chronyd [status should be inactive]

[root@c71f0fe92f3c ~]# systemctl enable chronyd [enables chrony to start on boot]

[root@c71f0fe92f3c ~]# systemctl start chronyd  [starts chrony]

[root@c71f0fe92f3c ~]# systemctl status chronyd [status should be running]

[root@c71f0fe92f3c ~]# chronyc tracking  [aws preventing me from using chrony to change their default sync system. checkout note on output]

    Reference ID    : 00000000 ()               <=====should show ip/server name like: 81.89.61.125(zabbix.iwik.org)
    Stratum         : 0
    Ref time (UTC)  : Thu Jan 01 00:00:00 1970
    System time     : 0.000000002 seconds slow of NTP time
    Last offset     : +0.000000000 seconds
    RMS offset      : 0.000000000 seconds
    Frequency       : 18.220 ppm slow
    Residual freq   : +0.000 ppm
    Skew            : 0.000 ppm
    Root delay      : 1.000000000 seconds
    Root dispersion : 1.000000000 seconds
    Update interval : 0.0 seconds
    Leap status     : Not synchronised         <====== when sync should show 'Normal'

[root@c71f0fe92f3c ~]# chronyc sources  [check below to understand symbols]

    210 Number of sources = 4
    MS Name/IP address         Stratum Poll Reach LastRx Last sample               
    ===============================================================================
    ^? 172-233-157-223.ip.linod>     0  10     0     -     +0ns[   +0ns] +/-    0ns
    ^? 72-14-186-59.ip.linodeus>     0  10     0     -     +0ns[   +0ns] +/-    0ns
    ^? 23.186.168.133                0  10     0     -     +0ns[   +0ns] +/-    0ns
    ^? dug.arpnet.net                0  10     0     -     +0ns[   +0ns] +/-    0ns

**NB
Symbol	Meaning
^*	    Current best source (synced)
^+	    Good candidate, combined with best
^-	    Good source, not currently used
^?	    Unreachable / connectivity lost

If you have a ^* from the last command, no need to run the fix below but you can do so to see how to edit the config file and add new sync sources.

The CentOS pool servers in your config require outbound UDP port 123. If your security group doesn't allow it, chrony can't reach any source.

The AWS fix — use Amazon's local Time Sync Service instead. It's available at 169.254.169.123 (link-local, no security group rules needed)

Lets enable and add an entry to /etc/chrony.conf

[root@c71f0fe92f3c ~]timedatectl set-ntp true

[root@c71f0fe92f3c ~]sed -i '1i server 169.254.169.123 prefer iburst minpoll 4 maxpoll 4' /etc/chrony.conf [adds Amazon Time Sync as preferred source]

[root@c71f0fe92f3c ~]# cat /etc/chrony.conf  [shows the new line above added using SED. You can use VI to add it manually]

[root@c71f0fe92f3c ~]systemctl restart chronyd [restarts chrony]

[root@c71f0fe92f3c ~]chronyc sources -v  [shows/verifies servers available and used for time sync]

    MS Name/IP address         Stratum Poll Reach LastRx Last sample               
    ===============================================================================
    ^* 169.254.169.123               3   4   377     8    +29us[  +30us] +/-  722us         <=== now has a current source for for time sync
    ^? ntp.netlinkify.com            0  10     0     -     +0ns[   +0ns] +/-    0ns
    ^? 23.186.168.131                0  10     0     -     +0ns[   +0ns] +/-    0ns
    ^? 170-187-147-56.ip.linode>     0  10     0     -     +0ns[   +0ns] +/-    0ns
    ^? ntp.sfo.icanbwell.com         0   9     0     -     +0ns[   +0ns] +/-    0ns

[root@c71f0fe92f3c ~]chronyc tracking   [Now has a chosenshows info about time sync]

    Reference ID    : A9FEA97B (169.254.169.123)                <===== now has a webserver compared to above output
    Stratum         : 4
    Ref time (UTC)  : Wed Apr 15 14:57:38 2026
    System time     : 0.000007220 seconds slow of NTP time
    Last offset     : -0.000008915 seconds
    RMS offset      : 0.000004833 seconds
    Frequency       : 20.636 ppm slow
    Residual freq   : -0.046 ppm
    Skew            : 0.104 ppm
    Root delay      : 0.000501188 seconds
    Root dispersion : 0.000414474 seconds
    Update interval : 16.2 seconds
    Leap status     : Normal                                    <====== when sync should show 'Normal'





<!-- Time Synchronization with NTPD -->

NTPD (Network Time Protocol Daemon) is the classic, long-standing daemon for keeping a Linux system's clock synchronized with remote NTP servers. While chronyd (Chrony) is the modern default on RHEL/CentOS 7+, NTPD is still widely used, especially on older systems and in environments that require full NTP server capabilities.

Key differences from Chrony
Feature	                        NTPD	                                    Chrony
Sync speed	                    Slower to converge	                        Faster, especially on boot
Intermittent connections	    Poor handling	                            Designed for it
Memory/CPU	                    Heavier	                                    Lighter
Acts as full NTP server	        Yes (broadcast, multicast, symmetric)	    Limited
Default on CentOS 7+	        No (replaced by Chrony)	                    Yes

How it works?
NTPD runs as a background daemon (ntpd).
It polls configured NTP servers (defined in /etc/ntp.conf).
It gradually slews (adjusts) the system clock toward the correct time — it avoids sudden jumps by default.
On startup, if the offset is very large (>128 ms), you can use -g flag to allow a one-time step correction.

Lets stop 'chrony' and disable it befor beginningwith NTPD

[root@c71f0fe92f3c ~] systemctl stop chronyd.service

[root@c71f0fe92f3c ~] systemctl disable chronyd.service

    Removed symlink /etc/systemd/system/multi-user.target.wants/chronyd.service.

[root@c71f0fe92f3c ~] yum install -y ntp  [Install ntp package (NTPD is in the 'ntp' package, not 'ntpd')]

[root@c71f0fe92f3c ~] vi /etc/ntp.conf  [View the config file. It should look somewhat similar to the chrony config file]

    ....
    ....
    restrict 127.0.0.1
    restrict ::1

    # Hosts on local network are less restricted.
    #restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap

    # Use public servers from the pool.ntp.org project.
    # Please consider joining the pool (http://www.pool.ntp.org/join.html).
    server 0.centos.pool.ntp.org iburst
    server 1.centos.pool.ntp.org iburst
    server 2.centos.pool.ntp.org iburst
    server 3.centos.pool.ntp.org iburst

    #broadcast 192.168.1.255 autokey        # broadcast server
    #broadcastclient                        # broadcast client
    #broadcast 224.0.1.1 autokey            # multicast server
    #multicastclient 224.0.1.1              # multicast client
    #manycastserver 239.255.254.254         # manycast server
    #manycastclient 239.255.254.254 autokey # manycas
    ....
    ....


You may need to add a server like we did for chrony like below, which uses Amazon's local Time Sync Service instead:

    restrict 169.254.169.123 mask 255.255.255.0 nomodify notrap

[root@c71f0fe92f3c ~] systemctl start ntpd [starts the service]

[root@c71f0fe92f3c ~] systemctl status ntpd  [shows status that service is running]

[root@c71f0fe92f3c ~] ntpq -p  [shows server/s used for time sync. The chosen server should have * prefix]

        remote           refid      st t when poll reach   delay   offset  jitter
    ==============================================================================
    172-104-28-175. .INIT.          16 u    -   64    0    0.000    0.000   0.000
    wdc1.us.ntp.li  .INIT.          16 u    -   64    0    0.000    0.000   0.000
    ntp2.wiktel.com .INIT.          16 u    -   64    0    0.000    0.000   0.000
    23.186.168.132  .INIT.          16 u    -   64    0    0.000    0.000   0.000

From the above no server is chosen for time sync