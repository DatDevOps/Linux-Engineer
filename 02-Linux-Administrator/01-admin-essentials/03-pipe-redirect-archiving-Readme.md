<!-- Redirect -->

# STDOUT (Standard output redirect is denoted as > or 1> to overwrite and >> or 1>> to append)

$ > file1 (creates a new file that is empty because no content was redirected)

$ echo "Hello World" > newfile

$ cat newfile 

  Hello World

$ > newfile 

$ cat newfile

  No content in file because no content was redirect when "$ >1 newfile " we ran which overwrote

$ ls -lah > newfile (writes the result of "ls -lah" to newfile)

$ cat newfile 

$ ls -lah 1>> newfile (appends the result of "ls -lah" to newfile)

$ cat newfile 

$ df -h 1> file1 (writes the result of "df -h" to file1)

$ cat file1

$ df -h 1>> file1 (appends the result of "df -h" to file1)

$ cat file1

$ set -o (Bash will print a list of all shell options and show whether each is currently: on/enabled or off/disabled)

Using set -o <option> (enable an optio)

Using set +o <option> (disable an option)

Notice that the nocclobber is turned off

$ set -o noclobber

$ set -o | grep noclobber

  noclobber       on

NoClobber prevents you from overwriting the content of an existting file but allows you to append to it. Use the pipe option, |, to force overwrite

$ date +%F+%T > file1

-bash: file1: cannot overwrite existing file

$ date +%F+%T > file2 (Able to write to file because it does not exist)

$ date +%F+%T > file2

  -bash: file2: cannot overwrite existing file

$ date +%F+%T >| file2 (forces overwrite with the | option)

$ date +%F+%T >> file2 (allows append operations)

# STDERR

$ ls -lah /etcw > err (Although it creates the file err, the error output below is not redirected to err)

  ls: cannot access /etcw: No such file or directory

$ ls -lah /etcw 2> err (because err already exist [remember we turn noclobber on/enable above] we get the below)

  -bash: err: cannot overwrite existing file

$ ls -lah /etcw 2>| err (forces overwrite to the existing file err)

$ cat err

  ls: cannot access /etcw: No such file or directory

$ find /etc/ -type l (returns a long list of files that are links in /etc, including permission denied error)

$ find /etc/ -type l 2> /dev/null (returns only files we have permission to and redirects the permission denied files to /dev/null)

$ find /etc/ -type l &> err.txt (redirects standard output and standard error into the same file. The files we have permissions to view are top of the list and files with permission denied are list last)

% # STDIN (Standard input denoted by < or less than symbol)

$ df -hlT

$ df -hlT > diskfree

$ cat diskfree 

  Filesystem     Type      Size  Used Avail Use% Mounted on
  devtmpfs       devtmpfs  864M     0  864M   0% /dev
  tmpfs          tmpfs     903M     0  903M   0% /dev/shm
  tmpfs          tmpfs     903M   17M  886M   2% /run
  tmpfs          tmpfs     903M     0  903M   0% /sys/fs/cgroup
  /dev/nvme0n1p1 xfs        20G  7.2G   13G  36% /
  tmpfs          tmpfs     181M   20K  181M   1% /run/user/1002

$ while read line; do     echo "$line"; done < diskfree (reads from the file using the while loop) OR

$ cat diskfree | while read line; do     echo "$line"; done (OR)

$ read var < diskfree (This reads only the first line from diskfree into variable var.)

$ echo $var (Print only the fist line of the file)

# HERE DOCUMENTS
After the first <<END press enter to get the prompt and do same after the >END to exist the promt

$ cat > myHereDoc <<END
> This is a sample of
> using HERE Documents to 
> create small files or scripts
> END

$ cat myHereDoc 
  This is a sample of
  using HERE Documents to 
  create small files or scripts

<!-- Piping/pipelines -->

# Unamed pipes

$ ls -lah | wc -l (list files in directory, ls -lah, then count, wc, not worlds but lines. wc would have just counted the words )

  34

