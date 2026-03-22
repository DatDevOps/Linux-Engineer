<!-- Installation of OpenLDAP -->

[cloud_user@c71f0fe92f2c ~]$ hostname

    c71f0fe92f2c.mylabserver.com

[cloud_user@c71f0fe92f2c ~]$ sudo su - 

    Last login: Thu Mar 19 14:52:01 UTC 2026 on pts/0

[root@c71f0fe92f2c ~]# ip a s [short form of "ip address show". checks ip address]

    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
        valid_lft forever preferred_lft forever
    2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
        link/ether 0a:ff:c9:ba:33:55 brd ff:ff:ff:ff:ff:ff
        inet 172.31.111.80/20 brd 172.31.111.255 scope global noprefixroute dynamic ens5
        valid_lft 2372sec preferred_lft 2372sec
        inet6 2600:1f18:502:2f01:aa92:7b63:a3d8:d0d0/128 scope global noprefixroute dynamic 
        valid_lft 435sec preferred_lft 125sec
        inet6 fe80::8ff:c9ff:feba:3355/64 scope link noprefixroute 
        valid_lft forever preferred_lft forever
        
[root@c71f0fe92f2c ~]# echo "172.31.111.80 c71f0fe92f2c.mylabserver.com" >> /etc/hosts [appends to the list of ips mapped to server in host file]

[root@c71f0fe92f2c ~]# ping c71f0fe92f2c.mylabserver.com -c 4

    PING c71f0fe92f2c.mylabserver.com (172.31.111.80) 56(84) bytes of data.
    64 bytes from c71f0fe92f2c.mylabserver.com (172.31.111.80): icmp_seq=1 ttl=64 time=0.021 ms
    64 bytes from c71f0fe92f2c.mylabserver.com (172.31.111.80): icmp_seq=2 ttl=64 time=0.038 ms
    64 bytes from c71f0fe92f2c.mylabserver.com (172.31.111.80): icmp_seq=3 ttl=64 time=0.032 ms
    64 bytes from c71f0fe92f2c.mylabserver.com (172.31.111.80): icmp_seq=4 ttl=64 time=0.030 ms

    --- c71f0fe92f2c.mylabserver.com ping statistics ---
    4 packets transmitted, 4 received, 0% packet loss, time 2999ms
    rtt min/avg/max/mdev = 0.021/0.030/0.038/0.007 ms

[root@c71f0fe92f2c ~]# netstat -ltn [used to show which TCP ports are currently listening for incoming connections on the system. t=tcp, l=listening, -n=numeric values for port and hostname (ip addresses and not their hostnames)]

    Active Internet connections (only servers)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State      
    tcp        0      0 0.0.0.0:31297           0.0.0.0:*               LISTEN     
    tcp        0      0 0.0.0.0:2049            0.0.0.0:*               LISTEN     
    tcp        0      0 0.0.0.0:38531           0.0.0.0:*               LISTEN     
    tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN     
    tcp        0      0 0.0.0.0:36304           0.0.0.0:*               LISTEN     
    tcp        0      0 0.0.0.0:20048           0.0.0.0:*               LISTEN     
    tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
    tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN     
    tcp6       0      0 :::2049                 :::*                    LISTEN     
    tcp6       0      0 :::111                  :::*                    LISTEN     
    tcp6       0      0 :::20048                :::*                    LISTEN     
    tcp6       0      0 :::52307                :::*                    LISTEN     
    tcp6       0      0 :::36308                :::*                    LISTEN     
    tcp6       0      0 :::22                   :::*                    LISTEN     
    tcp6       0      0 ::1:25     

OpenLDAP listens on port 389 and for SSL config on port 363

[root@c71f0fe92f2c ~]# rpm -q firewalld [use the RH rpm package manager to check if firewalld is installed]

    package firewalld is not installed

[root@c71f0fe92f2c ~]# yum install firewalld -y

[root@c71f0fe92f2c ~]# systemctl start firewalld

[root@c71f0fe92f2c ~]# systemctl enable firewalld

[root@c71f0fe92f2c ~]# systemctl status firewalld

[root@c71f0fe92f2c ~]# firewall-cmd --permanent --add-service=ldap [allow firewall to allow traffic from the LDAP service]

    success

[root@c71f0fe92f2c ~]# firewall-cmd --reload [reloading to allow LDAP traffic through immediately]
    
    success

[root@c71f0fe92f2c ~]# yum install -y openldap openldap-clients openldap-servers migrationtools.noarch [installs openldap resources]    

Note that the migrationtools.noarch isn't really necessary but it provides the advantage/mechanism of taking users from the /etc/passwd/ file to the  openldap service.
Like  you can create a single user and migrate it over to openldap and use that user as a template to crate other user

