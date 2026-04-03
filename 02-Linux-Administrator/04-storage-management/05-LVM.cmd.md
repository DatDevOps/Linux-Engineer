<!-- Logical Volumes -->

# Creating LVM
LVM stands for Logical Volume Manager.
It’s a storage management system used on Linux that lets you combine, resize, and manage disk space flexibly, without being locked into fixed disk partitions.

[root@ip-172-19-160-40 ~]# pvscan

    PV /dev/sdb   VG datavg          lvm2 [<35.00 GiB / <6.11 GiB free]
    Total: 1 [<35.00 GiB] / in use: 1 [<35.00 GiB] / in no VG: 0 [0   ]

[root@ip-172-19-160-40 ~]# vgscan   [returns all logical volume group.]

    Reading volume groups from cache.
    Found volume group "datavg" using metadata type lvm2

[root@ip-172-19-160-40 ~]# lvscan   [returns all logical volume.]

    ACTIVE            '/dev/datavg/lvvar' [6.00 GiB] inherit
    ACTIVE            '/dev/datavg/lvvarlog' [4.00 GiB] inherit
    ACTIVE            '/dev/datavg/lvvarlogaudit' [400.00 MiB] inherit
    ACTIVE            '/dev/datavg/lvhome' [4.50 GiB] inherit
    ACTIVE            '/dev/datavg/lvtmp' [3.00 GiB] inherit
    ACTIVE            '/dev/datavg/lvopt' [7.00 GiB] inherit
    ACTIVE            '/dev/datavg/lvswap' [4.00 GiB] inherit

[root@ip-172-19-160-40 ~]# fdisk -l /dev/nvme1n1

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

[root@ip-172-19-160-40 ~]# pvcreate /dev/nvme1n1p10     [creates an LVM physical volume on a specific block device/partition,/dev/nvme1n1p10. Any filesystem or data previously on /dev/nvme1n1p10 will be lost.]

    Physical volume "/dev/nvme1n1p10" successfully created.

[root@ip-172-19-160-40 ~]# pvcreate /dev/nvme1n1p11     [creates an LVM physical volume on a specific block device/partition,/dev/nvme1n1p10. Any filesystem or data previously on /dev/nvme1n1p11 will be lost.]

    Physical volume "/dev/nvme1n1p11" successfully created.

[root@ip-172-19-160-40 ~]# pvcreate /dev/nvme1n1p12     [creates an LVM physical volume on a specific block device/partition,/dev/nvme1n1p10. Any filesystem or data previously on /dev/nvme1n1p12 will be lost.]

    Physical volume "/dev/nvme1n1p12" successfully created.

[root@ip-172-19-160-40 ~]# vgcreate vg1 /dev/nvme1n1p10 /dev/nvme1n1p11 [creates an LVM Volume Group using two physical volumes. Pools the storage from both devices into a single LVM storage group]

    Volume group "vg1" successfully created

[root@ip-172-19-160-40 ~]# vgscan  [returns all logical volume group. note our new group, vg1]

    Reading volume groups from cache.
    Found volume group "vg1" using metadata type lvm2
    Found volume group "datavg" using metadata type lvm2

[root@ip-172-19-160-40 ~]# pvs
  PV         VG     Fmt  Attr PSize   PFree
  /dev/sdb   datavg lvm2 a--  <35.00g  <6.11g
  /dev/sdc10 vg1    lvm2 a--   92.00m      0
  /dev/sdc11 vg1    lvm2 a--   92.00m      0


[root@ip-172-19-160-40 ~]# vgs    [Lists all LVM Volume Groups (VGs) on the system and shows summary-level information (size, free space, number of LVs/PVs). Notethat vg1 has the combined space size of nvme1n1p10 and nvme1n1p11]

    VG     #PV #LV #SN Attr   VSize   VFree
    datavg   1   7   0 wz--n- <35.00g  <6.11g
    vg1      2   0   0 wz--n- 184.00m 184.00m           <===

✅ How to Read Each Column

