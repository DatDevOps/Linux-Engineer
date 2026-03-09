System used for Lab is Ubuntu

# OBJECTIVE 1
<!-- Monitor Linux System Processes -->
Running, operating, administering, or even just using a Linux system requires monitoring! You have to be able to observe what is happening on the system to understand what is going on, and how to solve problems when things go wrong. Note the associated course is Getting Started with the Linux Command Line; this lab is based on concepts covered in that course.

1. The /proc directory is a special virtual file system that is used across most Linux distributions. The /proc directory contains a hierarchy of special files representing the state of the kernel, which allows processes (applications) and users to view the kernel's perspective of the system.

You will now take a look at this virtual file system with ls.

Type ls /proc and hit enter.

As the screenshot above shows, you'll see quite a few files and directories (colored dark blue) inside /proc

The numbered directories correspond with running processes (procs) on the system. Specifically, each number corresponds to a PID (Process IDentifier).

You will take a look at individuals processes and PIDs later in this challenge.

2. Now you will take a look at a few of the special files inside of /proc.

Type cat /proc/cpuinfo | less and press enter.

This shows you the CPU information about the current system.

Use the PageDown and PageUp keys to navigate the information.

(On a Mac, you can use fn+down-arrow, and fn+up-arrow respectively. Normally, in other environments, note you can use Ctrl+F and Ctrl+B respectively; in the lab environment, you can press each twice in a row, for example Ctrl+F then Ctrl+F.)

3. Type q to exit less.

4. Type cat /proc/stat and press enter.

This shows you statistics about the system, including the number of processes running, info on interrupts and time in each CPU state.

5. Type cat /proc/meminfo and press enter.

This outputs system memory information, including the total amount of memory, the free memory, memory available, and more.

6. Now take a look at the top running processes with the top utility.

Type top and hit enter.

top will display and list of processes, with the processes using the most CPU displayed at the top of the list.

You'll see systemd, top itself, tmux, containerd, and other processes making an appearance near the top of top, if you watch for a minute.

Note: top can be a useful way to quickly see what is utilizing the most CPU or memory on a Linux system. top's newer cousin, htop, is also a very worthy utility to take a look at.

7. Hit the q key to exit top.

8. If you need to look at overall system memory usage, then the free utility can be a great tool to reach for.

Type free -h and hit enter.

The -h flag makes the free memory information human readable, displaying numbers in B (Bytes), Ki (Kilobytes), Mi (Megabytes), or Gi (Gigabytes).

With free's output, you can see the total memory, the used memory, the free memory, the available memory. In addition, you can see a second row with details about the Swap memory.

9. You'll now circle back to processes and PIDs using the ps utility.

Type ps aux | less and hit enter.

Here you will find a long list of Linux processes with the process USER (the user running the process) as the first column, the PID (Process IDentifier) as the second column, a few other details, and finally, the COMMAND as the final column, which shows you what binary or script is the source of the process.

Note: ps aux is the BSD syntax (the Berkeley Software Distribution, an older unix distribution no longer in use) that became very popular and has stuck around. You can also use the "standard" GNU ps syntax, and run something like ps -elf to get similar output.

10. Hit q to exit less.

11. Another basic Linux system administration task you'll likely encounter is to check the system for free disk space.

For that, you'll use the df utility.

Type df -h and hit enter.

The -h option displays the output in human readable format. This means that it outputs sizes in megabytes (M) and gigabytes (G), instead of bytes.

From the output, you can see a listing of file systems in the Filesystem column, along with their Size, amount of space Used, amount of space Avail (available), Use% (the usage percentage), and the path that each file system is Mounted on.

You've done it! You've learned how to navigate and inspect the /proc virtual file system, and how to use numerous utilities, including top, free, df, and ps, to inspect and monitor your Linux system.

# OBJECTIVE 2
<!-- Explore System Log Files -->
These days, most modern Linux distributions rely on systemd, a suite of system components that unifies service configuration and behavior across Linux distributions.

journald is a component of systemd for collecting, storing and viewing log data. You will utilize journalctl to look at recent events that have been logged to the system with journald.

1. Type journalctl --help and hit enter.

The journalctl (journal control) utility allows you to query the journal and view log file entries. The output of the --help option shows you the help text, which can help you understand how to appropriately use the utility.,

