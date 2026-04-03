<!-- Creating Linux file systems -->

# Formatting EXT4 File Systems

**NB** I created an AWS EC2 instance. Then created a 20G EBS volume and attached it to the instance for this sections. Then ran the parted.sh script from previous module to create the required partions

[root@ip-172-19-160-40 ~]# lsblk

    NAME                   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
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
    └─nvme0n1p128          259:3    0    1M  0 part
    nvme2n1                259:4    0   20G  0 disk         ====> the attached ebs volume with partition created by parted.sh
    ├─nvme2n1p9            259:5    0   95M  0 part
    ├─nvme2n1p10           259:6    0   94M  0 part
    ├─nvme2n1p11           259:7    0   95M  0 part
    ├─nvme2n1p12           259:8    0   94M  0 part
    ├─nvme2n1p13           259:9    0   94M  0 part
    ├─nvme2n1p1            259:10   0  512B  0 part
    ├─nvme2n1p5            259:11   0   93M  0 part
    ├─nvme2n1p6            259:12   0   95M  0 part
    ├─nvme2n1p7            259:13   0   94M  0 part
    ├─nvme2n1p8            259:14   0   94M  0 part
    └─nvme2n1p14           259:15   0   95M  0 part

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

[root@ip-172-19-160-40 ~]# mkfs.    [ Double tab to see all options avalaible ]

    mkfs.cramfs  mkfs.ext2    mkfs.ext3    mkfs.ext4    mkfs.fat     mkfs.minix   mkfs.msdos   mkfs.vfat    mkfs.xfs


L = LABEL that you add to identify the filesystems as shown on 2nd line of o/p. The labels can be changed after they are created using 'tune2fs'.
Labels can be anything you want and is optional

[root@ip-172-19-160-40 ~]# mkfs.ext4 -L DATA /dev/nvme2n1p6 [creating an ext4 filesystem on the 6th partition(/dev/nvme2n1p6)]

    mke2fs 1.42.9 (28-Dec-2013)
    Filesystem label=DATA
    OS type: Linux
    Block size=1024 (log=0)
    Fragment size=1024 (log=0)
    Stride=0 blocks, Stripe width=0 blocks
    24384 inodes, 97280 blocks
    4864 blocks (5.00%) reserved for the super user
    First data block=1
    Maximum filesystem blocks=33685504
    12 block groups
    8192 blocks per group, 8192 fragments per group
    2032 inodes per group
    Superblock backups stored on blocks:
            8193, 24577, 40961, 57345, 73729

    Allocating group tables: done
    Writing inode tables: done
    Creating journal (4096 blocks): done
    Writing superblocks and filesystem accounting information: done

The above command can also be written as: "mkfs -t ext4 -L DATA /dev/nvme2n1p6"

[root@ip-172-19-160-40 ~]# tune2fs -L MYDATA -c 0 -i 0 /dev/nvme2n1p6 [changes label from DATA to MYDATA. The -c and -i flags are optional]

    tune2fs 1.42.9 (28-Dec-2013)
    Setting maximal mount count to -1
    Setting interval between checks to 0 seconds

[root@ip-172-19-160-40 ~]# dumpe2fs /dev/nvme2n1p6 | less  [returns filesytems metadat and we can verify our changes]

    Filesystem volume name:   MYDATA                                ====> new label is MYDATA now instead of DATA
    Last mounted on:          <not available>
    Filesystem UUID:          abf332d0-8dbd-4516-b9b0-b7ba56f80965
    ...
    ...
    Mount count:              0                                     =====> mount count set by -c
    Maximum mount count:      -1
    Last checked:             Mon Mar 30 12:28:40 2026
    Check interval:           0 (<none>)                            ====> check internal set by -i
    ...
    ...

<!-- Mounting and unmounting the EXT4 filesystems -->

**NB not that the 'mount' command references '/proc/mounts'. You can run 'cat /proc/mounts' to see all system mounts

[root@ip-172-19-160-40 ~]# cat /proc/mounts

    sysfs /sys sysfs rw,seclabel,nosuid,nodev,noexec,relatime 0 0
    ...
    ...
    tmpfs /run/user/0 tmpfs rw,seclabel,nosuid,nodev,relatime,size=96272k,mode=700 0 0

[root@ip-172-19-160-40 ~]# mount /dev/nvme2n1p6 /mnt/   [mounts the nvme2n1p6 to /mnt]

[root@ip-172-19-160-40 ~]# ls /mnt/  [only has the lost and found folder that holds data after file system changes and they no longer have parent entries within the file system]

    lost+found

