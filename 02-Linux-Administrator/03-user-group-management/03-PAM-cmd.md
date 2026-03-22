<!-- PAM - Plugable Authentication Module -->

# Automate creation of User home directory during login

$ ls /etc/pam.d/ [list all PAM configuration programs and combined configuration programs in /lib64/security or /lib32/security/]

    atd                  gdm-fingerprint         password-auth     runuser-l          sudo-i
    chfn                 gdm-launch-environment  password-auth-ac  smartcard-auth     su-l
    chsh                 gdm-password            polkit-1          smartcard-auth-ac  system-auth
    config-util          gdm-pin                 postlogin         smtp               system-auth-ac
    crond                gdm-smartcard           postlogin-ac      smtp.postfix       systemd-user
    fingerprint-auth     login                   ppp               sshd               vlock
    fingerprint-auth-ac  other                   remote            su                 xserver
    gdm-autologin        passwd                  runuser           sudo

$ ls /lib64/security/ [list all shared module/library that will be used by PAM]

    pam_access.so     pam_gdm.so            pam_oddjob_mkhomedir.so  pam_tally2.so
    pam_cap.so        pam_gnome_keyring.so  pam_permit.so            pam_time.so
    pam_chroot.so     pam_group.so          pam_postgresok.so        pam_timestamp.so
    pam_console.so    pam_issue.so          pam_pwhistory.so         pam_tty_audit.so
    pam_cracklib.so   pam_keyinit.so        pam_pwquality.so         pam_umask.so
    pam_debug.so      pam_lastlog.so        pam_rhosts.so            pam_unix_acct.so
    pam_deny.so       pam_limits.so         pam_rootok.so            pam_unix_auth.so
    pam_echo.so       pam_listfile.so       pam_securetty.so         pam_unix_passwd.so
    pam_env.so        pam_localuser.so      pam_selinux_permit.so    pam_unix_session.so
    pam_exec.so       pam_loginuid.so       pam_selinux.so           pam_unix.so
    pam_faildelay.so  pam_mail.so           pam_sepermit.so          pam_userdb.so
    pam_faillock.so   pam_mkhomedir.so      pam_shells.so            pam_warn.so
    pam_filter        pam_motd.so           pam_stress.so            pam_wheel.so
    pam_filter.so     pam_namespace.so      pam_succeed_if.so        pam_xauth.so
    pam_ftp.so        pam_nologin.so        pam_systemd.so

This shows the configuration files and subdirectories that control logins, password policies, resource limits, security namespaces, console permissions, and more in "/etc/security/".

$ ls /etc/security/ [this where we begin to configure PAM]

    access.conf   console.handlers  group.conf   namespace.conf  opasswd         sepermit.conf
    chroot.conf   console.perms     limits.conf  namespace.d     pam_env.conf    time.conf
    console.apps  console.perms.d   limits.d     namespace.init  pwquality.conf

% Uses of /etc/security Files and Directories

access.conf => Controls who is allowed or denied login access based on user, group, or source (host/TTY). Used by pam_access.so.
chroot.conf => Defines directories users are jailed (chrooted) into at login. Used by pam_chroot.so.
console.apps => Lists programs considered console applications that may receive special device permissions when run by a local console user.
console.handlers => Maps handlers that apply or remove device permissions for users logged into the system console.
console.perms => Specifies device file permissions (ownership and mode changes) granted to console users.
console.perms.d/ => Drop‑in directory containing additional or override console permission rules.
group.conf => Assigns supplementary UNIX groups dynamically at login based on rules. Used by pam_group.so.
limits.conf => Sets resource limits (ulimits) such as max processes, open files, memory, core dumps. Used by pam_limits.so.
limits.d/ => Drop‑in directory for modular resource limit rules, often used by applications or services.
namespace.conf => Defines mount namespaces for user sessions (e.g., private /tmp). Used by pam_namespace.so.
namespace.d/ => Drop‑in configuration directory extending namespace isolation rules.
namespace.init => Script executed to initialize directories and permissions for user namespaces.
opasswd => Stores previous password hashes to enforce password history and prevent reuse.
pam_env.conf => Defines environment variables that are set automatically during login sessions. Used by pam_env.so.
pwquality.conf => Controls password complexity rules such as length, character classes, retries. Used by pam_pwquality.so.
sepermit.conf => Controls login permissions based on SELinux roles or contexts. Used by pam_sepermit.so.
time.conf => Restricts login access by time and day. Used by pam_time.so.   


% File-to-module mapping
access.conf → pam_access.so : Controls allow/deny login rules by user/group and source (host/TTY).

chroot.conf → pam_chroot.so : Configures chroot jails for users at login.

group.conf → pam_group.so : Grants supplementary groups to users dynamically at login.

limits.conf, limits.d/ → pam_limits.so : Sets per-user/group resource limits (ulimits).

