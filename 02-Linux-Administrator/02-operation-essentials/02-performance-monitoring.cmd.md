<!-- Linux Performance and Monitoring-->

# Listing  standard tools in procps-nh
Checking for all RedHat packages on your Linux machine using the RedHat package manager

rpm — The Red Hat Package Manager command used on RHEL, CentOS, Fedora, Rocky, AlmaLinux, etc.

-q — Query a package.

-l — List all files installed by that package.

procps-ng — The name of the package being queried, in this case procps-ng.


$ rpm -ql procps-ng [list a pogram included in this procps-ng package ]

    /usr/bin/free
    /usr/bin/pgrep
    /usr/bin/pkill
    ...
    ...
    /usr/lib64/libprocps.so.4.0.0
    /usr/sbin/sysctl

$ rpm -qf /usr/bin/top (quries the file /usr/biin/top to know which package it belongs to)

    procps-ng-3.3.10-28.el7.x86_64

$ rpm -qd procps-ng [Queries the documentation of the package procps-ng]

    /usr/share/doc/procps-ng-3.3.10/AUTHORS
    /usr/share/doc/procps-ng-3.3.10/BUGS
    ...
    ...
    /usr/share/man/man5/sysctl.conf.5.gz
    /usr/share/man/man8/sysctl.8.gz
    /usr/share/man/man8/vmstat.8.gz

$ rpm -qc procps-ng [Queries the configuration file of the package procps-ng if there is one]

$ rpm -ql procps-ng | grep '^/usr/bin/'  [fIlters query results by using th regex '^/usr/bin/' to return only program in those paths]

    /usr/bin/free
    ...
    ...
    /usr/bin/w
    /usr/bin/watch

