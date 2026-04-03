<!-- USERS AND GROUPS QUOTAS -->

# creating user and group quotas on ext4 filesystems

[root@ip-172-19-160-40 ~]# mount | grep mydata

    /dev/nvme1n1p6 on /data/mydata type ext4 (rw,noexec,relatime,seclabel,data=ordered)

[root@ip-172-19-160-40 ~]# quot  [ type and double tab to retuns package options. If no 'quota' yum install]

    quot            quotacheck      quotaon         quotasync       quote_readline
    quota           quotaoff        quotastats      quote

[root@ip-172-19-160-40 ~]# rpm -qf $(which quota) 
    
    quota-4.01-17.amzn2.x86_64

Thie above command finds which RPM package provides the quota command:

    -> which quota              —> Resolves the full path of the quota binary (e.g., /usr/bin/quota)
    -> **$(...) **              —> Command substitution — passes that path to the outer command
    -> rpm -qf /usr/bin/quota   —> Queries which installed RPM package owns that file
    -> -q                       —> Query mode
    -> -f                       —> Find the package that owns a specific file

[root@ip-172-19-160-40 ~]# vi /etc/fstab  [add 'usrquota' attribute to filesystem mount location /data/usrquota. Note that you can also add group quota attribute using: grpquota]


    UUID="abf332d0-8dbd-4516-b9b0-b7ba56f80965"   /data/mydata          ext4    noexec 0 2  (old line)
                            ||
                            ||
                            \/
    UUID="abf332d0-8dbd-4516-b9b0-b7ba56f80965"   /data/mydata          ext4    noexec,usrquota 0 2  (new line. added usrquota)   

[root@ip-172-19-160-40 ~]# umount /data/mydata/   [unmounts file system after adding the user quota attribute, usrquota]

[root@ip-172-19-160-40 ~]# mount -a  [autjo mount file system back]

[root@ip-172-19-160-40 ~]# mount | grep mydata  [checking file system to see if the new attribute has been added. yes it does now as shown below]

    /dev/nvme1n1p6 on /data/mydata type ext4 (rw,noexec,relatime,seclabel,quota,usrquota,data=ordered)    
                    
[root@ip-172-19-160-40 ~]# ls -la /data/mydata/  [no quota database file in  filesystem yet]

    total 20
    drwxr-xr-x. 3 root root  1024 Apr  3 16:20 .
    drwxr-xr-x. 4 root root    33 Mar 30 15:57 ..
    drwx------. 2 root root 12288 Mar 30 12:28 lost+found

[root@ip-172-19-160-40 ~]# quotacheck -mau [creates quota database or file. Should return nothing]

Lookup 'man quota' to understand all the other options

-u, --user
        Only user quotas listed in /etc/mtab or on the filesystems specified are  to  be  checked.
        This is the default action.

-m, --no-remount
        Don't try to remount filesystem read-only.    

-u, --user
        Only user quotas listed in /etc/mtab or on the filesystems specified are  to  be  checked.

✅ You run quotacheck against the mounted filesystem directory, for example /home, /data, /data/mydata, / etc
That will mean not using the -a, all filesystem, option. Examples: 'sudo quotacheck -cug /data'

    What this means:

    -c → create quota files
    -u → user quotas
    -g → group quotas
    /data → the filesystem mount point

[root@ip-172-19-160-40 ~]# ls -la /data/mydata/   [quota database file in  filesystem yet]

    total 20
    drwxr-xr-x. 3 root root  1024 Apr  3 16:20 .
    drwxr-xr-x. 4 root root    33 Mar 30 15:57 ..
    -rw-------. 1 root root  6144 Apr  3 16:20 aquota.user        <==== users quota database/file
    drwx------. 2 root root 12288 Mar 30 12:28 lost+found           

[root@ip-172-19-160-40 ~]# quotaon /dev/nvme1n1p6  [set quota to 'on' on partition nvme1n1p6]         