Column*************Meaning
VG       ==>       Volume Group name (vg1)
#PV      ==>       Number of Physical Volumes (e.g. /dev/nvme1n1p10, /dev/nvme1n1p11)
#LV      ==>       Number of Logical Volumes
#SN      ==>       Number of snapshots
Attr     ==>       VG attributes (normal if starts with w)
VSize    ==>       Total size of the VG
VFree    ==>       Free space available to create LVs  


[root@ip-172-19-160-40 ~]# lsblk [note o/p]


# Creating Logical Volumes, LV

[root@ip-172-19-160-40 ~]# lvcreate -n lv1 -L 184m vg1

    Logical volume "lv1" created.

The above is an LVM (Logical Volume Manager) command that creates a new logical volume:

    lvcreate — Creates a new logical volume.
    -n lv1 — Names the logical volume lv1.
    -L 184m — Sets its size to 184 MiB.
    vg1 — Creates it within the volume group vg1.

[root@ip-172-19-160-40 ~]# lvscan  [lvscan is an LVM command that scans all disks for logical volumes and displays their status. ]

    ACTIVE            '/dev/vg1/lv1' [184.00 MiB] inherit
    ACTIVE            '/dev/datavg/lvvar' [6.00 GiB] inherit
    ACTIVE            '/dev/datavg/lvvarlog' [4.00 GiB] inherit
    ACTIVE            '/dev/datavg/lvvarlogaudit' [400.00 MiB] inherit
    ACTIVE            '/dev/datavg/lvhome' [4.50 GiB] inherit
    ACTIVE            '/dev/datavg/lvtmp' [3.00 GiB] inherit
    ACTIVE            '/dev/datavg/lvopt' [7.00 GiB] inherit
    ACTIVE            '/dev/datavg/lvswap' [4.00 GiB] inherit

[root@ip-172-19-160-40 ~]# mkfs.xfs /dev/vg1/lv1    [creates a file system on our logical volume]

    meta-data=/dev/vg1/lv1           isize=512    agcount=4, agsize=11776 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=1, sparse=1, rmapbt=0
            =                       reflink=1    bigtime=0 inobtcount=0
    data     =                       bsize=4096   blocks=47104, imaxpct=25
            =                       sunit=0      swidth=0 blks
    naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
    log      =internal log           bsize=4096   blocks=1368, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
    realtime =none                   extsz=4096   blocks=0, rtextents=0


[root@ip-172-19-160-40 ~]# mkdir /lvm [we are creating a mount directory for our LV]

[root@ip-172-19-160-40 ~]# vi /etc/fstab  [open and edit file by appending the below  to the end of the file. Our LV /dev/vg1/lv1 is mounted on /lvm of filesystem type of xfs and default settings/attributes]

    /dev/vg1/lv1 /lvm xfs defaults 0 0

[root@ip-172-19-160-40 ~]# mount -a  [auto mount our new file system, i.e all the stuff in /etc/fstab/. Should return nothing or no error]

[root@ip-172-19-160-40 ~]# mount | grep lv1  [shows our LV is among mounted devices]

    /dev/mapper/vg1-lv1 on /lvm type xfs (rw,relatime,seclabel,attr2,inode64,noquota)

[root@ip-172-19-160-40 ~]# find /usr/share/doc/ -name '*.pdf' -exec cp {} /lvm/ \;

[root@ip-172-19-160-40 ~]# ls -alh /lvm/   [now contains copied files]

    total 464K
    drwxr-xr-x.  2 root root   61 Apr  2 08:03 .
    dr-xr-xr-x. 20 root root  275 Apr  2 07:51 ..
    -rw-r--r--.  1 root root 254K Apr  2 08:03 libtasn1.pdf
    -rw-r--r--.  1 root root  44K Apr  2 08:03 logo.pdf
    -rw-r--r--.  1 root root 162K Apr  2 08:03 pycrypt.pdf