pam_env.conf → pam_env.so : Sets environment variables for user sessions.

pwquality.conf → pam_pwquality.so : Enforces password complexity/quality rules.

time.conf → pam_time.so : Restricts logins by time/day.

sepermit.conf → pam_sepermit.so : Controls login based on SELinux user/context mapping.

namespace.conf, namespace.d/, namespace.init → pam_namespace.so : Configures per-session mount namespaces (e.g., private /tmp); namespace.init is the helper script run for initialization.

opasswd → pam_pwhistory.so (and pam_unix.so when configured with remember=) : Stores old password hashes to enforce password history.

console.apps, console.handlers, console.perms, console.perms.d/ → pam_console.so (legacy) : Legacy mechanism for granting device/file permissions to console users. Modern systems typically use systemd-logind, udev rules, and polkit instead. These files are only relevant if pam_console.so is in use.


When you create a user with useradd a home directory is created for each user whether you supply the -M or not because that is the default

Change defaults of creating home directory
$ sudo vi /etc/login.defs [this the file that contains the config for creating user home directory.Change "CREATE_HOME     yes" to "CREATE_HOME     no"]
    
    [sudo] password for cloud_user: 

$ sudo useradd bob [user bob is created]

$ ls /home [notice user bob home directory was not created]

    cloud_user  ssm-user  user2  user3  user33

$ grep bob /etc/passwd [shows that bob will get a home directory /home/bob/ that can be created later]

    bob:x:1008:1009::/home/bob:/bin/bash

This a typical usecase for creating thousands users like for schools and the create their home directory later or when the user logged in.
Lets now set the password for the user bob and then create him a directory upon login


$ sudo passwd bob

    [sudo] password for cloud_user: 
    Changing password for user bob.
    New password: 
    BAD PASSWORD: The password is shorter than 8 characters
    Retype new password: 
    passwd: all authentication tokens updated successfully.

The RPM Package Manager, used to query, install, update, and remove software packages on RPM‑based systems (CentOS, RHEL, Rocky, Alma, Fedora).
-q: Stands for query mode (ask RPM about packages).
-a: Means all installed packages.

$ rpm -qa | grep oddjob [Indicates Oddjob is installed.]

    oddjob-0.31.5-4.el7.x86_64
    oddjob-mkhomedir-0.31.5-4.el7.x86_64

oddjob => The core Oddjob daemon (oddjobd) which allows privileged operations via D-Bus.
oddjob-mkhomedir => Used with pam_oddjob_mkhomedir.so to automatically create home directories for users on first login (commonly when using LDAP/AD).

Oddjob is often used in environments with:
- LDAP / Active Directory
- SSSD
- Centralized authentication


$ systemctl status oddjobd

    ● oddjobd.service - privileged operations for unprivileged applications
    Loaded: loaded (/usr/lib/systemd/system/oddjobd.service; enabled; vendor preset: disabled)
    Active: inactive (dead)

$ systemctl start oddjobd

    ==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
    Authentication is required to manage system services or units.
    Authenticating as: cloud_user
    Password: 
    ==== AUTHENTICATION COMPLETE ===

$ systemctl enable oddjobd

    ==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-unit-files ===
    Authentication is required to manage system service or unit files.
    Authenticating as: cloud_user
    Password: 
    ==== AUTHENTICATION COMPLETE ===
    ==== AUTHENTICATING FOR org.freedesktop.systemd1.reload-daemon ===
    Authentication is required to reload the systemd state.
    Authenticating as: cloud_user
    Password: 
    ==== AUTHENTICATION COMPLETE ===

$ systemctl status oddjobd

    ● oddjobd.service - privileged operations for unprivileged applications
    Loaded: loaded (/usr/lib/systemd/system/oddjobd.service; enabled; vendor preset: disabled)
    Active: active (running) since Wed 2026-03-18 19:41:35 UTC; 55s ago
    Main PID: 11626 (oddjobd)
    CGroup: /system.slice/oddjobd.service
            └─11626 /usr/sbin/oddjobd -n -p /var/run/oddjobd.pid -t 300

    Mar 18 19:41:35 c71f0fe92f2c.mylabserver.com systemd[1]: Started privileged operations for unprivil...s.
    Hint: Some lines were ellipsized, use -l to show in full.

The command "sudo authconfig --enablemkhomedir --update " enables automatic home directory creation for users when they log in for the first time, and updates the system authentication (PAM) configuration to make it effective. This is commonly used in LDAP, Active Directory, or other centralized authentication setups.

The flags:

--enablemkhomedir
    1. Enables the “make home directory” feature
    2. Configures PAM to:
    3.Automatically create a user’s home directory on first successful login and set:
        -   correct ownership (user:user)
        -   default permissions (typically 0700)
        -   copy files from /etc/skel/

