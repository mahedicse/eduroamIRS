## eduroam Institutional Radius Server (IRS) Configuration Using CentOS-7

### Server Basic Configuration for IRS

#### Update System

```` bash
# yum update -y

````

#### Hostname Configuration:
```` bash 
# vim /etc/hostname

idp-irs.ins-XY.ac.bd
````
```` bash 
# hostname idp-irs.ins-XY.ac.bd
````
##### Check configuration:
```` bash 
# hostname

idp-irs.ins-XY.ac.bd
````
```` bash 
# hostname -d
ins-XY.ac.bd
````
#### Disable Selinux:
```` bash
[root@pust ~]# vim /etc/selinux/config

SELINUX=disabled

# reboot
````

#### Firewall Configuration 

```` bash
firewall-cmd --zone=public --permanent --add-service=http

firewall-cmd --zone=public --permanent --add-service=https

firewall-cmd --zone=public --permanent --add-service=radius

firewall-cmd --zone=public --permanent --add-port=1812/tcp

firewall-cmd --zone=public --permanent --add-port=1812/udp

firewall-cmd --zone=public --permanent --add-port=1813/tcp

firewall-cmd --zone=public --permanent --add-port=1813/udp

firewall-cmd --reload

firewall-cmd --zone=public --permanent --list-all
````

##### Output:
```` bash
[root@idp-irs ~]# firewall-cmd --zone=public --permanent --add-service=http
success

[root@idp-irs ~]# firewall-cmd --zone=public --permanent --add-service=https
success

[root@idp-irs ~]# firewall-cmd --zone=public --permanent --add-service=radius
success

[root@idp-irs ~]# firewall-cmd --zone=public --permanent --add-port=1812/tcp
success

[root@idp-irs ~]# firewall-cmd --zone=public --permanent --add-port=1812/udp
success

[root@idp-irs ~]# firewall-cmd --zone=public --permanent --add-port=1813/tcp
success

[root@idp-irs ~]# firewall-cmd --zone=public --permanent --add-port=1813/udp
success

[root@idp-irs ~]# firewall-cmd --reload
success

[root@idp-irs ~]# firewall-cmd --zone=public --permanent --list-all
public
  target: default
  icmp-block-inversion: no
  interfaces:
  sources:
  services: ssh dhcpv6-client http https radius
  ports: 1812/tcp 1812/udp 1813/tcp 1813/udp
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
  ````
### Add EPEL repo
```` bash
# yum install epel-release -y
````

### Install and Configure Database

#### Istall MariaDB Server
```` bash
# yum install mariadb-server mariadb -y
````
**Enable MariaDB Service on boot** 

````bash
# systemctl enable mariadb

