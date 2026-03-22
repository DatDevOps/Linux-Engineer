<!-- Server SSH -->

# Server SSH

My current Centos server public IP: 52.90.153.202
My AmazonLinux2 server public IP: 54.91.139.240

Note that the server IP addresses changes ech time the server is stopped and started

Make sure the SSH client is configure on the machines if you get an error with the commands below

1. ssh from Centos to AmazonLinux2

$ hostname

    c71f0fe92f2c.mylabserver.com

$ ssh 54.91.139.240 (enter yes and password)

    The authenticity of host '54.91.139.240 (54.91.139.240)' can't be established.
    ECDSA key fingerprint is SHA256:ERg8Jmamuvyo6QnFah2jcN4+0Hx4HBaaEv3MusAMer4.
    ECDSA key fingerprint is MD5:0f:d8:0b:d4:d2:c6:d6:99:58:8c:54:db:86:21:88:c4.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '54.91.139.240' (ECDSA) to the list of known hosts.
    Password: 
    Last login: Wed Feb 25 14:46:48 2026


Note that the terminal now has a portion of the AmazonLinux2 Hostname between @ and ~ sign

[cloud_user@c71f0fe92f1c ~]$ 

[cloud_user@c71f0fe92f1c ~]$ exit (logs you out of the Amazon server)

2. ssh from AmazonLinux2 to Centos

$ ssh 52.90.153.202 (enter yes and password)

    The authenticity of host '52.90.153.202 (52.90.153.202)' can't be established.
    ECDSA key fingerprint is SHA256:ERg8Jmamuvyo6QnFah2jcN4+0Hx4HBaaEv3MusAMer4.
    ECDSA key fingerprint is MD5:0f:d8:0b:d4:d2:c6:d6:99:58:8c:54:db:86:21:88:c4.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '52.90.153.202' (ECDSA) to the list of known hosts.
    Password: 
    Last login: Wed Feb 25 14:46:48 2026

Note that the terminal now has a portion of the Centos Hostname between @ and ~ sign

[cloud_user@c71f0fe92f2c ~]$ 

[cloud_user@c71f0fe92f2c ~]$ exit (logs you out of the Centos server)

Now move to your user directory to create custom ssh configs

$ cd ~ (go to home directory)

$ cd .ssh (create one if it does not exist - mkdir .ssh)

$ ls -la .ssh/

    total 12
    drwx------.  2 cloud_user cloud_user   46 Feb 25 16:43 .
    drwx------. 17 cloud_user cloud_user 4096 Feb 25 14:15 ..
    -rw-------.  1 cloud_user cloud_user  812 Feb  8 23:37 authorized_keys (contains base64 hat file stores the public SSH keys that are allowed to log in as that user.)
    -rw-r--r--.  1 cloud_user cloud_user  175 Feb 25 16:43 known_hosts (contained host you have connected to using ssh)


$ cat .ssh/known_hosts 
    54.91.139.240 ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTIt
    bmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBAfYUx6CVCK6B8zp//BoL+
    2CNx/opnptC/JWrdpmdiPjM/9n8b6EsfRVT+4bYHsMh9uAAGsGoxBI
    hsZ2WnyD5CI=