[root@ip-172-19-160-40 ~]# repquota -uv /dev/nvme1n1p6  [returns user quota report on partition. For now only root user with not limit indicated by 0]

    *** Report for user quotas on device /dev/nvme1n1p6
    Block grace time: 7days; Inode grace time: 7days
                            Block limits                File limits
    User            used    soft    hard  grace    used  soft  hard  grace
    ----------------------------------------------------------------------
    root      --      13       0       0              2     0     0

    Statistics:
    Total blocks: 6
    Data blocks: 1
    Entries: 1
    Used average: 1.000000

[root@ip-172-19-160-40 ~]# ls /home/

    ssm-user ec2-user   

Now lets set ec2-user disk usage limits on the filesystem. Limits must be in blocks of 1000's
[root@ip-172-19-160-40 ~]# setquota -u ec2-user 20000 25000 0 0 /dev/nvme1n1p6   [set quota on ec2-user for soft and hard limits for block and files respectively. Returns nothing] 

Syntax and meaning below:

setquota -u <user> <soft-block> <hard-block> <soft-inode> <hard-inode> <filesystem>

Field               Value               Meaning
-u                  userquota           Applies to a user
ec2-user            user                The target user
20000               soft block limit    20,000 blocks
25000               hard block limit    25,000 blocks
0 0                 inode limits        No inode limits. 0 means no limit
/dev/nvme1n1p6      filesystem          Resolved to the mounted filesystem


[root@ip-172-19-160-40 ~]# repquota -uv /dev/nvme1n1p6  [returns user quota report on partition. In addition to root user we now have limit for ec2-user. Note that 'used' for ec2-users is 0 because it has not written to the filesystemr]

    *** Report for user quotas on device /dev/nvme1n1p6
    Block grace time: 7days; Inode grace time: 7days
                            Block limits                File limits
    User            used    soft    hard  grace    used  soft  hard  grace
    ----------------------------------------------------------------------
    root      --      13       0       0              2     0     0
    ec2-user  --       0   20000   25000              0     0     0

    Statistics:
    Total blocks: 7
    Data blocks: 1
    Entries: 2
    Used average: 2.000000

Another way to set quota is using 'edquota' command with vi editor 

[root@ip-172-19-160-40 ~]# edquota -u ec2-user /dev/nvme1n1p6  [opens vi editor to edit soft/hard for block/file quota for ssm-user on the filesystem. This is an alternative to using setquota command. I entered teh soft and hard limits as shown below]

    No default entry for user ssm-user
    Quota for user ec2-user (uid 1000):
      Filesystem                   blocks       soft       hard     inodes     soft     hard
      /dev/nvme1n1p6               0            10000       5000      0        0        0

[root@ip-172-19-160-40 ~]# edquota -u ssm-user -p ec2-user   [copies the quota limits/setting of ec2-user to ssm-user]

[root@ip-172-19-160-40 ~]# repquota -uv /dev/nvme1n1p6  [report now shows ssm-user and ec2-user with same limits. Note that used for both users is 0 because they have not written to the filesystem]

    *** Report for user quotas on device /dev/nvme1n1p6
    Block grace time: 7days; Inode grace time: 7days
                            Block limits                File limits
    User            used    soft    hard  grace    used  soft  hard  grace
    ----------------------------------------------------------------------
    root      --      13       0       0              2     0     0
    ec2-user  --       0   20000   25000              0     0     0
    ssm-user  --       0   20000   25000              0     0     0

    Statistics:
    Total blocks: 8
    Data blocks: 1
    Entries: 3
    Used average: 3.000000

[root@ip-172-19-160-40 ~]# ls -lah /data/mydata/

    total 22K
    drwxr-xr-x. 3 root root 1.0K Apr  3 16:20 .
    drwxr-xr-x. 4 root root   33 Mar 30 15:57 ..
    -rw-------. 1 root root 8.0K Apr  3 16:20 aquota.user
    drwx------. 2 root root  12K Mar 30 12:28 lost+found

