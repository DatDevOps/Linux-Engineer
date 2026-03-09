<!-- Getting Started with Linux System Administration -->

<!-- # Monitoring systems -->
First of all, a lot of key system information is kept within virtual files in the /proc directory. The files are virtual in the sense that they're not persistent, but are created dynamically with each system boot and in response to system events.

* Note that the 'less' command displays one text page at a time to the stdout terminal

$ cd /proc

$ less meminfo (contains info about memory capacity: total,  usage, and available. It also shows yoour Swap memory amongst others)

$ less cpuinfo (contains specs about cpu and cpu cores)

For a view of how the hardware is being utilized at any given moment by processes on running  on a system, run:

$ top (constantly updating list of running process and their hardware utilizations)


To see a human friendly display of available memory resources run:

$ free

$ free -h


To see storage devices on your system or specific file system in human redable form, run:

$ df

$ df -t ext4 (perhaps  for Ubuntu)

$ df -t tmpfs (perhaps  for Amazon Linux 2)

$ df -ht ext4 (perhaps  for Ubuntu)

$ df -ht tmpfs (perhaps  for Amazon Linux 2)


Checking for network bandwidth: upload, download, and latency if issue isn't with you ISP. Check for which process/es that might be causing it using the package 'iftop". Install it as it is  not installed by default

$ sudo yum update

$ sudo yum unstall -y iftop


$ ip addr (shows your network interfaces and  pick the one you want to investigate, e.g eth0)

$ sudo iftop -i eth0 

The output of the command above shows a top section and and bottom sections. The top shows source and target address associated with requests, while the bottom shows the upload and download informations 

% Managing Processes

$ ps (shows program running within the current shell)

$ pgrep sshd (searches for running processes whose names match the pattern sshd using process grep [pgrep], and prints their process IDs (PIDs))

$ ps aux (shows all program running on your system)

$ ps aux | grep sshd (shows all running sshd process on your system)

Use the 'journalctl' (new utility on most linux systems) to check for system log of a any process with a timeframe. Most people still use the old /var/log only text file way of checking logs

$ journalctl --since "10 minutes ago" (new utility for logs)

$ ls /var/log (old way that stores logs in a text file) Or 

change directory to to /var/log and run if the  system has syslog file- /var/log/syslog:

$ cat syslog (Ubuntu used by instructor: take note of the timestamps of events)

$ cat messages (AWS Amazon Linux 2 used by me: take note of the timestamps of events)

$ dmesg (shows booth logs and other real time logs such as usb connections)

$ dmesg | less (shows booth logs and other real time logs such as usb connections one page at a time)

Now lets learn starting and killing processes using the 'yes' command as a sample.
The Linux yes command is a utility that repeatedly outputs a specified string to standard output until it is killed. By default, it outputs "y" followed by a newline. Its primary use is to automate responses to interactive command-line prompts by piping its output to another command. 

$ yes > /dev/null & (constantly outputs text to  /dev/null)

$ ps (to display processes in current shell. Make a not of the yes command process id. in this case it was 4212)

$ kill 4212

Do it one more time

$ yes > /dev/null & (constantly outputs text to  /dev/null)

$ ps (note the process id of the the recent yes command again)

$ killall yes (kills all instances of the yes command on the system. Becareful using 'killall' because this might be dangerous)

Now lets learn to start, diable, or check statuses of services using systemctl

You can also use 'systemctl' to check the status of a process, start it, or disable it

$ sudo systemctl <status|start|stop|disable> <SERVICE_NAME>

$ sudo systemctl status crond

$ sudo systemctl start crond

$ sudo systemctl stop crond