$ cat .ssh/authorized_keys
    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCv86K5tOtssAJsrc
    RzY4r6onAdB8ky3sDmw8SMv3+5iDqj6VMNBefjlqL0xSzo8GskgYvG
    pKO2hieZ52/MgYYo/OmrGD+BKhZfZIMExO38SPe+tduY2eKeNxLwR6
    BaTtKrj52vQ2EtwjwmNMj9t6Lj3Wq9WR3HLfj7Jup1+6Sk+msEiFD0
    KA9T3Egf+gy1JQevzJHZhzIvf5QCSw8I56TGAj4vc3IEmu2Xb0whdy
    C9eHFDOMR+HmGJ3LvcKLHvN3IbD8j8Op1NRyyGqNn0T2fILm6Je8/n
    Cnd8VtP8PF+J+n//lpv+JslkILrjru4O3/FdkT85mgIVuw8vg3iLt+
    un cloud_user@linuxacademy
    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCGk5BYUcWzVLyHZ3
    VepHl5NQcJoVuh1aRtpCViRwo9KZ5urmvDeLUU/xzLoenpi28HQVd6
    mhydIHIhAsq1BI/rk1m4x0Gf/jDDOatfGE2fiPjJHzeQcihocxJlCK
    TA4fgSG6Z6vj4aLj/185VM7dJ/ISf7hr+rwovPrr2daAJKGdR8DQzV
    Jvw2SbCMrqTE6Ly3fYFJsJzwO9urzQPScvZgVpDd1fx5abNvT6wOyV
    QgKzkC1Od7KT/ZphYhYyCcAqipHzkNqKme7dOWrEmloraL7fSJG2I2
    EPnjQWC5Jc8JcFvgwfRoE5vQUaY6rla875rRoRZPhYQMVtTsXa0WJtHH seang-labservers-us-east2

Configure custom file for ssh-ing into other servers

Configuring custom config to ssh into our Amazon server with IP 54.91.139.240

$ cd ~

$ cd .ssh

$ vi config (creates the file config or opens it if it exist in a VI editor)

Enter the below as the content of the file and exit the file with pressing esc keyand entering :wq or :x to write, save and exit the file.

    Host server1
            Hostname 54.91.139.240 (Amazon server ip)
            User cloud_user (the user you want allow to ssh with. In this caase the user that owns the file or that created it)
            Port 22 (the port you want to allow to ssh in. it is the default and you don't have to even specify it)

ssh enforces StrictModes for securityand throws an error like "Bad owner or permissions on /home/cloud_user/.ssh/config".
That error means the config file (or one of its parent directories) is writable by others or not owned by the current user, so SSH refuses to use it.
So make sure ONLY the user, and group perhaps [try to confirm it by making sure only group as the permission and that the user belong to the group], trying to use it has rw/x permissions as done below.

$ chmod 600 .ssh/config 

$ cat config

    Host server1
        Hostname 54.91.139.240 
        User cloud_user
        Port 22


In the Amazon server do the same. 
Configuring custom config to ssh into our Centos server with IP 52.90.153.202

$ cd ~

$ cd .ssh

$ vi config (creates the file config or opens it if it exist in a VI editor)

Enter the below as the content of the file and exit the file with pressing esc keyand entering :wq or :x to write, save and exit the file.

    Host server1
        Hostname 52.90.153.202
        User cloud_user
        Port 22

$ chmod 600 .ssh/config

$ cat config

    Host server1
        Hostname 52.90.153.202
        User cloud_user
        Port 22

Now ssh into Amazon server like below from Centos server:

$ ssh server1 (enter password)

   
Now ssh into Centos server like below from Amazon server:

$ ssh server1 (enter password)

   
% SSH using public and private key   
This method does not use the user password like above

From your centos server run:

$ cd ~/.ssh

$ ssh-keygen -t rsa

    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/cloud_user/.ssh/id_rsa): 
    Enter passphrase (empty for no passphrase): 
    Enter same passphrase again: 
    Your identification has been saved in /home/cloud_user/.ssh/id_rsa.
    Your public key has been saved in /home/cloud_user/.ssh/id_rsa.pub.
    The key fingerprint is:
    SHA256:wt0AATDHF/9S1/R9pyYCucSIraem0Y6k2FI3MKkFt+E cloud_user@c71f0fe92f2c
    .mylabserver.com
    The key's randomart image is:
    +---[RSA 2048]----+
    |  ooo.+o      .  |
    |. oo.o.= .   o ..|
    | + +..o B . . . =|
    |  E  o o B .   .o|
    | o o. + S + . o  |
    |. ..oo . . . o   |
    | .o.+.           |
    |o+ *             |
    |o.+ .            |
    +----[SHA256]-----+

