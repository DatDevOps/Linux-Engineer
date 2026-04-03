<!-- list block devices -->

NB that nvme0n1 has of 20G that is all used up. In reality, it should have some spare space to be able to add partition.
This is the reason this machine will not work and freezes in the "First sector" when creating the partion.
But the  below steps are accurate when creating partions with a block device with spare space or no partition.
For demonstration purposes, lets imagine that nvme0n1 has 25G of space and only 20G is been used.


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

<!-- partitioning using fdisk  -->
Fdisk creates a master boot record, MBR, or DOS partition table.

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

[root@c71f0fe92f3c ~]# fdisk /dev/nvme0n1  [creates a partition table if one does not exist. Just the table not the actual partitions]

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
    Select (default p):                             ======> Press Enter to select p, primary. Prima
    Using default response p
    Partition number (2-4, default 2):              ======> Press Enter to create partition 2

    First sector (… , default …):                   ======> Press Enter to accept default
    Last sector, +sectors or +size{K,M,G,T,P}: 5G   ======> Enter desired partition size say +5G

    Command (m for help): w                         ======> Enter w and press enter to write changes


[root@c71f0fe92f3c ~]# lsblk

    NAME        MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
    nvme0n1     259:0    0  20G  0 disk 
    └─nvme0n1p1 259:1    0  20G  0 part /
      nvme0n1p2 259:2    0   5G  0 part    

Prompts During n (New Partition) and meaning:
p or e	            ==>     Primary (max 4 on MBR) or Extended (container for logical partitions)
Partition number    ==>	    1–4 for primary; 5+ for logical (inside extended)
First sector        ==>	    Where the partition starts (default = first available)
Last sector         ==>    	Where it ends. You can specify: +500M (size in MiB), +2G (size in GiB), or an exact sector number

Common Partition Type Codes (t) and their type
83	==> Linux filesystem
82	==> Linux swap
8e	==> Linux LVM
fd	==> Linux RAID autodetect
ef	==> EFI System Partition
7	==> NTFS/HPFS (Windows)




You can modify these id's by using the 't' option and the 'w' to save if you desire as demonstrated below
Note that when trying to odify the block id it defaults to the last partition created. So change it if that is not what you want

[root@c71f0fe92f3c ~]# fdisk /dev/nvme0n1

    Welcome to fdisk (util-linux 2.23.2).

    Changes will remain in memory only, until you decide to write them.
    Be careful before using the write command.


    Command (m for help): p

    Disk /dev/nvme0n1: 21.5 GB, 21474836480 bytes, 41943040 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk label type: dos
    Disk identifier: 0x000aec37

            Device Boot      Start         End      Blocks   Id  System
    /dev/nvme0n1p1   *        2048    41943006    20970479+  83  Linux

    Command (m for help): t             =======> enter 't' to change system id and press enter
    Selected partition 1
    Hex code (type L to list all codes): 82
    Changed type of partition 'Linux' to 'Linux swap / Solaris'

    Command (m for help):               =======> enter 'w' and press enter if you want to save

[root@c71f0fe92f3c ~]# dd if=/dev/zero of=/dev/nvme0n1  count=1 bs=512 [This command is extremely destructive. It does not “partition” a disk, it completely wipes it.]


dd => A low‑level Unix utility that copies raw bytes from one place to another. It does not understand filesystems or partitions—it works directly on devices.

if=/dev/zero (input file) => /dev/zero is a special virtual device. It produces an endless stream of zero bytes (0x00). Think of it as an infinite source of zeros

of=/dev/nvme0n1 (output file) => /dev/nvme0n1 is an entire NVMe disk, not a partition. It is the entire drive. It is the  drive you want to wipe

count=1 => Write only ONE block 512K


<!-- Partition with gdisk -->
Gdisk creates GPT, GUI, partition table and supports a much larger zetabyte of data

