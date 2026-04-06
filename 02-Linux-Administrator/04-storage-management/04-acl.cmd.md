<!-- Access Control List -->

sh-4.2$  ls -l /boot/config-4.14.355-28  [type and double tab to see all available kernel versions]

    config-4.14.355-280.714.amzn2.x86_64  config-4.14.355-281.714.amzn2.x86_64

sh-4.2$ uname -r   [returns kernel version in use]

    4.14.355-281.714.amzn2.x86_64

sh-4.2$  grep ACL /boot/config-$(uname -r)  [looking for the word ACL to confirm the system is configured for ACL use. Looking at the various config for various filesystems shows it is]

    CONFIG_EXT2_FS_POSIX_ACL=y
    CONFIG_EXT3_FS_POSIX_ACL=y
    CONFIG_EXT4_FS_POSIX_ACL=y
    CONFIG_XFS_POSIX_ACL=y
    CONFIG_BTRFS_FS_POSIX_ACL=y
    CONFIG_FS_POSIX_ACL=y
    CONFIG_TMPFS_POSIX_ACL=y
    # CONFIG_HFSPLUS_FS_POSIX_ACL is not set
    CONFIG_NFS_V3_ACL=y
    CONFIG_NFSD_V2_ACL=y
    CONFIG_NFSD_V3_ACL=y
    CONFIG_NFS_ACL_SUPPORT=m
    CONFIG_CEPH_FS_POSIX_ACL=y
    CONFIG_CIFS_ACL=y

[we are interest in those two filesystems and to check if they are have 'acl' attributes. Although they dont have the attribute but they can still be configure for ACL use bcs our kernel has it configured]
sh-4.2$ mount | grep data   

    /dev/nvme1n1p7 on /data/data2 type xfs (rw,relatime,seclabel,attr2,inode64,usrquota,gqnoenforce)
    /dev/nvme1n1p6 on /data/mydata type ext4 (rw,noexec,relatime,seclabel,quota,usrquota,data=ordered)
    ....
    ....

sh-4.2$ sudo tune2fs -l /dev/nvme1n1p6 | grep default  [No result because string did not match.]


[using case insensitive search here. Searching the filesystems/partition nvme1n1p6 for 'default' to see if the  acl attribute is part of te default attributes of a filesystem]
sh-4.2$ sudo tune2fs -l /dev/nvme1n1p6 | grep -i default  

    Default mount options:    user_xattr acl
    Default directory hash:   half_md4    

sh-4.2$ sudo su - ec2-user [swict to ec2-user]

    44 CDT 2026 on pts/0

Note the the dot, ., at the end of a file permission shows that ACL is not set but a + indicates that ACL is both supported and set

[ec2-user@ip-172-19-160-40 ~]$ ls -lah [all dots at the end of file permission shows their ACL is not set]

    total 20K
    drwx------.  3 ec2-user ec2-user 121 Apr  3 17:29 .
    drwxr-xr-x. 14 root     root     196 Mar 30 12:04 ..
    -rw-------.  1 ec2-user ec2-user 414 Apr  3 18:12 .bash_history
    -rw-r--r--.  1 ec2-user ec2-user  18 Jul 15  2020 .bash_logout
    -rw-r--r--.  1 ec2-user ec2-user 193 Jul 15  2020 .bash_profile
    -rw-r--r--.  1 ec2-user ec2-user 231 Jul 15  2020 .bashrc
    -rw-rw-r--.  1 ec2-user ec2-user  50 Apr  3 17:24 ec2-user-quota.txt
    drwx------.  2 ec2-user ec2-user  29 Mar 15 10:00 .ssh    

[ec2-user@ip-172-19-160-40 ~]$ getfacl ec2-user-quota.txt  [Get file ACL. ACL not set as indicated by the one user and group and everyone else]

    # file: ec2-user-quota.txt
    # owner: ec2-user
    # group: ec2-user
    user::rw-
    group::rw-
    other::r--

# Setting default ACL
[ec2-user@ip-172-19-160-40 ~]$ mkdir test-acl  [creates a directory]

[ec2-user@ip-172-19-160-40 ~]$ ls -ld test-acl/  [list the specific directory. The dot at the  end of the permission indicates that ACL is not set]

    drwxrwxr-x. 2 ec2-user ec2-user 6 Apr  5 17:31 test-acl/

