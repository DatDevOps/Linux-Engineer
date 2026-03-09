
<!-- Scheduling -->

$ vi df.sh

Add the below content to file:

    #!/bin/bash
    FILE=/tmp/df.txt
    df -h > $FILE
    cat $FILE
    rm $FILE


$ chmod +x df.sh

$ ./df.sh

    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        864M     0  864M   0% /dev
    tmpfs           903M     0  903M   0% /dev/shm
    tmpfs           903M   17M  886M   2% /run
    tmpfs           903M     0  903M   0% /sys/fs/cgroup
    /dev/nvme0n1p1   20G  7.3G   13G  37% /
    tmpfs           181M   20K  181M   1% /run/user/1002
 
$ ls /etc/cron*

    /etc/cron.deny  /etc/crontab

    /etc/cron.d:
    0hourly  awslogs  awslogs_log_rotate  raid-check  sysstat

    /etc/cron.daily:
    logrotate  man-db.cron  mlocate

    /etc/cron.hourly:
    0anacron

    /etc/cron.monthly:

    /etc/cron.weekly:

/etc/crontab is the system-wide cron configuration file used to schedule automated jobs (commands or scripts) to run at specific times or intervals.

% CRONTAB [system has to be ON for jobs to runs]
$ sudo su -

    [sudo] password for cloud_user: 
    Last login: Fri Mar  6 16:27:25 UTC 2026 on pts/0

root@# vi /etc/crontab 

root@# cat /etc/crontab 

    SHELL=/bin/bash
    PATH=/sbin:/bin:/usr/sbin:/usr/bin
    MAILTO=root

    # For details see man 4 crontabs

    # Example of job definition:
    # .---------------- minute (0 - 59)
    # |  .------------- hour (0 - 23)
    # |  |  .---------- day of month (1 - 31)
    # |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
    # |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
    # |  |  |  |  |
    # *  *  *  *  * user-name  command to be executed

root@# vi /etc/crontab 

    Add this to file: 2 8-18 * * 1-5 root df -h

root@# cat /etc/crontab 

    SHELL=/bin/bash
    PATH=/sbin:/bin:/usr/sbin:/usr/bin
    MAILTO=root

    # For details see man 4 crontabs

    # Example of job definition:
    # .---------------- minute (0 - 59)
    # |  .------------- hour (0 - 23)
    # |  |  .---------- day of month (1 - 31)
    # |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
    # |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
    # |  |  |  |  |
    # *  *  *  *  * user-name  command to be executed
     8-18 * * 1-5 root df -h > /home/cloud_user/mycrontab.txt

 8-18 * * 1-5 root df -h > /home/cloud_user/mycrontab.txt ==> runs the command 'df -h' and appends result to /home/cloud_user/mycrontab.txt as root for every 2 mins, 8am-6pm, every day, every, month, and from Mon-Fri [sunday is day 0]


root@# exit

    logout

$ crontab -l [checks for user specific crontab jobs]

    no crontab for cloud_user

$ crontab -e [opens vi editor for creating user crontab and does not you to enter the user like in /etc/crontab]

Enter content to run every minute all the time:

* * * * * /home/cloud_user/df.sh > /home/cloud_user/df_log 

$ ls -lh df_log

    -rw-r--r--. 1 cloud_user cloud_user 378 Mar  6 17:41 df_log

$ crontab -r [remove user cron job]

$ crontab -l

    no crontab for cloud_user

% ANACRON
Anacron is used for running periodic jobs on systems that are not always powered on (like laptops).

$ sudo su -

root@# ls /etc/anacrontab 

    /etc/anacrontab

root@# cat /etc/anacrontab 

    # /etc/anacrontab: configuration file for anacron

    # See anacron(8) and anacrontab(5) for details.

    SHELL=/bin/sh
    PATH=/sbin:/bin:/usr/sbin:/usr/bin
    MAILTO=root
    # the maximal random delay added to the base delay of the jobs
    RANDOM_DELAY=45
    # the jobs will be started during the following hours only
    START_HOURS_RANGE=3-22

    #period in days   delay in minutes   job-identifier   command
    1       5       cron.daily              nice run-parts /etc/cron.daily
    7       25      cron.weekly             nice run-parts /etc/cron.weekly
    @monthly 45     cron.monthly            nice run-parts /etc/cron.monthly