[root@ip-172-19-160-40 ~]# df -h  [/lvm now contains space less the combined copied files size]

    Filesystem                        Size  Used Avail Use% Mounted on
    devtmpfs                          460M     0  460M   0% /dev
    tmpfs                             471M     0  471M   0% /dev/shm
    tmpfs                             471M  596K  470M   1% /run
    tmpfs                             471M     0  471M   0% /sys/fs/cgroup
    /dev/nvme0n1p1                     10G  2.7G  7.4G  27% /
    /dev/nvme1n1p7                     84M  7.3M   77M   9% /data/data2
    /dev/nvme1n1p6                     87M   14K   80M   1% /data/mydata
    /dev/mapper/datavg-lvopt          7.0G  2.0G  5.1G  28% /opt
    /dev/mapper/datavg-lvhome         4.5G   67M  4.5G   2% /home
    /dev/mapper/datavg-lvtmp          3.0G   54M  3.0G   2% /tmp
    /dev/mapper/datavg-lvvar          6.0G  759M  5.3G  13% /var
    /dev/mapper/datavg-lvvarlog       4.0G  468M  3.6G  12% /var/log
    /dev/mapper/datavg-lvvarlogaudit  395M   64M  331M  17% /var/log/audit
    tmpfs                              95M     0   95M   0% /run/user/0
    /dev/mapper/vg1-lv1               179M   12M  168M   7% /lvm            <=====


[root@ip-172-19-160-40 ~]# lsblk [note o/p]



# Resizing LV Dynamically or on the fly

[root@ip-172-19-160-40 ~]# df -h /lvm

Filesystem           Size  Used Avail Use% Mounted on
    /dev/mapper/vg1-lv1  179M   12M  168M   7% /lvm

[root@ip-172-19-160-40 ~]# pvscan

    PV /dev/sdc10   VG vg1             lvm2 [92.00 MiB / 0    free]
    PV /dev/sdc11   VG vg1             lvm2 [92.00 MiB / 0    free]
    PV /dev/sdb     VG datavg          lvm2 [<35.00 GiB / <6.11 GiB free]
    PV /dev/sdc12                      lvm2 [94.00 MiB]
    Total: 4 [<35.27 GiB] / in use: 3 [<35.18 GiB] / in no VG: 1 [94.00 MiB]

[root@ip-172-19-160-40 ~]# vgextend vg1 /dev/sdc12

    Volume group "vg1" successfully extended

[root@ip-172-19-160-40 ~]# pvs      [/dev/sdc12, represented by /dev/nvme1n1p12, now added to vg1]

    PV         VG     Fmt  Attr PSize   PFree
    /dev/sdb   datavg lvm2 a--  <35.00g <6.11g
    /dev/sdc10 vg1    lvm2 a--   92.00m     0
    /dev/sdc11 vg1    lvm2 a--   92.00m     0
    /dev/sdc12 vg1    lvm2 a--   92.00m 40.00m

[root@ip-172-19-160-40 ~]# vgs      [Note that the size of vg1 has increased by the added size of /dev/sdc12 or /dev/nvme1n1p12]
    VG     #PV #LV #SN Attr   VSize   VFree
    datavg   1   7   0 wz--n- <35.00g <6.11g
    vg1      3   1   0 wz--n- 276.00m 40.00m

[root@ip-172-19-160-40 ~]# lvextend -L +50 /dev/vg1/lv1     [adding some new space, not all the space added to vg1, to increate lv1 size. Saving the left over space for LVM snapshots of our logical volumes]

    Rounding size to boundary between physical extents: 52.00 MiB.
    Size of logical volume vg1/lv1 changed from 184.00 MiB (46 extents) to 236.00 MiB (59 extents).
    Logical volume vg1/lv1 successfully resized.

[root@ip-172-19-160-40 ~]# df -h /lvm  [note that the size of our filesystem on /lvm is still 168m and has not increase despite adding more space to LV from vg1/lv1]

    Filesystem           Size  Used Avail Use% Mounted on
    /dev/mapper/vg1-lv1  179M   12M  168M   7% /lvm