[root@c71f0fe92f3c ~]# gdisk /dev/nvme0n1

    GPT fdisk (gdisk) version 0.8.10

    Partition table scan:
    MBR: MBR only
    BSD: not present
    APM: not present
    GPT: not present


    ***************************************************************
    Found invalid GPT and valid MBR; converting MBR to GPT format
    in memory. THIS OPERATION IS POTENTIALLY DESTRUCTIVE! Exit by
    typing 'q' if you don't want to convert your MBR partitions
    to GPT format!
    ***************************************************************


    Command (? for help): ?
    b       back up GPT data to a file
    c       change a partition's name
    d       delete a partition
    i       show detailed information on a partition
    l       list known partition types
    n       add a new partition
    o       create a new empty GUID partition table (GPT)
    p       print the partition table
    q       quit without saving changes
    r       recovery and transformation options (experts only)
    s       sort partitions
    t       change a partition's type code
    v       verify disk
    w       write table to disk and exit
    x       extra functionality (experts only)
    ?       print this menu

    Command (? for help): p                     =====> enter p to print only existing partitions
    Disk /dev/nvme0n1: 41943040 sectors, 20.0 GiB
    Logical sector size: 512 bytes
    Disk identifier (GUID): 94252201-8A00-41A7-B4D2-C278464A0037
    Partition table holds up to 128 entries
    First usable sector is 34, last usable sector is 41943006
    Partitions will be aligned on 2048-sector boundaries
    Total free space is 2014 sectors (1007.0 KiB)

    Number  Start (sector)    End (sector)  Size       Code  Name
    1            2048        41943006   20.0 GiB    8300  Linux filesystem

    Command (? for help): n
    Partition number (2-128, default 2): 
    First sector (34-2047, default = 34) or {+-}size{KMGTP}: 
    Last sector (34-2047, default = 2047) or {+-}size{KMGTP}: +5k  ======> Enter desired partition size say +5k
    Current type is 'Linux filesystem'
    Hex code or GUID (L to show codes, Enter = 8300): 8300   =====> enter 8300 or just press enter to accept the default which is 8300 
    Changed type of partition to 'Linux filesystem'

    Command (? for help): p                           =====> enter p to print partition including the one you you just created before saving it 
    Disk /dev/nvme0n1: 41943040 sectors, 20.0 GiB
    Logical sector size: 512 bytes
    Disk identifier (GUID): 94252201-8A00-41A7-B4D2-C278464A0037
    Partition table holds up to 128 entries
    First usable sector is 34, last usable sector is 41943006
    Partitions will be aligned on 2048-sector boundaries
    Total free space is 2004 sectors (1002.0 KiB)

    Number  Start (sector)    End (sector)  Size       Code  Name
    1            2048        41943006   20.0 GiB    8300  Linux filesystem
    2              34              43   5.0 KiB     8300  Linux filesystem

    Command (? for help): w         =====> enter w and press enter to save 

Note that you can create many partitions before saving them using the 'w'. 
You just have to enter 'n' again after the set the "Hex code or GUID (L to show codes, Enter = 8300): xxx" to a value you want
In Gdisk, enter 'l' to see all system Hex code and meaning. Below are those used in this lab:

8300 ==> Linux filesystem
8200 ==> Linux swap
8e00 ==> Linux LVM 

NOTE the following about partition bytes:
- 1st 512 Bytes = Protective MBR
- 2nd 512 Bytes = GPT Headers
- Next 16KB = Primary GPT
- last 16KB = Backup GPT

[root@c71f0fe92f3c ~]# dd if=/dev/zero of=/dev/nvme0n1 bs=16K [This command is extremely destructive. It does not “partition” a disk, it completely wipes it.]

dd => A low‑level Unix utility that copies raw bytes from one place to another. It does not understand filesystems or partitions—it works directly on devices.

if=/dev/zero (input file) => /dev/zero is a special virtual device. It produces an endless stream of zero bytes (0x00). Think of it as an infinite source of zeros