Note:

period -- how often the job should run & are represented by daily [1] week [7], and monthly [@monthly]
Delays -- number of minutes to wait after boot before running. Here it is set 5mins, 25mins, and 45mions after power on
Job Identifier -- a label used for logging/tracking
Command: scripts to run

<!-- Irregular scheduling using 'at'-->

root@# systemctl status atd

    ● atd.service - Job spooling tools
    Loaded: loaded (/usr/lib/systemd/system/atd.service; enabled; vendor preset: enabled)
    Active: active (running) since Fri 2026-03-06 16:43:09 UTC; 2h 25min ago
    Main PID: 1420 (atd)
    CGroup: /system.slice/atd.service
            └─1420 /usr/sbin/atd -f

    Mar 06 16:43:09 c71f0fe92f2c.mylabserver.com systemd[1]: Started Job spooling tools.

root@# at noon [Runs at next noon ]

    at> ls /etc
    at> ls /tmp
    at> [press ctrl+D to exit] <EOT>
    job 2 at Sat Mar  7 12:00:00 2026

root@# atq [shows queued jobs]

    2       Sat Mar  7 12:00:00 2026 a root

root@# at wednesday [Runs on the next Wednesday]

    at> ls /etc
    at> [press ctrl+D to exit] <EOT>
    job 3 at Wed Mar 11 19:14:00 2026

root@# atq

2       Sat Mar  7 12:00:00 2026 a root
3       Wed Mar 11 19:14:00 2026 a root

root@# atrm 2  [removes the job id 2 from queue]

root@# atrm 3 [removes the job id 3 from queue]

root@# atq [returns no result bcs of no queue jobs]

    at 13:23 jun 23
    at> ls /etc
    at> [press ctrl+D to exit] <EOT>
    job 4 at Tue Jun 23 13:23:00 2026

root@# atq

    4       Tue Jun 23 13:23:00 2026 a root

root@# atrm 4

root@# atq [nothing in queue]

By default everyone is allowed to run the 'at' and 'cron' jobs except those listed therein

root@# s /etc/*.deny

    /etc/at.deny  /etc/cron.deny  /etc/hosts.deny

If you create an allow file, only those listed will be allowed to run at and cron


<!-- Log files and Log rotation -->

Note that the 'last' command as a reqular user reads from /var/log/wtmp/ file

And that the 'lastb' command as a root user reads from /var/log/btmp file

$ lastlog [all user account with log in history]

    Username         Port     From             Latest
    root             pts/0                     Fri Mar  6 18:52:29 +0000 2026
    bin                                        **Never logged in**
    ...
    ...
    cloud_user       pts/0    localhost        Fri Mar  6 16:47:36 +0000 2026
    ssm-user                                   **Never logged in**
    ntp                                        **Never logged in**

-v — invert match → show lines that do NOT match the pattern
$ lastlog | grep -v "Never" [returns only user with login history and not those that 'never' logged in (-v — invert match → show lines that do NOT match the pattern) ]

    Username         Port     From             Latest
    root             pts/0                     Fri Mar  6 18:52:29 +0000 2026
    cloud_user       pts/0    localhost        Fri Mar  6 16:47:36 +0000 2026

$  last [reads logs from /var/log/wtmp]
cloud_us pts/0        localhost        Fri Mar  6 16:47   still logged in   
reboot   system boot  3.10.0-1160.119. Fri Mar  6 16:42 - 20:32  (03:50)    
cloud_us pts/0        localhost        Fri Mar  6 13:24 - down   (03:15)  
...
...  
reboot   system boot  3.10.0-1160.119. Fri Mar  6 12:40 - 16:40  (04:00)    
  
wtmp begins Tue Jan 27 20:55:42 2026