$ head -n1 /etc/passwd

  root:x:0:0:root:/root:/bin/bash

$ cut -f7 -d: /etc/passwd (f = field and d=delimiter)

  /bin/bash
  /sbin/nologin
  /sbin/nologin
  ...
  ...

$ cut -f7 -d: /etc/passwd | sort (sorts the above result)

$ cut -f7 -d: /etc/passwd | sort | uniq (returns only the uniques items from the result)
  /bin/bash
  /bin/sync
  /sbin/halt
  /sbin/nologin
  /sbin/shutdown

$ cut -f7 -d: /etc/passwd | sort | uniq | wc -l

  5

# Named pipes (You create a named pipe using 'mkfifo'. It queues the info but never holds it)
A named pipe (FIFO) is a special file that allows inter‑process communication (IPC). One process writes to it, another reads from it—just like a regular pipe (|), but persistent in the filesystem.


$ mkfifo myNamedPipe

$ ls -lah myNamedPipe (notice the 'p' at the start of the output, like you would similarly observe for 'b' for block device, and 'l' for links)

  prw-rw-r--. 1 cloud_user cloud_user 0 Feb 19 16:15 myNamedPipe

$ ls -F myNamedPipe (notice the |' at the end of the output)

  myNamedPipe|

SSH into the machine you are using from another machine, terminal B

In terminal A, run(This command will block until a reader is available because pipes require both ends.):

$ echo "Hello from process A" > mypipe  

In terminal B, run:

$ cat < mypipe

  Hello from process A

You can tail a named pipe

Terminal B:

$ tail -f mypipe

Terminal A:

$ echo "Event 1" > mypipe
$ echo "Event 2" > mypipe

Terminal B will output:

  Event 1
  Event 2

# Command tee (enables you to redirect to file and screen and may allow to read a file you don't have access to read with 'sudo')  

$ ls -lah | tee tee.txt (writes to screen and tee.txt. Note that it will overwrite the content of tee.txt if it exist)

  total 120K
  drwx------. 13 cloud_user cloud_user 4.0K Feb 19 16:35 .
  drwxr-xr-x.  4 root       root         38 Oct 16  2019 ..
  -rw-rw-r--.  1 cloud_user cloud_user  118 Feb 19 13:37 1
  ...
  ...
  ...

$ cat tee.txt 

  total 120K
  drwx------. 13 cloud_user cloud_user 4.0K Feb 19 16:35 .
  drwxr-xr-x.  4 root       root         38 Oct 16  2019 ..
  -rw-rw-r--.  1 cloud_user cloud_user  118 Feb 19 13:37 1
  ...
  ...
  ...

$ > tee.txt (overwrites content/ removes content since we are redirecting no string or character)

$ ls -lah | head -n 5 |  tee tee.txt (writes the output to screen and to the file)

  total 120K
  drwx------. 13 cloud_user cloud_user 4.0K Feb 19 16:35 .
  drwxr-xr-x.  4 root       root         38 Oct 16  2019 ..
  -rw-rw-r--.  1 cloud_user cloud_user  118 Feb 19 13:37 1
  -rw-------.  1 cloud_user cloud_user 7.6K Feb 19 16:40 .bash_history

$ cat tee.txt

  total 120K
  drwx------. 13 cloud_user cloud_user 4.0K Feb 19 16:35 .
  drwxr-xr-x.  4 root       root         38 Oct 16  2019 ..
  -rw-rw-r--.  1 cloud_user cloud_user  118 Feb 19 13:37 1
  -rw-------.  1 cloud_user cloud_user 7.6K Feb 19 16:40 .bash_history

$ ls -lah | tail -n 5 | tee -a tee.txt (writes the output to screen and append it to the file)

  drwx------.  2 cloud_user cloud_user   28 Oct  8  2018 .ssh
  -rw-rw-r--.  1 cloud_user cloud_user  252 Feb 19 16:41 tee.txt
  -rw-------.  1 cloud_user cloud_user 3.9K Feb 18 17:42 .viminfo
  drwxr-xr-x.  2 cloud_user cloud_user 4.0K Feb 19 12:46 .vnc
  -rw-------.  1 cloud_user cloud_user 9.8K Feb 19 12:46 .Xauthority

$ cat tee.txt 

  total 120K
  drwx------. 13 cloud_user cloud_user 4.0K Feb 19 16:35 .
  drwxr-xr-x.  4 root       root         38 Oct 16  2019 ..
  -rw-rw-r--.  1 cloud_user cloud_user  118 Feb 19 13:37 1
  -rw-------.  1 cloud_user cloud_user 7.6K Feb 19 16:40 .bash_history
  drwx------.  2 cloud_user cloud_user   28 Oct  8  2018 .ssh
  -rw-rw-r--.  1 cloud_user cloud_user  252 Feb 19 16:41 tee.txt
  -rw-------.  1 cloud_user cloud_user 3.9K Feb 18 17:42 .viminfo
  drwxr-xr-x.  2 cloud_user cloud_user 4.0K Feb 19 12:46 .vnc
  -rw-------.  1 cloud_user cloud_user 9.8K Feb 19 12:46 .Xauthority

$  sudo echo '127.0.0.1 bob' >> /etc/hosts (You get an error because the sudo echo '127.0.0.1 bob' runs as sudo user but the redirect /etc/hosts is ran using the regular user credentials)

  -bash: /etc/hosts: Permission denied

$ echo '127.0.0.1 bob' | sudo tee -a /etc/hosts (writes the output to screen and the /etc/hosts file)

  127.0.0.1 bob

$ tail -n 1 /etc/hosts

  127.0.0.1 bob


<!-- Achiving -->

# Tar

$ tar -cf doc.tar /usr/share/doc (creates [c] an archive with file [f] named doc.tar containing file doc from /usr/share. Note that it keeps the folder heirrachy with the is extracted, i.e when extracted files will be in usr/share/doc)

$ ls -lah doc.tar

  -rw-rw-r--. 1 cloud_user cloud_user 59M Feb 19 17:24 doc.tar

$ file doc.tar (shows type of file)

  doc.tar: POSIX tar archive (GNU)

$ tar -cvf doc.tar /usr/share/doc (sames as above but with the v switch, verbose, which prints the archiving actions to the screen in realtime)

$ tar -cvvf doc.tar /usr/share/doc (sames as above but shows file permissions)

$ tar --list --file=doc.tar (returns content of tar/compressed files to screen without uncompressing it)

$ tar -tf doc.tar (same as above)

$ tar -tvf doc.tar (same as above but with file permissions added to the output on screen)

$ tar --extract --verbose --file doc.tar (extracts individual files to current user directory, $PWD)

$ tar -xvf doc.tar (same as abov, only a shorthand)

$ mkdir backup && cd backup

$ cp /etc/hosts .

$ cp /etc/hostname .

$ cp /etc/services .

$ cd ~

Creates a compreesed file my0.tar with an incremental backup file, my.snapshot or any name you give it, that is used to backup the backup folder. The backup folder will contain all backed up files without compression.

Note that the my.snapshort file contains a hash used for the backup

Note this for next command: g=incremental backup. But because this is the first time of doing a backup of the backup folder, it does a complete backup

$ tar -cvf my0.tar -g my.snapshot backup 

  tar: backup: Directory is new
  backup/
  backup/hostname
  backup/hosts
  backup/services

$ ls 
  backup  Desktop  doc  doc.tar  Documents  my0.tar  my.snapshot  ntp  ntp.conf  ntp.new  usr

$ cat my.snapshot 

GNU tar-1.26-2
17715237514006271201771523530928503054663059073449backupNhostnameYhostsNservices

$ echo hi >> backup/hosts (Modifies one of the files)

Now lets creates an incremental backup. Note that the returned results show that only backup/hosts is being backup since only that file change

$ tar -cvf my1.tar -g my.snapshot backup 
  backup/
  backup/hosts

$ rm backup/hostname 

$ tar -cvf my2.tar -g my.snapshot backup

  backup/

$ rm -rf backup/ 

$ tar -xvf my0.tar -g /dev/null (restores the backup folder and subfolder/files from day 0)

  backup/
  backup/hostname
  backup/hosts
  backup/services

$ tar -xvf my1.tar -g /dev/null (restores the backup folder and subfolder/files from day 1. showing that only hosts file has changed from previous the backup done )

  backup/
  backup/hosts

$ tar -xvf my2.tar -g /dev/null (restores the backup folder and subfolder/files from day 2. It deletes hostname file bcs restored from the previous backup bcs we deleted it b4 creating this backup. It is able to know bcs of my.snapshot)

  backup/
  tar: Deleting `backup/hostname'

$ ls backup/ (note that it only contains the file left in it b4 we deleted the backup folder above)

  hosts  services

$ cat backup/hosts (contains 'hi' that was added)

  127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
  ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
  # Cloud Server Hostname mapping
  172.31.111.80   c71f0fe92f2c.mylabserver.com
  hi

# Compression (for better size and free more space)

% # GZIP

Note the sizes of the files in the below output, especially my0.tar which is 660k

$ ls -lah *my*

  -rw-rw-r--. 1 cloud_user cloud_user 660K Feb 19 17:54 my0.tar
  -rw-rw-r--. 1 cloud_user cloud_user  10K Feb 19 17:55 my1.tar
  -rw-rw-r--. 1 cloud_user cloud_user  10K Feb 19 18:12 my2.tar
  -rw-rw-r--. 1 cloud_user cloud_user   99 Feb 19 18:12 my.snapshot

$ gzip my0.tar (the my0.tar is now replaced with my0.tar.gzand is now smaller in size, 134k)

$ file my0.tar

  my0.tar.gz: gzip compressed data, was "my0.tar", from Unix, last modified: Thu Feb 19 17:54:03 2026

$ ls -lah | grep my0*

  -rw-rw-r--.  1 cloud_user cloud_user 134K Feb 19 17:54 my0.tar.gz

$ gunzip my0.tar.gz (unzips the file back to it's original form with .tar ext)

$ ls -lah | grep my0*

  -rw-rw-r--.  1 cloud_user cloud_user 660K Feb 19 17:54 my0.tar

<!-- #BZIP (better than gzip) -->

$ bzip2 my0.tar 

$ file my0.tar.bz2 

  my0.tar.bz2: bzip2 compressed data, block size = 900k

$ ls -lah | grep my0* (note the size is 12k less than when we used gzip)

  -rw-rw-r--.  1 cloud_user cloud_user 122K Feb 19 17:54 my0.tar.bz2

$ bunzip2 my0.tar.bz2 (restores it to it'ss original tar file of 660k size)

$ ls -lah | grep my0*

  rw-rw-r--.  1 cloud_user cloud_user 660K Feb 19 17:54 my0.tar  

# combining tar, gzip, bzip

Archives home directory as tar. The 'time' options shows us the time used for the process. We are interested in 'real' time which increases from tar => gz => bz

$ time tar -cvf  com.tar $HOME (Creates a tar file. Note the 'real' time at the end of the output)

$ ls -lah | grep com.tar

  -rw-rw-r--.  1 cloud_user cloud_user 194M Feb 19 19:15 com.tar

$ time tar -cvzf  com.tar.gz $HOME (Creates a gz file using the 'g' switch. Note the 'real' time at the end of the output - it should take more time than tar)

$ ls -lah | grep com.tar.gz
  
  -rw-rw-r--.  1 cloud_user cloud_user 168M Feb 19 19:17 com.tar.gz

$ time tar -cvjf  com.tar.bz2 $HOME (Creates a gz file using the 'j' switch. Note the 'real' time at the end of the output - it should take more time than gz)

$ ls -lah | grep com.tar.bz2 (file size should be less than .gz but not so. Figure out why)
  
  -rw-rw-r--.  1 cloud_user cloud_user 517M Feb 19 19:25 com.tar.bz2