[ec2-user@ip-172-19-160-40 ~]$ getfacl test-acl/  [returns acl of test-acl file. No acl set] 

    # file: test-acl/
    # owner: ec2-user
    # group: ec2-user
    user::rwx
    group::rwx
    other::r-x    


[ec2-user@ip-172-19-160-40 ~]$ setfacl -m d:o:--- test-acl/  [sets/modifies the default ACL on the directory test-acl/.]

NB:
-m   => Modify (add or change) an ACL entry.
d    => default ACL (applies to future files/dirs)
o:   => others (users not owner or group)
---  => no permissions (no read, write, execute)

All newly created files and subdirectories inside test-acl/ will give no permissions to “others”. It does not change permissions on:
    - Existing files
    - Existing subdirectories
    - The directory test-acl/ itself

[ec2-user@ip-172-19-160-40 ~]$ getfacl test-acl/  [now default ACL on test-acl for all but removes all permissions on 'other']

    # file: test-acl/
    # owner: ec2-user
    # group: ec2-user
    user::rwx
    group::rwx
    other::r-x
    default:user::rwx
    default:group::rwx
    default:other::---   

[ec2-user@ip-172-19-160-40 ~]$ ls -ld test-acl/  [notice the + at the end of the directory indicating ACL is now set for the directory]

    drwxrwxr-x+ 2 ec2-user ec2-user 6 Apr  5 17:31 test-acl/

[ec2-user@ip-172-19-160-40 ~]$  touch test-acl/file1  [create new file]

[ec2-user@ip-172-19-160-40 ~]$ ls -l test-acl/  [new file has no permission according to acl]

    total 0
    -rw-rw----. 1 ec2-user ec2-user 0 Apr  5 17:54 file1 

[ec2-user@ip-172-19-160-40 ~]$ setfacl -dm u:ssm-user:rw test-acl/  [set acl for a ssm-user]

[ec2-user@ip-172-19-160-40 ~]$ getfacl test-acl/  [shows that the ssm-user acl permission added to test-acl directory]

    # file: test-acl/
    # owner: ec2-user
    # group: ec2-user
    user::rwx
    group::rwx
    other::r-x
    default:user::rwx
    default:user:ssm-user:rw-
    default:group::rwx
    default:mask::rwx
    default:other::---

[ec2-user@ip-172-19-160-40 ~]$ touch test-acl/file2  []

[ec2-user@ip-172-19-160-40 ~]$ ls -l test-acl/file*  [list file1 and file2. file1 created b4 we added acl to test-acl means it has not acl but file2 has acl as shown by the + sign as it was created after we added the acl to test-acl]

    -rw-rw----. 1 ec2-user ec2-user 0 Apr  5 17:54 test-acl/file1
    -rw-rw----+ 1 ec2-user ec2-user 0 Apr  5 18:05 test-acl/file2

[ec2-user@ip-172-19-160-40 ~]$ getfacl test-acl/file2  [although 'others' should have no permissions according to acl, ssm-user has rw access because we added/set it with rw permissions above]

    # file: test-acl/file2
    # owner: ec2-user
    # group: ec2-user
    user::rw-
    user:ssm-user:rw-
    group::rwx                      #effective:rw-
    mask::rw-
    other::---  

# Adding ACL entries  

[ec2-user@ip-172-19-160-40 ~]$ exit

    logout

sh-4.2$ sudo su -

    Last login: Sun Apr  5 17:22:46 CDT 2026 on pts/0

[root@ip-172-19-160-40 ~]# mkdir /work

[root@ip-172-19-160-40 ~]# ls -ld /work/  [new folder can be access by the root user or group]

    drwxr-xr-x. 2 root root 6 Apr  5 18:11 /work/

[root@ip-172-19-160-40 ~]# chmod o= /work/  [removes all permission for 'others']

[root@ip-172-19-160-40 ~]# ls -ld /work/  ['other' permission removed]

    drwxr-x---. 2 root root 6 Apr  5 18:11 /work/

[root@ip-172-19-160-40 ~]# sudo su - ec2-user   [switching to ec2-user to check if it can access the /work folder]

    Last login: Sun Apr  5 18:04:40 CDT 2026 on pts/1

[ec2-user@ip-172-19-160-40 ~]$ cd /work/   [ec2-user permission denied as expected. The ec2-user is not root or belong to the root group]

-bash: cd: /work/: Permission denied

[ec2-user@ip-172-19-160-40 ~]$ exit   [exit to return to theroot user shell]

    logout