$ last -n 10 [ returns only last 10 histtory ]

    cloud_us pts/0        localhost        Fri Mar  6 16:47   still logged in   
    reboot   system boot  3.10.0-1160.119. Fri Mar  6 16:42 - 20:35  (03:52)    
    cloud_us pts/0        localhost        Fri Mar  6 13:24 - down   (03:15)    
    reboot   system boot  3.10.0-1160.119. Fri Mar  6 12:40 - 16:40  (04:00)    
    cloud_us pts/0        localhost        Fri Mar  6 00:24 - down   (03:50)    
    reboot   system boot  3.10.0-1160.119. Fri Mar  6 00:13 - 04:15  (04:01)    
    cloud_us pts/0        localhost        Thu Mar  5 17:58 - crash  (06:15)    
    reboot   system boot  3.10.0-1160.119. Thu Mar  5 17:37 - 04:15  (10:37)    
    cloud_us pts/0        localhost        Thu Mar  5 14:20 - crash  (03:17)    
    cloud_us pts/0        localhost        Thu Mar  5 13:42 - 14:20  (00:37)    

    wtmp begins Tue Jan 27 20:55:42 2026

$ last | grep 'still' [returnsuser 'still' logged in]

    cloud_us pts/0        localhost        Fri Mar  6 16:47   still logged in   

$ last reboot [returns 'reboot' history]

    reboot   system boot  3.10.0-1160.119. Fri Mar  6 16:42 - 20:40  (03:58)    
    ...
    ...    
    reboot   system boot  3.10.0-1160.119. Sun Feb  8 23:36 - 02:15 (7+02:39)   

    wtmp begins Tue Jan 27 20:55:42 2026

$ last -n 10 cloud_user [ returns only last 10 history for the user cloud_user ]

    cloud_us pts/0        localhost        Fri Mar  6 16:47   still logged in   
    cloud_us pts/0        localhost        Fri Mar  6 13:24 - down   (03:15)    
    cloud_us pts/0        localhost        Fri Mar  6 00:24 - down   (03:50)    
    cloud_us pts/0        localhost        Thu Mar  5 17:58 - crash  (06:15)    
    cloud_us pts/0        localhost        Thu Mar  5 14:20 - crash  (03:17)    
    cloud_us pts/0        localhost        Thu Mar  5 13:42 - 14:20  (00:37)    
    cloud_us pts/0        localhost        Mon Mar  2 18:20 - crash (2+19:10)   
    cloud_us pts/0        localhost        Mon Mar  2 18:00 - crash  (00:10)    
    cloud_us pts/1        localhost        Mon Mar  2 14:32 - crash  (03:12)    
    cloud_us pts/0        localhost        Mon Mar  2 13:51 - crash  (03:53    

$ lastb [only root access can read btmp file]
    
    lastb: /var/log/btmp: Permission denied

$ sudo su -
Enter password

root@# lastb -n 10
    
    ssh:notty    8.219.222.66     Fri Mar  6 19:14 - 19:14  (00:00)    
    oracle   ssh:notty    8.138.44.199     Fri Mar  6 17:50 - 17:50  (00:00)    
    oracle   ssh:notty    8.138.44.199     Fri Mar  6 17:50 - 17:50  (00:00)    
    centos   ssh:notty    8.138.44.199     Fri Mar  6 17:49 - 17:49  (00:00)    
    centos   ssh:notty    8.138.44.199     Fri Mar  6 17:49 - 17:49  (00:00)    
    admin    ssh:notty    8.138.44.199     Fri Mar  6 17:49 - 17:49  (00:00)    
    admin    ssh:notty    8.138.44.199     Fri Mar  6 17:49 - 17:49  (00:00)    
    gitlab   ssh:notty    8.138.44.199     Fri Mar  6 17:49 - 17:49  (00:00)    
    gitlab   ssh:notty    8.138.44.199     Fri Mar  6 17:49 - 17:49  (00:00)    
    flask    ssh:notty    8.138.44.199     Fri Mar  6 17:49 - 17:49  (00:00) 

    btmp begins Mon Mar  2 15:44:53 2026

root@# cd /var/log

root@log# ls
    amazon                   btmp              dpkg.log            messages-20260209  secure-20260305       xrdp-sesman.log-20181213.gz
    anaconda                 btmp-20260302     firewalld           messages-20260216  spooler               xrdp-sesman.log-20190116.gz
    audit                    cfn-init-cmd.log  gdm                 messages-20260225  spooler-20260209      xrdp-sesman.log-20190213.gz
    {auth.log,               cfn-init.log      grubby              messages-20260305  spooler-20260216      xrdp-sesman.log-20190509.gz
    awslogs-agent-setup.log  cfn-wire.log      grubby_prune_debug  ntpstats           spooler-20260225      yum.log
    awslogs.log              chrony            lastlog             pm-powersave.log   spooler-20260305      yum.log-20210312
    boot.log                 cloud-init.log    lastlog,            ppp                tallylog              yum.log-20221221
    boot.log-20260219        cron              logstatus           sa                 tuned                 yum.log-20231012
    boot.log-20260220        cron-20260209     maillog             samba              wpa_supplicant.log    yum.log-20260216
    boot.log-20260221        cron-20260216     maillog-20260209    secure             wtmp
    boot.log-20260225        cron-20260225     maillog-20260216    secure}            xrdp.log-20181213.gz
    boot.log-20260302        cron-20260305     maillog-20260225    secure-20260209    xrdp.log-20190116.gz
    boot.log-20260305        dmesg             maillog-20260305    secure-20260216    xrdp.log-20190213.gz
    boot.log-20260306        dmesg.old         messages            secure-20260225    xrdp.log-20190509.gz

root@#log# ls secure*

    secure  secure}  secure-20260209  secure-20260216  secure-20260225  secure-20260305