$ sudo systemctl disable crond  (ensures service isn't started at boot time )

$ sudo systemctl enable crond (ensures service starts at boot time )


<!-- % Managing process priorities -->

You can use the 'nice' utility to give priority to processes and make sure they have the required resources to run. Niceness values range from -20 (highest priority) to 19 (lowest priority). Default nice valueis 0

$ top (see the default nice value under the NI column for a user, and the COMMAND column that shows the process)

$ yes > /dev/null & (starts a yes process with the default nice value)

$ kill 8742 (kills yes process)

Starting a yes process with a specified nice

$ nice -n 19 yes > /dev/null (This process will take the longest to complete because of the nice of 19 reduces process resource demand in favour of any competing process)

Note that your user cloumn, cloud_user/cloud_u+, the nice value of 19 under NI, and 'yes' command under the COMMAND column

You can change the nice value of a running process using 'renice': 

  renice -<re_nice_value> -p <process_id>

$ ps (get the process id of above yes process)

$ renice -15 -p 9349 (new yes process nice value changed to -15)

<!-- Linux Users and Groups -->
All users of the system have their encrypted password stored in /etc/shadow that can only be access with admin/sudo permission. The file has the format:

<USER>:<ENCRYPTED_PASSWORD>

$ less /etc/shadow (You should get permission denied)

$ sudo less /etc/shadow (look for cloud_user and the note the encrypted password)

The /etc/passwd contains user account information using the format:

<USER>:x:USER_ID:GROUP_ID:USER:PATH_TO_USER_FILE_SYSTEM: DEFAULT_USER_SHELL

where x means the user password is stored in the /etc/shadow

Example:

ubuntu:x:1000:1000:Ubuntu:/home/ubuntu:/bin/bash (For Ubuntu system)

cloud_user:x:1001:1001::/home/cloud_user:/bin/bash (This is from Amazon Linux 2, note the double :: after userid:groupid)

The /etc/group contains user groups and IDs

$ sudo less /etc/group (Note groups returned and their ID)

A quick way to see user grp information, run: id <user_name>, which returms all user information and groups

$ id cloud_user

  Returned: uid=1001(cloud_user) gid=1001(cloud_user) groups=1001(cloud_user),10(wheel)

$ id ubuntu (for Ubuntu system)

To see all users currently logged in, run:

$ who

  cloud_user pts/1        2026-02-03 13:02 (c71f0fe92f1c.mylabserver.com)

To see users logged in and what they are currently doing:

$ w

  13:29:48 up 31 min,  1 user,  load average: 0.00, 0.00, 0.00
  USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
  cloud_us pts/1    c71f0fe92f1c.myl 13:02    0.00s  0.05s  0.00s w 

See see all users logged in for the past month:

$ last

  cloud_us pts/1        c71f0fe92f1c.myl Tue Feb  3 13:02   still logged in   
  reboot   system boot  4.14.355-280.713 Tue Feb  3 12:58 - 13:31  (00:32)    
  cloud_us pts/1        c71f0fe92f1c.myl Tue Jan 27 13:11 - 16:50  (03:38)    
  reboot   system boot  4.14.355-280.713 Tue Jan 27 12:46 - 13:31 (7+00:45)   
  cloud_us pts/1        c71f0fe92f1c.myl Mon Jan 26 22:11 - 02:00  (03:49)    
  cloud_us pts/1        c71f0fe92f1c.myl Mon Jan 26 22:02 - 22:03  (00:00)    
  cloud_us pts/1        c71f0fe92f1c.myl Mon Jan 26 21:59 - 22:00  (00:00)    
  reboot   system boot  4.14.355-280.713 Mon Jan 26 21:55 - 02:00  (04:04) 


<!-- Creating users ad groups -->
Before we create a new user account run the below to see that the user does not exist

$ ls -lah /home/jane

  ls: cannot access /home/jane: No such file or directory

Now create the user 'jane'

$ sudo useradd -m jane

runs the below to see that the new user, jane, now has a directory in home

$ ls -lh /home

drwxr-xr-x  4 root       root        36 Feb  3 13:35 .
dr-xr-xr-x 18 root       root       237 Jan 26 19:43 ..
drwx------  3 cloud_user cloud_user 111 May 12  2021 cloud_user
drwx------  2 jane       jane        62 Feb  3 13:35 jane

Move into the new user directory using sudo

$  sudo su - jane

$whoami (should return 'jane')

Note as 'jane' run:

$ ls -lah

drwx------ 2 jane jane  62 Feb  3 13:35 .
drwxr-xr-x 4 root root  36 Feb  3 13:35 ..
-rw-r--r-- 1 jane jane  18 Jul 15  2020 .bash_logout
-rw-r--r-- 1 jane jane 193 Jul 15  2020 .bash_profile
-rw-r--r-- 1 jane jane 231 Jul 15  2020 .bashrc

Although you did not create this file,they come from the /etc/skel/ directory that is short for Skeleton for all new users created - that is, all basic file that should be added/created along with a new user

Exit the terminal as jane to return to your cloud_user

$ exit

Now create a temporary password for jane user that she can change later

$ sudo passwd jane

  Enter new UNIX password: 
  Retype new UNIX password: 
  passwd: all authentication tokens updated successfully.

When jane logs in using the new password, she can change it by running the below and enteriing the password:

$ passwd

Let return the to the terminal as cloud_user


$ whoami (should return 'cloud_user')

Creating groups and allowing access to only those who need access

$ sudo mkdir /var/secret (creates a directory)

$ sudo groupadd secret-group (creates a group)

Create ownership of files using: 

chmod <user>:<group> <path_to_file_directory> (changes users and groups)
chmod :<group> <path_to_file_directory> (changes only groups)
chmod <user>: <path_to_file_directory> (changes only users)

creates ownership of /var/secret and can only be access by users in the group. Now lets add jane to the new group:

$ sudo chown :secret-group /var/secret/ 

$ sudo usermod -a -G secret-group jane (adds, -a, jane to an existing group, -G, secret-group)

$ sudo chmod g+w /var/secret/ (gives group, g, write access, +x, to the /var/secret/ directory)

Now lets test if jane can access the /var/secret/ directory
$ su jane (enter your password)

Now as jane run:

$ touch /var/secret/jane_file

$ vi /var/secret/jane_file

Enter any txt of choice, save, and exit editor. Still as jane run to read the file content:

$ cat /var/secrt/jane_file

Make jane and secret_grop the user and group that owns jane_file

$ chown jane:secret_group /var/secret/jane_file

check permission

$ ls -lah /var/secret/jane_file

  -rw-rw-r-- 1 jane secret-group 0 Feb  3 14:05 /var/secret/jane_file

Jane/secret_group have rw permissions and Others have only read permissions

make sure only jane/secret_group have access by removing the read permission for Others

$ chmod o-r /var/secrt/jane_file

Now check permission:

$ ls -la /var/secret/jane_file 
  -rw-rw---- 1 jane secret_group 20 Feb  4 15:12 /var/secret/jane_file

Switch user to cloud_user and try to read the file

$ su cloud_user (ebter cloud_user password )

$ cat /var/secret/jane_file

  cat: /var/secret/jane_file: Permission denied

Perfect!!! Just what we wanted

% Introduction to protecting user files

Each user on a Linux system has a home directory where all their files are stored. By default, these directories are located in /home/<username>. The permissions on these directories are set to ensure that only the owner has access to their files.

You can prevent other user from deleting files that the don't own using chmod with th +t flag

$ sudo mkdir /var/shared_dir (creates a shared directory)

Create a 2 files one for each user 

$ sudo touch /var/shared_dir/cloud_user.txt
$ sudo touch /var/shared_dir/jane_user.txt

Now assign the files ownership to the user( jane and cloud_user) and its group (secret_group and cloud_user)

$ sudo chown cloud_user:cloud_user /var/shared_dir/cloud_user.txt
$ sudo chown jane:secret_group /var/shared_dir/jane_user.txt

Check new files permissions and should look like this

drwxrwxrwx  2 jane       secret_group  75 Feb  4 18:13 .
drwxr-xr-x 20 root       root         283 Feb  3 14:03 ..
-rw-r--r--  1 cloud_user cloud_user     0 Feb  4 18:12 cloud_user_file.tx
-rw-rw----  1 jane       secret_group  20 Feb  4 15:12 jane_file
-rw-r--r--  1 jane       secret_group   0 Feb  4 18:13 jane_user_file.txt

Now add the  sticky bit so that users can only delete their own files and not those of other users

$ sudo chmod 1777 /var/shared_dir (gives read, write, execute permission to all users and the sticky bit, 1, that prevents users from deleting files they don't own)

or

$ sudo chmod +t /var/shared_dir (gives read, write, execute permission to all users and the sticky bit, 1, that prevents users from deleting files they don't own) OR 

Check for Sticky Bit

$ ls -dl /var/shared_dir (note the 't' where 'x' should be in Others at the end of drwxrwxrwt)

  drwxrwxrwt 2 jane secret_group 75 Feb  4 18:13 /var/secret/

Now switch users and try to delete another user file in /var/secret/ directory

$ su jane

$ rm /var/shared_dir/cloud_user.txt
  rm: cannot remove ‘/var/secret/cloud_user_file.txt’: Operation not permitted


$ su cloud_user

$ rm /var/shared_dir/jane_user.txt
  rm: cannot remove ‘/var/secret/jane_user_file.txt’: Operation not permitted

To remove the sticky bit run any of the below 2 commands:

$ sudo chmod 0777 /var/shared_dir

$ sudo chmod -t /var/shared_dir

After removing the sticky bit any jane and cloud_user can delete each others file

$ su jane (enter passwd when prompted)

$ rm /var/shared_dir/cloud_user.txt (enter y when prompted)
  rm: remove regular file ‘/var/shared_dir/cloud_user.txt’? y

$ su cloud_user (enter passwd when prompted)
$ rm /var/shared_dir/jane_user.txt (enter y when prompted)
  rm: remove regular file ‘/var/shared_dir/jane_user.txt’? y

$ ls -lah /var/secret (confrm the files were deleted)

Operations successful because sticky bit was removed

<!-- Creating Symbolic Links -->
A symbolic link is a special type of file that contains a reference/points to another file or directory in the form of an absolute or relative path. 
When you access the symbolic link, the operating system automatically redirects you to the target file or directory.

$ su cloud_user

e.g 1 Link the my-secret folder in /home/cloud_user/my-secret to /var/secret/ and creates it if not already exist

$ sudo ln -s /var/secret /home/cloud_user/my-secret (creates a symbolic link to /var/secret in cloud_user home directory. Note my-secret -> /var/secret/)

Note that the flags ln = link and s = symbolic . Both flags help you crete a sysmbolic links

$ ls -lah

total 28K
drwx------ 3 cloud_user cloud_user  128 Feb  5 14:56 .
drwxr-xr-x 4 root       root         36 Feb  3 13:35 ..
-rw------- 1 cloud_user cloud_user 4.5K Feb  4 21:01 .bash_history
-rw-r--r-- 1 cloud_user cloud_user   18 Jul 27  2018 .bash_logout
-rw-r--r-- 1 cloud_user cloud_user  193 Jul 27  2018 .bash_profile
-rw-r--r-- 1 cloud_user cloud_user  231 Jul 27  2018 .bashrc
lrwxrwxrwx 1 root       root         12 Feb  5 14:56 my-secret -> /var/secret/
drwx------ 2 cloud_user cloud_user   29 Nov 13  2019 .ssh
-rw------- 1 cloud_user cloud_user 5.5K Feb  4 14:36 .viminfo

Now ls my-secret should list all files in /var/secret/

$ ls -lah my-secret

total 4.0K
drwxrwxrwx  2 jane secret_group  23 Feb  4 18:48 .
drwxr-xr-x 20 root root         283 Feb  3 14:03 ..
-rw-rw----  1 jane secret_group  20 Feb  4 15:12 jane_file

e.g 2

$ sudo touch /home/home-script.sh

$ vi /home/home-script.sh

Add the below to the file:

  #!/bin/bash

  echo "This is my home script for automation!!"

Save and exit the file editor

$ sudo cat /home/home-script.sh (displays file content)

$ sudo chmod +x /home/home-script.sh (makes the script executable)

Still as cloud_user

$ touch cloud_user_scritp.sh

Link cloud_user_script to /home/home-script.sh

$ sudo ln -s /home/home-script.sh cloud_user_script.sh

$ ls -lah /home/cloud_user (shows the cloud_user_script.sh linked to /home/home-script.sh)

Running the cloud_user_script.sh will run /home/home-script.sh

$ ./cloud_user_script.sh

  This is my home script for automation!!

<!-- Hardening servers -->

Check for open ports (actually means which application is running and on what ports)
Closing ports means shutting down applications listening on those ports (not that there is an actual port to close). When traffic tries to hit a port with no running application listening on the port, the traffic is dropped.

$ sudo netstat -tuln (shows all listening ports and their associated applications)

You can also use the nmap package

$ sudo yum install nmap -y

ctive Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:31297           0.0.0.0:*               LISTEN     
tcp6       0      0 :::111                  :::*                    LISTEN     
tcp6       0      0 :::22                   :::*                    LISTEN     
udp        0      0 0.0.0.0:68              0.0.0.0:*                          
udp        0      0 0.0.0.0:111             0.0.0.0:*                          
udp        0      0 127.0.0.1:323           0.0.0.0:*                          
udp        0      0 0.0.0.0:839             0.0.0.0:*                          
udp6       0      0 :::111                  :::*                               
udp6       0      0 ::1:323                 :::*                               
udp6       0      0 fe80::8ff:f2ff:fe17:546 :::*                               
udp6       0      0 :::839                  :::*

$ nmap -v -sT localhost (-v = verbose, -s = scan, and -T = tcp protocol)

Starting Nmap 6.40 ( http://nmap.org ) at 2026-02-05 16:13 UTC
Initiating Ping Scan at 16:13
Scanning localhost (127.0.0.1) [2 ports]
Completed Ping Scan at 16:13, 0.00s elapsed (1 total hosts)
Initiating Connect Scan at 16:13
Scanning localhost (127.0.0.1) [1000 ports]
Discovered open port 25/tcp on 127.0.0.1
Discovered open port 111/tcp on 127.0.0.1
Discovered open port 22/tcp on 127.0.0.1
Completed Connect Scan at 16:13, 0.02s elapsed (1000 total ports)
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00030s latency).
Not shown: 997 closed ports
PORT    STATE SERVICE
22/tcp  open  ssh
25/tcp  open  smtp
111/tcp open  rpcbind
80/tcp open  httpd

The summary from nmap seem easier to read and helps to know what service to disable to close a specific port

To disable a service from starting at boot time run:

$ sudo systemctl disable <SERVICE_NAME>

To check for the actual name of the ssh service return in our nmap command, run:

$ sudo systemctl list-units --type=service --all

or better still

$ sudo systemctl list-units --type=service --all | grep ssh

  sshd-keygen.service                                   loaded    inactive dead    OpenSSH Server Key Generation
  sshd.service                                          loaded    active   running OpenSSH server daemon

e.g disabling and re-enabling the sshd service to close port 22

$ sudo systemctl disable <SERVICE_NAME>
$ sudo systemctl status <SERVICE_NAME>
$ sudo systemctl enable <SERVICE_NAME>
$ sudo systemctl status <SERVICE_NAME> (re-enables the service)

<!-- LAB  -->

Note that the LAB sies an Ubuntu system

























