This course uses **Centos 7** enterprise which is very similar to **RedHat 7** enterprise

<!-- Learning the Essentials of CentOS Enterprise Linux 7 Administration -->

Check your system release, run:

$ cat /etc/system-release

Count words

$ wc -l !$ (counts last command using last command !$)

$ date (gives the current date)

$ date --date "40 days" (gives date 40 later)

$ date --date "40 days ago" (gives date 40 b4 current date)

$ date --date "40 hours ago" (gives date 40 ago)

$ date --date "40 hours" (gives date 40 ago)

$ cal (shows current month calender)

$ cal -3 (shows a 3 month calender starting from a month b4)

$ cal -1 (shows current month calender. Ch)

$ cal 7 1966 (shows calender for July 1966)

When you run the tty command in your command line, it prints the file name of the terminal connected to your standard input (keyboard in this case). 

$ tty

/dev/pts/0

$ ls -l /dev/pts/0

crw--w----. 1 cloud_user tty 136, 0 Feb  8 23:59 /dev/pts/0

$ ls -l $(tty) [shorthand of the above using command substitution]

crw--w----. 1 cloud_user tty 136, 0 Feb  8 23:59 /dev/pts/0

$ pwd

/home/cloud_user

$ ls -l $(pwd) [shorthand of the above using command substitution]

total 4
drwxr-xr-x. 2 cloud_user cloud_user    6 Oct 15  2018 Desktop
drwxrwxr-x. 2 cloud_user cloud_user 4096 Mar  8  2019 Documents

<!-- Listig files -->

$ pwd (shows your home directory)

$ ls -li (shows the inode of file. Give metadata info . (current) and .. (parent) in a newly created directory without content)

"17121563" -rw-rw-r--. 1 cloud_user cloud_user       0 Mar  8  2019 code_ideas.odt
  "609173" drwxrwxrwx. 2 cloud_user cloud_user       6 Feb 15 22:44 d1
 "8552340" drwxrwxrwx. 2 cloud_user cloud_user       6 Feb 15 22:44 d2

$ ls (list files)

$ ls -a  (list files including all hidden)

$ ls -aF (list files along with thier type. In this case Blue colour seem to be for directories, back for just files and green/RED with @ appended for links)

$ ls -aF /etc/ (list files along with thier type. In this case Blue colour seem to be for directories, back for just files and green/RED with @ appended for links. Files with  * appended are executables)

$ ls -lrt (list files in reverse other using the timestamp. List files from the last  modified to most recently modified)

$ ls -ld (list only direectory)

$ ls -lah (list all files in human readable format)

Check for storage device and partitions

$ lsblk (list block devices)

$ ls -lah /dev (shows  line blok device and partition)

$ ls -lah /dev/nvme0n1 (brw-rw---- shows it is a block device - prefixed with 'b')

brw-rw----. 1 root disk 259, 0 Feb  8 23:36 /dev/nvme0n1 

$ ls -lah /dev/nvme0n1p1 (brw-rw---- shows it is a block device - prefixed with 'b')

brw-rw----. 1 root disk 259, 0 Feb  8 23:36 /dev/nvme0n1p1 

$ ls -l /etc/system-release /etc/centos-release /etc/redhat-release 

-rw-r--r--. 1 root root 37 May 21  2024 /etc/centos-release
lrwxrwxrwx. 1 root root 14 Jul  2  2024 /etc/redhat-release -> centos-release
lrwxrwxrwx. 1 root root 14 Jul  2  2024 /etc/system-release -> centos-release

Copies files from one location to another using the CP

$ cp /etc/host .  (copied file to current direectory with same name)


$ cp /etc/passwd ./my_new_passwd (copied file to current direectory with a new name my_new_passwd)


Move or renaming files using the MV command

$ cd Desktop

$ touch movable_file (create a file)

$ mv moveable_file immovable_file (renames the file in same location)

$  mv moveable_file ./moved/immovable_file (moves the file from current location to a new folder, moved [which it creates if it does not exist], while retaining same name)

$  mv ./moved/immovable_file ./moved/move_file_2 (moves the file from current location to a new folder, moved [which it creates if it does not exist], giving it a new name)

# working with directory

$ mkdir my_new_directory (creates a new directory)  

$ mkdir -p my_new_directory_2/sub_directory (creates a new parent directory, my_new_directory_2, with a sub directory sub_directory. It will throw an error without the -p saying "No such file exist". So the -p makes the command to create the parent directory if it does not exist)

$ rmdir my_new_directory/sub_directory (removes the sub directory)

$ rmdir my_new_directory (removes the parent directory)

$ rm -rf sales (removes the sales directory recursively)

$ mkdir one two (creates 2 folders: one and two)

$ touch one/file{1..5} (creates file1=file5 in folder one)

$ cp -R one two (copies folder one to two - recursively copies all folder one content)

$ sudo yum install tree (installs the directory structure utility)

$ tree twotree tw 

two/
└── one
    ├── file1
    ├── file2
    ├── file3
    ├── file4
    └── file5

$ mkdir -m 777 d1 ( creates a directory that gives read, write, and execute permissions to users, groups, and others)    

$ mkdir -m 700 d2 ( creates a directory that gives read, write, and execute permissions to users only)

# Links