[root@ip-172-19-160-40 ~]# setfacl -m u:ec2-user:rx /work/  [set/modifies /work acl to allow ec2-user to read and execute]

[root@ip-172-19-160-40 ~]# getfacl /work/   [shows ec2-user can now only read and execute]

    getfacl: Removing leading '/' from absolute path names
    # file: work/
    # owner: root
    # group: root
    user::rwx
    user:ec2-user:r-x
    group::r-x
    mask::r-x
    other::---

[root@ip-172-19-160-40 ~]# sudo su - ec2-user  [switching user to ec2-user]

    Last login: Sun Apr  5 18:13:00 CDT 2026 on pts/1

[ec2-user@ip-172-19-160-40 ~]$ cd /work/   [now able to change into /work directory because of setting the acl  to allow ec2-user to read and execute to /work]

[ec2-user@ip-172-19-160-40 work]$ touch /work/file1  [permission denied because ec2-user does not have write permissions]

touch: cannot touch ‘/work/file1’: Permission denied

[ec2-user@ip-172-19-160-40 work]$ exit  [exit ec2-user shell to move  to root user shell]

    logout    

[root@ip-172-19-160-40 ~]# setfacl -m d:o:--- /work/  [using acl to remove all permissions for 'others']

[root@ip-172-19-160-40 ~]# getfacl /work/   [shows the default acl for 'other' is no permission]

    getfacl: Removing leading '/' from absolute path names
    # file: work/
    # owner: root
    # group: root
    user::rwx
    user:ec2-user:r-x
    group::r-x
    mask::r-x
    other::---
    default:user::rwx
    default:group::r-x
    default:other::---

[root@ip-172-19-160-40 ~]# echo file1 > /work/file1     [creates file1]

[root@ip-172-19-160-40 ~]# echo file2 > /work/file2     [creates file2]

[root@ip-172-19-160-40 ~]# ls -l /work/  

    total 8
    -rw-r-----. 1 root root 6 Apr  5 18:29 file1
    -rw-r-----. 1 root root 6 Apr  5 18:29 file2

[root@ip-172-19-160-40 ~]# setfacl -m u:ec2-user:rw /work/file1

[root@ip-172-19-160-40 ~]$ ls -l /work/  [acl for file1 set as shown by +]

    total 8
    -rw-rw----+ 1 root root 11 Apr  5 18:32 file1
    -rw-r-----. 1 root root  6 Apr  5 18:29 file2

[root@ip-172-19-160-40 ~]# sudo su - ec2-user

    Last login: Sun Apr  5 18:23:30 CDT 2026 on pts/1

[ec2-user@ip-172-19-160-40 ~]$ echo hell >> /work/file1

[ec2-user@ip-172-19-160-40 ~]$ cat /work/file1

    file1
    hell

[ec2-user@ip-172-19-160-40 ~]$ cat /work/file2  [no acl permission for file2, hence the denial]

    cat: /work/file2: Permission denied
  
# Removing single ACL entries and all ACL

[ec2-user@ip-172-19-160-40 ~]$ exit  [exit to switch back to root shell]

[root@ip-172-19-160-40 ~]# cd /work/

[root@ip-172-19-160-40 work]# ls -l  [shows acl is set on file1]

    total 8
    -rw-rw----+ 1 root root 11 Apr  5 18:32 file1
    -rw-r-----. 1 root root  6 Apr  5 18:29 file2

[root@ip-172-19-160-40 work]# setfacl -x u:ec2-user file1  [removing the acl for ec2-user, using -x flag, on file which had allowed it access]

[root@ip-172-19-160-40 work]# getfacl file1  [shows that the single acl entry for ec2-user is gone]

    # file: file1
    # owner: root
    # group: root
    user::rw-
    group::r--
    mask::r--
    other::---

[root@ip-172-19-160-40 work]# ls -l  [file1 still has + sign even when we have removed the acl that was set on it when we granted ec2-user access]

    total 8
    -rw-r-----+ 1 root root 11 Apr  5 18:32 file1
    -rw-r-----. 1 root root  6 Apr  5 18:29 file2

[root@ip-172-19-160-40 work]# setfacl -b file1  [removing all the acl on file1]

[root@ip-172-19-160-40 work]# ls -l  [acl on file no longer exist]

    total 8
    -rw-r-----. 1 root root 11 Apr  5 18:32 file1
    -rw-r-----. 1 root root  6 Apr  5 18:29 file2