of=/dev/nvme0n1 (output file) => /dev/nvme0n1 is an entire NVMe disk, not a partition. It is the entire drive. It is the  drive you want to wipe

<!-- Partitioning using parted  -->

[root@ip-172-19-160-242 ~]# lsblk

    NAME                   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
    nvme1n1                259:0    0   35G  0 disk
    ├─datavg-lvvar         253:0    0    6G  0 lvm  /var
    ├─datavg-lvvarlog      253:1    0    4G  0 lvm  /var/log
    ├─datavg-lvvarlogaudit 253:2    0  400M  0 lvm  /var/log/audit
    ├─datavg-lvhome        253:3    0  4.5G  0 lvm  /home
    ├─datavg-lvtmp         253:4    0    3G  0 lvm  /tmp
    ├─datavg-lvopt         253:5    0    7G  0 lvm  /opt
    └─datavg-lvswap        253:6    0    4G  0 lvm  [SWAP]
    nvme0n1                259:1    0   10G  0 disk
    ├─nvme0n1p1            259:2    0   10G  0 part /
    ├─nvme0n1p127          259:3    0    1M  0 part
    └─nvme0n1p128          259:4    0   10M  0 part

[root@ip-172-19-160-242 ~]# parted [starts by displaying available partitions]

    GNU Parted 3.4
    Using /dev/nvme1n1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) select /dev/nvme1n1
    Using /dev/nvme1n1

    (parted) help
    align-check TYPE N                       check partition N for TYPE(min|opt) alignment
    help [COMMAND]                           print general help, or help on COMMAND
    mklabel,mktable LABEL-TYPE               create a new disklabel (partitiontable)
    mkpart PART-TYPE [FS-TYPE] START END     make a partition
    name NUMBER NAME                         name partition NUMBER as NAME
    print [devices|free|list,all|NUMBER]     display the partition table,available devices, free space, all found partitions, or a particularpartition
    quit                                     exit program
    rescue START END                         rescue a lost partition near START and END
    resizepart NUMBER END                    resize partition NUMBER
    rm NUMBER                                delete partition NUMBER
    select DEVICE                            choose the device to edit
    disk_set FLAG STATE                      change the FLAG on selected device
    disk_toggle [FLAG]                       toggle the state of FLAG on selected device
    set NUMBER FLAG STATE                    change the FLAG on partition NUMBER
    toggle [NUMBER [FLAG]]                   toggle the state of FLAG on partition NUMBER
    unit UNIT                                set the default unit to UNIT
    version                                  display the version number and copyright information of GNU Parted
    
    (parted) p                                      ===> prints current partion table
    Error: /dev/nvme1n1: unrecognised disk label
    Model: Amazon Elastic Block Store (nvme)
    Disk /dev/nvme1n1: 37.6GB
    Sector size (logical/physical): 512B/512B
    Partition Table: unknown
    Disk Flags:
    (parted) mklabel msdos                          ===> creates standard MBR DOS partition. This can be changed as shown below
    (parted) p                                      ===> prints current partion table.  Note it is an MSDOS partition
    Model: Amazon Elastic Block Store (nvme)
    Disk /dev/nvme1n1: 37.6GB
    Sector size (logical/physical): 512B/512B
    Partition Table: msdos
    Disk Flags:

    Number  Start  End  Size  Type  File system  Flags

    (parted) mklabel gpt                            ====> changes current partitionby destroying existing data as shown by the warning below
    Warning: The existing disk label on /dev/nvme1n1 will be destroyed and all data on this disk will be
    lost. Do you want to continue?
    Yes/No? Yes                                     ===> enter Yes to  change partition table type
    (parted) p                                      ===> prints current partion table. Note that it is a GPT partition table
    Model: Amazon Elastic Block Store (nvme)
    Disk /dev/nvme1n1: 37.6GB
    Sector size (logical/physical): 512B/512B
    Partition Table: gpt
    Disk Flags:

    Number  Start  End  Size  File system  Name  Flags

    (parted) mklabel msdos
    Warning: The existing disk label on /dev/nvme1n1 will be destroyed and all data on this disk will be
    lost. Do you want to continue?
    Yes/No? Yes
    (parted) mkpart primary 1 200                   ===> creates a primary partition from 1k to 200MB
    (parted) p                                      ===> prints current partion table
    Model: Amazon Elastic Block Store (nvme)
    Disk /dev/nvme1n1: 37.6GB
    Sector size (logical/physical): 512B/512B
    Partition Table: msdos
    Disk Flags:

    Number  Start   End    Size   Type     File system  Flags
    1      1049kB  200MB  199MB  primary               lba

    (parted) mkpart extended 201 300                   ===> creates an extended partition from 201MB to 300MB
    (parted) p
    Model: Amazon Elastic Block Store (nvme)
    Disk /dev/nvme1n1: 37.6GB
    Sector size (logical/physical): 512B/512B
    Partition Table: msdos
    Disk Flags:

    Number  Start   End    Size    Type      File system  Flags
    1      1049kB  200MB  199MB   primary                lba
    2      201MB   300MB  98.6MB  extended               lba

    (parted) mkpart logical 202 300                   ===> creates a logical partition from 20MB to 300MB
    (parted) p                                        ===> prints current partion table [note that partion 3,4 are reserved]
    Model: Amazon Elastic Block Store (nvme)
    Disk /dev/nvme1n1: 37.6GB
    Sector size (logical/physical): 512B/512B
    Partition Table: msdos
    Disk Flags:

    Number  Start   End    Size    Type      File system  Flags
    1      1049kB  200MB  199MB   primary                lba
    2      201MB   300MB  98.6MB  extended               lba
    5      202MB   300MB  97.5MB  logical                lba

    (parted) quit                                   ===> save and quit
    Information: You may need to update /etc/fstab.

    [root@ip-172-19-160-242 ~]# lsblk [shows our new partition]

    NAME                   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
    nvme1n1                259:0    0   35G  0 disk
    ├─datavg-lvvar         253:0    0    6G  0 lvm  /var
    ├─datavg-lvvarlog      253:1    0    4G  0 lvm  /var/log
    ├─datavg-lvvarlogaudit 253:2    0  400M  0 lvm  /var/log/audit
    ├─datavg-lvhome        253:3    0  4.5G  0 lvm  /home
    ├─datavg-lvtmp         253:4    0    3G  0 lvm  /tmp
    ├─datavg-lvopt         253:5    0    7G  0 lvm  /opt
    ├─datavg-lvswap        253:6    0    4G  0 lvm  [SWAP]
    ├─nvme1n1p1            259:5    0  190M  0 part         ===> my new partition
    ├─nvme1n1p2            259:6    0    1K  0 part         ===> my new partition
    └─nvme1n1p5            259:7    0   93M  0 part         ===> my new partition
    nvme0n1                259:1    0   10G  0 disk
    ├─nvme0n1p1            259:2    0   10G  0 part /
    ├─nvme0n1p127          259:3    0    1M  0 part
    └─nvme0n1p128          259:4    0   10M  0 part   