[shows log from all file that begins with the word 'secure' and has any 1/more characters after]
root@#log#  less secure* 

[shows log from all file that begins with the word 'secure' and has any 1/more characters after and filters to return only content with the word 'sudo']
root@#log# grep sudo secure* 

root@#log# tail -n1 secure

    Mar  6 20:56:22 c71f0fe92f2c sudo: pam_unix(sudo:session): session closed for user root

[prints all lines, $0, with the word 'sudo' and uses the default delimiter of space]
root@#log# awk '/sudo/ {print $0 }' secure  

    Mar  5 14:45:36 c71f0fe92f2c sudo: cloud_user : TTY=pts/0 ; PWD=/home/cloud_user ; USER=root ; COMMAND=/bin/su -
    Mar  5 14:45:36 c71f0fe92f2c sudo: pam_unix(sudo:session): session opened for user root by cloud_user(uid=0)

root@#log# awk '/sudo/ {print $5, $6 }' secure [prints 5th item of each line with a space delimiter]

root@#log# awk '/sudo/ {print $5, $6 }' secure [prints 5th and 6th item of each line with a space delimiter]

root@#log# awk '/sudo/ {print $5, $6, $14 }' secure [prints 5th, 6th, and 14th item of each line with a space delimiter]


# rsyslog

$ sudo su -

root@# cd /etc/

root@#etc# ls rsyslog*

    rsyslog.conf

    rsyslog.d:
    21-cloudinit.conf  listen.conf

root@#etc# cat rsyslog.conf [Go through the file and see the rules controlling logging on the machine]

Adding our own logging to a specific file using 'local 0-7' that we can use

root@#etc# vi rsyslog.conf [opens file in editor]

Add this somewhere in the rule section local1.info /var/log/chuks, like below with log level of info and above

    # Log anything (except mail) of level info or higher.
    # Don't log private authentication messages!
    *.info;mail.none;authpriv.none;cron.none                /var/log/messages
    
    #Chuks custom rule
    local1.info /var/log/chuks

    # The authpriv file has restricted access.
    authpriv.*     

Save ad exit editor

root@#etc# systemctl restart rsyslog [restarts the system]

root@#etc# systemctl status rsyslog [checks the rsyslog status, should be running and green]

root@#etc# logger -p local1.warn "Logging from Chuks" [trigger our local logging with a warning log level]

