<!-- Reading OS data -->

$ cat /etc/system-release   [shows release version]

    CentOS Linux release 7.9.2009 (Core)

OR

$ lsb_release -d

    -bash: lsb_release: command not found

You might need to install the core package for Centos-7 if you get the above error: 

$ sudo yum install -y redhat-lsb-core

$ lsb_release -d [shows release version with a Description  field]

    Description:    CentOS Linux release 7.9.2009 (Core)

$ rpm -qf $(which lsb_release) [shows the parent package from  which the Centos system was derived or which package the lsb_release belong to]

    redhat-lsb-core-4.1-27.el7.centos.1.x86_64

$ uname -r [shows kernel version running on machine] 

    3.10.0-1160.119.1.el7.x86_64    

OR

$ cat /proc/version [shows kernel version with some extra details]

    Linux version 3.10.0-1160.119.1.el7.x86_64 (mockbuild@kbuilder.bsys.centos.org) (gcc version 4.8.5 20150623 (Red Hat 
    4.8.5-44) (GCC) ) #1 SMP Tue Jun 4 14:43:51 UTC 2024

$ cat /proc/cmdline [shows the commandline argument passed to the kernel during the booth process]

    BOOT_IMAGE=/boot/vmlinuz-3.10.0-1160.119.1.el7.x86_64 root=UUID=0f790447-ebef-4ca0-b229-d0aa1985d57f ro console=ttyS0
    ,115200 console=tty0 console=ttyS0,115200n8 vconsole.font=latarcyrheb-sun16 crashkernel=auto vconsole.keymap=us LANG=
    en_US.UTF-8    

$ lsblk [shows system block devices on machine]

    NAME        MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
    nvme0n1     259:0    0  20G  0 disk 
    └─nvme0n1p1 259:1    0  20G  0 part /


Duplicate server terminal in another browser; Broswer A and Browser B and switch to root and cloud_user respectively.

With both terminal open and visible to you,  run in browser A as root:

$ write cloud_user [writes/communicates with a single user - cloud_user]

    <Enter a message and watch it appear in the terminal for cloud_user in browser B.>
    <CTRL+D to exit in Browser A and just press enter to exit message in browser B>

Broadcasting to all machines using here documents and 'wall' command

Creates HereDocs
$root@# cat > message << END
> The server is coming down at 5pm today
> Please make sure all your works is saved
> and that you are logged off
> END

Verify content to broadcast
$root@# cat message 

    The server is coming down at 5pm today
    Please make sure all your works is saved
    and that you are logged off

Broad cat meesage t o all users. The content of message should appear in the terminal in browser B, because terminal A is using root credentials, whether or not the machine has mesaging enabled

$root@# wall < message 
 
Broadcast message from root@c71f0fe92f2c.mylabserver.com (Mon Mar  2 15:48:45 2026):

The server is coming down at 5pm today
Please make sure all your works is saved
and that you are logged of

Press enter to exit message screen in terminal A and in terminal B

Do can check, enable and disable messaging on a machine as shown below:

$ mesg [check mesaging status]

    is y

$ mesg n [disables messaging]

$ mesg

    is n

$   [Terminal still receives messages from root even when messaging is disabled]
Broadcast message from root@c71f0fe92f2c.mylabserver.com (Mon Mar  2 15:58:27 2026):

The server is coming down at 5pm today
Please make sure all your works is saved
and that you are logged off

[cloud_user@c71f0fe92f2c ~]$ mesg  [checks messaging status]

    is n

[cloud_user@c71f0fe92f2c ~]$ mesg y [enables messaging]

[cloud_user@c71f0fe92f2c ~]$ mesg  [checks messaging status]

    is y



<!-- Boot, reboot, and shutdown -->

$ shutdown [threw error saying you have to have root previlege]
    
    Must be root.

$ sudo su -

    [sudo] password for cloud_user: 
    Last login: Mon Mar  2 15:55:51 UTC 2026 on pts/1
    Last failed login: Mon Mar  2 17:02:28 UTC 2026 from 162.243.54.231 on ssh:notty
    There were 115 failed login attempts since the last successful login.

$root@# shutdown -h

    Shutdown scheduled for Mon 2026-03-02 18:05:41 UTC, use 'shutdown -c' to cancel.

root@# shutdown  --help

    shutdown [OPTIONS...] [TIME] [WALL...]

    Shut down the system.

        --help      Show this help
    -H --halt      Halt the machine
    -P --poweroff  Power-off the machine
    -r --reboot    Reboot the machine
    -h             Equivalent to --poweroff, overridden by --halt
    -k             Don't halt/power-off/reboot, just send warnings
        --no-wall   Don't send wall message before halt/power-off/reboot
    -c             Cancel a pending shutdown