[root@ip-172-19-160-40 ~]# chmod 777 /data/mydata/ [setting loose permission so all users can write to filesystem]

[root@ip-172-19-160-40 ~]# exit

    logout

sh-4.2$ sudo su - ec2-user

[ec2-user@ip-172-19-160-40 ~]$ sudo echo "Writing to take up my first block space on quotas" > /data/mydata/ec2-user-quota.txt

[ec2-user@ip-172-19-160-40 ~]$ ls -lah /data/mydata/total 
    
    24K
    drwxrwxrwx. 3 root     root     1.0K Apr  3 17:26 .
    drwxr-xr-x. 4 root     root       33 Mar 30 15:57 ..
    -rw-------. 1 root     root     8.0K Apr  3 16:20 aquota.user
    -rw-rw-r--. 1 ec2-user ec2-user   50 Apr  3 17:26 ec2-user-quota.txt
    drwx------. 2 root     root      12K Mar 30 12:28 lost+found

[ec2-user@ip-172-19-160-40 ~]$ exit  [exit as ec2-user]

sh-4.2$ sudo su -  [log in as root user]

    Last login: Fri Apr  3 17:29:23 CDT 2026 on pts/1

[root@ip-172-19-160-40 ~]# repquota -uv /dev/nvme1n1p6

    *** Report for user quotas on device /dev/nvme1n1p6     [usage report]
    Block grace time: 7days; Inode grace time: 7days
                            Block limits                File limits
    User            used    soft    hard  grace    used  soft  hard  grace
    ----------------------------------------------------------------------
    root      --      13       0       0              2     0     0
    ec2-user  --       2   20000   25000              1     0     0        <=Note that ec2-user now has 2 in the 'used' column because of the file ec2-user-quota.txt written to filesystem
    ssm-user  --       0   20000   25000              0     0     0

    Statistics:
    Total blocks: 8
    Data blocks: 1
    Entries: 3
    Used average: 3.000000

[root@ip-172-19-160-40 ~]# repquota -uv /data/mydata/   [Same as above. You can use the filesystem mount location or partition options]

    *** Report for user quotas on device /dev/nvme1n1p6
    Block grace time: 7days; Inode grace time: 7days
                            Block limits                File limits
    User            used    soft    hard  grace    used  soft  hard  grace
    ----------------------------------------------------------------------
    root      --      13       0       0              2     0     0
    ec2-user  --       2   20000   25000              1     0     0
    ssm-user  --       0   20000   25000              0     0     0

    Statistics:
    Total blocks: 8
    Data blocks: 1
    Entries: 3
    Used average: 3.000000    


# creating user and group quotas on xfs filesystems

[root@ip-172-19-160-40 ~]# mount | grep /dev/nvme1n1p7

    /dev/nvme1n1p7 on /data/data2 type xfs (rw,relatime,seclabel,attr2,inode64,noquota)

[root@ip-172-19-160-40 ~]# blkid /dev/nvme1n1p7

    /dev/nvme1n1p7: LABEL="DATA2" UUID="78265d5d-be6f-4517-a02f-ee9126332761" TYPE="xfs" PARTUUID="000e9320-07"

[root@ip-172-19-160-40 ~]# vi /etc/fstab  [locate the /dev/nvme1n1p7 partition using the UUID above and add usrquota, grpquota, gqnoenforce(group quota no enforcement just sends a reminder that users have gone over without limiting them)]  

This line:

    UUID="78265d5d-be6f-4517-a02f-ee9126332761"   /data/data2           xfs     defaults 0 0

Should no be this:

    UUID="78265d5d-be6f-4517-a02f-ee9126332761"   /data/data2           xfs     usrquota,grpquota,gqnoenforce 0 0

[root@ip-172-19-160-40 ~]# umount /data/data2/   [unmount filesystems so the new attributes can take effect]