root@# tail /var/log/messages
    
    Mar  6 21:50:00 c71f0fe92f2c systemd: Starting Network Manager Script Dispatcher Service...
    Mar  6 21:50:00 c71f0fe92f2c dbus[672]: [system] Successfully activated service 'org.freedesktop.nm_dispatcher'
    Mar  6 21:50:00 c71f0fe92f2c systemd: Started Network Manager Script Dispatcher Service.
    Mar  6 21:50:00 c71f0fe92f2c nm-dispatcher: req:1 'dhcp6-change' [ens5]: new request (5 scripts)
    Mar  6 21:50:00 c71f0fe92f2c nm-dispatcher: req:1 'dhcp6-change' [ens5]: start running ordered scripts...
    Mar  6 21:50:01 c71f0fe92f2c systemd: Created slice User Slice of root.
    Mar  6 21:50:01 c71f0fe92f2c systemd: Started Session 72 of user root.
    Mar  6 21:50:01 c71f0fe92f2c systemd: Started Session 71 of user root.
    Mar  6 21:50:01 c71f0fe92f2c systemd: Removed slice User Slice of root.
    Mar  6 21:50:37 c71f0fe92f2c cloud_user: Logging from Chuks

root@etc# tail /var/log/chuks

    Mar  6 22:05:54 c71f0fe92f2c cloud_user: Logging from Chuks

% Log rotation

Log rotation prevents logs from consuming machine resources  and to free up space

root@etc# cd ~

root@# cd /etc/cron.daily/

root@cron.daily# ls -lhF [contains executables, *, that runs once a day]

    total 12K
    -rwx------. 1 root root 219 Apr  1  2020 logrotate*
    -rwxr-xr-x. 1 root root 618 Oct 30  2018 man-db.cron*
    -rwx------. 1 root root 208 Apr 10  2018 mlocate*


root@cron.daily# cat logrotate [Your file may be different but still points to the file that is executed during log rotation]

    #!/bin/sh

    /usr/sbin/logrotate -s /var/lib/logrotate/logrotate.status /etc/logrotate.conf
    EXITVALUE=$?
    if [ $EXITVALUE != 0 ]; then
        /usr/bin/logger -t logrotate "ALERT exited abnormally with [$EXITVALUE]"
    fi
    exit 0


root@# ls -lhF /etc/logrotate.conf  [configuration for log rotate]

    -rw-r--r--. 1 root root 662 Jul 31  2013 /etc/logrotate.conf

root@etc# cd /etc/

root@etc# less logrotate.conf [logrotate configurations contents]

    # see "man logrotate" for details
    # rotate log files weekly
    weekly

    # keep 4 weeks worth of backlogs
    rotate 4

    # create new (empty) log files after rotating old ones
    create

    # use date as a suffix of the rotated file
    dateext

    # uncomment this if you want your log files compressed
    #compress

    # RPM packages drop log rotation information into this directory
    include /etc/logrotate.d

    # no packages own wtmp and btmp -- we'll rotate them here
    /var/log/wtmp {
        monthly
        create 0664 root utmp
            minsize 1M
        rotate 1
    }

    /var/log/btmp {
        missingok
        monthly
        create 0600 root utmp
        rotate 1
    }


[root@c71f0fe92f2c etc]# less logrotate.conf [edits by adding the below to the end of file the logrotate configurations contents]

    /var/log/chuks {
        missingok
        notifempty
        size 10
        compress
    }

Note that you can put a list of file with similar configuration delimited by commas, e.g  

    /var/log/chuks, /var/log/zyx, /var/log/xyz {
        missingok
        notifempty
        size 10
        compress
    }

Read man pages for more info: man logrotate. 
You can place this in: /etc/logrotate.d/custom-logs [custom-logs created by you] or any filename under /etc/logrotate.d/

<!-- sample start -->
Sample of custom logs on logrotate.d

root@etc# ls -lF logrotate.d/

    total 36
    -rw-r--r--. 1 root root 222 Sep 23  2016 awslogs
    -rw-r--r--. 1 root root  91 Sep 30  2020 bootlog
    -rw-r--r--. 1 root root 160 Sep 19  2018 chrony
    -rw-r--r--. 1 root root 172 Sep 29  2016 iscsiuiolog
    -rw-r--r--. 1 root root 136 Feb 27  2020 ppp
    -rw-r--r--. 1 root root 115 Sep 12  2023 samba
    -rw-r--r--. 1 root root 224 Jan 13  2022 syslog
    -rw-r--r--. 1 root root 100 Mar 16  2021 wpa_supplicant
    -rw-r--r--. 1 root root 103 Oct  1  2020 yum

