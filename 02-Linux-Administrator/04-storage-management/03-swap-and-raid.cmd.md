<!-- SWAP AND RAID -->

# SWAP 

[root@ip-172-19-160-40 ~]# lsblk

    NAME                   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTnvme1n1                259:0    0   35G  0 disk
    ├─datavg-lvvar         253:0    0    6G  0 lvm  /var
    ├─datavg-lvvarlog      253:1    0    4G  0 lvm  /var/log
    ├─datavg-lvvarlogaudit 253:2    0  400M  0 lvm  /var/log/audit
    ├─datavg-lvhome        253:3    0  4.5G  0 lvm  /home
    ├─datavg-lvtmp         253:4    0    3G  0 lvm  /tmp
    ├─datavg-lvopt         253:5    0    7G  0 lvm  /opt
    └─datavg-lvswap        253:6    0    4G  0 lvm  [SWAP]
    nvme0n1                259:1    0   10G  0 disk
    ├─nvme0n1p1            259:2    0   10G  0 part /
    └─nvme0n1p128          259:3    0    1M  0 part
    nvme2n1                259:4    0   20G  0 disk
    ├─nvme2n1p1            259:16   0    1K  0 part
    ├─nvme2n1p5            259:17   0   93M  0 part
    ├─nvme2n1p6            259:18   0   95M  0 part /data/mydata├─nvme2n1p7            259:19   0   94M  0 part /data/data2
    ├─nvme2n1p8            259:20   0   94M  0 part
    ├─nvme2n1p9            259:21   0   95M  0 part
    ├─nvme2n1p10           259:22   0   94M  0 part
    ├─nvme2n1p11           259:23   0   95M  0 part
    ├─nvme2n1p12           259:24   0   94M  0 part
    ├─nvme2n1p13           259:25   0   94M  0 part
    └─nvme2n1p14           259:26   0   95M  0 part

[root@ip-172-19-160-40 ~]# fdisk -l /dev/nvme2n1

    Disk /dev/nvme2n1: 20 GiB, 21474836480 bytes, 41943040 sectors
    Units: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disklabel type: dos
    Disk identifier: 0x000e9320

    Device          Boot   Start      End  Sectors Size Id Type
    /dev/nvme2n1p1          2048 41940991 41938944  20G  f W95 Ext'd (LBA)
    /dev/nvme2n1p5          4096   194559   190464  93M 82 Linux swap / Solaris
    /dev/nvme2n1p6        196608   391167   194560  95M 83 Linux
    /dev/nvme2n1p7        393216   585727   192512  94M 83 Linux
    /dev/nvme2n1p8        587776   780287   192512  94M 83 Linux
    /dev/nvme2n1p9        782336   976895   194560  95M 83 Linux
    /dev/nvme2n1p10       978944  1171455   192512  94M 8e Linux LVM
    /dev/nvme2n1p11      1173504  1368063   194560  95M 8e Linux LVM
    /dev/nvme2n1p12      1370112  1562623   192512  94M 8e Linux LVM
    /dev/nvme2n1p13      1564672  1757183   192512  94M fd Linux raid autodetect
    /dev/nvme2n1p14      1759232  1953791   194560  95M fd Linux raid autodetect


mkswap prepares a block device (partition or file) to be used as swap space.
✅ It writes swap metadata (swap header, UUID, label)
✅ It does not format it like a filesystem
✅ Any data previously on that partition is destroyed


** Here I had to restart my ec2 instance and the volumes attached change labels to nvme1n1. Previous for it was nvme2n1

[root@ip-172-19-160-40 ~]# lsblk

    NAME                   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    nvme1n1                259:0    0   20G  0 disk
    ├─nvme1n1p1            259:2    0    1K  0 part
    ├─nvme1n1p5            259:3    0   93M  0 part
    ├─nvme1n1p6            259:4    0   95M  0 part /data/mydata
    ├─nvme1n1p7            259:5    0   94M  0 part /data/data2
    ├─nvme1n1p8            259:6    0   94M  0 part
    ├─nvme1n1p9            259:7    0   95M  0 part
    ├─nvme1n1p10           259:8    0   94M  0 part
    ├─nvme1n1p11           259:9    0   95M  0 part
    ├─nvme1n1p12           259:10   0   94M  0 part
    ├─nvme1n1p13           259:11   0   94M  0 part
    └─nvme1n1p14           259:12   0   95M  0 part
    nvme2n1                259:1    0   35G  0 disk
    ├─datavg-lvvar         253:0    0    6G  0 lvm  /var
    ├─datavg-lvvarlog      253:1    0    4G  0 lvm  /var/log
    ├─datavg-lvvarlogaudit 253:2    0  400M  0 lvm  /var/log/audit
    ├─datavg-lvhome        253:3    0  4.5G  0 lvm  /home
    ├─datavg-lvtmp         253:4    0    3G  0 lvm  /tmp
    ├─datavg-lvopt         253:5    0    7G  0 lvm  /opt
    └─datavg-lvswap        253:6    0    4G  0 lvm  [SWAP]
    nvme0n1                259:13   0   10G  0 disk
    ├─nvme0n1p1            259:14   0   10G  0 part /
    └─nvme0n1p128          259:15   0    1M  0 part

