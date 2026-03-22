<!-- list block devices -->

[cloud_user@c71f0fe92f3c ~]$ lsblk

    NAME        MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
    nvme0n1     259:0    0  20G  0 disk 
    └─nvme0n1p1 259:1    0  20G  0 part /

lsblk (list block devices) shows information about block storage devices attached to the system, such as:

    -Disks
    -Partitions
    -Logical volumes
    -Their sizes and types
    -Where they are mounted in the filesystem

It reads this information from the Linux kernel and presents it in a tree‑like structure, making parent/child relationships easy to see.

<!-- partitioning using fdisk -->

[cloud_user@c71f0fe92f3c ~]$ sudo su -

    Last failed login: Fri Mar 20 21:23:05 UTC 2026 from 144.126.208.149 on ssh:notty
    There were 100 failed login attempts since the last successful login.

[root@c71f0fe92f3c ~]# lsblk

    NAME        MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
    nvme0n1     259:0    0  20G  0 disk 
    └─nvme0n1p1 259:1    0  20G  0 part /

[root@c71f0fe92f3c ~]# fdisk -l /dev/nvme0n1  [list all available partitions for nvme0n1. Its has only one partition /dev/nvme0n1p1]

    Disk /dev/nvme0n1: 21.5 GB, 21474836480 bytes, 41943040 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk label type: dos
    Disk identifier: 0x000aec37

            Device Boot      Start         End      Blocks   Id  System
    /dev/nvme0n1p1   *        2048    41943006    20970479+  83  Linux

Note that when using fdisk you can use the following options:

Command action
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition
   g   create a new empty GPT partition table
   G   create an IRIX (SGI) partition table
   l   list known partition types
   m   print this menu
   n   add a new partition
   o   create a new empty DOS partition table
   p   print the partition table
   q   quit without saving changes
   s   create a new empty Sun disklabel
   t   change a partition's system id
   u   change display/entry units
   v   verify the partition table
   w   write table to disk and exit
   x   extra functionality (experts only)

[root@c71f0fe92f3c ~]# fdisk -l /dev/nvme0n1

    Disk /dev/nvme0n1: 21.5 GB, 21474836480 bytes, 41943040 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk label type: dos
    Disk identifier: 0x000aec37

            Device Boot      Start         End      Blocks   Id  System
    /dev/nvme0n1p1   *        2048    41943006    20970479+  83  Linux
    [root@c71f0fe92f3c ~]# fdisk /dev/nvme0n1
    Welcome to fdisk (util-linux 2.23.2).

    Changes will remain in memory only, until you decide to write them.
    Be careful before using the write command.


    Command (m for help): m                ======> Enter  m and press enter to see all command options
    Command action
    a   toggle a bootable flag
    b   edit bsd disklabel
    c   toggle the dos compatibility flag
    d   delete a partition
    g   create a new empty GPT partition table
    G   create an IRIX (SGI) partition table
    l   list known partition types
    m   print this menu
    n   add a new partition
    o   create a new empty DOS partition table
    p   print the partition table
    q   quit without saving changes
    s   create a new empty Sun disklabel
    t   change a partition's system id
    u   change display/entry units
    v   verify the partition table
    w   write table to disk and exit
    x   extra functionality (experts only)

    Command (m for help): p                       ======> Enter  p to print avalaible partitions

    Disk /dev/nvme0n1: 21.5 GB, 21474836480 bytes, 41943040 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk label type: dos
    Disk identifier: 0x000aec37

            Device Boot      Start         End      Blocks   Id  System
    /dev/nvme0n1p1   *        2048    41943006    20970479+  83  Linux

    Command (m for help): n                        ======> Enter n to create new partition
    Partition type:
    p   primary (1 primary, 0 extended, 3 free)
    e   extended
    Select (default p):                             ======> Press Enter to select p, primary
    Using default response p
    Partition number (2-4, default 2):              ======> Press Enter to create partition 2

    First sector (… , default …):                   ======> Press Enter to accept default
    Last sector, +sectors or +size{K,M,G,T,P}:      ======> Enter desired partition size say +5G

    Command (m for help): w                         ======> Enter w and press enter to write changes

[root@c71f0fe92f3c ~]# lsblk

    NAME        MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
    nvme0n1     259:0    0  20G  0 disk 
    └─nvme0n1p1 259:1    0  20G  0 part /
      nvme0n1p2 259:2    0   5G  0 part    