2. Type q to exit the journalctl help page.

3. Type sudo journalctl --system -r and hit enter to output the system log in reverse chronological order.

Near the top of the log, you should see an entries similar to the following:

You can scroll to the right to see more of each line using the right-arrow key.

This log and output shows activities occurring at the system level, but not at the individual user level.

4.Hit q to exit the journalctl log view.

5.Type journalctl --user and hit enter.

In this user specific log, you should see an entry like:

pslearner : TTY=pts/1 ; PWD=home/pslearner ; USER=root ; COMMAND=usr/bin/journalctl --system -r

(You can scroll over to the right by pressing the right-arrow key.)

This is a log entry for the command that you ran in the previous task of this challenge!

So, you've learned how to look at system logs and user logs with journalctl, but what about logs for a specific service or application?

6.First, create a journal entry for the ssh unit by entering the following:

ssh pslearner@$(hostname)

and when prompted, type in **yes** and press enter:

Your output will likely be a bit different, including a slightly different host name and its IP.

This command uses ssh to attempt to securely login to a machine as pslearner (@ is the separator between the username to login as, and the target machine).

$(hostname) resolves to the current machine's name, so you are attempting to login remotely to the current machine. This will fail, which in this case is fine, as the goal here is just to create a journal entry to view.

7. Type sudo journalctl -r -u ssh and hit enter.

With this command, you are using root user privileges by specifying sudo, and calling the journalctl as before, but this time and you're using the -u (unit) option to specify that you only want to see logs from a specific unit. A unit is systemd's name for a service, or a program that runs in the background, listening for requests from other services or users.

You specify ssh as the unit/service name that you want to see logs from; you will see a log entry in the journal from the command in the previous task, which will be similar to the following:

8. Hit q to exit the journalctl log view.

9. Most Linux services and software that gets installed to Linux now integrates with systemd and journald, and can thus be managed using the systemd utilities. However, some legacy applications may not have integrated with systemd yet. For these applications, you may need to go hunting for log files in /var/log.

Type ls /var/log and hit enter.

This will display a listing of directories and files, including amazon.

10. Type sudo ls /var/log/amazon and hit enter.

11. Type sudo ls /var/log/amazon/ssm and hit enter.

A set of files is displayed, including amazon-ssm-agent.log

12. Type sudo cat /var/log/amazon/ssm/amazon-ssm-agent.log | less and hit enter.

The output will show you log entries from the amazon-ssm-agent. This is a great example of how to go searching for, and find, log files outside of journald, without journalctl.

Feel free to use the up/down arrows and the PageUp/PageDown keys to navigate the log file.

13. Hit q to exit less.

Nicely done! You have explored various logs on the file system, and you now know how to find and retrieve log information on Linux.

# OBJECTIVE 3

Manage Linux Users

You will now take a look at the files and utilities that can be used to manage Linux system users.

1. Type sudo cat /etc/passwd and hit enter.

The passwd file is output to your terminal.

At the very bottom of the output, you should see a line for pslearner, which is the user that you are currently logged in as.

This line will look as follows:

pslearner:x:1001:1001::/home/pslearner:/bin/bash

This file contains a colon delimited set of values per line that defines local system users and their properties. The first value is the username; in the second field, an x character represents that the user's encrypted password, which is stored in the /etc/shadow file; the third slot is for the user ID; the 4th is the primary group ID (both 1001 in this case); the fifth is the full name of the user (which is blank here); the the sixth is for the user's home directory; and finally, the user's login shell is stored as the seventh field (/bin/bash).

2. Type sudo cat /etc/shadow and hit enter.

On the last line, you'll see an encrypted representation of the user's password; you couldn't, for example use this string seen here as the user's password.

3. Type id and hit enter.

You should see output similar to this:

uid=1001(pslearner) gid=1001(pslearner) groups=1001(pslearner),27(sudo),118(admin)

This id utility prints user and group information for the current user when no command options are provided.

4. Type who and press enter.

You'll see some output which contains the following:

pslearner pts/1

The who utility prints information about users who are currently logged into the Linux system, including their username, terminal identifier, the date/time they logged in, and where they are connecting from.

5. Now you will add a user to the system!