[root@ip-172-19-160-40 ~]# mkswap /dev/nvme1n1p5  [setting the uuid for swap vol but not formatting it yet]

[root@ip-172-19-160-40 ~]# swapon -s  [general system swap sub-summary]

    Filename                                Type            Size    Used    Priority
    /dev/dm-6                               partition       4194300 32768   -2

Note that the priority controls how the swap space is used, i.e which swap space is used first as set by the priority - decreasing priority sequence: -1, -2, -3, etc. Greater numbers have first priority

[root@ip-172-19-160-40 ~]# swapon /dev/nvme1n1p5  [activates a swap partition so the system can start using it immediately.]

[root@ip-172-19-160-40 ~]# swapon -s  [general system swap sub-summary]

    Filename                                Type            Size    Used    Priority
    /dev/dm-6                               partition       4194300 32768   -2
    /dev/nvme1n1p5                          partition       95228   0       -3

[root@ip-172-19-160-40 ~]# swapoff /dev/nvme1n1p5 [deactivates a swap partition so the system can start using it immediately.]

[root@ip-172-19-160-40 ~]# swapon -s  [general system swap sub-summary]

    Filename                                Type            Size    Used    Priority
    /dev/dm-6                               partition       4194300 32396   -2

[root@ip-172-19-160-40 ~]# swapon /dev/nvme1n1p5  [activates a swap partition so the system can start using it immediately.]

[root@ip-172-19-160-40 ~]# swapon /dev/nvme1n1p5

[root@ip-172-19-160-40 ~]# free -m

                total        used        free      shared  buff/cache   available
    Mem:            940         390          62           0         487         410
    Swap:          4188          31        4157

[root@ip-172-19-160-40 ~]# swapoff /dev/nvme1n1p5

[root@ip-172-19-160-40 ~]# free -m

                total        used        free      shared  buff/cache   available
    Mem:            940         391          72           0         475         409
    Swap:          4095          31        4064
[root@ip-172-19-160-40 ~]# swapon /dev/nvme1n1p5

[root@ip-172-19-160-40 ~]# free -m

                total        used        free      shared  buff/cache   available
    Mem:            940         389          74           0         476         411
    Swap:          4188          31        4157


[root@ip-172-19-160-40 ~]# swapoff -a        [disables all active swap spaces on the system immediately.]

[root@ip-172-19-160-40 ~]# swapon -s         [returns nothings as all swap spaces are disabled]

[root@ip-172-19-160-40 ~]# free -m

                total        used        free      shared  buff/cache   available
    Mem:            940         408          72           0         458         391
    Swap:             0           0           0

[root@ip-172-19-160-40 ~]# vi /etc/fstab   [add the below to end of the file which adds the uuid of the swap volume with priority of 5]

    UUID="ea0b9d20-7365-40e0-87cb-619367b43726"   swap                  swap     sw,pri=1 0 0

[root@ip-172-19-160-40 ~]# swapon -a  [enables all active swap spaces on the system immediately.]

[root@ip-172-19-160-40 ~]# swapon -s

    Filename                                Type            Size    Used    Priority
    /dev/dm-6                               partition       4194300 0       -2
    /dev/nvme1n1p5                          partition       95228   0       5


# RAID 

There are ways that we can choose to add in extra performance using RAID and RAID can give us extra performance or fault tolerance. 
When we look at software RAID in Linux, we mean a redundant array of inexpensive disks, or at least that's what the acronym stands for. 
The disk may be inexpensive, but very often, they're still going to be relatively expensive.

