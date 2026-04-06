<!-- File Permissions -->

$ df -hT (df shows the used and free storage spaces. The T switch adds a column for the file type)

    Filesystem     Type      Size  Used Avail Use% Mounted on
    devtmpfs       devtmpfs  864M     0  864M   0% /dev
    tmpfs          tmpfs     903M     0  903M   0% /dev/shm
    tmpfs          tmpfs     903M   17M  886M   2% /run
    tmpfs          tmpfs     903M     0  903M   0% /sys/fs/cgroup
    /dev/nvme0n1p1 xfs        20G  8.3G   12G  42% /
    tmpfs          tmpfs     181M   20K  181M   1% /run/user/1002

$ mount (shows all the mounted file system including virtual devices)

    ysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime,seclabel)
    proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
    devtmpfs on /dev type devtmpfs (rw,nosuid,seclabel,size=884452k,nr_inodes=221113,mode=755)
    ...
    ...
    debugfs on /sys/kernel/debug type debugfs (rw,relatime)
    nfsd on /proc/fs/nfsd type nfsd (rw,relatime)
    tmpfs on /run/user/1002 type tmpfs (rw,nosuid,nodev,relatime,seclabel,size=184892k,mode=700,uid=1002,gid=1003)

$ mount | grep root


$ ls -lah com.tar (shows he permission for user:group:others)

    -rw-rw-r--. 1 cloud_user cloud_user 194M Feb 19 19:15 com.tar

$ stat com.tar (shows same text permission above along with text(symbolic) and octal format (0664/-rw-rw-r--) permissions, and other info)

    File: ‘com.tar’
    Size: 203089920       Blocks: 396664     IO Block: 4096   regular file
    Device: 10301h/66305d   Inode: 58827599    Links: 1
    Access: (0664/-rw-rw-r--)  Uid: ( 1002/cloud_user)   Gid: ( 1003/cloud_user)
    Context: unconfined_u:object_r:user_home_t:s0
    Access: 2026-02-19 19:16:57.931186330 +0000
    Modify: 2026-02-19 19:15:51.013967123 +0000
    Change: 2026-02-19 19:15:51.013967123 +0000
    Birth: -

$ stat -c %A com.tar (text/symbolic permissions only)

    -rw-rw-r--

$ stat -c %a com.tar (octal permissions only)

    664

Note that the default permission for a file is '-rw-rw-r--' and that of a directory is 'drwx-rwx-rwx'

# umask

The umask for link user by default is '0002'. Counting from right to left, i.e others=>group=>user
Umask removes permissions and can not add permissions

0   => Remove no permission for
0   => Remove no permission for user
0   => Remove no permission for group
2   => Remove permission write permssions  for others

Note that added execute permissions, x, are not shown in output excepts files are executables which they are not

$ touch file1

$ ls -l | grep file1 (display default permissions based on umask)

 -rw-rw-r--. 1 cloud_user cloud_user       0 Feb 20 21:21 file1

$ umask 0 (changes umask to allow 'others' to have permissions like users and groups)

$ touch file2

$ ls -l | grep file2

    -rw-rw-rw-. 1 cloud_user cloud_user       0 Feb 20 21:24 file2

$ umask 27

$ touch file3

$ ls -l | grep file3

    -rw-r-----. 1 cloud_user cloud_user       0 Feb 20 21:29 file3

$ umask 77

$ touch file4

$ ls -l | grep file4

    -rw-------. 1 cloud_user cloud_user       0 Feb 20 21:29 file4

Another way  to set umask is show below by using text to add only what is needed:

$ umask u=rwx,g=rx,o=rx

$ touch file5

$ ls -l | grep file5

    -rw-r--r--. 1 cloud_user cloud_user       0 Feb 20 21:32 file5

$ umask 033 (same as last command but using numeric values to assign umask)

$ touch file6

$ ls -l | grep file6

    -rw-r--r--. 1 cloud_user cloud_user       0 Feb 20 21:34 file6

You can group items for which you want similar permissions

$ umask u=rwx,go=  (removes all permissions for groups and others)

$ umask

    0077

$ touch file7

$ ls -l | grep file7

-rw-------. 1 cloud_user cloud_user       0 Feb 20 21:37 file7

# Setting file permissions

$ chmod 467 file1 

$ ls -l file1 

    -r--rw-rwx. 1 cloud_user cloud_user 0 Feb 20 21:46 file1

$ chmod u=r,g=rw,o=rwx file1 (same as above)


$ ls -l file1 

    -r--rw-rwx. 1 cloud_user cloud_user 0 Feb 20 21:46 file1

Note that for a anyone trying to access a file, permissions are not cummulative and are checked in this order: user => group => others. The resulting permission is assigned for the first permission matched.

In tthe case of file1, the user, cloud_user, which is the user who created and trying to access the file is first matched as user and the permissions for group and others are not considered. This user has only read access. Try to edit the  file in VI editor and it will dsplay [readonly] at the bottom

$ vi file1 (it dsplays [readonly] because the user has only read permissions)

$ touch file2

$ chmod a+x file2

$ ls -l file2

    -rwx--x--x. 1 cloud_user cloud_user 0 Feb 20 22:00 file2

$ chmod a-x file2

$ ls -l file2

    -rw-------. 1 cloud_user cloud_user 0 Feb 20 22:00 file2

$ chmod a+x file2

