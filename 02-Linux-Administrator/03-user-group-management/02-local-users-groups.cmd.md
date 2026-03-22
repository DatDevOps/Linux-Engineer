<!-- Users -->

$ id [o/p's user info including the Wheel group. Users with admin access must belong to the wheel group. Add users you want to have sudo access to the wheel group ]

    uid=1002(cloud_user) gid=1003(cloud_user) groups=1003(cloud_user),10(wheel) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023

Note the the cloud_user above can access resources using the cloud_user(primary group) and wheel(secondary group) group. When the cloud_user creates resources, it uses the uid and gid and will result in the created resources having the user=cloud_user and group=cloud_user

$ id -g [returns user primary group id]

    1003

$ id -G [returns user primary and secondary group id]

    1003 10

$ id -Gn [returns user primary and secondary group names]

    cloud_user wheel

 <!-- creating local users -->

$ sudo useradd -m user1 [the -m switch creates a directory for the new user under /home. It is the default so you don't have to add it. it also creates a private group for the user1]

    password for cloud_user: 

$ id user1

    uid=1004(user1) gid=1005(user1) groups=1005(user1)


$ tail -n 1 /etc/passwd [Shows the last user added to /etc/passwd]

    user1:x:1004:1005::/home/user1:/bin/bash

$ ls /home/ [shows a directory was created for user1 under /home]

    cloud_user  ssm-user  user1

$ sudo ls -la /home/user1 [retruns files created in new user1 home directory which used /etc/skel as discussed in the preceeding lesson]

    total 12
    drwx------. 2 user1 user1  59 Mar 13 20:18 .
    drwxr-xr-x. 5 root  root   50 Mar 13 20:18 ..
    -rw-r--r--. 1 user1 user1  18 Nov 24  2021 .bash_logout
    -rw-r--r--. 1 user1 user1 193 Nov 24  2021 .bash_profile
    -rw-r--r--. 1 user1 user1 231 Nov 24  2021 .bashrc

[ Creates a new user user2 with no private group but instead adds the user to the existing group users. Note we are not using the -m switch but the /home/user2 will be created anyway]

$ sudo useradd -N user2 -g users -G adm 

*NB
N = no private group
g = primary group - users
G = secondary group - adm

$ id user2

    uid=1005(user2) gid=100(users) groups=100(users),4(adm)

$ tail -n 1 /etc/passwd [Shows the last user added to /etc/passwd]

    user2:x:1005:100::/home/user2:/bin/bash

$ ls /home [a new home directory was created for user2]

    cloud_user  ssm-user  user1  user2

[ Creates a new user user3 with private group. Note we are not using the -m switch but the /home/user2 will be created anywaya and assign the shell of the user to be /bin/sh instead of the default /bin/bash on the system]

$ sudo useradd user3 -G adm -s /bin/sh 

$ id user3

    uid=1006(user3) gid=1006(user3) groups=1006(user3),4(adm)

$ sudo tail -n 1 /etc/passwd

    user3:x:1006:1006::/home/user3:/bin/sh    

See below the script that runn the useradd command:

$ ls -l /sbin/adduser 

    lrwxrwxrwx. 1 root root 7 Sep 17  2019 /sbin/adduser -> useradd

% adding password to our users

$ sudo passwd user1

    [sudo] password for cloud_user: 
    Changing password for user user1.
    New password: 
    BAD PASSWORD: The password is shorter than 8 characters
    Retype new password: 
    passwd: all authentication tokens updated successfully.

$ sudo grep user1 /etc/shadow [using the delimiter, :, the second field contains the hash of the user1 password]

    user1:$6$rCR3.BPi$hcYiP54VIigR7.Ps7UPpaiDj2f2E0scetxT1B7rJSRe/sJiHw1NI1RKPfEYHhKdFs0U0C.o.kiywDn3A7OlLX0:20525:0:99999:7:::

Note how user2 and user3 has !!, meaning they dont have password set for them
$ sudo grep user. /etc/shadow [returns items, users in this case, that has exactly one chanracter after the word 'user']

    rpcuser:!!:16342::::::
    cloud_user:$6$7Y6dMCy9$j0FL0bPH2PDzAtNZOjlZrYKtsjbS860Ii3r7Zj.WnfE21SfwbHNf055UaWoC
    8SuH/W42OehEtrYV0K1VNk3WV.:20492:0:99999:7:::
    ssm-user:!!:17812:0:99999:7:::
    user1:$6$rCR3.BPi$hcYiP54VIigR7.Ps7UPpaiDj2f2E0scetxT1B7rJSRe/sJiHw1NI1RKPfEYHhKdFs0U0C.o.kiywDn3A7OlLX0:20525:0:99999:7:::
    user2:!!:20525:0:99999:7:::
    user3:!!:20525:0:99999:7:::

Lets now add password for user2 and user3 using a different menthod. The output of the 2st command is passed to the second and hence it does not ask you to enter password for user2

$ echo 'user2:Passwword1' | sudo chpasswd

    [sudo] password for cloud_user: 

$ echo 'user3:Passwword1' | sudo chpasswd

$ sudo grep user2 /etc/shadow

    user2:$6$Axc5zTHupHDoVd$ossTRN5dgM3MLkAJWCzDqDM9zR0nSRM9WBAhonb2/ff4b2TYoG8l/vHnlPluKMI1dCaUNXuwlKdVlA338ir3Z/:20525:0:99999:7:::

$ sudo grep user3 /etc/shadow

    user3:$6$gdHb2MVmZ1/gWgFU$/LceReuyA8A.BIvi1bwJUfIQ1Y6zIQoGwRArAhN0GnrOfKzo9DOZtk6K9zaViAQhYzliNlJMZmw6ApilheRFA.:20525:0:99999:7:::


Another way to add password for a user is shown below but only works for RH and Centos but not for Debian systems

$ sudo useradd user33 -G adm -s /bin/sh 

$ sudo tail -n 1 /etc/passwd

    user33:x:1007:1007::/home/user33:/bin/sh

$ echo password1 | sudo passwd user33 --stdin [where the password is password1]

$ sudo grep user33 /etc/shadow

    user33:!!:20525:0:99999:7:::

$ echo password1 | sudo passwd user33 --stdin [Only works for RH and Centos but not for Debian systems]

    Changing password for user user33.
    passwd: all authentication tokens updated successfully.

$ sudo grep user33 /etc/shadow

    user33:$6$S24VEthE$PPm7MclzOXGtxZsShLhxuWTpD4dUAgOeEBIyU3SXFtKmuXQaDP4E8d0nscKFGejZDPBy6NLDUwgwE0avQmE.z/:20525:0:99999:7:::


<!-- User password Age data -->

$  chage -l cloud_user [It displays password aging and expiration information for the user cloud_user.]


    Last password change                                    : Feb 08, 2026
    Password expires                                        : never
    Password inactive                                       : never
    Account expires                                         : never
    Minimum number of days between password change          : 0
    Maximum number of days between password change          : 99999
    Number of days of warning before password expires       : 7

$ grep user2 /etc/passwd [the x in the user1 info returned indicates that user1 password is stored in the /etc/shadow. Does not contain password hash so that ordinary users can't read them]

    user2:x:1005:100::/home/user2:/bin/bash   

This command will store user password hash in the /etc/passwd/ file without using x like above. Users can now read the hash and perhaps use brute force to decrypt it. Not ideal and is a security risk

$ sudo pwunconv 

[sudo] password for cloud_user: 

$ grep user2 /etc/passwd [ placeholder x replaced by user password hash ]

    user2:$6$Axc5zTHupHDoVd$ossTRN5dgM3MLkAJWCzDqDM9zR0nSRM9WBAhonb2/ff4b2TYoG8l/vHnlPluKMI1dCaUNXuwlKdVlA338ir3Z/:1005:100::/home/user2:/bin/bash

This commands replaces the user password hash with x in the /etc/passwd/ file  with isthe default behaviour. This is ideal
$ sudo pwconv

$ grep user2 /etc/passwd [confirms user password hash is replaced withthe placeholder x]

    user2:x:1005:100::/home/user2:/bin/bash

$ chage --help

    Usage: chage [options] LOGIN

    Options:
    -d, --lastday LAST_DAY        set date of last password change to LAST_DAY
    -E, --expiredate EXPIRE_DATE  set account expiration date to EXPIRE_DATE
    -h, --help                    display this help message and exit
    -I, --inactive INACTIVE       set password inactive after expiration
                                    to INACTIVE
    -l, --list                    show account aging information
    -m, --mindays MIN_DAYS        set minimum number of days before password
                                    change to MIN_DAYS
    -M, --maxdays MAX_DAYS        set maximum number of days before password
                                    change to MAX_DAYS
    -R, --root CHROOT_DIR         directory to chroot into
    -W, --warndays WARN_DAYS      set expiration warning days to WARN_DAYS

$ sudo chage -M 40 user1 [sets maximum day for password b4 user is forced to change it]

$ sudo chage -l user1 [now contains the number of days,40, user have between password change]

    Last password change                                    : Mar 16, 2026
    Password expires                                        : Apr 25, 2026
    Password inactive                                       : never
    Account expires                                         : never
    Minimum number of days between password change          : 0
    Maximum number of days between password change          : 40
    Number of days of warning before password expires       : 7

$ sudo grep user1 /etc/shadow [now contains the number of days,40, user have between password change]

    user1:$6$rCR3.BPi$hcYiP54VIigR7.Ps7UPpaiDj2f2E0scetxT1B7rJSRe/sJiHw1NI1RKPfEYHhKdFs0U0C.o.kiywDn3A7OlLX0:20528:0:40:7:::

$ sudo passwd -l user1 [locks user1 password so they can't login in]

    Locking password for user user1.
    passwd: Success

$ sudo grep user1 /etc/shadow [note the prepended !! to the password hash. This indicates that the password is invalid, silimar to when the password is not set]

    user1:!!$6$rCR3.BPi$hcYiP54VIigR7.Ps7UPpaiDj2f2E0scetxT1B7rJSRe/sJiHw1NI1RKPfEYHhKdFs0U0C.o.kiywDn3A7OlLX0:20528:0:40:7:::

$ sudo passwd -u user1 [unlocks user1 password so they can now login in again]

    Unlocking password for user user1.
    passwd: Success

$ sudo grep user1 /etc/shadow [note that the prepended !! is now removed. This indicates that the password is now valid]

    user1:$6$rCR3.BPi$hcYiP54VIigR7.Ps7UPpaiDj2f2E0scetxT1B7rJSRe/sJiHw1NI1RKPfEYHhKdFs0U0C.o.kiywDn3A7OlLX0:20528:0:40:7:::


<!-- user defaults -->

This are defaults that are aplied to users if you do not provide them at the point of creation

$ less /etc/login.defs [contains all user defaults]

        #
        # Please note that the parameters in this configuration file control the
        # behavior of the tools from the shadow-utils component. None of these
        # tools uses the PAM mechanism, and the utilities that use PAM (such as the
        # passwd command) should therefore be configured elsewhere. Refer to
        # /etc/pam.d/system-auth for more information.
        #

        # *REQUIRED*
        #   Directory where mailboxes reside, _or_ name of file, relative to the
        #   home directory.  If you _do_ define both, MAIL_DIR takes precedence.
        #   QMAIL_DIR is for Qmail
        #
        #QMAIL_DIR      Maildir
        MAIL_DIR        /var/spool/mail
        #MAIL_FILE      .mail

        # Password aging controls:
        #
        #       PASS_MAX_DAYS   Maximum number of days a password may be used.
        #       PASS_MIN_DAYS   Minimum number of days allowed between password changes.
        #       PASS_MIN_LEN    Minimum acceptable password length.
        #       PASS_WARN_AGE   Number of days warning given before a password expires.
        #
        PASS_MAX_DAYS   99999
        PASS_MIN_DAYS   0
        PASS_MIN_LEN    5
        PASS_WARN_AGE   7

        #
        # Min/max values for automatic uid selection in useradd
        #
        UID_MIN                  1000
        UID_MAX                 60000
        # System accounts
        SYS_UID_MIN               201
        SYS_UID_MAX               999

        #
        # Min/max values for automatic gid selection in groupadd
        #
        GID_MIN                  1000
        GID_MAX                 60000
        # System accounts
        SYS_GID_MIN               201
        SYS_GID_MAX               999

        #
        # If defined, this command is run when removing a user.
        # It should remove any at/cron/print jobs etc. owned by
        # the user to be removed (passed as the first argument).
        #
        #USERDEL_CMD    /usr/sbin/userdel_local

        #
        # If useradd should create home directories for users by default
        # On RH systems, we do. This option is overridden with the -m flag on

        # useradd command line.
        #
        CREATE_HOME     yes

        # The permission mask is initialized to this value. If not specified, 
        # the permission mask will be initialized to 022.
        UMASK           077

        # This enables userdel to remove user groups if no members exist.
        #
        USERGROUPS_ENAB yes

        # Use SHA512 to encrypt password.
        ENCRYPT_METHOD SHA512

$ sudo useradd -D [returns the defaults of the logged in user]

    GROUP=100
    HOME=/home
    INACTIVE=-1
    EXPIRE=
    SHELL=/bin/bash
    SKEL=/etc/skel
    CREATE_MAIL_SPOOL=yes

$ sudo useradd -Ds /bin/sh [changes the logged-in user shell from bash to sh]

$ sudo useradd -D [shows the change made using useradd]

    GROUP=100
    HOME=/home
    INACTIVE=-1
    EXPIRE=
    SHELL=/bin/sh
    SKEL=/etc/skel
    CREATE_MAIL_SPOOL=yes

$ sudo vi /etc/default/useradd [editing user default using vi instead of useradd. Change the user shell from /bin/sh = /bin/bash. Save and exit]

    # useradd defaults file
    GROUP=100
    HOME=/home
    INACTIVE=-1
    EXPIRE=
    SHELL=/bin/bash
    SKEL=/etc/skel
    CREATE_MAIL_SPOOL=yes

% modify and delete user accounts

$ sudo usermod -c "User One" user1 [adds the user fullname]

    [sudo] password for cloud_user: 

$ grep user1 /etc/passwd [user fullname added]

    user1:x:1004:1005:User One:/home/user1:/bin/bash

$ chsh -l [list all available shell]

    /bin/sh
    /bin/bash
    /usr/bin/sh
    /usr/bin/bash

$ grep cloud_user /etc/passwd [shows that cloud_user shell is bash]

    cloud_user:x:1002:1003::/home/cloud_user:/bin/bash   

$ chsh -s /bin/sh cloud_user [change logged in user, cloud_user, shell to sh ]

    Changing shell for cloud_user.
    Password: 
    Shell changed.

$ grep cloud_user /etc/passwd [shows the cloud_user shell has been changed from /bin/bash to /bin/sh]

    cloud_user:x:1002:1003::/home/cloud_user:/bin/sh

$ sudo usermod -s /bin/bash cloud_user [change cloud_user shell back to bash using usermod instead of chsh command]

    [sudo] password for cloud_user: 

$ grep cloud_user /etc/passwd [shows that shell has beenchanged back to bash]

    cloud_user:x:1002:1003::/home/cloud_user:/bin/bash  
    
To change the shell of other users, switch to root user, and use usermod with root privileges

$ ls /home/

    cloud_user  ssm-user  user1 user2  user3  user33

$ sudo userdel -r user1 [deletes user1 including it home directory /home/user1]
    
    [sudo] password for cloud_user: 

$ ls /home/

    cloud_user  ssm-user  user2  user3  user33

$ sudo userdel user2  [without using the -r deletes user2 but keeps the user2 home directory /home/user2. The user name of the directory is no  changed from user2 to the uid of user2]

    [sudo] password for cloud_user: 

$ ls /home

    cloud_user  ssm-user  user2  user3  user33

$ ls -lh /home [left behind user2 home directory now has the user name as the uid which is 1005]

    total 4.0K
    drwx------. 16 cloud_user cloud_user 4.0K Mar 16 13:37 cloud_user
    drwx------.  3 ssm-user   ssm-user     74 Oct  8  2018 ssm-user
    drwx------.  2       1005 users        59 Mar 13 20:28 user2
    drwx------.  2 user3      user3        59 Mar 13 20:40 user3
    drwx------.  2 user33     user33       59 Mar 13 21:31 user33

$ sudo find home -uid 1005 -delete [scrubs/delete user2 home directory using its uid. You can copy it to another location if you do not want to delete it]

$ ls /home [user2 /home/user2 gone]

    cloud_user  ssm-user user3  user33

$ sudo userdel -r user3 [deletes user3 including it home directory /home/user3]

$ ls /home [user2 /home/user2 gone]

    cloud_user  ssm-user  user33


<!-- Groups -->

$ id [returns logged in user info including groups they belong to. The user cloud_user belong to the cloud-user and wheel (root) groups]

    uid=1002(cloud_user) gid=1003(cloud_user) groups=1003(cloud_user),10(wheel) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023

$ grep cloud_user /etc/group [shows the groups the user cloud_user belong to]

    wheel:x:10:cloud_user
    cloud_user:x:1003:

Note that when a user is created, a private group is also created and the user is assigne to the private/default. Usually the name of the group is also the name of the user

$ newgrp wheel [Starts a new subshell,  in this case sets wheel(root) as the effective primary group, applies group permissions immediately ]

$ id

    uid=1002(cloud_user) gid=10(wheel) groups=10(wheel),1003(cloud_user) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023

$ touch g1  [new file that should belongs to wheel group]

$ ls -l g1 [new file belongs to wheel group]

    -rw-r--r--. 1 cloud_user wheel 0 Mar 17 12:24 g1

$ exit [exits subshell and return cloud_user to its primary/default group]

    exit

$ id [user back to it's primary group]

    uid=1002(cloud_user) gid=1003(cloud_user) groups=1003(cloud_user),10(wheel) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023

$ touch g2 [new file that should belongs to cloud_user group]

$ ls -l g2 [new file belongs to cloud_user group]

    -rw-rw-r--. 1 cloud_user cloud_user 0 Mar 17 12:32 g2


%  creating local groups

$ sudo groupadd sales [creates a new group called sales]

    [sudo] password for cloud_user: 

$ grep sales /etc/group [checks for all groups and return only the group called sales]

    sales:x:1008:

NB: users password hash is stored in the /etc/shadow while group password are stored in the /etc/gshadow file. Not the difference in name]

$ sudo grep sales /etc/gshadow [group password is invalid or not set evident by ! in the gshadow file]

    sales:!::

Other group commands are listed below and use the man pages ["man groupadd" or "man groupdel"] for the various flags to use:

- groupmod [modifies a group. You would not be doing a lot of that]

- groupdel [deletes a groups]


$ id [o/p's user info including the Wheel group. Users with admin access must belong to the wheel group. Add users you want to have sudo access to the wheel group ]

    uid=1002(cloud_user) gid=1003(cloud_user) groups=1003(cloud_user),10(wheel) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023

Note the the cloud_user above can access resources using the cloud_user(primary group) and wheel(secondary group) group. When the cloud_user creates resources, it uses the uid and gid and will result in the created resources having the user=cloud_user and group=cloud_user

$ id -g [returns user primary group id]

    1003

$ id -G [returns user primary and secondary group id]

    1003 10

$ id -Gn [returns user primary and secondary group names]

    cloud_user wheel

$ sudo usermod -G sales,wheel cloud_user [assigns the user cloud_user to the sales and wheel groups]

    [sudo] password for cloud_user: 

$ id -Gn cloud_user [returns the cloud_user primary and secondary groups]

    cloud_user wheel sales

$ sudo usermod -G sales cloud_user  [assigns the user cloud_user to only the sales groups]

$ id -Gn cloud_user [returns the cloud_user primary and secondary groups]

    cloud_user sales

$ sudo usermod -G sales,wheel cloud_user [assigns the user cloud_user to the sales and wheel groups]

$ id -Gn cloud_user [returns the cloud_user primary and secondary groups]

    cloud_user wheel sales

$ sudo usermod -G wheel cloud_user [assigns the user cloud_user to only the wheel groups. This is what it was before all our changes above]

$ id -Gn cloud_user [returns the cloud_user primary and secondary groups]

    cloud_user wheel    

<!-- adding users to groups -->

$ gpasswd --help [display all our options that can be used when adding users to group/s]

    Usage: gpasswd [option] GROUP

    Options:
    -a, --add USER                add USER to GROUP
    -d, --delete USER             remove USER from GROUP
    -h, --help                    display this help message and exit
    -Q, --root CHROOT_DIR         directory to chroot into
    -r, --delete-password         remove the GROUP's password
    -R, --restrict                restrict access to GROUP to its members
    -M, --members USER,...        set the list of members of GROUP
    -A, --administrators ADMIN,...
                                    set the list of administrators for GROUP
    Except for the -A and -M options, the options cannot be combined.

$ sudo gpasswd -a cloud_user sales [adds a single user, cloud_user, to the sales group]

    Adding user cloud_user to group sales

$ sudo gpasswd -M cloud_user,root sales  [adds a list of users, cloud_user and root, to the sales group]

$ grep cloud_user /etc/group [returns all cloud_user groups]

    wheel:x:10:cloud_user
    cloud_user:x:1003:
    sales:x:1008:cloud_user,root

Now log out and log back in to refresh 'id' command list. Otherwise the change of adding cloud_user to the sales group will not be reflected when you run 'id -Gn'

Or better still do the below to refresh the list

$ sudo su - cloud_user

    [sudo] password for cloud_user: 
    Last login: Tue Mar 17 12:14:48 UTC 2026 from localhost on pts/0

$ id -Gn

    cloud_user wheel sales

$ sudo yum install httpd w3m -y [install apache server and command line web browser, w3m]

$ ls -ld /var/www/html/ [not the permissions for Others.]

    drwxr-xr-x. 2 root root 6 May 30  2023 /var/www/html/

$ sudo vi /var/www/html/index.html [Addd the text "Welcome" , save and close using :x]

$ sudo cat /var/www/html/index.html

    Welcome

$ sudo systemctl status httpd [ should show running. Else start it if stopped and enable it if you want it running each time system boots up]

$ sudo systemctl start httpd [ starts the httpd service immediately]

$ sudo systemctl enable httpd [ stars service when machine boots up]

$ w3m localhost [should open a page displaying "Welcome". Mine did not work because of pluralsight config I guess]

    w3m: Can't load localhost.

The 'w3m localhost' command uses the permission for Others in /var/www/html/ because the service is not root nor belong to the root group

$ grep apache /etc/group [apache server group created when service was installed]

    apache:x:48:

$ sudo chgrp -R apache /var/www/ [recursively change all file group to apache]

    [sudo] password for cloud_user: 

$ ls -ld /var/www/ [shows files belong to the apache group]

    drwxr-xr-x. 4 root apache 31 Mar 17 13:18 /var/www/

$ sudo chmod -R o= /var/www/ [recursively removing all permissions for Others]

$ ls -ld /var/www/ [shows all permissions removed for Others]

    drwxr-x---. 4 root apache 31 Mar 17 13:18 /var/www/

$ sudo ls -ld /var/www/html [shows files belong to the apache group]

    drwxr-x---. 2 root apache 23 Mar 17 13:22 /var/www/html

$ sudo ls -ld /var/www/html/index.html [shows files belong to the apache group]

    -rw-r-----. 1 root apache 8 Mar 17 13:22 /var/www/html/index.html

$ w3m localhost [should open a page displaying "Welcome". Mine did not work because of pluralsight config I guess]

    w3m: Can't load localhost.

The 'w3m localhost' command now uses the permission for 'apache' group in /var/www/html/ to load file and not that of others since all permissions for others have been removed. This is more secure.

$ sudo -i [enter password and become root]

root@# cd /var/www/html/ [change to directory]

root@html# ls -ld . [list all directories in this path. Notice the x bit in the group part of the permissions]

    drwxr-x---. 2 root apache 23 Mar 17 13:22 .

The setgid bit on directories Helps teams share files without manual chgrp

root@html# chmod g+s . [change directory permissions to always use parent directory when new files are created in the html directory. ]

root@html# ls -ld . [x bit is now changed to the s bit ensuring all files in html have the same group permission as parent]

    drwxr-s---. 2 root apache 23 Mar 17 13:22 .

root@html# umask 027 [removes all permission for others, 7, write permissions for groups, 2, and removes nothing for users]

root@html# vi  test.html [creates a file test.html file. Add "This is a test page" using the editor, save and exit]

root@html# ls -l [files have ame permissions as parent directory]

    total 8
    -rw-r-----. 1 root apache  8 Mar 17 13:22 index.html
    -rw-r-----. 1 root apache 20 Mar 17 14:35 test.html

root@html# w3m localhost/test.html  [should open a page displaying "This is a test page". Mine did not work because of pluralsight config I guess]

    w3m: Can't load localhost/test.html.

root@html# id

    uid=0(root) gid=0(root) groups=0(root),1008(sales) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023

root@html# exit

    exit

$ id -Gn [displays primary and secondary groups]

    cloud_user wheel sales

$ id -gn [displays primary group]

    cloud_user

Rember the 'newgrp' command starts a new subshell, in this case sets adm as the effective primary group, applies group permissions immediately
if you try to set a user, cloud_user in this case, to a primary you don't belong to, you are prompted to enter group password as shown below.
Also note that the /etc/gshadow follows the format "group_name : group_password : group_admins : group_members" Any empty field means it is not set

$ grep adm /etc/group [returns groups that are admin]
    adm:x:4:user3,user33
    printadmin:x:988:

$ sudo grep adm /etc/gshadow [ note the empty fields on line 1, :::, and the ! (invalid/not set password) on line 2 ]
    adm:::user3,user33
    printadmin:!::

Note that if logged in user belonged the adm group, it would not have prompted you for password
$ newgrp adm  [ You will always get invalid password because the password is not set]

    Password: 
    Invalid password

$ sudo gpasswd adm [creates a password for the group adm]

    [sudo] password for cloud_user:         <------------ enter cloud_user/logged-in user pasword here
    Changing the password for group adm
    New Password:                           <------------ enter adm group pasword here
    Re-enter new password:                  <------------ re-enter adm group pasword here

$ newgrp adm [starts a subshell and switches user primary group to adm]

    Password:  <------------ enter adm group pasword here

$ id [note that the user now belonges to the adm group]

    uid=1002(cloud_user) gid=4(adm) groups=4(adm),10(wheel),1003(cloud_user),1008(sales) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023

$ id -Gn [ user primary and all secondary groups]

    adm wheel cloud_user sales

$ id -gn [ user primary group ]

    adm

$ exit [exits subshell and switches a yser back to its default primary group]

    exit

$ id -gn

    cloud_user