To remove specific partitions created do the  below:

[root@ip-172-19-160-242 ~]# parted

    GNU Parted 3.4
    Using /dev/nvme1n1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) p
    Model: Amazon Elastic Block Store (nvme)
    Disk /dev/nvme1n1: 37.6GB
    Sector size (logical/physical): 512B/512B
    Partition Table: msdos
    Disk Flags:

    Number  Start   End    Size    Type      File system  Flags
    1      1049kB  200MB  199MB   primary   xfs
    2      201MB   300MB  98.6MB  extended               lba
    5      202MB   300MB  97.5MB  logical

    (parted) rm 1                               ===> removes partion number 1
    (parted) p
    Model: Amazon Elastic Block Store (nvme)
    Disk /dev/nvme1n1: 37.6GB
    Sector size (logical/physical): 512B/512B
    Partition Table: msdos
    Disk Flags:

    Number  Start  End    Size    Type      File system  Flags
    2      201MB  300MB  98.6MB  extended               lba
    5      202MB  300MB  97.5MB  logical

    (parted) rm 2                               ===> removes partion number 2 and 5 since their start and End overlap
    (parted) p
    Model: Amazon Elastic Block Store (nvme)
    Disk /dev/nvme1n1: 37.6GB
    Sector size (logical/physical): 512B/512B
    Partition Table: msdos
    Disk Flags:

    Number  Start  End  Size  Type  File system  Flags

    (parted) quit
    Information: You may need to update /etc/fstab.