$ ls -l file2

    -rwx--x--x. 1 cloud_user cloud_user 0 Feb 20 22:00 file2

$ chmod o-x file2

$ ls -l file2

    -rwx--x---. 1 cloud_user cloud_user 0 Feb 20 22:00 file2

$ chmod a+x file2

$ ls -l file2

    -rwx--x--x. 1 cloud_user cloud_user 0 Feb 20 22:00 file2

$ chmod go= file2

$ ls -l file2

    -rwx------. 1 cloud_user cloud_user 0 Feb 20 22:00 file2
    
$umask (shows the permission that are remove and for group and others)
    
    0077

Adding and removing sticky bits as 1 in the permission 1777 and t in drwxrwxrwt

$ mkdir -m 1777 test
$ ls -ld test/

    drwxrwxrwt. 2 cloud_user cloud_user 6 Feb 20 22:08 test/

$ chmod o-t test/

$ ls -ld test/

    drwxrwxrwx. 2 cloud_user cloud_user 6 Feb 20 22:08 test/

$ chmod o+t test/

$ ls -ld test/

    drwxrwxrwxt. 2 cloud_user cloud_user 6 Feb 20 22:08 test/


# File ownership

$ ls -l file2

-rwx------. 1 cloud_user cloud_user 0 Feb 20 22:00 file2

$ id -u (output user id)

    1002

$ id -un  (output user name)

    cloud_user

$ id -gn  (output user primary group)

    cloud_user  (output)

$ id -Gn  (output other user primary and secondary/complimentary group)

    cloud_user wheel

When we are creating file resources our primary group, in this case cloud_user, is used.
And when we are accessing file resources for permission through the group ID we use the complimentary or secondary group, wheel in this case. 
Wheel group is the admin group for Centos


$ chgrp wheel file2 (changes file2 group from cloud_user to wheel)

$ ls -l file2

    -rwx------. 1 cloud_user wheel 0 Feb 20 22:00 file2

$ newgrp wheel (changes the group of the user from cloud_user group to wheel group. This starts a new bash not noticeable to you and the new group is the default for this bash shell)

$ id -gn

    wheel

$ touch newgrpFile (creates a new file to see that the default group is now wheel)

$ ls -l newgrpFile 

    -rw-r--r--. 1 cloud_user wheel 0 Feb 21 02:23 newgrpFile

$ exit (exist the newgrp shell)
    
    exit

$ id -gn (defaults back to the original user group, cloud_user, before the newgrp command)

    cloud_user

$ ls -l file2
    
    -rwx------. 1 cloud_user wheel 0 Feb 20 22:00 file2

$ sudo chown root file2 (changes file2 user permission to root)

$ ls -l file2

    -rwx------. 1 root wheel 0 Feb 20 22:00 file2

$ sudo chown cloud_user.cloud_user file2 (changes file2 user and group permission to cloud_user and cloud_user respectively)

$ sudo chown cloud_user:cloud_user file2 (same as above. You can either use . or :)

$ ls -l file2

    -rwx------. 1 cloud_user cloud_user 0 Feb 20 22:00 file2

(copies file2 to /root with a new name file2na but with sudo which changes the user and group of file and timestamp to current time after copying as shown below)
$ sudo cp file2 /root/file2na 

$ sudo ls -l /root/file2na

    -rwx------. 1 root root 0 Feb 21 02:41 /root/file2na

(copies file2 to /root with a new name file2a but with sudo but retains the user and group and timestamp of file after copying as shown below)
$ sudo cp -a file2 /root/file2a 

$ sudo ls -l /root/file2a

    -rwx------. 1 cloud_user cloud_user 0 Feb 20 22:00 /root/file2a

<!-- Accessing Root Account -->

su (substitute user)

##1
$ su (Without argument it assumes you want to switch to root. SU prompts for root password for the user, root, you want to assume)

Password:

$ whoami (shows you are root)

    root

##2
$ sudo -i (login in as root)

$ pwd (shows you are in as /root) or 

$ whoami (shows your are root)

##3
$ sudo su -

$ pwd (shows you are in as /root) or 

$ whoami (shows your are root)


##4
$ sudo su (switches user to root access keeping the user environment to have access to the Linux root directory and other files not in the home/user directory [i.e ~ or /home])

$ pwd (shows you are in as /home/cloud_user) or 

$ whoami (shows your are root)

$ sudo su - YOUR_SPECIFIC_USERNAME' (switches user to root but keeps the user environment )

$ pwd (shows you are in as /home/cloud_user) or 

$ whoami (shows your are cloud_user)


Sudo users generally belong to the wheel group that specifies command users in the group can run with or without permissions.

The /etc/sudoers file shows the wheel group, %wheel, and the permissions

$ sudo cat /etc/sudoers

    ....
    ....   
    ## Allows people in group wheel to run all commands
    %wheel  ALL=(ALL)       ALL

    ## Same thing without a password
    # %wheel        ALL=(ALL)       NOPASSWD: ALL

    ## Allows members of the users group to mount and unmount the 
    ## cdrom as root
    # %users  ALL=/sbin/mount /mnt/cdrom, /sbin/umount /mnt/cdro
    ....
    ....

To edit the sudoers file run:

$ sudo visudo (opens the sudoers file in the default editor, which is VI editor)

**** To read more about controlling file permissions using ACL, go to 02-Linux-Administrator\04-storage-management\04-acl.cmd.md module