[cloud_user@c71f0fe92f2c ~]$ ls -lah ~/.ssh/
total 24K
drwx------.  2 cloud_user cloud_user   89 Feb 25 17:49 .
drwx------. 17 cloud_user cloud_user 4.0K Feb 25 17:48 ..
-rw-------.  1 cloud_user cloud_user  812 Feb  8 23:37 authorized_keys (contains base64 hat file stores the public SSH keys that are allowed to log in as that user.)
-rw-------.  1 cloud_user cloud_user   57 Feb 25 17:48 config
-rw-------.  1 cloud_user cloud_user 1.8K Feb 25 17:49 id_rsa (contains private ssh key)
-rw-r--r--.  1 cloud_user cloud_user  421 Feb 25 17:49 id_rsa.pub (contains public ssh key that you can copy to other servers you want to use to ssh into this Centos server)
-rw-r--r--.  1 cloud_user cloud_user  175 Feb 25 16:43 known_hosts (contained host you have connected to using ssh)   

Copying our new generated ssh public key to Amazon server and any other server you might have in the Centos server ~/.ssh/config file

You can ct the id_rsa.pub file, copy the content, log into the Amazon server, create a file in with same name in ~/.ssh/id_rsa.pub, and paste the copied content.

OR do the below from Centos server :

$ ssh-copy-id -i id_rsa.pub server1  (enter password when ssh-ing for first time or passphrase if not first time)

    /bin/ssh-copy-id: INFO: Source of key(s) to be installed: "id_rsa.pub"
    /bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
    /bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
    Password: 
    Password: 

    Number of key(s) added: 1

    Now try logging into the machine, with:   "ssh 'server1'"
    and check to make sure that only the key(s) you wanted were added.

$ ssh server1 (enter passphrase when prompted)
Enter passphrase for key '/home/cloud_user/.ssh/id_rsa': 
Last failed login: Wed Feb 25 19:04:31 UTC 2026 from 98.93.244.117 on ssh:notty
There was 1 failed login attempt since the last successful login.
Last login: Wed Feb 25 19:03:26 2026 from 98.93.244.117
   ,     #_
   ~\_  ####_        Amazon Linux 2
  ~~  \_#####\
  ~~     \###|       AL2 End of Life is 2026-06-30.
  ~~       \#/ ___
   ~~       V~' '->
    ~~~         /    A newer version of Amazon Linux is available!
      ~~._.   _/
         _/ _/       Amazon Linux 2023, GA and supported until 2028-03-15.
       _/m/'           https://aws.amazon.com/linux/amazon-linux-2023/

14 package(s) needed for security, out of 17 available
Run "sudo yum update" to apply all updates.

[cloud_user@c71f0fe92f1c ~]$ ls -la (checking files id_rsa.pub was copied)

    total 16
    drwx------ 2 cloud_user cloud_user  80 Feb 25 19:03 .
    drwx------ 3 cloud_user cloud_user 184 Feb 25 19:03 ..
    -rw------- 1 cloud_user cloud_user 802 Feb 25 19:04 authorized_keys
    -rw------- 1 cloud_user cloud_user  88 Feb 25 19:03 config
    -rw-rw-r-- 1 cloud_user cloud_user 421 Feb 25 18:44 id_rsa.pub
    -rw-r--r-- 1 cloud_user cloud_user 524 Feb 25 19:03 known_hosts

[cloud_user@c71f0fe92f1c ~]$ exit

    logout

    Connection to 54.91.141.10 closed.

[cloud_user@c71f0fe92f2c .ssh]$ 

# Pre-authenticate using the private key on the Centos machine to avoid being prompted for passphrase

$ ssh-agent bash

$ ssh-add  (enter passphrase when prompted)

    Enter passphrase for /home/cloud_user/.ssh/id_rsa: 
    Identity added: /home/cloud_user/.ssh/id_rsa (/home/cloud_user/.ssh/id_rsa)

