<!-- Reading files and the VIM Editor -->

# Reading files

$ cat /etc/hosts (returns host names and domain)

$ cat /etc/hostname (returns domaain names)

$ wc -l /etc/services (word count the total number of lines in the file)

  11176 /etc/services

$ less cat /etc/services (displays outpage in paginated format to page through using keyboard pageup/pagedown)

$ head cat /etc/service (displays top 10 lines)

$ head -n -3 cat /etc/service (displays top 3 lines)

$ tail cat /etc/service (displays bottom 10 lines)

$ tail -5 cat /etc/service (displays bottom 5 lines)


# Search files

$ yum list installed | grep kernel (returns list with the keyword 'kernel' anywhere in its name)

$ yum list installed | grep ^kernel (returns list that starts/begin with the keyword 'kernel')

$ sudo yum install ntp -y (installs the ntp package)

$ ls -lah /etc/ntp
total 20K
drwxr-xr-x.   3 root root   49 Feb 16 17:47 .
drwxr-xr-x. 109 root root 8.0K Feb 16 17:47 ..
drwxr-x---.   2 root ntp    15 Feb 16 17:47 crypto
-rw-------.   1 root root   86 Nov 27  2019 keys
-rw-r--r--.   1 root root   74 Nov 27  2019 step-tickers

$ sudo wc -l /etc/ntp/crypto/pw

  5 /etc/ntp/crypto/pw

$ sudo cat /etc/ntp/crypto/pw (returns below)

# This file is included from /etc/ntp.conf. It specifies the password used to
# decrypt files containing private keys and identity parameters. The password
# is required only if the files have been encrypted.
#

$ ls -alh /etc/ntp.conf 

  -rw-r--r--. 1 root root 2.0K Nov 27  2019 /etc/ntp.conf

$ wc -l /etc/ntp.conf 

  58 /etc/ntp.conf



$ sudo cp -R /etc/ntp .

$ ls -lah ntp/
  total 12K
  drwxr-xr-x.  3 cloud_user cloud_user   49 Feb 16 17:54 .
  drwx------. 13 cloud_user cloud_user 4.0K Feb 16 17:54 ..
  drwxr-x---.  2 cloud_user cloud_user   15 Feb 16 17:54 crypto
  -rw-------.  1 root       root         86 Feb 16 17:54 keys
  -rw-r--r--.  1 cloud_user cloud_user   74 Feb 16 17:54 step-tickers

$ sudo wc -l ntp/crypto/pw 

  5 ntp/crypto/pw

$ sudo cp -R /etc/ntp.conf .

$ sudo wc -l ntp.conf

  58 ntp.conf

$ grep server ntp.conf (lines that contains the word 'server')
  # Use public servers from the pool.ntp.org project.
  server 0.centos.pool.ntp.org iburst
  server 1.centos.pool.ntp.org iburst
  server 2.centos.pool.ntp.org iburst
  server 3.centos.pool.ntp.org iburst
  #broadcast 192.168.1.255 autokey        # broadcast server
  #broadcast 224.0.1.1 autokey            # multicast server
  #manycastserver 239.255.254.254         # manycast server

$  grep '\bserver\b' ntp.conf (uses word boundary, b, to return lines that contains the word 'server')
  # Use public servers from the pool.ntp.org project.
  server 0.centos.pool.ntp.org iburst
  server 1.centos.pool.ntp.org iburst
  server 2.centos.pool.ntp.org iburst
  server 3.centos.pool.ntp.org iburst
  #broadcast 192.168.1.255 autokey        # broadcast server
  #broadcast 224.0.1.1 autokey            # multicast server
  #manycastserver 239.255.254.254         # manycast server