<!-- Configuration of OpenLDAP -->

[root@c71f0fe92f2c ~]# cp /usr/share/openldap-servers/DB_CONFIG.example /var/lib/ldap/DB_CONFIG [copies standard configuration across to get started]

[root@c71f0fe92f2c ~]# ls -l /var/lib/ldap/ [checks for our newly copied file]

    total 4
    -rw-r--r--. 1 root root 845 Mar 19 15:48 DB_CONFIG

[root@c71f0fe92f2c ~]# slaptest [ nevermind the error because from the error you can see that they are domain and database related because we have not config our domain. ]

    69bc1ad6 hdb_db_open: database "dc=my-domain,dc=com": db_open(/var/lib/ldap/id2entry.bdb) failed: No such file or directory (2).
    69bc1ad6 backend_startup_one (type=hdb, suffix="dc=my-domain,dc=com"): bi_db_open failed! (2)
    slap_startup failed (test would succeed using the -u switch)


'slaptest' parses the OpenLDAP configuration and checks it for syntax errors, logical errors, and correctness. It is commonly used after making configuration changes to ensure the LDAP server will start cleanly.

Lets now check for our database files

[root@c71f0fe92f2c ~]# ls -l /var/lib/ldap/ [note that ldap files has root as both the user and group]

    total 18996
    -rw-r--r--. 1 root root     2048 Mar 20 12:27 alock
    -rw-------. 1 root root  2351104 Mar 20 12:27 __db.001
    -rw-------. 1 root root 17457152 Mar 20 12:27 __db.002
    -rw-------. 1 root root  1884160 Mar 20 12:27 __db.003
    -rw-r--r--. 1 root root      845 Mar 20 12:27 DB_CONFIG

[root@c71f0fe92f2c ~]# chown ldap.ldap /var/lib/ldap/* [change the ldap files to have ldap as teh user and group. Same as "chown ldap:ldap /var/lib/ldap/*"]

[root@c71f0fe92f2c ~]# systemctl start slapd [starts service now]

[root@c71f0fe92f2c ~]# systemctl enable slapd [starts service upon boot]

    Created symlink from /etc/systemd/system/multi-user.target.wants/slapd.service to /usr/lib/systemd/system/slapd.service.

[root@c71f0fe92f2c ~]# systemctl status slapd [Should be running. Make sure it is RUNNING]

[root@c71f0fe92f2c ~]# netstat -ltn [note that ldap is now listening on port 389]

    Active Internet connections (only servers)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State      
    ...
    ...    
    tcp6       0      0 :::389                  :::*                    LISTEN     
    ...
    ...

[root@c71f0fe92f2c ~]# netstat -lt [ without the -n it shows list the ports by name only and not numbers. Not the ldap service is listening]

    Active Internet connections (only servers)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State      
    ...
    ...    
    tcp6       0      0 [::]:ldap               [::]:*                  LISTEN     
    ...
    ...

[root@c71f0fe92f2c ~]# cd /etc/openldap/schema/ [contains schemas for openldap. It defines what can be created in our directory service]

Schemas define the structure and rules of your LDAP directory, much like a database schema defines tables and columns.
Common schema files and their purpose:
- core.schema
    - The base LDAP schema
    - Defines fundamental objectClasses and attributes
    - Required for almost all LDAP setups
- cosine.schema
    - Defines common attributes (email, phone, organization info)
    - Frequently used by other schemas
- inetorgperson.schema
    - Defines the inetOrgPerson objectClass
    - Commonly used for users
    - Includes attributes like: mail, givenName, sn, uid
- nis.schema
    - Supports NIS‑style objects
    - Used for UNIX account integration
    - Defines posixAccount, posixGroup, etc.
- sudo.schema
    - Enables LDAP‑based sudo rules
    - Required for sudo integration with LDAP/SSSD

[root@c71f0fe92f2c schema]# ldapadd -Y EXTERNAL -H ldapi:/// -D "cn=config" -f cosine.ldif [ note tha /// means localhost]

    SASL/EXTERNAL authentication started
    SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
    SASL SSF: 0
    adding new entry "cn=cosine,cn=schema,cn=config"

[root@c71f0fe92f2c schema]# ldapadd -Y EXTERNAL -H ldapi:/// -D "cn=config" -f nis.ldif [ note tha /// means localhost]

    SASL/EXTERNAL authentication started
    SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
    SASL SSF: 0
    adding new entry "cn=nis,cn=schema,cn=config"

[root@c71f0fe92f2c schema]# cd [move back to root home directory]

[root@c71f0fe92f2c ~]# slappasswd -s Password1 -n > rootpwd [creates password for ldap]