$ ssh server1 (notice you are not prompted for password and same for any other server in .ssh/config)

    Last login: Wed Feb 25 19:11:12 2026 from 98.93.244.117
    ,     #_
    ~\_  ####_        Amazon Linux 2
    ~~  \_#####\
    ~~     \###|       AL2 End of Life is 2026-06-30.
    ~~       \#/ ___
    ~~       V~' '->
        ~~~         /    A newer version of Amazon Linux is available!
        ~~._.   _/
            _/ _/       Amazon Linux 2023, GA and supported until 2028-03-15.
        _/m/'           https://aws.amazon.com/linux/amazon-linux-2023/

    14 package(s) needed for security, out of 17 available
    Run "sudo yum update" to apply all updates.


# Prevent SSH with root login or to only servers with rsa public key on the Amazon Server.

Login into each of the server and run

$ sudo vi /etc/ssh/sshd_config

Change the line:

PermitRootLogin yes/no ==> PermitRootLogin without-password

Exit and save file (:q)

$ exit (exit as root user)

$ systemctl restart sshd

Now you cannot ssh into from to either  server without private/public rsa key on the server (see above methods of ssh-ing using private key to do so into a server with the public key)


# copy files between server (Centos => Amazon and vice/versa)

Using the Centos server copy from it to Amazon server 

$ scp /etc/hosts server1:. (no password asked because we have above authenticated with our private key)

    hosts                                   100%  235   132.0KB/s   00:00 

scp => command to copy

/etc/hosts => file to copy

server1 => where to copy file to

.  => directory to copy to on the other server(AmzonLinux2)

$ ssh server1 (we want to check if host file was copied)

$ ls -l

    total 4
    lrwxrwxrwx 1 root       root        20 Feb  5 15:40 cloud_user_script.sh -> /home/home-script.sh
    -rw-rw-r-- 1 cloud_user cloud_user   0 Feb  5 15:39 cloud_user_scritp.sh
    -rw-r--r-- 1 cloud_user cloud_user 235 Feb 25 19:34 hosts
    lrwxrwxrwx 1 root       root        12 Feb  5 14:56 my-secret -> /var/secret/

$ touch test (used for reverse copy)

    total 4
    lrwxrwxrwx 1 root       root        20 Feb  5 15:40 cloud_user_script.sh -> /home/home-script.sh
    -rw-rw-r-- 1 cloud_user cloud_user   0 Feb  5 15:39 cloud_user_scritp.sh
    -rw-r--r-- 1 cloud_user cloud_user 235 Feb 25 19:34 hosts
    lrwxrwxrwx 1 root       root        12 Feb  5 14:56 my-secret -> /var/secret/
    drw-rw-r-- 1 cloud_user cloud_user   0 Feb 25 19:59 test

$ exit (exit server1)

Copy in reverse, i.e Using the Centos server copy from Amazon to Centos server 

$ scp server1:/home/cloud_user/test .

    ./test: Is a directory

$ ls -lh

    total 12K
    drwxrwxr-x. 2 cloud_user cloud_user   33 Feb 19 18:11 backup
    drwxr-xr-x. 2 cloud_user cloud_user   20 Feb 18 17:42 Desktop
    drwxrwxr-x. 2 cloud_user cloud_user   20 Feb 19 17:38 doc
    drwxrwxr-x. 8 cloud_user cloud_user 4.0K Feb 20 21:41 Documents
    -r--rw-rwx. 1 cloud_user cloud_user    0 Feb 20 21:46 file1
    -rwx------. 1 cloud_user cloud_user    0 Feb 20 22:00 file2
    -rw-r--r--. 1 cloud_user wheel         0 Feb 21 02:23 newgrpFile
    drwxr-xr-x. 3 cloud_user cloud_user   49 Feb 16 17:54 ntp
    -rw-r--r--. 1 cloud_user cloud_user  320 Feb 16 21:35 ntp.conf
    -rw-r--r--. 1 cloud_user cloud_user  325 Feb 16 21:44 ntp.new
    drwxrwxrwt. 2 cloud_user cloud_user    6 Feb 20 22:08 test
    drwxrwxr-x. 3 cloud_user cloud_user   18 Feb 19 17:44 usr

scp => command to copy

server1 => where to copy file from

/home/cloud_user/test  => file to copy

.  => directory to copy to on this server (Centos)

You can login into the Amazon server and run similar command from the Amazon server as long as the server has the private and public key