$ type grep (shows you the alias)

  grep is aliased to `grep --color=auto'

$ grep ^server ntp.conf (lines that begin with server)

$ sudo yum install words -y (gives a dictionary file we can use to practice searching)

$ grep -E 'ion$' /usr/share/dict/words (uses grep Enhanced serach returns words ending in 'ion')

$ grep -E '^po..ute$' /usr/share/dict/words (returns words that starts with 'po', two letters in between, and ends in 'ute')

$ grep -E '[aeiou]{5}' /usr/share/dict/words (words that contains 5 of these vowels in a row)

$ grep -E '[aeiou]{6}' /usr/share/dict/words (words that contains 6 of these vowels in a row)

Applies deletion to only standard output and not the file. So it returns the content of ntp.conf deleting lines that start with #, ^#, and lines that start with whitespace, indicated by $, ^$. Note that the  actual file is not edited but the output is. The colon seperates the list of action to run when more than one

$ sed '/^#/d ; /^$/d' ntp.conf 

This actually edits the file

$ sed -i '/^#/d ; /^$/d' ntp.conf (don't run if you don't want to change file). OR you can put it in a function in a file as a script:

function clean_file{
  sed -i '/^#/d ; /^$/d' $1
}

Now run the script:

$ clean_file ntp.conf

# Comparing files:

$ cp ntp.conf ntp.new

$ cp ntp.conf ntp.new

$ echo new >> ntp.new (Appends the word 'new' to the end of the file content)

$ diff ntp.conf ntp.new 

  11a12
  > new

File ntp.conf has 11 lines and a new line with the word 'new' was appended/added, 'a', to ntp.new making 12 lines

Now change this line in ntp.new: server 0.centos.pool.ntp.org iburst ==> server 20.centos.pool.ntp.org iburst

Run: 

$ diff ntp.conf ntp.new 

  5c5
  < server 0.centos.pool.ntp.org iburst
  ---
  > server 20.centos.pool.ntp.org iburst
  11a12
  > new

Note the new 5c5. It means comparing, c, line 5 to line 5 in both files. ' < server 0.centos.pool.ntp.org iburst' content of ntp.conf and ' < server 0.centos.pool.ntp.org iburst' content of ntp.new 


$ diff /etc/ntp.conf ntp.new 
  1,3d0
  < # For more information about this file, see the man pages
  < # ntp.conf(5), ntp_acc(5), ntp_auth(5), ntp_clock(5), ntp_misc(5), ntp_mon(5).
  < 
  5,7d1

  ....
  ....
  ....
  # CVE-2013-5211 for more details.
  < # Note: Monitoring will not be disabled with the limited restriction flag.
  58a12
  > new

Shows line 1-3 has be deleted - d. Same for other lines to the end of your output


For comparing binaryfiles use the checksum

$ md5sum /usr/bin/passwd 

  3e522d6f3bf5cf88bb77e9ff3d138543  /usr/bin/passwd

# Finding files

$ find /usr/share/doc/ -name '*.pdf' (find files /usr/share/doc/ ending with .pdf )

  /usr/share/doc/libsrtp-1.4.4/libsrtp.pdf
  /usr/share/doc/libtasn1-4.10/libtasn1.pdf
  /usr/share/doc/python-babel-0.9.6/doc/logo.pdf


$ find /usr/share/doc/ -name '*.pdf' -print (same as above because -print is the default)

  /usr/share/doc/libsrtp-1.4.4/libsrtp.pdf
  /usr/share/doc/libtasn1-4.10/libtasn1.pdf
  /usr/share/doc/python-babel-0.9.6/doc/logo.pdf

Find files and copies them to the current directory. The \; end the command

$ find /usr/share/doc/ -name '*.pdf' -exec cp {} . \;  (finds files and copies to current  directory)

$ ls -lah | grep .pdf (or: ls -lah | grep '\.pdf$')

  -rw-r--r--. 1 cloud_user cloud_user 339K Feb 16 21:59 libsrtp.pdf
  -rw-r--r--. 1 cloud_user cloud_user 254K Feb 16 21:59 libtasn1.pdf
  -rw-r--r--. 1 cloud_user cloud_user  44K Feb 16 21:59 logo.pdf

$ find -name '*.pdf'

  ./libsrtp.pdf
  ./libtasn1.pdf
  ./logo.pdf

$ find -name '*.pdf' -delete (deletes all pdf files in current directory)

$ find -name '*.pdf'

No result bcs files are deleted

$ find /etc/ -type l (finds all files in /etc and subdirectories that are a link, l)

$  find /etc/ -maxdepth 1 -type l (finds all files only in /etc that are a link, l)

$ df -h /boot/

  Filesystem      Size  Used Avail Use% Mounted on
  /dev/nvme0n1p1   20G  7.2G   13G  36% /

$ find /boot/ -size +20000k -type f  (finds all files in /boot that is at least +20000k or > 20MB)

  find: ‘/boot/grub2’: Permission denied
  find: ‘/boot/efi/EFI/centos’: Permission denied
  /boot/initramfs-0-rescue-f9afeb75a5a382dce8269887a67fbf58.img
  /boot/initramfs-3.10.0-1160.108.1.el7.x86_64.img
  /boot/initramfs-3.10.0-1160.119.1.el7.x86_64.img
  /boot/initramfs-3.10.0-1160.99.1.el7.x86_64.img
  /boot/initramfs-3.10.0-1160.95.1.el7.x86_64.img
  /boot/initramfs-3.10.0-1160.102.1.el7.x86_64.img

$ find /boot/ -size +10000k -type f -exec du -h \;  (finds all files in /boot that is at least +10000k or > 10MB)

$ stat Desktop/

    File: ‘Desktop/’
    Size: 6               Blocks: 0          IO Block: 4096   directory
  Device: 10301h/66305d   Inode: 41947746    Links: 2
  Access: (0755/drwxr-xr-x)  Uid: ( 1002/cloud_user)   Gid: ( 1003/cloud_user)
  Context: system_u:object_r:user_home_t:s0
  Access: 2026-02-18 14:10:17.963446425 +0000
  Modify: 2018-10-15 19:53:05.504865937 +0000
  Change: 2018-10-15 19:53:05.504865937 +0000
  Birth: -

$ touch Desktop/newfile

$ cd Desktop/

$ echo "Hellow world" >> newfile

$ stat newfile 

    File: ‘newfile’
    Size: 13              Blocks: 8          IO Block: 4096   regular file
  Device: 10301h/66305d   Inode: 42266547    Links: 1
  Access: (0664/-rw-rw-r--)  Uid: ( 1002/cloud_user)   Gid: ( 1003/cloud_user)
  Context: unconfined_u:object_r:user_home_t:s0
  Access: 2026-02-18 17:23:32.796699937 +0000
  Modify: 2026-02-18 17:25:09.931469694 +0000
  Change: 2026-02-18 17:25:09.931469694 +000

# VIM Editors