[root@c71f0fe92f2c ~]# cat rootpwd [prints the above password hash]

    {SSHA}O4M2/qwYSPJ0FOM/CdpDmFC+kpOVwKyr

[root@c71f0fe92f2c ~]# vi config.ldif [I did not get have this file and had to create it manually using the current content. Then modified it replacing the current with using replacement below. Note that in the replacement we replace hostnames 'dc=example,dc=com' ==>  'dc=c71f0fe92f1c.mylabserver,dc=com' and added password above]

current content

    dn: olcDatabase={2}hdb,cn=config
    changetype: modify
    replace: olcSuffix
    olcSuffix: dc=example,dc=com

    dn: olcDatabase={2}hdb,cn=config
    changetype: modify
    replace: olcRootDN
    olcRootDN: cn=Manager,dc=example,dc=com

    dn: olcDatabase={2}hdb,cn=config
    changetype: modify
    replace: olcRootPW
    olcRootPW: <yourpassword>

    dn: cn=config
    changetype: modify
    replace: olcLogLevel
    olcLogLevel: 0

    dn: olcDatabase={1}monitor,cn=config
    changetype: modify
    replace: olcAccess
    @

replacement content:

    dn: olcDatabase={2}hdb,cn=config
    changetype: modify
    replace: olcSuffix
    olcSuffix: dc=c71f0fe92f1c.mylabserver,dc=com

    dn: olcDatabase={2}hdb,cn=config
    changetype: modify
    replace: olcRootDN
    olcRootDN: cn=Manager,dc=c71f0fe92f1c.mylabserver,dc=com

    dn: olcDatabase={2}hdb,cn=config
    changetype: modify
    replace: olcRootPW
    olcRootPW: {SSHA}O4M2/qwYSPJ0FOM/CdpDmFC+kpOVwKyr

    dn: cn=config
    changetype: modify
    replace: olcLogLevel
    olcLogLevel: 0

    dn: olcDatabase={1}monitor,cn=config
    changetype: modify
    replace: olcAccess
    @


[root@c71f0fe92f2c ~]# ldapmodify -Y EXTERNAL -H ldapi:/// -f config.ldif [if no error, it means our configurtion was all good]

    SASL/EXTERNAL authentication started
    SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
    SASL SSF: 0
    modifying entry "olcDatabase={2}hdb,cn=config"

    modifying entry "olcDatabase={2}hdb,cn=config"

    modifying entry "olcDatabase={2}hdb,cn=config"

    modifying entry "cn=config"

    ldapmodify: invalid format (line 24) entry: "olcDatabase={1}monitor,cn=config"


<!-- create OpenLDAP directory structure -->

[root@c71f0fe92f2c ~]# vi structure.ldif [creates the file if does not exist and add the below content and then save and exit. This file contains the directory structure]

    dn: dc=c71f0fe92f1c.mylabserver,dc=com
    dc: c71f0fe92f1c.mylabserver
    ObjectClass: top
    ObjectClass: domain

    dn: ou=people,dc=c71f0fe92f1c.mylabserver,dc=com
    ou: people
    ObjectClass: top
    ObjectClass: OrganizationalUnit

    dn: ou=group,dc=c71f0fe92f1c.mylabserver,dc=com
    ou: group
    ObjectClass: top
    ObjectClass: OrganizationalUnit

This command below adds LDAP entries (such as organizational units, users, or groups) into the directory database using the credentials of the directory manager, reading the data from the file structure.ldif.

-x ==> Enables simple authentication
-W ==> Prompts for the bind password
-D ==> Specifies the bind DN (Distinguished Name - "cn=Manager,dc=c71f0fe92f1c.mylabserver,dc=com")
-f ==> Reads LDAP entries from the LDIF file structure.ldif. This file usually contains:

    dc= base entry
    ou=People
    ou=Groups
    or other directory structure objects

[root@c71f0fe92f1c ~]# ldapadd -x -W -D "cn=Manager,dc=c71f0fe92f1c.mylabserver,dc=com" -f structure.ldif [make usre to enter the plain text password created above]
    
    Enter LDAP Password: 
    adding new entry "dc=c71f0fe92f1c.mylabserver,dc=com"

    adding new entry "ou=people,dc=c71f0fe92f1c.mylabserver,dc=com"

    adding new entry "ou=group,dc=c71f0fe92f1c.mylabserver,dc=com"

