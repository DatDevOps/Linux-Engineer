**Rememeber to 'sudo -i (login in as root) or 'sudo su -' (same as sudo -i) or 'sudo su' (switches user to root access) to have access to the Linux root directory and other files not in the home/user directory (i.e ~ or /home)

***Learn About the Linux System***

1. Start by running the uname command command (a GNU utility) to print the kernel name. Type uname and then hit enter.

    ~# uname

You should see the output Linux.

2. You will now check the Linux kernel version on the lab system using uname with an additional -a OPTION, along with the base command. These options are often called flags or parameters.

In the bash shell, type the command uname -a and then hit enter. This will display information about the current Linux kernel version in use, along with additional information.

3. On most Linux distributions, you can get more OS information from 1 or more release files located in the /etc directory.

Use the ls GNU utility to list files that end with the name release in this directory.

  ~# ls /etc/*release and hit enter.

In the bash shell, the asterisk character is a wildcard, also known as a globbing pattern, that allows for expansion. When using *release, the asterisk (*) expands to include any file name that ends in release, with any characters before it, like /etc/os-release, which you should see in your output.

Now you will get some information on the Operating System using the cat GNU utility.

4. Type the command cat /etc/*release and then hit enter, to find information on the Operating System.

  ~# cat /etc/*release

The cat command will concatenate and output the contents of all of the files that match the file name parameter that is specified after it.

5. Lastly for this challenge, you'll again use the ls ( list ) GNU utility to look at the /boot partition and view the available kernel versions.

Type the command ls -lah /boot and then hit enter to list all the files in the /boot directory.

The l option lists one file per line, the a option lists all files, including hidden files, and the h option displays human readable file sizes.

  ~# ls -lah /boot

Notice that in this example screenshot above, the active Linux kernel is version 6.2.0-1011-aws. You can determine this because the initrd.img and vmlinuz boot files are symbolically linked (pointed) to boot files with this kernel version specified.

***Explore Linux File System Directories, Disks, and Partitions***

1. Type the command ls -lah / and hit enter. The output should display all files in the root directory.

   ~# ls -lah /

  - You can see a few symbolic links defined here, which are similar to shortcuts or pointers. The /bin directory is linked ( indicated by -> ) to usr/bin, for example.
  
  - The /boot directory contains boot files and kernel files, as you noticed in the previous challenge.
  
  - The /etc directory often contains configuration files for system packages and other software.
  
  - The /home directory is used to store the home directories for the various system users. User specific files are stored here. The /home directory is often split out to its own partition, as the data can be quite   variable and grow to a large size.
  
  - The /opt directory contains "optional" software, and is the location that is often used for installing additional software on the system by System Administrators.
  
  - The /var directory contains "variable" files and is most often used to store logs. This is another directory that is often partitioned off on its own, due to the aggressive file size growth that commonly occurs here.

2. Run the ls command to explore the contents of the var top level directory: ls -lah /var

    ~# ls -lah /var
   
4. Now find the block devices on the system! Type the command lsblk and hit enter to list out block devices.

    ~# lsblk

  In this example, you can ignore all the loop block devices, which are pseudo devices that make a file accessible as a block device.

  You'll focus on nvme0n1 disk and its part (partition) children: here you can see that the nvme0n1 disk has three partitions,
  
  - nvme0n1p1 with 49.9 GB of space,
  - nvme0n1p14 with 4 MB, and
  - nvme0n1p15 with 106 MB of space.
    
  You can see the root file system, indicated by a single forward slash /, is mounted on the partition nvme0n1p1.

4. Now look at disk partitions! Type the command sudo fdisk -l /dev/nvme0n1 and hit enter.

    ~# sudo fdisk -l /dev/nvme0n1
  
   In this screenshot example, you can see that the device or partition /dev/nvme0n1p14 is a BIOS boot type, and /dev/nvme0n1p1 is a Linux filesystem.

6. How much space is being used on the current file system? Are you close to running out of space? Time to find out! Type the command df -h and hit enter.

    ~# df -h

   In this example screenshot, you can see that /dev/root is your primary filesystem, it has 49G (GB) of Available disk space, and it is 9% used.

***Become Familiar with Shell Variables***

Shell variables allow you store a piece of information with a specific name, which can then be used at a later time. This is very similar to variables in most modern programming languages.

The shell already has many built in variables, including for the shell executable itself! You will use that one to start.

1. Type echo $SHELL and hit enter. Should return the name of the shell where you are running this command, e.g /bin/bash

   ~# $SHELL

You can call a variable name by prefixing it with the $ symbol. Shell variables are typically UPPERCASE with underscores (_) used to delimit words in the name. In this example, you used the echo command to write out (or echo) the value of the shell variable named SHELL to the standard output (also known as stdout). You can see here that the value assigned to this variable is /bin/bash , which is the location of the binary executable of the shell being used.

2. So you can see that there is a "built in" shell variable named SHELL, but what about the other shell variables available on the system? To find those, you'll use the env (environment) command. Type env and hit enter.

     ~# env
   
4. You can also set your own custom shell variable! Type OPEN_SOURCE="rocks!" and hit enter.

   ~# OPEN_SOURCE="rocks!"

  Note:
  - You cannot have any spaces between the shell variable name, the equal sign, and the value that you are assigning to the shell variable.
  - Values are often surrounded in double quotes or single quotes to avoid shell expansion of the values.
  - You set the variable's value without using the $ symbol, but when you reference it, you must prepend the $ symbol to the variable name to let the bash shell know that you are referencing a variable.

  Run the command below to make sure your new variable is in the list of variabels:

  ~# env | grep OPEN_SOURCE

6. Then type echo $OPEN_SOURCE and hit enter. You should see output similar to the below screenshot.

   ~# echo $OPEN_SOURCE

8. Type bash and hit enter to launch a new shell session. Type echo $OPEN_SOURCE and hit enter.

   ~# bash
   ~# $OPEN_SOURCE

Notice that nothing is printed or echo'ed out. What happened to your shell variable?
When you set a shell variable's value, by default, it only exists in its current shell environment. 
If you want it to persist across other shell sessions, you need to export it. You will try that next. for now exit the new shell

  ~# exit

9. You can overwrite or update the value of a shell variable by initializing it or setting it again, with a new value.

  ~# OPEN_SOURCE="persists!"
  ~# echo $OPEN_SOURCE

10. Type export OPEN_SOURCE and hit enter. This allows the shell variable to persist across other shell sessions/environments.

    ~# export OPEN_SOURCE

11. Start a new shell

    ~# bash

12. Print custom variable

   ~# echo $OPEN_SOURCE

  Run the command below to make sure your new variable is in the list of variabels:

   ~# env | grep OPEN_SOURCE

13. To delete a shell variable, use unset:

  ~# unset OPEN_SOURCE
  
Note no $ is used, similar to when setting it.

You can use echo and env to notice that the variable no longer exists in this shell.

  ~# echo $OPEN_SOURCE
  ~# env

Here are some things to try out:

- Use the cd command to further explore the directories and locations on the file system.
- Use the ls command to check for files and symbolic links on the file system.
- Use the cat command to view the contents of a file that you have not yet inspected.
- Explore the shell variables that are available to you and learn what information they provide. You have unlimited power within this virtual
























  