[root@ip-172-19-160-40 ~]# lsblk  [list block]

    NAME                   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    nvme1n1                259:0    0   35G  0 disk
    ...
    ...
    nvme2n1                259:4    0   20G  0 disk
    ├─nvme2n1p1            259:16   0    1K  0 part
    ├─nvme2n1p5            259:17   0   93M  0 part
    ├─nvme2n1p6            259:18   0   95M  0 part /mnt                ===> our new mount location        
    ...
    ...

    [root@ip-172-19-160-40 ~]# umount /mnt    [unmounts the volume from /mnt]
    [root@ip-172-19-160-40 ~]# lsblk
    NAME                   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    nvme1n1                259:0    0   35G  0 disk
    ...
    ...
    nvme2n1                259:4    0   20G  0 disk
    ├─nvme2n1p1            259:16   0    1K  0 part
    ├─nvme2n1p5            259:17   0   93M  0 part
    ├─nvme2n1p6            259:18   0   95M  0 part                    ===> mount location gone
    ...
    ...

[root@ip-172-19-160-40 ~]# mkdir -p /data/{mydata,data2} [create a folder called data as parent with two subfolders mydata and data2]

[root@ip-172-19-160-40 ~]# ls /data

    data2  mydata

[root@ip-172-19-160-40 ~]# mount /dev/nvme2n1p6 /data/mydata/   [mounts nvme2n1p6 to /data/mydata]

[root@ip-172-19-160-40 ~]# mount | grep mydata          [returns mount location and available options]

    /dev/nvme2n1p6 on /data/mydata type ext4 (rw,relatime,seclabel,data=ordered)

[root@ip-172-19-160-40 ~]# mount -o remount,noexec /dev/nvme2n1p6 /data/mydata/     [remounts nvme2n1p6 to /data/mydata but this time adding the oprion 'noexec'. Scripts/binaries can't be executed from here]

[root@ip-172-19-160-40 ~]# mount | grep mydata

    /dev/nvme2n1p6 on /data/mydata type ext4 (rw,noexec,relatime,seclabel,data=ordered)

[root@ip-172-19-160-40 ~]# umount /data/mydata/   [umounts the volume]

[root@ip-172-19-160-40 ~]# mount | grep mydata  [checks for where the mydata folder is mounted. Should now return nothing ]

[root@ip-172-19-160-40 ~]#



Note that all the above are not persisted yet. So, lets checkout how to mount our filesystems and persist the mount in /etc/fstab

[root@ip-172-19-160-40 ~]# blkid /dev/nvme2n1p6

    /dev/nvme2n1p6: LABEL="MYDATA" UUID="abf332d0-8dbd-4516-b9b0-b7ba56f80965" TYPE="ext4" PARTUUID="000e9320-06"

[root@ip-172-19-160-40 ~]# vi /etc/fstab    [add the below line to the end of the file, save and exit with :x. This mounts the volume nvme2n1p6 using its uuid and to /data/mydata]

    UUID="abf332d0-8dbd-4516-b9b0-b7ba56f80965"   /data/mydata          ext4    noexec 0 2

Note that you can use 'default' in place of the options  'noexec' above. You can set multiple options as comma-delimited values, e.g: noexec,noatime,uquota    

[root@ip-172-19-160-40 ~]# mount -a    [mount all filesystems mentioned in fstab. if the command returns no error you are good to go]

[root@ip-172-19-160-40 ~]# mount | grep mydata [returns volume name and mount location]

    /dev/nvme2n1p6 on /data/mydata type ext4 (rw,noexec,relatime,seclabel,data=ordered)


# Formatting XFS File Systems. Used by many enterprise systems

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

[root@ip-172-19-160-40 ~]# mkfs. [double tab to see all available file systems]

    mkfs.cramfs  mkfs.ext3    mkfs.fat     mkfs.msdos   mkfs.xfs mkfs.ext2    mkfs.ext4    mkfs.minix   mkfs.vfat

creates an xfs file system with block size 1k (-b size=1k) and log location in the same volume/partition and (-l). Note that you can pass a different location for the log if you want, like '-l /dev/sda1' for example or any other volume/partition you desire

[root@ip-172-19-160-40 ~]# mkfs.xfs -b size=1k -l size=10m /dev/nvme2n1p7 

    meta-data=/dev/nvme2n1p7        isize=512    agcount=4, agsize=24064 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=1, sparse=1, rmapbt=0
            =                       reflink=1    bigtime=0 inobtcount=0
    data    =                       bsize=1024   blocks=96256, imaxpct=25
            =                       sunit=0      swidth=0 blks
    naming  =version 2              bsize=4096   ascii-ci=0, ftype=1
    log     =internal log           bsize=1024   blocks=10240, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
    realtime =none                   extsz=4096   blocks=0, rtextents=0