[root@c71f0fe92f1c ~]# ldapsearch -x -W -D "cn=Manager,dc=c71f0fe92f1c.mylabserver,dc=com" -b "dc=c71f0fe92f1c.mylabserver,dc=com" -s  [make usre to enter the plain text password created above]

    sub "(objectclass=organizationalUnit)"
    Enter LDAP Password: 
    # extended LDIF
    #
    # LDAPv3
    # base <dc=c71f0fe92f1c.mylabserver,dc=com> with scope subtree
    # filter: (objectclass=organizationalUnit)
    # requesting: ALL
    #

    # people, c71f0fe92f1c.mylabserver.com
    dn: ou=people,dc=c71f0fe92f1c.mylabserver,dc=com
    ou: people
    objectClass: top
    objectClass: organizationalUnit

    # group, c71f0fe92f1c.mylabserver.com
    dn: ou=group,dc=c71f0fe92f1c.mylabserver,dc=com
    ou: group
    objectClass: top
    objectClass: organizationalUnit

    # search result
    search: 2
    result: 0 Success

    # numResponses: 3
    # numEntries: 2    

[root@c71f0fe92f1c ~]# vi group.ldif [creates the file. Add the content below]

    dn: cn=ldapusers,ou=group,dc=c71f0fe92f1c.mylabserver,dc=com
    objectClass: posixGroup
    cn: ldapusers
    gidNumber: 4000

[root@c71f0fe92f1c ~]# cat group.ldif 

    dn: cn=ldapusers,ou=group,dc=c71f0fe92f1c.mylabserver,dc=com
    objectClass: posixGroup
    cn: ldapusers
    gidNumber: 4000

[root@c71f0fe92f1c ~]# ldapadd -x -W -D "cn=Manager,dc=c71f0fe92f1c.mylabserver,dc=com" -f group.ldif 
    
    Enter LDAP Password: 
    adding new entry "cn=ldapusers,ou=group,dc=c71f0fe92f1c.mylabserver,dc=com"

To add users we will use the  migration tool we installed earlier

[root@c71f0fe92f1c migrationtools]# vi migrate_common.ph 

Replace:

    # Default DNS domain
    $DEFAULT_MAIL_DOMAIN = "padl.com";

    # Default base
    $DEFAULT_BASE = "dc=padl,dc=com";

with:

    # Default DNS domain
    $DEFAULT_MAIL_DOMAIN = "c71f0fe92f1c.mylabserver.com";

    # Default base
    $DEFAULT_BASE = "dc=c71f0fe92f1c.mylabserver,dc=com";

[root@c71f0fe92f1c migrationtools]# /usr/share/migrationtools//migrate_passwd.pl passwd user.ldif [this will take the users in the /etc/passwd file and convert them to an ldif format that can be imported into openldap]

[root@c71f0fe92f1c ~]# cat user.ldif 

    dn: uid=cloud_user,ou=People,dc=c71f0fe92f1c.mylabserver,dc=com
    uid: cloud_user
    cn: cloud_user
    objectClass: account
    objectClass: posixAccount
    objectClass: top
    objectClass: shadowAccount
    userPassword: {crypt}$6$ynKQ36WX$ESUYbpJhYUAI0rQIJHA.Xs7ixmQJXRAG1s.hIF3eAbcR8rTrZl5szKs9JRScG3B.F7iQ3pyRbdTRaDbSofuV00
    shadowLastChange: 20521
    shadowMin: 0
    shadowMax: 99999
    shadowWarning: 7
    loginShell: /bin/bash
    uidNumber: 1002
    gidNumber: 1003
    homeDirectory: /home/cloud_user

[root@c71f0fe92f1c ~]# vi user.ldif  [change file content to below - chnage user name to fred and uid/gid to 4000 to distinguish our regular (starts from 1000) user from ldap user]

    dn: uid=fred,ou=People,dc=c71f0fe92f1c.mylabserver,dc=com
    uid: fred
    cn: fred
    objectClass: account
    objectClass: posixAccount
    objectClass: top
    objectClass: shadowAccount
    userPassword: {crypt}$6$ynKQ36WX$ESUYbpJhYUAI0rQIJHA.Xs7ixmQJXRAG1s.hIF3eAbcR8rTrZl5szKs9JRScG3B.F7iQ3pyRbdTRaDbSofuV00
    shadowLastChange: 20521
    shadowMin: 0
    shadowMax: 99999
    shadowWarning: 7
    loginShell: /bin/bash
    uidNumber: 4000
    gidNumber: 4000
    homeDirectory: /home/fred

[root@c71f0fe92f1c ~]# ldapadd -x -W -D "cn=Manager,dc=c71f0fe92f1c.mylabserver,dc=com" -f user.ldif [adding user fred as ldap user. User the plain password set above - Password1]

    Enter LDAP Password: 
    adding new entry "uid=fred,ou=People,dc=c71f0fe92f1c.mylabserver,dc=com"

<!-- OpenLDAP Authentication -->