$ rpm -ql procps-ng | grep '^/usr/bin/'  | wc -l [Counts the number fIltered query results by using th regex ^/usr/bin/'  to return only program in those paths and wc]
    
    15

# pwdx and pmap
$ free --help

    Usage:
    free [options]

    Options:
    -b, --bytes         show output in bytes
    -k, --kilo          show output in kilobytes
    -m, --mega          show output in megabytes
    -g, --giga          show output in gigabytes
        --tera          show output in terabytes
        --peta          show output in petabytes
    -h, --human         show human-readable output
        --si            use powers of 1000 not 1024
    -l, --lohi          show detailed low and high memory statistics
    -t, --total         show total for RAM + swap
    -s N, --seconds N   repeat printing every N seconds
    -c N, --count N     repeat printing N times, then exit
    -w, --wide          wide output

        --help     display this help and exit
    -V, --version  output version information and exit

    For more details see free(1).

$ free [checks for memory usage]

$ free -h [checks for memory usage in human readable format]

$ free -m [checks for memory, in megabyte, usage in human readable format]

$ free -g [checks for memory usage, in gigagbyte, in human readable format. Returns 0 for units not up to 1GB]

In Linux, $$ is a special shell variable that expands to the Process ID (PID) of the current running shell.

$ echo $$

    2800

pmap

A tool that reports the virtual memory layout of a process.
Useful for debugging memory issues or understanding how much memory a process consumes.

$ pmap 2800 [The command displays the memory map of the process whose PID is 2800.]

    2800:   -bash
    0000000000400000    888K r-x-- bash
    00000000006dd000      4K r---- bash
    00000000006de000     36K rw--- bash
    00000000006e7000     24K rw---   [ anon ]
    000000000123b000   2376K rw---   [ anon ]
    ....
    ....

Since $$ expands to the PID of the current shell, you are effectively asking: “What directory is my current shell running in?”

$ pwdx $$ [returns the process ID of current shell and directory]

    2800: /home/cloud_user

$ pwdx $(pgrep sshd)

1384: Permission denied

$ sudo pwdx $(pgrep sshd)
[sudo] password for cloud_user: 

    1384: /

# Uptime and Tload

$ uptime
 
    01:10:56 up 57 min,  1 user,  load average: 0.00, 0.02, 0.05

  - 01:10:56 — Current system time
  - up 57 min — System uptime
  - 1 user — Number of logged‑in users
  - load average: 0.00, 0.02, 0.05 — System load averages
    
    These three numbers represent the average system load over:

    1 minute → 0.00
    5 minutes → 0.02
    15 minutes → 0.05

How to interpret the numbers?
Lower is better when comparing to the number of CPU cores.

A load of 1.00 on a single‑core system = fully used CPU
A load of 1.00 on a 4‑core system = 25% total CPU load

Your values (0.03–0.05) are very low, meaning your system is almost idle.    

$ who

    cloud_user pts/0        2026-03-06 00:24 (localhost)

$ w
 
 01:11:02 up 57 min,  1 user,  load average: 0.00, 0.02, 0.05
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
cloud_us pts/0    localhost        00:24    6.00s  0.18s  0.02s w

$ lscpu [returns number of cpu and other details]

    Architecture:          x86_64
    CPU op-mode(s):        32-bit, 64-bit
    Byte Order:            Little Endian
    CPU(s):                2

$ cat /proc/uptime  [returns uptime and idle values in seconds as 3793.67 and 7470.81]

    3793.67 7470.81

$ cat /proc/loadavg [returns the load average over 1,5, and 15 mins including number of process, 1/413, and last process ID, 5391]

    0.00 0.01 0.05 1/413 5391

$ watch uptime [runs the uptime command every 2 secs. Note how the seconds part of the time on the top RHS of the screen changes every 2 secs]

    Every 2.0s: uptime                                                                        Fri Mar  6 01:23:49 2026

    01:23:49 up  1:10,  1 user,  load average: 0.00, 0.01, 0.05

$ watch -n 4  uptime

    Every 4.0s: uptime                                                                        Fri Mar  6 01:27:19 2026

    01:27:19 up  1:13,  1 user,  load average: 0.01, 0.02, 0.05

To constantly see your system load and watch it use tload

$ tload [constantly watches our load. These three numbers represent the average system load over 1, 5, and 15mins.]

    0.00, 0.01, 0.05 

You can open another terminal for same machine and run a process that consumes cpu or loads the system
Watch those values in each time interval go up
Stop the process to watch them reduce or go down

# Top ans Vmstat

$ top [shows all running processes by continously running the command and updates the list constantly based on system. Can't use terminal when this is running]

$ top -b -n1 [ -b = batch mode, n1= 1 iteration, Runs the top command with those flags once and frees the terminal for your use]

$ top -b -n1 > filetop.txt [sends command result to file]

$ less filetop.txt

vmstat (Virtual Memory Statistics) reports system performance metrics such as:

- memory usage
- swap usage
- CPU utilization
- I/O activity
- system processes

The -S flag (or --unit) specifies the unit used to display memory values. 
Where <unit> can be:

k → kilobytes
K → kilobytes (same as lowercase k, just accepted for convenience)
m → megabytes
M → megabytes

By default, vmstat shows values in kilobytes (KB). 

$ vmstat

    procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
    r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
    1  0      0 803656   1036 492680    0    0    44     1   45   64  0  0 99  0  0

$ vmstat -S K

    procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
    r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
    1  0      0 803136   1036 492736    0    0    43     1   45   64  0  0 99  0  0

$ vmstat -S k

    procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
    r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
    1  0      0 822292   1060 504561    0    0    43     1   45   64  0  0 99  0  0

$ vmstat -S m

    procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
    r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
    1  0      0    822      1    504    0    0    43     1   45   64  0  0 99  0  0

$ vmstat 5 3 [returns the vmstat systems information 3 times, iterations, in a 5 secs delay period ]

    procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
    r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
    1  0      0 802708   1036 492816    0    0    40     1   44   63  0  0 99  0  0
    0  0      0 802592   1036 492816    0    0     0     0   56   83  0  0 100  0  0
    0  0      0 803336   1036 492816    0    0     0     0   59   94  0  0 100  0  0


# Sysstat

<!-- installations of sysstat -->

$ sudo su -
[sudo] password for cloud_user: 
...
...

root@# yum install -y sysstat

Once the package is completely install, it creates the sysstat file in /etc/cron.d/sysstat with preconfigured schedules

root@# cat /etc/cron.d/sysstat 

    # Run system activity accounting tool every 10 minutes
    */10 * * * * root /usr/lib64/sa/sa1 1 1
    # 0 * * * * root /usr/lib64/sa/sa1 600 6 &
    # Generate a daily summary of process accounting at 23:53
    53 23 * * * root /usr/lib64/sa/sa2 -A



root@# cat /etc/sysconfig/sysstat [contains sysstat configurations]

    # sysstat-10.1.5 configuration file.

    # How long to keep log files (in days).
    # If value is greater than 28, then log files are kept in
    # multiple directories, one for each month.
    HISTORY=28

    # Compress (using gzip or bzip2) sa and sar files older than (in days):
    COMPRESSAFTER=31

    # Parameters for the system activity data collector (see sadc manual page)
    # which are used for the generation of log files.
    SADC_OPTIONS="-S DISK"

    # Compression program to use.
    ZIP="bzip2"    

root@c# systemctl start sysstat

root@c# systemctl enable sysstat

root@c# systemctl status sysstat

    ● sysstat.service - Resets System Activity Logs
    Loaded: loaded (/usr/lib/systemd/system/sysstat.service; enabled; vendor preset: enabled)
    Active: active (exited) since Fri 2026-03-06 13:37:30 UTC; 19s ago
    Main PID: 5089 (code=exited, status=0/SUCCESS)
    CGroup: /system.slice/sysstat.service

    Mar 06 13:37:30 c71f0fe92f2c.mylabserver.com systemd[1]: Starting Resets System Activity Logs...
    Mar 06 13:37:30 c71f0fe92f2c.mylabserver.com systemd[1]: Started Resets System Activity Logs.

% Using sysstat

root@# iostat   [shows the i/o of your disk activities in Kilobytes]

    Linux 3.10.0-1160.119.1.el7.x86_64 (c71f0fe92f2c.mylabserver.com)       03/06/2026      _x86_64_        (2 CPU)

    avg-cpu:  %user   %nice %system %iowait  %steal   %idle
            0.36    0.00    0.37    1.25    0.02   97.99

    Device:            tps    kB_read/s    kB_wrtn/s    kB_read    kB_wrtn
    nvme0n1           5.35       191.03         6.25     736520      24110

root@# iostat -m    [shows the i/o of your disk activities in Megabytes]

    Linux 3.10.0-1160.119.1.el7.x86_64 (c71f0fe92f2c.mylabserver.com)       03/06/2026      _x86_64_        (2 CPU)

    avg-cpu:  %user   %nice %system %iowait  %steal   %idle
            0.36    0.00    0.37    1.24    0.02   98.01

    Device:            tps    MB_read/s    MB_wrtn/s    MB_read    MB_wrtn
    nvme0n1           5.31         0.19         0.01        719         23

root@# iostat -m 5 3 [shows the i/o of your disk activities in Megabytes but runs the command 3 times in a 5 secs window]

    Linux 3.10.0-1160.119.1.el7.x86_64 (c71f0fe92f2c.mylabserver.com)       03/06/2026      _x86_64_        (2 CPU)

    avg-cpu:  %user   %nice %system %iowait  %steal   %idle
            0.35    0.00    0.37    1.19    0.02   98.07

    Device:            tps    MB_read/s    MB_wrtn/s    MB_read    MB_wrtn
    nvme0n1           5.13         0.18         0.01        719         23

    avg-cpu:  %user   %nice %system %iowait  %steal   %idle
            0.70    0.00    0.70    0.10    0.00   98.50

    Device:            tps    MB_read/s    MB_wrtn/s    MB_read    MB_wrtn
    nvme0n1           0.40         0.00         0.00          0          0

    avg-cpu:  %user   %nice %system %iowait  %steal   %idle
            0.00    0.00    0.00    0.00    0.00  100.00

    Device:            tps    MB_read/s    MB_wrtn/s    MB_read    MB_wrtn
    nvme0n1           0.00         0.00         0.00          0          0

You can open another terminal of the machine, run "dd if=/dev/zero of=test" to write to the disk by writing to the file 'test' and then run "iostat -m 5 3" to see the changes
. Make sure nopt to write to the disk for too long, perhaps just write to it for 10 secs and ctrl+D to stop it. Make sure to remove the file 'test'

if=/dev/zero

Input file = /dev/zero
/dev/zero is a special file that produces an endless stream of null bytes (0x00).
Commonly used for creating empty test files or clearing disks.


of=test

Output file = a file named test
dd will write zeros into this file.

Sample commands 
root@# dd if=/dev/zero of=test bs=1M count=10 [This creates a 10 MB file of zeros.]
root@# dd if=/dev/zero of=test bs=1M count=100 [This creates a 100 MB file of zeros.]
root@# dd if=/dev/zero of=test bs=1M count=1000 [This creates a 1GB file of zeros.]


Component           Meaning
if=/dev/zero        Reads infinite zeros
of=test             Writes to file testNo 
bs/countEndless     write → fills disk!

pidstat is a performance monitoring tool provided by the sysstat package on Linux.
It is used to monitor individual processes (by PID) and display their usage of:

- CPU
- Memory
- I/O
- Task switching
- Thread activity
- Network (with options)

It is extremely useful for per‑process performance troubleshooting.

root@# pidstat [Show CPU usage per process]

root@# pidstat 1 [Show CPU usage per process every 1 sec]

root@# pidstat -u [Show CPU usage per process]
root@# pidstat -r [Show MEM usage per process]
root@# pidstat -D [Show read/write usage per process]
root@# pidstat -t [Show thread-level statistics per process]
root@# pidstat -w [Show context switch statistics per process]


root@# pidstat -p $$ 5 3 [runs pidstat of current shell, $$, 3 times for 5 secs interval]

    Linux 3.10.0-1160.119.1.el7.x86_64 (c71f0fe92f2c.mylabserver.com)       03/06/2026      _x86_64_        (2 CPU)

    02:03:34 PM   UID       PID    %usr %system  %guest    %CPU   CPU  Command
    02:03:39 PM     0      4646    0.00    0.00    0.00    0.00     0  bash
    02:03:44 PM     0      4646    0.00    0.00    0.00    0.00     0  bash
    02:03:49 PM     0      4646    0.00    0.00    0.00    0.00     0  bash
    Average:        0      4646    0.00    0.00    0.00    0.00     -  bash


mpstat reports CPU usage statistics. Part of the sysstat performance tools.

root@# mpstat -P ALL 2 3 [ It prints CPU usage for ALL CPUs, at 2‑second intervals, and repeats 3 times.]

    Linux 3.10.0-1160.119.1.el7.x86_64 (c71f0fe92f2c.mylabserver.com)       03/06/2026      _x86_64_        (2 CPU)

    02:14:08 PM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
    02:14:10 PM  all    0.50    0.00    0.25    0.00    0.00    0.00    0.00    0.00    0.00   99.25
    02:14:10 PM    0    0.00    0.00    0.50    0.00    0.00    0.00    0.00    0.00    0.00   99.50
    02:14:10 PM    1    0.50    0.00    0.00    0.00    0.00    0.00    0.50    0.00    0.00   98.99

    02:14:10 PM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
    02:14:12 PM  all    0.25    0.00    0.00    0.00    0.00    0.00    0.25    0.00    0.00   99.50
    02:14:12 PM    0    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
    02:14:12 PM    1    0.50    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00   99.50

    02:14:12 PM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
    02:14:14 PM  all    0.00    0.00    0.75    0.00    0.00    0.00    0.00    0.00    0.00   99.25
    02:14:14 PM    0    0.50    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00   99.50
    02:14:14 PM    1    0.00    0.00    1.01    0.00    0.00    0.00    0.00    0.00    0.00   98.99

    Average:     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
    Average:     all    0.25    0.00    0.33    0.00    0.00    0.00    0.08    0.00    0.00   99.33
    Average:       0    0.17    0.00    0.17    0.00    0.00    0.00    0.00    0.00    0.00   99.67
    Average:       1    0.33    0.00    0.33    0.00    0.00    0.00    0.17    0.00    0.00   99.16

sar (System Activity Reporter) is a command-line tool from the sysstat package used to collect, report, and save system performance data.
It can display historical or real-time statistics about:

- CPU usage
- Memory utilization
- Disk I/O
- Network usage
- Kernel activity
- System load
- Power management metrics
- And more

Unlike tools such as top or vmstat, sar stores data, allowing you to review performance issues after they happen.

sar gets its data from:
-   The sadc (system activity data collector) daemon
-   Log files stored under:

        /var/log/sa/saXX

where XX is the day of the month.

These logs are updated automatically (usually via cron or systemd timers).

Meaning of Each Field:

Field                       Meaning
%user                       CPU time running user processes
%nice                       CPU time running "nice" (lower‑priority) processes
%system                     Time spent running kernel processes
%iowait                     Time waiting for I/O (disk, network)
%steal                      Virtualization overhead (CPU stolen by hypervisor)
%idle                       CPU idle time

Commonly Used sar Options

Option          Meaning
-u              CPU usage-rMemory usage
-d              Block device I/O
-n              Network statistics
-q              Load averages & queue length
-b              I/O and transfer rate
-W              Swap statistics
-p              Pretty output for devices
-f              Read data from a specific log file

root@# sar [note that the time interval is 10mins to match what is in /etc/cron.d/sysstat]

    Linux 3.10.0-1160.119.1.el7.x86_64 (c71f0fe92f2c.mylabserver.com)       03/06/2026      _x86_64_        (2 CPU)

    01:37:30 PM       LINUX RESTART

    01:40:01 PM     CPU     %user     %nice   %system   %iowait    %steal     %idle
    01:50:01 PM     all      0.15      0.00      0.18      0.01      0.01     99.65
    02:00:01 PM     all      0.15      0.00      0.20      0.00      0.01     99.64
    02:10:01 PM     all      0.15      0.00      0.19      0.00      0.01     99.65
    02:20:01 PM     all      0.15      0.00      0.19      0.00      0.01     99.64
    02:30:01 PM     all      0.14      0.00      0.18      0.00      0.01     99.66
    02:40:01 PM     all      0.15      0.00      0.18      0.00      0.01     99.66
    02:50:01 PM     all      0.14      0.00      0.17      0.00      0.01     99.68
    Average:        all      0.15      0.00      0.18      0.00      0.01     99.65

root@# sar 2 5 [Live monitoring every 2 seconds, 5 times]

root@# sar -u [Show CPU usage]

root@# sar -r [Show memory usage]

root@# sar -d [Show disk I/O]

root@# sar -n DEV [Show network usage]

root@# sar -u -f /var/log/sa/sa05 [View historical data of day 5 of the month]

root@ ~# cd /var/log/sa [changed to system activity folder, sa]

root@sa# ls -lah

    total 36K
    drwxr-xr-x.  2 root root   17 Mar  6 13:37 .
    drwxr-xr-x. 12 root root 4.0K Mar  6 14:30 ..
    -rw-r--r--.  1 root root  28K Mar  6 15:20 sa06

root@# cd

root@# sar [returns all system activities]

    Linux 3.10.0-1160.119.1.el7.x86_64 (c71f0fe92f2c.mylabserver.com)       03/06/2026      _x86_64_        (2 CPU)

    01:37:30 PM       LINUX RESTART

    01:40:01 PM     CPU     %user     %nice   %system   %iowait    %steal     %idle
    01:50:01 PM     all      0.15      0.00      0.18      0.01      0.01     99.65
    02:00:01 PM     all      0.15      0.00      0.20      0.00      0.01     99.64
    02:10:01 PM     all      0.15      0.00      0.19      0.00      0.01     99.65
    02:20:01 PM     all      0.15      0.00      0.19      0.00      0.01     99.64
    02:30:01 PM     all      0.14      0.00      0.18      0.00      0.01     99.66
    02:40:01 PM     all      0.15      0.00      0.18      0.00      0.01     99.66
    02:50:01 PM     all      0.14      0.00      0.17      0.00      0.01     99.68
    03:00:01 PM     all      0.15      0.00      0.18      0.00      0.01     99.66
    03:10:01 PM     all      0.15      0.00      0.19      0.00      0.02     99.63
    03:20:01 PM     all      0.14      0.00      0.18      0.00      0.02     99.66
    03:30:01 PM     all      0.16      0.00      0.20      0.01      0.02     99.61
    Average:        all      0.15      0.00      0.19      0.00      0.01     99.65

root@# sar -s 14:40:00 -e 15:20:00 [returns all system activities between the specifies times]

    Linux 3.10.0-1160.119.1.el7.x86_64 (c71f0fe92f2c.mylabserver.com)       03/06/2026      _x86_64_        (2 CPU)

    02:40:01 PM     CPU     %user     %nice   %system   %iowait    %steal     %idle
    02:50:01 PM     all      0.14      0.00      0.17      0.00      0.01     99.68
    03:00:01 PM     all      0.15      0.00      0.18      0.00      0.01     99.66
    03:10:01 PM     all      0.15      0.00      0.19      0.00      0.02     99.63
    Average:        all      0.15      0.00      0.18      0.00      0.01     99.66

Note that sysstat can only retrieve info from when it was installed not after it

root@# sar -s 14:40:00 -e 15:20:00 -f /var/log/sa/sa04 [returns all system activities between the specifies times for the 4th day of the month]