[root@ip-172-19-160-242 ~]# lsblk

    NAME                   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
    nvme1n1                259:0    0   35G  0 disk
    ├─datavg-lvvar         253:0    0    6G  0 lvm  /var
    ├─datavg-lvvarlog      253:1    0    4G  0 lvm  /var/log
    ├─datavg-lvvarlogaudit 253:2    0  400M  0 lvm  /var/log/audit
    ├─datavg-lvhome        253:3    0  4.5G  0 lvm  /home
    ├─datavg-lvtmp         253:4    0    3G  0 lvm  /tmp
    ├─datavg-lvopt         253:5    0    7G  0 lvm  /opt
    └─datavg-lvswap        253:6    0    4G  0 lvm  [SWAP]
    nvme0n1                259:1    0   10G  0 disk
    ├─nvme0n1p1            259:2    0   10G  0 part /
    ├─nvme0n1p127          259:3    0    1M  0 part
    └─nvme0n1p128          259:4    0   10M  0 part


<!-- Partioning using scripts  -->

[root@c71f0fe92f3c ~]# parted

    GNU Parted 3.1
    Using /dev/nvme0n1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) help                                                             
    align-check TYPE N                        check partition N for TYPE(min|opt) alignment
    help [COMMAND]                           print general help, or help on COMMAND
    mklabel,mktable LABEL-TYPE               create a new disklabel (partition table)
    mkpart PART-TYPE [FS-TYPE] START END     make a partition
    name NUMBER NAME                         name partition NUMBER as NAME
    print [devices|free|list,all|NUMBER]     display the partition table, available devices, free space, all found partitions, or a particular partition
    quit                                     exit program
    rescue START END                         rescue a lost partition near START and END
    
    resizepart NUMBER END                    resize partition NUMBER
    rm NUMBER                                delete partition NUMBER
    select DEVICE                            choose the device to edit
    disk_set FLAG STATE                      change the FLAG on selected device
    disk_toggle [FLAG]                       toggle the state of FLAG on selected device
    set NUMBER FLAG STATE                    change the FLAG on partition NUMBER
    toggle [NUMBER [FLAG]]                   toggle the state of FLAG on partition NUMBER
    unit UNIT                                set the default unit to UNIT
    version                                  display the version number and copyright information of GNU Parted
    (parted) print                                                            
    Model: NVMe Device (nvme)
    Disk /dev/nvme0n1: 21.5GB
    Sector size (logical/physical): 512B/512B
    Partition Table: msdos
    Disk Flags: 

    Number  Start   End     Size    Type     File system  Flags
    1      1049kB  21.5GB  21.5GB  primary  xfs          boot


To create partitions using the scripts below. 