--update
    1. Applies and writes the changes to the syste. 
    2. Without this flag, authconfig would only display what would change
    3. This flag: 
        -   Regenerates PAM configuration files
        -   Activates --enablemkhomedir

$ sudo authconfig --enablemkhomedir --update

    [sudo] password for cloud_user: 

$ sudo su -

root@# cd /etc/pam.d/

root@pam.d# grep mkhomedir * [system sessions mapped to thr right oddjob module which simplifies the process of creating user home firectory when they log in]

    fingerprint-auth:session     optional      pam_oddjob_mkhomedir.so umask=0077
    fingerprint-auth-ac:session     optional      pam_oddjob_mkhomedir.so umask=0077
    password-auth:session     optional      pam_oddjob_mkhomedir.so umask=0077
    password-auth-ac:session     optional      pam_oddjob_mkhomedir.so umask=0077
    smartcard-auth:session     optional      pam_oddjob_mkhomedir.so umask=0077
    smartcard-auth-ac:session     optional      pam_oddjob_mkhomedir.so umask=0077
    system-auth:session     optional      pam_oddjob_mkhomedir.so umask=0077
    system-auth-ac:session     optional      pam_oddjob_mkhomedir.so umask=0077

Remember the user bob above did not have a home directory. Let us now test our oddjob

root@pam.d# sudo su - bob [login in as bob]

    Creating home directory for bob.
    Last failed login: Mon Mar  2 15:54:48 UTC 2026 from 192.81.217.119 on ssh:notty
    There were 6 failed login attempts since the last successful login.

$ pwd

    /home/bob

[bob@c71f0fe92f2c ~]$ ls /home/

    bob  cloud_user  ssm-user  user2  user3  user33

Enter exit twice to go back to cloud_user

[bob@c71f0fe92f2c ~]$ exit
    
    logout

[root@c71f0fe92f2c pam.d]# exit

logout

[cloud_user@c71f0fe92f2c ~]$  

[cloud_user@c71f0fe92f2c ~]$ cat /etc/pam.d/system-auth [Show among other things the password quality programs. Note the line with ***, the asterik was added by me that was]

    #%PAM-1.0
    # This file is auto-generated.
    # User changes will be destroyed the next time authconfig is run.
    auth        required      pam_env.so
    auth        required      pam_faildelay.so delay=2000000
    ..
    ..
    password    requisite     pam_pwquality.so try_first_pass local_users_only retry=3 authtok_type=  ***
    password    sufficient    pam_unix.so sha512 shadow nullok try_first_pass use_authtok
    ...
    ...


[cloud_user@c71f0fe92f2c ~]$ less /etc/security/pwquality.conf [modify this file to configure password quality]

    # Configuration for systemwide password quality limits
    # Defaults:
    #
    # Number of characters in the new password that must not be present in the
    # old password.
    # difok = 5
    #
    # Minimum acceptable size for the new password (plus one if
    # credits are not disabled which is the default). (See pam_cracklib manual.)
    # Cannot be set to lower value than 6.
    # minlen = 9
    ...
    ...

NOTE THIS IMPORTANT FACT:

Password quality settings can be defined directly in PAM, which may override or supplement pwquality.conf.
So options specified on the PAM line override settings in /etc/security/pwquality.conf.
Common files that can overwrite settings in pwquality.conf
    - /etc/pam.d/system-auth
    - /etc/pam.d/password-auth

pwscore checks for password quality and scores it from 0-100. Zero being the lowest

[cloud_user@c71f0fe92f2c ~]$ pwscore [password fails check because the passowrd is a word in the dictionary which is a setting in pwquality.conf]

    password01      ==> [the password I entered]
    Password quality check failed:
    The password fails the dictionary check - it is based on a dictionary word

Password strength checking depends on dictionary data, not only rules.
The above password dictionary error is using the settings in:
    -   /usr/share/dict/
    -   /usr/share/cracklib/
    -   /usr/lib64/cracklib_dict.*

[cloud_user@c71f0fe92f2c ~]$ pwscore [scores it high and meets it quality check]

    adtheon01@3#       ==> [the password I entered]
    64


<-- Restricting or limiting Access to resources -->

[cloud_user@c71f0fe92f2c ~]$ ulimit -a [current logged in user restrictions]

    core file size          (blocks, -c) 0
    data seg size           (kbytes, -d) unlimited
    scheduling priority             (-e) 0
    file size               (blocks, -f) unlimited
    pending signals                 (-i) 6909
    max locked memory       (kbytes, -l) 64
    max memory size         (kbytes, -m) unlimited
    open files                      (-n) 4096
    pipe size            (512 bytes, -p) 8
    POSIX message queues     (bytes, -q) 819200
    real-time priority              (-r) 0
    stack size              (kbytes, -s) 8192
    cpu time               (seconds, -t) unlimited
    max user processes              (-u) 4096
    virtual memory          (kbytes, -v) unlimited
    file locks                      (-x) unlimited