[root@ip-172-19-160-40 ~]# xfs_growfs /lvm      [extending the file system to include the new available space on vg1/lv1]

    meta-data=/dev/mapper/vg1-lv1    isize=512    agcount=4, agsize=11776 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=1, sparse=1, rmapbt=0
            =                       reflink=1    bigtime=0 inobtcount=0
    data     =                       bsize=4096   blocks=47104, imaxpct=25
            =                       sunit=0      swidth=0 blks
    naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
    log      =internal log           bsize=4096   blocks=1368, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
    realtime =none                   extsz=4096   blocks=0, rtextents=0
    data blocks changed from 47104 to 60416

[root@ip-172-19-160-40 ~]# df -h /lvm   [note that /lvm now has approximately the extra 50M added to lv1, 168 + 50 = 218]

    Filesystem           Size  Used Avail Use% Mounted on
    /dev/mapper/vg1-lv1  231M   12M  219M   6% /lvm


So we have increase the size of LV1 and its filesystem space while volume was still live and have not lost our data as shown below:

[root@ip-172-19-160-40 ~]# ls -alh /lvm/   [still contains copied files]

    total 464K
    drwxr-xr-x.  2 root root   61 Apr  2 08:03 .
    dr-xr-xr-x. 20 root root  275 Apr  2 07:51 ..
    -rw-r--r--.  1 root root 254K Apr  2 08:03 libtasn1.pdf
    -rw-r--r--.  1 root root  44K Apr  2 08:03 logo.pdf
    -rw-r--r--.  1 root root 162K Apr  2 08:03 pycrypt.pdf

# LVM Snapshots

[root@ip-172-19-160-40 ~]# lsblk [note o/p]

[root@ip-172-19-160-40 ~]# pvs  [shows lv's and their respective volume group. In this case vg1]

    PV         VG     Fmt  Attr PSize   PFree
    /dev/sdb   datavg lvm2 a--  <35.00g <6.11g
    /dev/sdc10 vg1    lvm2 a--   92.00m     0
    /dev/sdc11 vg1    lvm2 a--   92.00m     0
    /dev/sdc12 vg1    lvm2 a--   92.00m 40.00m

[root@ip-172-19-160-40 ~]# vgs [note the free space in vg1. That is what we will use for backup]

    VG     #PV #LV #SN Attr   VSize   VFree
    datavg   1   7   0 wz--n- <35.00g <6.11g
    vg1      3   1   0 wz--n- 276.00m 40.00m

[root@ip-172-19-160-40 ~]# lvcreate -L 30m -s -n backup /dev/vg1/lv1   [It creates a point-in-time copy of /dev/vg1/lv1 called backup (available at /dev/vg1/backup). The snapshot uses a copy-on-write (COW) mechanism ]

    Rounding up size to full physical extent 32.00 MiB
    Logical volume "backup" created.


The above command creates an LVM snapshot of an existing logical volume:

    lvcreate — Creates a new logical volume
    -L 30m — Allocates 30 MiB for the snapshot
    -s — Specifies this is a snapshot (not a regular logical volume)
    -n backup — Names the snapshot backup
    /dev/vg1/lv1 — The origin logical volume being snapshotted   

[root@ip-172-19-160-40 ~]# lvs

    LV            VG     Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
    lvhome        datavg -wi-ao----   4.50g
    lvopt         datavg -wi-ao----   7.00g
    lvswap        datavg -wi-ao----   4.00g
    lvtmp         datavg -wi-ao----   3.00g
    lvvar         datavg -wi-ao----   6.00g
    lvvarlog      datavg -wi-ao----   4.00g
    lvvarlogaudit datavg -wi-ao---- 400.00m
    backup        vg1    swi-aos---  32.00m      lv1    6.42                                            <==== our new LV
    lv1           vg1    owi-aos--- 236.00m

[root@ip-172-19-160-40 ~]# mount /dev/vg1/backup /mnt -o nouuid,ro  [mounts the backup lv to /mnt with 'no uuid and read only' attributes]