Created symlink from /etc/systemd/system/multi-user.target.wants/mariadb.service to /usr/lib/systemd/system/mariadb.service.
````
**Start MariaDB Service**
``` bash
# systemctl start mariadb
````
**Check Status of MariaDB Service**
````bash
# systemctl status mariadb
   
● mariadb.service - MariaDB database server
   Loaded: loaded (/usr/lib/systemd/system/mariadb.service; enabled; vendor preset: disabled)
   Active: active (running) since Wed 2019-09-18 16:48:39 +06; 46min ago
  Process: 28727 ExecStartPost=/usr/libexec/mariadb-wait-ready $MAINPID (code=exited, status=0/SUCCESS)
  Process: 28645 ExecStartPre=/usr/libexec/mariadb-prepare-db-dir %n (code=exited, status=0/SUCCESS)
 Main PID: 28726 (mysqld_safe)
    Tasks: 20
   CGroup: /system.slice/mariadb.service
           ├─28726 /bin/sh /usr/bin/mysqld_safe --basedir=/usr
           └─28889 /usr/libexec/mysqld --basedir=/usr --datadir=/var/lib/mysql --plugin-dir=/usr/lib64/mysql/plugin
````
**Connect with MariaDB server**
````bash
# mysql -p

Enter password:

Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 2
Server version: 5.5.60-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
````
**Create Database and User**

````bash
MariaDB [(none)]> CREATE DATABASE radius;
Query OK, 1 row affected (0.00 sec)

MariaDB [(none)]> GRANT ALL ON radius.* TO radius@localhost IDENTIFIED BY "radiuspass";
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> quit
Bye
````

### Install Apache Web Server
````bash 
# yum install httpd httpd-devel -y
````
**Enable Apache service on boot**
````bash
# systemctl enable httpd
Created symlink from /etc/systemd/system/multi-user.target.wants/httpd.service to /usr/lib/systemd/system/httpd.service.
````
**Start Apache Service**
````bash
# systemctl start httpd
````
**Check Status of MariaDB Service**
````bash
# systemctl status httpd

````

#### Install required php
````bash
# yum install php-pear php-pear-DB php-devel php-mysql php-common php-gd php-mbstring php-mcrypt php php-xml -y
````

## Installation and Configure Radius Server

#### Install Radius Server:
````bash
# yum install freeradius freeradius-utils freeradius-mysql -y
````
**Start and enable freeradius to start at boot up:**
````bash
# systemctl start radiusd.service

# systemctl enable radiusd.service
 
Created symlink from /etc/systemd/system/multi-user.target.wants/radiusd.service to /usr/lib/systemd/system/radiusd.service.
````
**Now check the radius service status:**
````bash
[root@ns1 ~]# systemctl status radiusd.service
● radiusd.service - FreeRADIUS high performance RADIUS server.
   Loaded: loaded (/usr/lib/systemd/system/radiusd.service; enabled; vendor preset: disabled)
   Active: active (running) since Sun 2017-08-20 02:42:40 EDT; 22s ago
 Main PID: 8283 (radiusd)
   CGroup: /system.slice/radiusd.service
           └─8283 /usr/sbin/radiusd -d /etc/raddb

Aug 20 02:42:39 ns1.mahedi.net systemd[1]: Starting FreeRADIUS high performance RADIUS server....
Aug 20 02:42:40 ns1.mahedi.net systemd[1]: Started FreeRADIUS high performance RADIUS server..
````

#### Configure FreeRADIUS

To Configure FreeRADIUS to use MariaDB, follow steps below.

**Import the Radius database scheme to populate radius database**
````bash
# mysql -u root -p radius < /etc/raddb/mods-config/sql/main/mysql/schema.sql
````
– First you have to create a soft link for SQL under /etc/raddb/mods-enabled
````bash
# ln -s /etc/raddb/mods-available/sql /etc/raddb/mods-enabled/
````
Configure SQL module /raddb/mods-available/sql and change the database connection parameters to suite your environment:
 ````bash
 # vim /etc/raddb/mods-available/sql
 ````
sql section should look similar to below.
````bash
sql {
        dialect = "mysql"
        driver = "rlm_sql_mysql"
        
        sqlite {
                filename = "/tmp/freeradius.db"
                busy_timeout = 200
                bootstrap = "${modconfdir}/${..:name}/main/sqlite/schema.sql"
        }
        
        mysql {
                warnings = auto
        }
        
        postgresql {
                send_application_name = yes
        }
        
     # Connection info:
     
        server = "localhost"
        port = 3306
        login = "radius"
        password = "radpass"
        radius_db = "radius"
        
        acct_table1 = "radacct"
        acct_table2 = "radacct"
        
        postauth_table = "radpostauth"
        authcheck_table = "radcheck"
        
        groupcheck_table = "radgroupcheck"
        authreply_table = "radreply"
        
        groupreply_table = "radgroupreply"
        usergroup_table = "radusergroup"
        delete_stale_sessions = yes
        
        pool {
                start = ${thread[pool].start_servers}
                min = ${thread[pool].min_spare_servers}
                max = ${thread[pool].max_servers}
                spare = ${thread[pool].max_spare_servers}
                uses = 0
                retry_delay = 30
                lifetime = 0
                idle_timeout = 60
        }
        
        read_clients = yes
        client_table = "nas"
        
        group_attribute = "SQL-Group"
        
        $INCLUDE ${modconfdir}/${.:name}/main/${dialect}/queries.conf
}

````
**Configure Default virtual server for MySQL login support**

At first backup the original file:
````
cp /etc/raddb/sites-available/default /etc/raddb/sites-available/default.ori
````
**Edit /etc/raddb/sites-available/default file and replace the content with below configuration:**
````
# vim /etc/raddb/sites-available/default 
````
````
server default {
listen {
        type = auth
        ipaddr = *
        port = 0
        limit {
              max_connections = 16
              lifetime = 0
              idle_timeout = 30
        }
}
listen {
        ipaddr = *
        port = 0
        type = acct
        limit {
        }
}
listen {
        type = auth
        port = 0
        limit {
              max_connections = 16
              lifetime = 0
              idle_timeout = 30
        }
}
listen {
        ipv6addr = ::
        port = 0
        type = acct
        limit {
        }
}
authorize {
        filter_username
        preprocess
        chap
        mschap
        digest
        suffix
        eap {
                ok = return
        }
        files
        sql
        -ldap
        expiration
        logintime
        pap
}
authenticate {
        Auth-Type PAP {
                pap
        }
        Auth-Type CHAP {
                chap
        }
        Auth-Type MS-CHAP {
                mschap
        }
        mschap
        digest
        eap
}
preacct {
        preprocess
        acct_unique
        suffix
        files
}
accounting {
        detail
        unix
        sql
        exec
        attr_filter.accounting_response
}
session {
        sql
}
post-auth {
        if (session-state:User-Name && reply:User-Name && request:User-Name && (reply:User-Name == request:User-Name)) {
                update reply {
                        &User-Name !* ANY
                }
        }
        update {
                &reply: += &session-state:
        }
        sql
        exec
        remove_reply_message_if_eap
        Post-Auth-Type REJECT {
                sql
                attr_filter.access_reject
                eap
                remove_reply_message_if_eap
        }
        Post-Auth-Type Challenge {
        }
}
pre-proxy {
}
post-proxy {
        eap
}
}
````
Backup the original file:
````
cp /etc/raddb/sites-available/inner-tunnel /etc/raddb/sites-available/inner-tunnel.ori
````
Edit /etc/raddb/sites-available/inner-tunnel file and replace the content with below configuration:
````
# vim /etc/raddb/sites-available/inner-tunnel  
````
````
server inner-tunnel {
listen {
       ipaddr = 127.0.0.1
       port = 18120
       type = auth
}
authorize {
        filter_username
        chap
        mschap
        suffix
        update control {
                &Proxy-To-Realm := LOCAL
        }
        eap {
                ok = return
        }
        files
        sql
        -ldap
        expiration
        logintime
        pap
}
authenticate {
        Auth-Type PAP {
                pap
        }
        Auth-Type CHAP {
                chap
        }
        Auth-Type MS-CHAP {
                mschap
        }
        mschap
        eap
}
session {
        radutmp
        sql
}
post-auth {
        sql
        if (0) {
                update reply {
                        User-Name !* ANY
                        Message-Authenticator !* ANY
                        EAP-Message !* ANY
                        Proxy-State !* ANY
                        MS-MPPE-Encryption-Types !* ANY
                        MS-MPPE-Encryption-Policy !* ANY
                        MS-MPPE-Send-Key !* ANY
                        MS-MPPE-Recv-Key !* ANY
                }
                update {
                        &outer.session-state: += &reply:
                }
        }
        Post-Auth-Type REJECT {
                sql
                attr_filter.access_reject
                update outer.session-state {
                        &Module-Failure-Message := &request:Module-Failure-Message
                }
        }
}
pre-proxy {
}
post-proxy {
        eap
}
````
**Now create a virtual server for eduroam

````
# vim /etc/raddb/sites-enabled/eduroam
````

#
````
````
server eduroam {
        authorize {
                filter_username
                rewrite_called_station_id
                rewrite_calling_station_id
                if ("%{client:shortname}" != "nro-1.bdren.net.bd") {
                        update request {
                                &Operator-Name := "1inst-xy.ac.bd"
                                &Eduroam-SP-Country := "BD"
                        }
                }
                cui
                auth_log
                suffix
                eap {
                        ok = return
                }
        }
        authenticate {
                eap
        }
        preacct {
                rewrite_called_station_id
                rewrite_calling_station_id
                suffix
        }
        accounting {
        }
        post-auth {
                update {
                        &reply: += &session-state:
                }
                reply_log
                f_ticks
                remove_reply_message_if_eap
                Post-Auth-Type REJECT {
                        reply_log
                        f_ticks
                        attr_filter.access_reject
                        eap
                        remove_reply_message_if_eap
                }
        }
        pre-proxy {
                cui
                pre_proxy_log
                if("%{Packet-Type}" != "Accounting-Request") {
                        attr_filter.pre-proxy
                }
        }
        post-proxy {
                post_proxy_log
                attr_filter.post-proxy
                eap
        }
}
````

> **ProTip:** You can disable any **Markdown extension** in the **File properties** dialog.