root@etc# ls -lF logrotate.d/awslogs 

    -rw-r--r--. 1 root root 222 Sep 23  2016 logrotate.d/awslogs

root@etc# cat logrotate.d/awslogs 

    # Version: 1.3.9
    /var/log/awslogs.log {
        
        missingok
        notifempty
        size 100M
        create 0600 root root
        delaycompress
        compress
        rotate 4
        postrotate
            service awslogs restart
        endscript
    }
<!-- sample end -->

root@etc# ls /var/log/chuks* [checking for our file for custom rule added to rsyslog.conf]

    /var/log/chuks

[root@c71f0fe92f2c etc]# logrotate /etc/logrotate.conf [this actions creates a log rotation]

root@etc# ls /var/log/chuks* [shows our original rsyslog file and the compressed and rotated file]

    /var/log/chuks  /var/log/chuks-20260308.gz


<!-- journalctl -->
JornalCTL is systemD base. Rather than know which file to read log from, we can read logs from a unified log locations using jornalctl

root@# journalctl [outputs all log events. Page through the logs using the page up/down on keyboard]

root@# journalctl -n [outputs all last 10 log events]

root@# journalctl -n15 [outputs all last 15 log events]

root@# journalctl -f [tails/follow the last log events]

root@# journalctl -u sshd.service [outputs log events for the ssh service. Do similar for other service]

root@# journalctl -u -n5 sshd.service [outputs the last 5 log events for the ssh service. Do similar for other service]

root@# journalctl -b [The log events since the last boot, which is the default] 

root@# journalctl --since "2026-03-8 12:00:00" [outputs log events since 12pm on March 08, 2026]

root@# journalctl --since "10 minutes ago"  [outputs log events since the last 10 minutes]



Creating your custom log file or additional log records with journalctl

root@# mkdir /var/log/journal

root@# vi /etc/systemd/journald.conf

#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.
#
# Entries in this file show the compile time defaults.
# You can change settings by editing this file.
# Defaults can be restored by simply deleting this file.
#
# See journald.conf(5) for details.

[Journal]
    #Storage=auto
    #Compress=yes
    #Seal=yes
    #SplitMode=uid
    #SyncIntervalSec=5m
    #RateLimitInterval=30s
    #RateLimitBurst=1000
    #SystemMaxUse=
    #SystemKeepFree=
    #SystemMaxFileSize=
    #RuntimeMaxUse=
    #RuntimeKeepFree=
    #RuntimeMaxFileSize=
    #MaxRetentionSec=
    #MaxFileSec=1month
    #ForwardToSyslog=yes
    #ForwardToKMsg=no
    #ForwardToConsole=no
    #ForwardToWall=yes
    #TTYPath=/dev/console
    #MaxLevelStore=debug
    #MaxLevelSyslog=debug
    #MaxLevelKMsg=notice
    #MaxLevelConsole=info
    #MaxLevelWall=emerg
    #LineMax=48K

Now make this change:

#Storage=auto ===> #Storage=persistent [persistent tells journalctl to save log in file and  ]

Now reboot system to complete  configuration for journalctl

root@# reboot [ OR 'shutdown -r' ]

Once machineis backk up and running, continue:

$ sudo su -

    [sudo] password for cloud_user: 
    Last login: Sun Mar  8 21:45:35 UTC 2026 on pts/0
    Last failed login: Sun Mar  8 23:14:27 UTC 2026 from 162.243.82.199 on ssh:notty
    There were 13 failed login attempts since the last successful login.

root@# journalctl --list-boots [ 0 = current boot and -1 = previous boot]

    -1 d1fcaf2927b844f5bdb72ded4e077a83 Sun 2026-03-08 21:35:11 UTC—Mon 2026-03-09 00:07:07 UTC
    0 9e5e58a501eb401da09865814898cc25 Mon 2026-03-09 00:08:17 UTC—Mon 2026-03-09 00:10:01 UTC

root@# journalctl -b -1 [outputs logs during the previous boot, -1]

root@# journalctl -b [outputs logs during the current boot, 0. No need to pass 0 as it is the default with -b flag]

root@#

root@#






root@cron.daily#
root@cron.daily#
root@cron.daily#