[root@ip-172-19-160-40 ~]# ls -lh /lvm/  [shows lv1 contents in mounted directories]

    total 464K
    -rw-r--r--. 1 root root 254K Apr  2 08:03 libtasn1.pdf
    -rw-r--r--. 1 root root  44K Apr  2 08:03 logo.pdf
    -rw-r--r--. 1 root root 162K Apr  2 08:03 pycrypt.pdf

[root@ip-172-19-160-40 ~]# ls -lh /mnt/  [backup directory now has content of /lvm]

    total 464K
    -rw-r--r--. 1 root root 254K Apr  2 08:03 libtasn1.pdf
    -rw-r--r--. 1 root root  44K Apr  2 08:03 logo.pdf
    -rw-r--r--. 1 root root 162K Apr  2 08:03 pycrypt.pdf

[root@ip-172-19-160-40 ~]# rm /lvm/logo.pdf  [deletes file from /lvm]

    rm: remove regular file ‘/lvm/logo.pdf’? y

[root@ip-172-19-160-40 ~]# ls -lh /lvm/  [file no longer exist in /lvm]

    total 420K
    -rw-r--r--. 1 root root 254K Apr  2 08:03 libtasn1.pdf
    -rw-r--r--. 1 root root 162K Apr  2 08:03 pycrypt.pdf

[root@ip-172-19-160-40 ~]# ls -lh /mnt/  [file still exist in backup and can be restored if needed]

    total 464K
    -rw-r--r--. 1 root root 254K Apr  2 08:03 libtasn1.pdf
    -rw-r--r--. 1 root root  44K Apr  2 08:03 logo.pdf
    -rw-r--r--. 1 root root 162K Apr  2 08:03 pycrypt.pdf

[root@ip-172-19-160-40 ~]# tar -cf /root/backup.tar /mnt  [zips out backup files in /mnt to /root/backup.tar]

[root@ip-172-19-160-40 ~]# umount /mnt  [umounts our backup directory /mnt]


[root@ip-172-19-160-40 ~]# lvremove /dev/vg1/backup  [removes our 'backup' lv. The original volume /dev/vg1/lv1 is not affected. This frees 30 MiB of the snapshot back to the volume group vg1. use -f to skip confirmation]

    Do you really want to remove active logical volume vg1/backup? [y/n]: y
    Logical volume "backup" successfully removed

[root@ip-172-19-160-40 ~]# lvs   [lv 'backup' removed from vg1]  

    LV            VG     Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
    lvhome        datavg -wi-ao----   4.50g
    lvopt         datavg -wi-ao----   7.00g
    lvswap        datavg -wi-ao----   4.00g
    lvtmp         datavg -wi-ao----   3.00g
    lvvar         datavg -wi-ao----   6.00g
    lvvarlog      datavg -wi-ao----   4.00g
    lvvarlogaudit datavg -wi-ao---- 400.00m
    lv1           vg1    -wi-ao---- 236.00m

# Migrating a Physical Volume from one disk  to another

Here we will create a new partition LVM volume, add it to vg1,  and remove /dev/nvme1n1p10/, also known as /dev/sdc10, from vg1