[root@ip-172-19-160-40 ~]# mount -a   [auto mount filesystems]

[root@ip-172-19-160-40 ~]# mount | grep /dev/nvme1n1p7   [filesystem now has the user/group quotas attributes]

    /dev/nvme1n1p7 on /data/data2 type xfs (rw,relatime,seclabel,attr2,inode64,usrquota,gqnoenforce)

[root@ip-172-19-160-40 ~]# chmod a+rwx /data/data2/  [allow loose access to all]

[root@ip-172-19-160-40 ~]# xfs_quota -c 'quota ec2-user' /dev/nvme1n1p7  [should retrun nothing as the ec2-user has no content on filesystem/partition]

[root@ip-172-19-160-40 ~]# xfs_quota -xc 'limit -u bsoft=30m bhard=35m ec2-user' /data/data2/  [sets user quota on filesystem /data/data2]  

[root@ip-172-19-160-40 ~]# exit  [exit as root]

sh-4.2$ sudo su - ec2-user  [login as ec2-user]

    Last login: Fri Apr  3 17:32:29 CDT 2026 on pts/0

[ec2-user@ip-172-19-160-40 ~]$ sudo echo "Writing to take up my first block space on quotas" > /data/data2/ec2-user-quota.txt  [write to filesystem to test usage report]

[ec2-user@ip-172-19-160-40 ~]$ exit    [exit as ec2-user]

    logout

sh-4.2$ sudo su -   [login as root]

    Last login: Fri Apr  3 17:34:45 CDT 2026 on pts/0
  
Now lets run a report on the /data/data2 filesystem or partition. it should returns a report because the user wrote content to the filesystem aboven Note the limits we set above and the 'used' column of 1 bcs of our file written to partition

[root@ip-172-19-160-40 ~]# xfs_quota -c 'quota ec2-user'  /dev/nvme1n1p7  [report now with content]

    Disk quotas for User ec2-user (1000)
    Filesystem              Blocks      Quota      Limit  Warn/Time      Mounted on
    /dev/nvme1n1p7               1      30720      35840   00 [--------] /data/data2


[root@ip-172-19-160-40 ~]# xfs_quota -c 'quota -h ec2-user'  /dev/nvme1n1p7  [human readable report with -h]

    Disk quotas for User ec2-user (1000)
    Filesystem   Blocks  Quota  Limit Warn/Time    Mounted on
    /dev/nvme1n1p7
                    1K    30M    35M  00 [------] /data/data2

                    
[root@ip-172-19-160-40 ~]# xfs_quota -xc 'report -h'  /dev/nvme1n1p7  [better formatted report of the partition]

    User quota on /data/data2 (/dev/nvme1n1p7)
                            Blocks
    User ID      Used   Soft   Hard Warn/Grace
    ---------- ---------------------------------
    root            0      0      0  00 [------]
    ec2-user       1K    30M    35M  00 [------]

    Group quota on /data/data2 (/dev/nvme1n1p7)
                            Blocks
    Group ID     Used   Soft   Hard Warn/Grace
    ---------- ---------------------------------
    root            0      0      0  00 [------]
    ec2-user       1K      0      0  00 [------]

[root@ip-172-19-160-40 ~]# xfs_quota -xc 'report -h'  /data/data2/  [better formatted report of the partition. Same as above. You can use the filesystem mount location or partition options]

    User quota on /data/data2 (/dev/nvme1n1p7)
                            Blocks
    User ID      Used   Soft   Hard Warn/Grace
    ---------- ---------------------------------
    root            0      0      0  00 [------]
    ec2-user       1K    30M    35M  00 [------]

    Group quota on /data/data2 (/dev/nvme1n1p7)
                            Blocks
    Group ID     Used   Soft   Hard Warn/Grace
    ---------- ---------------------------------
    root            0      0      0  00 [------]
    ec2-user       1K      0      0  00 [------]