[root@ip-172-19-160-40 ~]# xfs_     [enter and double tab to see available metadata options]

    xfs_admin      xfs_db         xfs_fsr        xfs_io         xfs_metadump   xfs_quota      xfs_spaceman
    xfs_bmap       xfs_estimate   xfs_growfs     xfs_logprint   xfs_mkfile     xfs_repair
    xfs_copy       xfs_freeze     xfs_info       xfs_mdrestore  xfs_ncheck     xfs_rtcp

[root@ip-172-19-160-40 ~]# xfs_db -x /dev/nvme2n1p7 [command to see file system metadata. -x = expert mode. Starts a terminal to run xfs commands]

xfs_db> help                                            ===> displays all available command
ablock filoff -- set address to file offset (attr fork)
addr [field-expression] -- set current address
...
... 
stack -- view the location stack
timelimit [--classic|--bigtime] [--pretty] -- display timestamp limits
type [newtype] -- set/show current data type
uuid [uuid] -- write/print FS uuid
version [feature | [vnum fnum]] -- set feature bit(s) in the sb version field
write [-c|-d] [field or value]... -- write value to disk

Use 'help commandname' for extended help.

xfs_db> uuid                                            ===> display file system uuid  
UUID = 78265d5d-be6f-4517-a02f-ee9126332761
xfs_db> label                                           ===> returns label of file system which is not set yet
label = ""
xfs_db> label DATA2                                     ===> set file system label
writing all SBs
new label = "DATA2"
xfs_db> label                                           ===> returns label of file system which is now set  
label = "DATA2"
xfs_db> quit                                            ===> exits the xfs terminal


<!-- Mounting the XFS filesystems -->

**NB not that the 'mount' command references '/proc/mounts'. You can run 'cat /proc/mounts' to see all system mounts

[root@ip-172-19-160-40 ~]# cat /proc/mounts

    sysfs /sys sysfs rw,seclabel,nosuid,nodev,noexec,relatime 0 0
    ...
    ...
    tmpfs /run/user/0 tmpfs rw,seclabel,nosuid,nodev,relatime,size=96272k,mode=700 0 0

[root@ip-172-19-160-40 ~]# blkid /dev/nvme2n1p7

    /dev/nvme2n1p7: LABEL="DATA2" UUID="78265d5d-be6f-4517-a02f-ee9126332761" TYPE="xfs" PARTUUID="000e9320-07"

[root@ip-172-19-160-40 ~]# vi /etc/fstab    [add the below line to the end of the file, save and exit with :x. This mounts the volume nvme2n1p7 using its uuid and to /data/data2]

    UUID="78265d5d-be6f-4517-a02f-ee9126332761"   /data/data2           xfs     defaults 0 0

Note that you can use 'default' like above for options or explicitly set the options as comma-delimited values, e.g: noexec,noatime,uquota

[root@ip-172-19-160-40 ~]# mount -a    [mount all filesystems mentioned in fstab. if the command returns no error you are good to go]

[root@ip-172-19-160-40 ~]# mount | grep mydata [returns volume name and mount location]

    /dev/nvme2n1p7 on /data/data2 type xfs (rw,relatime,seclabel,attr2,inode64,noquota)

[root@ip-172-19-160-40 ~]# xfs_i [type and double tab to see options]
xfs_info  xfs_io

[root@ip-172-19-160-40 ~]# xfs_info /dev/nvme2n1p7  [shows volume metadata. Below are those we set at volume creation time]

    meta-data=/dev/nvme2n1p7         isize=512    agcount=4, agsize=24064 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=1, sparse=1, rmapbt=0
            =                       reflink=1    bigtime=0 inobtcount=0
    data     =                       bsize=1024   blocks=96256, imaxpct=25         =                       sunit=0      swidth=0 blks
    naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
    log      =internal log           bsize=1024   blocks=10240, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
    realtime =none                   extsz=4096   blocks=0, rtextents=0    

[root@ip-172-19-160-40 ~]# mount | grep data

    ....
    ....
    /dev/nvme2n1p6 on /data/mydata type ext4 (rw,noexec,relatime,seclabel,data=ordered)
    /dev/nvme2n1p7 on /data/data2 type xfs (rw,relatime,seclabel,attr2,inode64,noquota)


**** Go to 06-user-group-quota to learn how to set set quota on filesystems ****