[root@ip-172-19-160-40 ~]# lsblk

    NAME                   MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
    nvme1n1                259:0    0   20G  0 disk
    ├─nvme1n1p1            259:2    0    1K  0 part
    ├─nvme1n1p5            259:3    0   93M  0 part  [SWAP]
    ├─nvme1n1p6            259:4    0   95M  0 part  /data/mydata
    ├─nvme1n1p7            259:5    0   94M  0 part  /data/data2
    ├─nvme1n1p8            259:6    0   94M  0 part
    ├─nvme1n1p9            259:7    0   95M  0 part
    ├─nvme1n1p10           259:8    0   94M  0 part                     <=== note this partition because we will remove it later from vg1
    │ └─vg1-lv1            253:7    0  236M  0 lvm   /lvm
    ├─nvme1n1p11           259:9    0   95M  0 part
    │ └─vg1-lv1            253:7    0  236M  0 lvm   /lvm
    ├─nvme1n1p12           259:10   0   94M  0 part
    │ └─vg1-lv1            253:7    0  236M  0 lvm   /lvm
    ├─nvme1n1p13           259:11   0   94M  0 part
    │ └─md0                  9:0    0 93.9M  0 raid1
    └─nvme1n1p14           259:12   0   95M  0 part
    └─md0                  9:0    0 93.9M  0 raid1
    nvme2n1                259:1    0   35G  0 disk
    ├─datavg-lvvar         253:0    0    6G  0 lvm   /var
    ├─datavg-lvvarlog      253:1    0    4G  0 lvm   /var/log
    ├─datavg-lvvarlogaudit 253:2    0  400M  0 lvm   /var/log/audit
    ├─datavg-lvhome        253:3    0  4.5G  0 lvm   /home
    ├─datavg-lvtmp         253:4    0    3G  0 lvm   /tmp
    ├─datavg-lvopt         253:5    0    7G  0 lvm   /opt
    └─datavg-lvswap        253:6    0    4G  0 lvm   [SWAP]
    nvme0n1                259:13   0   10G  0 disk
    ├─nvme0n1p1            259:14   0   10G  0 part  /
    └─nvme0n1p128          259:15   0    1M  0 part

[root@ip-172-19-160-40 ~]# pvs 

    PV         VG     Fmt  Attr PSize   PFree
    /dev/sdb   datavg lvm2 a--  <35.00g <6.11g
    /dev/sdc10 vg1    lvm2 a--   92.00m     0
    /dev/sdc11 vg1    lvm2 a--   92.00m     0
    /dev/sdc12 vg1    lvm2 a--   92.00m 40.00m

   O   dump disk layout to sfdisk script file


[root@ip-172-19-160-40 ~]# fdisk /dev/nvme1n1  [creates new partition nvme1n1p15]

    Welcome to fdisk (util-linux 2.30.2).
    Changes will remain in memory only, until you decide to write them.
    Be careful before using the write command.


    Command (m for help): n                                                             ===> starts creation of new partitions
    All space for primary partitions is in use.
    Adding logical partition 15
    First sector (1955840-41940991, default 1955840):
    Last sector, +sectors or +size{K,M,G,T,P} (1955840-41940991, default 41940991): +300M          ===> partition size

    Created a new partition 15 of type 'Linux' and of size 300 MiB.

    Command (m for help): p                                                             ===> prints partition
    Disk /dev/nvme1n1: 20 GiB, 21474836480 bytes, 41943040 sectors
    Units: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disklabel type: dos
    Disk identifier: 0x000e9320

    Device          Boot   Start      End  Sectors  Size Id Type
    /dev/nvme1n1p1          2048 41940991 41938944   20G  f W95 Ext'd (LBA)
    /dev/nvme1n1p5          4096   194559   190464   93M 82 Linux swap / Solaris
    /dev/nvme1n1p6        196608   391167   194560   95M 83 Linux
    /dev/nvme1n1p7        393216   585727   192512   94M 83 Linux
    /dev/nvme1n1p8        587776   780287   192512   94M 83 Linux
    /dev/nvme1n1p9        782336   976895   194560   95M 83 Linux
    /dev/nvme1n1p10       978944  1171455   192512   94M 8e Linux LVM
    /dev/nvme1n1p11      1173504  1368063   194560   95M 8e Linux LVM
    /dev/nvme1n1p12      1370112  1562623   192512   94M 8e Linux LVM
    /dev/nvme1n1p13      1564672  1757183   192512   94M fd Linux raid autodetect
    /dev/nvme1n1p14      1759232  1953791   194560   95M fd Linux raid autodetect
    /dev/nvme1n1p15      1955840  2570239   614400  300M 83 Linux

    Command (m for help): t                                                             ===> option to change partition id
    Partition number (1,5-15, default 15):                                              ===> accepts the default by pressing enter because it is partition 15 ID we want to change. Otherwise enter partition number
    Hex code (type L to list all codes): 8e

    Changed type of partition 'Linux' to 'Linux LVM'.

    Command (m for help): p                                                             ===> prints partitions
    Disk /dev/nvme1n1: 20 GiB, 21474836480 bytes, 41943040 sectors
    Units: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disklabel type: dos
    Disk identifier: 0x000e9320

    Device          Boot   Start      End  Sectors  Size Id Type
    /dev/nvme1n1p1          2048 41940991 41938944   20G  f W95 Ext'd (LBA)
    /dev/nvme1n1p5          4096   194559   190464   93M 82 Linux swap / Solaris
    /dev/nvme1n1p6        196608   391167   194560   95M 83 Linux
    /dev/nvme1n1p7        393216   585727   192512   94M 83 Linux
    /dev/nvme1n1p8        587776   780287   192512   94M 83 Linux
    /dev/nvme1n1p9        782336   976895   194560   95M 83 Linux
    /dev/nvme1n1p10       978944  1171455   192512   94M 8e Linux LVM
    /dev/nvme1n1p11      1173504  1368063   194560   95M 8e Linux LVM
    /dev/nvme1n1p12      1370112  1562623   192512   94M 8e Linux LVM
    /dev/nvme1n1p13      1564672  1757183   192512   94M fd Linux raid autodetect
    /dev/nvme1n1p14      1759232  1953791   194560   95M fd Linux raid autodetect
    /dev/nvme1n1p15      1955840  2570239   614400  300M 8e Linux LVM

    Command (m for help): w                                                             ===> saves and exits
    The partition table has been altered.
    Calling ioctl() to re-read partition table.
    Re-reading the partition table failed.: Device or resource busy

    The kernel still uses the old table. The new table will be used at the next reboot or after you run partprobe(8) or kpartx(8).