When we're looking at the different types of RAID levels or the RAID arrays that we can create, we have: 
- Linear array: all we're really doing is expanding our disk across multiple volumes and these volumes then don't need to be of the same size. 
- RAID0:  is very similar, but with RAID0 we have to use partitions or disks of the same size. 
- RAID1:  that's what we're going to set up in the demonstration, we're mirroring data and the drives that need to be of the same size, 
  so we can then go through and let's say add in a 1-gig drive for the file system, but that will be made up of two 1-gig physical drives. 
  So our dev mapper device is going to show as 1 gig, but it's going to be made up of two 1-gig disks, 
  but we mirror whatever we write to disk 1 to disk 2, so in this way we're providing very good redundancy. 
- But we can combine our redundancy and our fault tolerance by looking at RAID4, 5, and 6 and this then allows us to start adding in more drives and 
  having redundant data stored on one or more of those drives, so this is called striping with parity. 

[root@ip-172-19-160-40 ~]# fdisk -l /dev/nvme1n1  [ note nvme1n1p13 and nvme1n1p14 raid volumes.]

    Disk /dev/nvme1n1: 20 GiB, 21474836480 bytes, 41943040 sectors
    Units: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disklabel type: dos
    Disk identifier: 0x000e9320

    Device          Boot   Start      End  Sectors Size Id Type
    /dev/nvme1n1p1          2048 41940991 41938944  20G  f W95 Ext'd (LBA)
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

The raid devices (nvme1n1p13  and nvme1n1p14) are on the same volume but ideally should be on defifferent volumes for fault tolerance. This just to show how to setup raid devices

[root@ip-172-19-160-40 ~]# cat /proc/mdstat  [mdstat = multipl disk  stat. The presence of this file shows the system you are using supports RAID and displays raid devices. For now no raid device]

    Personalities :
    unused devices: <none>

[root@ip-172-19-160-40 ~]# mdadm --create --verbose /dev/md0 --level=mirror --raid-devices=2 /dev/nvme1n1p13 /dev/nvme1n1p14

    mdadm: Note: this array has metadata at the start and
        may not be suitable as a boot device.  If you plan to
        store '/boot' on this device please ensure that
        your boot-loader understands md/v1.x metadata, or use
        --metadata=0.90
    mdadm: size set to 96128K
    mdadm: largest drive (/dev/nvme1n1p14) exceeds size (96128K) by more than 1%
    Continue creating array? y
    mdadm: Defaulting to version 1.2 metadata
    mdadm: array /dev/md0 started.


mdadm	            ==>       Multiple Disk Admin tool — manages software RAID
--create	        ==>       Create a new RAID array
--verbose	        ==>       Show detailed output during creation
/dev/md0	        ==>       Name of the new RAID device being created
--level=mirror	    ==>    RAID level 1 (mirrors data between drives)
--raid-devices=2    ==> 	Use 2 physical devices in this array
/dev/nvme1n1p13	    ==>     First partition to add to the array
/dev/nvme1n1p14	    ==>     Second partition to add to the array

[root@ip-172-19-160-40 ~]# ls -l /dev/md0

    brw-rw----. 1 root disk 9, 0 Apr  1 10:46 /dev/md0

[root@ip-172-19-160-40 ~]# cat /proc/mdstat  [now we have a raid device md0]

    Personalities : [raid1]
    md0 : active raid1 nvme1n1p14[1] nvme1n1p13[0]
        96128 blocks super 1.2 [2/2] [UU]

    unused devices: <none>

[root@ip-172-19-160-40 ~]# lsmod | grep raid  [shows how many raid devices are in use. This command lists loaded kernel modules and filters for RAID-related ones:]

    raid1                  45056  1    

Now lets create a file system on our raid device md0

[root@ip-172-19-160-40 ~]# mkfs.xfs /dev/md0  [creates an xfs file system on our raid device md0]

meta-data=/dev/md0               isize=512    agcount=4, agsize=6008 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=0 inobtcount=0
data     =                       bsize=4096   blocks=24032, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=1368, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0

[root@ip-172-19-160-40 ~]# mdadm --detail --scan  [returns details of raid device md0]

    ARRAY /dev/md0 metadata=1.2 name=0 UUID=ef0b908e:1a8e3f98:59152bbf:2c374d33

[root@ip-172-19-160-40 ~]# mdadm --detail --scan >> /etc/mdadm.conf  [important to write the  metadata of a raid device to mdadm.conf]

[root@ip-172-19-160-40 ~]# mdadm --stop /dev/md0  [stops the raid device]

    mdadm: stopped /dev/md0

[root@ip-172-19-160-40 ~]# mdadm --assemble --scan [On system reboot, RAID arrays don't automatically activate. This command ensures that raid devices are aujtomatically setup on boot using the mdadm.conf file]

    mdadm: /dev/md0 has been started with 2 drives.