Type sudo adduser newstaff and hit enter.

You'll be prompted to enter a new password.

6. Type in a few words in the New Password: prompt, and then hit enter. You will need to type this password twice to confirm it (but for this lab you will not need to remember this password, as you will not use it again).

Note: If you're unable to confirm the password twice, then the adduser utility will ask you if you'd like to Try again? type y and hit enter to try setting the password again.

7. In the Full Name []: prompt, type New Staff and hit enter.

8. At the Room Number []: prompt, keep it blank and just hit enter.

9. For the Work Phone []: prompt, type 234-567-8901 and hit enter.

10. Leave Home Phone []: empty and hit enter.

11. Leave Other []: empty and hit enter.

12. At the prompt Is the information correct? [Y/n] , type y and hit enter.

13. Type cat /etc/passwd | grep newstaff and hit enter.

You should see output similar to:

newstaff:x:1002:1002:New Staff,,234-567-8901,:/home/newstaff:/bin/bash

This confirms that you have created a new user on the system with the username newstaff!

14. Type id newstaff and hit enter.

Here you can see that the newstaff user is only assigned to one group, the newstaff group.

Now get newstaff assigned to another supplementary group!

15. Type sudo usermod -aG pslearner newstaff and hit enter.

There should be no output.

16. Type id newstaff and hit enter.

Voila! You can see that the newstaff user is now also assigned to the pslearner group.

Way to go! In this challenge, you learned how to find information about users, how to add a new user to the system, and how to add a new user to another group.


# OBJECTIVE 4

% Check and Apply Permissions to Files
Files are the core part of the Linux system. Files have permissions, and you need to know how to check and manipulate those permissions to successfully use a Linux system.

1. Start by creating a file and inspecting the default permissions.

2. Type touch dummyfile and hit enter.

3. Type ls and hit enter to show a file listing.

You should see the new file dummyfile listed.

Type ls -lah to show a detailed listing.

This will output a whole lot more lines, files, and directories than the prior command, but you want to focus on the line that looks like this:

-rw-rw-r-- 1 pslearner pslearner 0 Oct 23 05:13 dummyfile

The first character, - in this case, specifies whether or not the file is a directory. If it contains a d character, then it's a directory, if it does not, it will be a hyphen (-), which means it is a regular file and not a directory.

The next nine characters (rw-rw-r--) are grouped into groups of 3, corresponding to the permissions assigned to different users or groups on the system.

The first three characters, **rw-**rw-r--, represent the user. In other words, the user that created the file, or the user that owns the file. In this case the user has read (r) and write (w) permissions.
The second set of three characters, rw-**rw-**r--, represent the group; the same permissions, in this case, are applied to the group.
The last set of three characters, rw-rw-r-- represent other users on this Linux system; in this case, other users only have permissions to read the file.
The final slot or bit, by the way, which is blank (a -) in each set, represents execute permissions. This would be represented by an x if set; you will later set this permissions bit.

4. Type chmod 764 dummyfile and hit enter.

There should be no output.

Permissions are also represented by numerical values. Read (r) has a numerical value of 4, write (w) has a numerical value of 2, and execute (x) has a numerical value of 1.

If you want to give multiple permissions to a group using the numerical values, you add them together.

In this example, you used 7 for the first group (the user's permissions), which is a combination of read (4) + write (2) + execute (1). Assigning a value of 7 here gives the user owner of this file all of these permissions, read, write, and execute.

(64, in this case respectively set the group and other permissions, and are required, and further here, keep the permissions as they were before.)

5. Type ls -lah dummyfile and hit enter.

You can now see the updated permissions on the file, including the x in the fourth character slot, indicating that the user now has execute permission on the file.

The file also now appears in green, indicating that it is executable.

6. Another way to assign permissions is using the letter notation.

Type chmod g+x dummyfile and hit enter.

There should be no output.

7. Using this syntax, the g refers to group, the + sign indicates that you are adding a permission, and the x indicates that the permission you are adding is the execute permission.

Use the up arrow on your keyboard to get back to the command ls -lah dummyfile from your terminal history, and then hit enter.

You should now see that an x has appeared in the seventh character slot in the permissions listing, which indicates that the group now has execute permission on the file.

Congratulations! You can now check and update permissions on a file in Linux!