[root@ip-172-19-160-40 ~]# fdisk -l /dev/nvme1n1

    Disk /dev/nvme1n1: 20 GiB, 21474836480 bytes, 41943040 sectorsUnits: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disklabel type: dos
    Disk identifier: 0x000e9320

    Device          Boot   Start      End  Sectors  Size Id Type
    /dev/nvme1n1p1          2048 41940991 41938944   20G  f W95 Ext'd (LBA)
    /dev/nvme1n1p5          4096   194559   190464   93M 82 Linux swap / Solaris
    /dev/nvme1n1p6        196608   391167   194560   95M 83 Linux
    /dev/nvme1n1p7        393216   585727   192512   94M 83 Linux
    /dev/nvme1n1p8        587776   780287   192512   94M 83 Linux
    /dev/nvme1n1p9        782336   976895   194560   95M 83 Linux
    /dev/nvme1n1p10       978944  1171455   192512   94M 8e Linux LVM
    /dev/nvme1n1p11      1173504  1368063   194560   95M 8e Linux LVM
    /dev/nvme1n1p12      1370112  1562623   192512   94M 8e Linux LVM
    /dev/nvme1n1p13      1564672  1757183   192512   94M fd Linux raid autodetect
    /dev/nvme1n1p14      1759232  1953791   194560   95M fd Linux raid autodetect
    /dev/nvme1n1p15      1955840  2570239   614400  300M 8e Linux LVM

[root@ip-172-19-160-40 ~]# pvcreate /dev/nvme1n1p15    [creates an LVM physical volume on a specific block device/partition,/dev/nvme1n1p15. Any filesystem or data previously on /dev/nvme1n1p10 will be lost.]

    Physical volume "/dev/nvme1n1p15" successfully created.

[root@ip-172-19-160-40 ~]# vgextend vg1 /dev/nvme1n1p15   [adds /dev/nvme1n1p15 to the vg1 logical volume group]

    Physical volume "/dev/nvme1n1p15" successfully created.
    Volume group "vg1" successfully extended