[cloud_user@c71f0fe92f2c ~]$ ulimit -u [max number of user processes allowed, also indicated in 'ulimit -a']

    4096    

[cloud_user@c71f0fe92f2c ~]$ ulimit -u 10 [changes max number of user processes allowed to 10, also indicated in 'ulimit -a']

[cloud_user@c71f0fe92f2c ~]$ ulimit -u

    10

Read the definition and usage in each column: <domain> <type>  <item>  <value>

[cloud_user@c71f0fe92f2c ~]$ sudo cat /etc/security/limits.conf [Shows how to add limits using * for all users and @GroupName for group. Make sure to uncomment, modify, or add you limits as desired]
    ...
    ...
    #
    #<domain>        <type>  <item>  <value>
    #
    #Where:
    #<domain> can be:
    #        - a user name
    #        - a group name, with @group syntax
    #        - the wildcard *, for default entry
    #        - the wildcard %, can be also used with %group syntax,
    #                 for maxlogin limit
    #
    #<type> can have the two values:
    #        - "soft" for enforcing the soft limits
    #        - "hard" for enforcing hard limits
    #
    #<item> can be one of the following:
    #        - core - limits the core file size (KB)
    #        - data - max data size (KB)
    #        - fsize - maximum filesize (KB)
    #        - memlock - max locked-in-memory address space (KB)
    #        - nofile - max number of open file descriptors
    #        - rss - max resident set size (KB)
    #        - stack - max stack size (KB)
    #        - cpu - max CPU time (MIN)
    #        - nproc - max number of processes
    #        - as - address space limit (KB)
    #        - maxlogins - max number of logins for this user
    #        - maxsyslogins - max number of logins on the system
    #        - priority - the priority to run user process with
    #        - locks - max number of file locks the user can hold
    #        - sigpending - max number of pending signals
    #        - msgqueue - max memory used by POSIX message queues (bytes)
    #        - nice - max nice priority allowed to raise to values: [-20, 19]
    #        - rtprio - max realtime priority
    #
    #<domain>      <type>  <item>         <value>
    #

    #*               soft    core            0
    #*               hard    rss             10000
    #@student        hard    nproc           20
    #@faculty        soft    nproc           20
    #@faculty        hard    nproc           50
    #ftp             hard    nproc           0
    #@student        -       maxlogins       4

    # End of file

<-- Restricting or limiting Access to a particular time or service -->

Lets say we want to restrict access  to ssh during a specific time

[cloud_user@c71f0fe92f2c ~]$ sudo cd /etc/pam.d/

[cloud_user@c71f0fe92f2c ~]$ cd /etc/pam.d/

[cloud_user@c71f0fe92f2c pam.d]$ ls [PAM services]

    atd                  gdm-fingerprint         password-auth     runuser-l          sudo-i
    chfn                 gdm-launch-environment  password-auth-ac  smartcard-auth     su-l
    chsh                 gdm-password            polkit-1          smartcard-auth-ac  system-auth
    config-util          gdm-pin                 postlogin         smtp               system-auth-ac
    crond                gdm-smartcard           postlogin-ac      smtp.postfix       systemd-user
    fingerprint-auth     login                   ppp               sshd               vlock
    fingerprint-auth-ac  other                   remote            su                 xserver
    gdm-autologin        passwd                  runuser           sudo

[cloud_user@c71f0fe92f2c pam.d]$ sudo vi /etc/pam.d/sshd [add "account    required     pam_time.so" at the top of the account section. ]

...
...
account    required     pam_time.so  ------> added line by me
account    required     pam_nologin.so
account    include      password-auth
password   include      password-auth
...
...

Now we will edit the time.conf file to restrict ssh access to a specific time

Read the time.conf file as it documents because it explains its content and you will understand how to make your custom changes

[cloud_user@c71f0fe92f2c pam.d]$ sudo vi /etc/security/time.conf 

Add this lines below to the end of the file. 
This lines are a PAM time‑based access rule from /etc/security/time.conf, used by the pam_time.so module.
Start by adding one line at a time, save and exit, and try loging in from a machine using putty to test each Remove the line and try testing the next

    -   sshd;*;*;Wd0900-1700"       ====> controls when SSH logins are allowed from 0900-1700 from all users and terminals. Does not apply to root, console or GUI logins
    -   *;*;tux|bob;Wk0800-1800     ====> controls when the users bob and tux users can access all services and terminal types between 0800-1800 during weekdays, wd
    -   *;*;tux|bob;!Wk0800-1800     ====> controls when the users bob and tux users can access all services and terminal types not between 0800-1800 during weekdays, wd

Play around with different settings to get confortable