$ reboot []

reboot [OPTIONS...] [ARG]

    Reboot the system.

        --help      Show this help
        --halt      Halt the machine
    -p --poweroff  Switch off the machine
        --reboot    Reboot the machine
    -f --force     Force immediate halt/power-off/reboot
    -w --wtmp-only Don't halt/power-off/reboot, just write wtmp record
    -d --no-wtmp   Don't write wtmp record
        --no-wall   Don't send wall message before halt/power-off/reboot

$ halt

    halt the system.

        --help      Show this help
        --halt      Halt the machine
    -p --poweroff  Switch off the machine
        --reboot    Reboot the machine
    -f --force     Force immediate halt/power-off/reboot
    -w --wtmp-only Don't halt/power-off/reboot, just write wtmp record
    -d --no-wtmp   Don't write wtmp record
        --no-wall   Don't send wall message before halt/power-off/reboot

$ poweroff

poweroff [OPTIONS...]

    Power off the system.

        --help      Show this help
        --halt      Halt the machine
    -p --poweroff  Switch off the machine
        --reboot    Reboot the machine
    -f --force     Force immediate halt/power-off/reboot
    -w --wtmp-only Don't halt/power-off/reboot, just write wtmp record
    -d --no-wtmp   Don't write wtmp record
        --no-wall   Don't send wall message before halt/power-off/reboot

$init --help [legacy command for shudown, reboot, and poweroff]

    init [OPTIONS...] {COMMAND}
    Send control commands to the init daemon.

        --help      Show this help
        --no-wall   Don't send wall message before halt/power-off/reboot
    Commands:
    0              Power-off the machine
    6              Reboot the machine
    2, 3, 4, 5     Start runlevelX.target unit
    1, s, S        Enter rescue mode
    q, Q           Reload init daemon configuration
    u, U           Reexecute init daemon

$ telinit --help [legacy command for shudown, reboot, and poweroff]

    telinit [OPTIONS...] {COMMAND}

    Send control commands to the init daemon.

        --help      Show this help
        --no-wall   Don't send wall message before halt/power-off/reboot

    Commands:
    0              Power-off the machine
    6              Reboot the machine
    2, 3, 4, 5     Start runlevelX.target unit
    1, s, S        Enter rescue mode
    q, Q           Reload init daemon configuration
    u, U           Reexecute init daemon

$ shutdown -h 10 "The server will shutdown in 10mins" 

$ sudo su -
[sudo] password for cloud_user: 

root@# shutdown -h 10 "The server will shutdown in 10mins"  [Shuts down th server in 10mins and broadcast the message using wall]

Shutdown scheduled for Thu 2026-03-05 14:55:43 UTC, use 'shutdown -c' to cancel.
Broadcast message from root@c71f0fe92f2c.mylabserver.com (Thu 2026-03-05 14:45:43 UTC):

The servr will shutdown in 10mins

The system is going down for power-off at Thu 2026-03-05 14:55:43 UTC!

^C  [CTRL+C to free terminal for running next command]

root@# shutdown -c      [Cancels the shutdown]

Broadcast message from root@c71f0fe92f2c.mylabserver.com (Thu 2026-03-05 14:45:52 UTC):

The system shutdown has been cancelled at Thu 2026-03-05 14:46:52 UTC!


# Run Levels

#Runlevel       Name                    Description

0               Halt                    ShutdownShuts down the system safely [poweroff.target].
1               Single-User Mode        Maintenance mode. Only root is logged in. Networking is disabled. Used for repairs, password resets, etc [rescue.target].
2               Multi-User Mode         Mode Multi-user, but typically without network services [multi-user.target].
3               Multi-User              Fully multi-user with networking but no graphical UI. Standard server mode [multi-user.target].
4               Unused / Custom         Not used by most distros. Available for custom configurations [multi-user.target or custom target].
5               Graphical Mode          Multi-user with networking and graphical desktop environment [graphical.target]
6               Reboot                  Reboots the system safely [reboot.target].

Note:
Different distros may treat runlevels 2–5 differently, but systemd unifies them into two key targets:
- multi-user.target
- graphical.target


root@# who
    
    cloud_user pts/0        2026-03-05 14:20 (localhost)

root@# who -r

         run-level 3  2026-03-05 13:31

[root@c71f0fe92f2c ~]# runlevel 
    
    N 3

root@# runlevel --help

runlevel [OPTIONS...]

Prints the previous and current runlevel of the init system.

     --help      Show this help

root@# systemctl get-default

    multi-user.target

root@# systemctl set-default multi-user.target







<!-- Boot systems into different run levels manually -->

<!-- Manipulate the Linux system during boot process -->