[root@ip-172-19-160-40 ~]# pvs

    PV         VG     Fmt  Attr PSize   PFree
    /dev/sdb   datavg lvm2 a--  <35.00g  <6.11g
    /dev/sdc10 vg1    lvm2 a--   92.00m      0
    /dev/sdc11 vg1    lvm2 a--   92.00m      0
    /dev/sdc12 vg1    lvm2 a--   92.00m  40.00m
    /dev/sdc15 vg1    lvm2 a--  296.00m 296.00m             ==> new lv add to vg1


The below command migrates all physical extents (data) from one physical volume to another within the same volume group:

    pvmove — Moves data between physical volumes
    /dev/sdc10 — Source PV (data is moved from here)
    /dev/sdc15 — Destination PV (data is moved to here)

[root@ip-172-19-160-40 ~]# pvmove /dev/sdc10 /dev/sdc15     [use -b flag if you not want terminal output]

    /dev/sdc10: Moved: 4.35%
    /dev/sdc10: Moved: 100.00%

[root@ip-172-19-160-40 ~]# vgreduce vg1 /dev/sdc10  [removes /dev/sdc10 or /dev/nvme1n1p10 from vg1 lv group]

    Removed "/dev/sdc10" from volume group "vg1"

[root@ip-172-19-160-40 ~]# pvs

    PV         VG     Fmt  Attr PSize   PFree
    /dev/sdb   datavg lvm2 a--  <35.00g  <6.11g
    /dev/sdc10        lvm2 ---   94.00m  94.00m     ==> not in vg1 anymore
    /dev/sdc11 vg1    lvm2 a--   92.00m      0
    /dev/sdc12 vg1    lvm2 a--   92.00m  40.00m
    /dev/sdc15 vg1    lvm2 a--  296.00m 204.00m   

[root@ip-172-19-160-40 ~]# lsblk

    NAME                   MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
    nvme1n1                259:0    0   20G  0 disk
    ├─nvme1n1p1            259:2    0    1K  0 part
    ├─nvme1n1p5            259:3    0   93M  0 part  [SWAP]
    ├─nvme1n1p6            259:4    0   95M  0 part  /data/mydata
    ├─nvme1n1p7            259:5    0   94M  0 part  /data/data2
    ├─nvme1n1p8            259:6    0   94M  0 part
    ├─nvme1n1p9            259:7    0   95M  0 part
    ├─nvme1n1p10           259:8    0   94M  0 part                 <== not in vg1 anymore
    ├─nvme1n1p11           259:9    0   95M  0 part
    │ └─vg1-lv1            253:0    0  236M  0 lvm   /lvm
    ├─nvme1n1p12           259:10   0   94M  0 part
    │ └─vg1-lv1            253:0    0  236M  0 lvm   /lvm
    ├─nvme1n1p13           259:11   0   94M  0 part
    │ └─md0                  9:0    0 93.9M  0 raid1
    ├─nvme1n1p14           259:12   0   95M  0 part
    │ └─md0                  9:0    0 93.9M  0 raid1
    └─nvme1n1p15           259:13   0  300M  0 part
    └─vg1-lv1            253:0    0  236M  0 lvm   /lvm
    nvme2n1                259:1    0   35G  0 disk
    ...
    ...

[root@ip-172-19-160-40 ~]# pvremove /dev/sdc10  [Wipes the PV metadata from a device from partition]

    Labels on physical volume "/dev/sdc10" successfully wiped.

[root@ip-172-19-160-40 ~]# pvs  [sdc10 not exist as a block in vg1]

  PV         VG     Fmt  Attr PSize   PFree
  /dev/sdb   datavg lvm2 a--  <35.00g  <6.11g
  /dev/sdc11 vg1    lvm2 a--   92.00m      0
  /dev/sdc12 vg1    lvm2 a--   92.00m  40.00m
  /dev/sdc15 vg1    lvm2 a--  296.00m 204.00m    

[root@ip-172-19-160-40 ~]# vgs  [vg1 now has a combined size of sdc11 to 15,  or nvme1n1p11 to 15]

    VG     #PV #LV #SN Attr   VSize   VFree
    datavg   1   7   0 wz--n- <35.00g  <6.11g
    vg1      3   1   0 wz--n- 480.00m 244.00m   