[root@c71f0fe92f3c ~]# vi parted.sh    [opens the vi editor. Enter the content below into it]

    #!/bin/bash
    DISK="/dev/nvme2n1" # change this if your disk is different, perhaps sda, sdb, nvme0n1, mvme1n1 etc
    #Create MBR partition table and extended partition across disk starting from 1MB to the last MB of the disk, -1m
    parted -s $DISK -- mklabel msdos mkpart extended 1m -1m

    #Create swap partition as partition 5 the first logical partition [partition 3,4 are reserved by systems]
    parted -s $DISK mkpart logical linux-swap 2m 100m # partition 5

    parted -s $DISK mkpart logical 101m 200m # partition 6
    parted -s $DISK mkpart logical 201m 300m # partition 7
    parted -s $DISK mkpart logical 301m 400m # partition 8
    parted -s $DISK mkpart logical 401m 500m  # partition 9

    #Create 3 more logical partitions for LVMs
    parted -s $DISK mkpart logical 501m 600m # partition 10
    parted -s $DISK mkpart logical 601m 700m # partition 11
    parted -s $DISK mkpart logical 701m 800m # partition 12
    parted -s $DISK set 10 lvm on # setpartition 10 to LVM
    parted -s $DISK set 11 lvm on # setpartition 11 to LVM
    parted -s $DISK set 12 lvm on # setpartition 12 to LVM

    #Create 2 more Partitions for RAID
    parted -s $DISK mkpart logical 801m 900m # partition 13
    parted -s $DISK mkpart logical 901m 1000m # partition 14
    parted -s $DISK set 13 raid on # set partition 13 to RAID
    parted -s $DISK set 14 raid on # set partition 14 to RAID

    parted -s $DISK print

[root@c71f0fe92f3c ~]# chmod +x parted.sh 
[root@c71f0fe92f3c ~]# ./parted.sh 

    Model: Amazon Elastic Block Store (nvme)
    Disk /dev/nvme1n1: 37.6GB
    Sector size (logical/physical): 512B/512B
    Partition Table: msdos
    Disk Flags:

    Number  Start   End     Size    Type      File system  Flags
    1      1049kB  37.6GB  37.6GB  extended               lba
    5      2097kB  99.6MB  97.5MB  logical
    6      101MB   200MB   99.6MB  logical
    7      201MB   300MB   98.6MB  logical
    8      301MB   400MB   98.6MB  logical
    9      401MB   500MB   99.6MB  logical
    10      501MB   600MB   98.6MB  logical                lvm
    11      601MB   700MB   99.6MB  logical                lvm
    12      701MB   800MB   98.6MB  logical                lvm
    13      801MB   900MB   98.6MB  logical                raid
    14      901MB   1000MB  99.6MB  logical                raid

[root@ip-172-19-160-242 ~]# fdisk -l /dev/nvme1n1

    Disk /dev/nvme1n1: 35 GiB, 37580963840 bytes, 73400320 sectors
    Disk model: Amazon Elastic Block Store
    Units: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 4096 bytes / 4096 bytes
    Disklabel type: dos
    Disk identifier: 0xc596b6d8

    Device          Boot   Start      End  Sectors Size Id Type
    /dev/nvme1n1p1          2048 73398271 73396224  35G  f W95 Ext'd (LBA)
    /dev/nvme1n1p5          4096   194559   190464  93M 82 Linux swap / Solaris
    /dev/nvme1n1p6        196608   391167   194560  95M 83 Linux
    /dev/nvme1n1p7        393216   585727   192512  94M 83 Linux
    /dev/nvme1n1p8        587776   780287   192512  94M 83 Linux
    /dev/nvme1n1p9        782336   976895   194560  95M 83 Linux
    /dev/nvme1n1p10       978944  1171455   192512  94M 8e Linux LVM
    /dev/nvme1n1p11      1173504  1368063   194560  95M 8e Linux LVM
    /dev/nvme1n1p12      1370112  1562623   192512  94M 8e Linux LVM
    /dev/nvme1n1p13      1564672  1757183   192512  94M fd Linux raid autodetect
    /dev/nvme1n1p14      1759232  1953791   194560  95M fd Linux raid autodetect   

