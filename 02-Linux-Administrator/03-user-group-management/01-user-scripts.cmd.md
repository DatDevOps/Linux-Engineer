<!-- Users/Groups -->

# Getting users and groups

$ cat /etc/passwd [returns all local users]

    root:x:0:0:root:/root:/bin/bash
    bin:x:1:1:bin:/bin:/sbin/nologin
    ...
    ...
    cloud_user:x:1002:1003::/home/cloud_user:/bin/bash
    ssm-user:x:1003:1004::/home/ssm-user:/bin/bash
    ntp:x:38:38::/etc/ntp:/sbin/nologin

$ getent passwd [returns the list of all local and 3rd party users linked to active directory, LDAP, etc]

But because we do not have a 3rd party users loging in from LDAP or AD, it returns the same list as before

    root:x:0:0:root:/root:/bin/bash
    bin:x:1:1:bin:/bin:/sbin/nologin
    ...
    ...
    cloud_user:x:1002:1003::/home/cloud_user:/bin/bash
    ssm-user:x:1003:1004::/home/ssm-user:/bin/bash
    ntp:x:38:38::/etc/ntp:/sbin/nologin

$ grep passwd /etc/nsswitch.conf [returns the lines with passwd, the local is commented out (#passwd:db files nisplus nis) and then we see the system security subsystem (passwd:files sss) used for 3rd auth]

    #passwd:    db files nisplus nis
    passwd:     files sss

$ getent group

    root:x:0:
    bin:x:1:
    ...
    ...
    cloud_user:x:1003:
    ssm-user:x:1004:
    ntp:x:38:

$ getent group [returns the list of all local groups and 3rd party users linked to active directory, LDAP, etc]

But because we do not have a 3rd party groups, say from LDAP or AD, it returns the same list as before

    root:x:0:
    bin:x:1:
    ...
    ...
    cloud_user:x:1003:
    ssm-user:x:1004:
    ntp:x:38:

$ getent networks [returns a list networks on the system]

    default               0.0.0.0
    loopback              127.0.0.0
    link-local            169.254.0.0

$ getent services [returns a list of services and the port on which they are running ]

    ...
    ...
    n2h2server            9285/tcp
    n2h2server            9285/udp
    n2receive             9286/udp
    cumulus               9287/tcp
    ...
    ...
    iqobject              48619/udp
    matahari              49000/tcp

# Managing login scripts

The sequence of operations when you login as a regular user or root, the systems checks for your profile and if found executes the corresponding login scripts in user home directory:

    1. /etc/profile => ~/.bash_profile (in home of user)

    2. ~/.bashrc  (in home of user)  => /etc/bashrc

When you logout as a user, the system runs the script in the home directory of the specific user => ~/.bash_logout

Lets test the root user account and cloud_user accounts

$ sudo su -  [Logs into the system using root profile, '/etc/profile => ~/.bash_profile', and bashrc files in ''~/.bashrc  => /etc/bashrc']

[root@c71f0fe92f2c ~]# pwd

    /root

root@# echo $USER

    root

root@# id

    uid=0(root) gid=0(root) groups=0(root) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023

root@# exit

    logout

$ pwd

    home/cloud_user

$ exit

    exit

$ sudo su - cloud_user [Logs into the system using cloud_user profile, '/etc/profile => ~/.bash_profile', and bashrc files in ''~/.bashrc  => /etc/bashrc']

    [sudo] password for cloud_user: 
    Last login: Tue Mar 10 12:29:01 UTC 2026 from localhost on pts/0

$ pwd

    /home/cloud_user

$ echo $USER

    cloud_user

$ id

    uid=1002(cloud_user) gid=1003(cloud_user) groups=1003(cloud_user),10(wheel) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1
    023

Modifying root log in script in /root/.bashrc. Swith to root user

$ sudo su -

root@# # cat .bashrc 

    # .bashrc

    # User specific aliases and functions

    alias rm='rm -i'
    alias cp='cp -i'
    alias mv='mv -i'

    # Source global definitions
    if [ -f /etc/bashrc ]; then
            . /etc/bashrc
    fi

root@# vi .bashrc [add: echo "This is root bash" - to the end of the file then save and exit the editor]


    # .bashrc

    # User specific aliases and functions

    alias rm='rm -i'
    alias cp='cp -i'
    alias mv='mv -i'

    # Source global definitions
    if [ -f /etc/bashrc ]; then
            . /etc/bashrc
    fi
    echo "This is root bash"


root@# cat .bash_profile 

    # .bash_profile

    # Get the aliases and functions
    if [ -f ~/.bashrc ]; then
            . ~/.bashrc
    fi

    # User specific environment and startup programs

    PATH=$PATH:$HOME/bin

    export PATH

root@# vi .bash_profile [add: echo "This is root profile" - to the end of the file then save and exit the editor]

    # .bash_profile

    # Get the aliases and functions
    if [ -f ~/.bashrc ]; then
            . ~/.bashrc
    fi

    # User specific environment and startup programs

    PATH=$PATH:$HOME/bin

    export PATH
    echo "This is root profile"

Testing the login and profile script of root

root@# exit

Do similar to the cloud_user .bashrc and .bash_profile scripts

$ vi .bashrc [add: echo "This is cloud_user bash" - to the end of the file then save and exit the editor]

$ vi .bash_profile [add: echo "This is cloud_user profile" - to the end of the file then save and exit the editor]

$ sudo su - [note the last 2 lines of the o/p are the lines you added to the /root/.bashrc and /root/.bash_profile scripts]
    
    Last login: Tue Mar 10 13:35:50 UTC 2026 on pts/0
    Last failed login: Tue Mar 10 13:52:57 UTC 2026 on pts/0
    This is root bash
    This is root profile

root@# exit [runs the /root/.bash_logout]

Below grants user root access without switching cloud_user directory. So /home/cloud_user/.bashrc should run but not /home/cloud_user/.bash_profile

$ sudo su - cloud_user [note the last 2 lines of the o/p showing the cloud_user scripts is what is being ran]

    Last login: Tue Mar 10 15:48:06 UTC 2026 on pts/0
    This is cloud_user bash
    This is cloud_user profile

$ pwd

    /home/cloud_user

$ id

    uid=1002(cloud_user) gid=1003(cloud_user) groups=1003(cloud_user),10(wheel) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1
    023

$ exit [runs the /home/cloud_user/.bash_logout and which should close the cloud_user command terminal but does seem to do so. Why I don't know. Perhaps custom stuff from PluralSights]

Now undo your additions to the /root/.bashrc, /root/.bash_profile, /home/cloud_user/.bashrc,  /home/cloud_user/.bash_profile scripts removing just the lines you added

Now lets customize a new user home directory. This doeas not apply to existing users

$ sudo su -

root@# cd /etc/skel/ [switches to the directory that has the skeleton/config for when you ae creating a new user]

root@/etc/skel/# ls -la [contains all the scripts used to create user login scripts]

    total 24
    drwxr-xr-x.   2 root root   59 Dec  7  2021 .
    drwxr-xr-x. 111 root root 8192 Mar 13 13:52 ..
    -rw-r--r--.   1 root root   18 Nov 24  2021 .bash_logout
    -rw-r--r--.   1 root root  193 Nov 24  2021 .bash_profile
    -rw-r--r--.   1 root root  231 Nov 24  2021 .bashrc

In any of the above Script you can add a variable and export it using the same shown ther or add any other custom stuff you want each new user created to have.





