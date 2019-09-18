# eduroam Institutional Radius Server (IRS) Configuration

### Server Basic Configuration for IRS

#### Update System

```` bash
# yum update -y

````

#### Hostname Configuration:
```` bash 
# vim /etc/hostname

idp-irs.pust.ac.bd
````
```` bash 
# hostname idp-irs.pust.ac.bd
````
##### Check configuration:
```` bash 
# hostname

idp-irs.pust.ac.bd
````
```` bash 
# hostname -d
pust.ac.bd
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

### Istall MariaDB Server

[root@idp-irs ~]# yum install mariadb-server mariadb -y

[root@idp-irs ~]# systemctl enable mariadb

Created symlink from /etc/systemd/system/multi-user.target.wants/mariadb.service to /usr/lib/systemd/system/mariadb.service.

[root@idp-irs ~]# systemctl status mariadb
   mariadb.service - MariaDB database server
   Loaded: loaded (/usr/lib/systemd/system/mariadb.service; enabled; vendor preset: disabled)
   Active: active (running) since Wed 2019-09-18 16:48:39 +06; 1min 35s ago
  Process: 28727 ExecStartPost=/usr/libexec/mariadb-wait-ready $MAINPID (code=exited, status=0/SUCCESS)
  Process: 28645 ExecStartPre=/usr/libexec/mariadb-prepare-db-dir %n (code=exited, status=0/SUCCESS)
 Main PID: 28726 (mysqld_safe)
    Tasks: 20
   CGroup: /system.slice/mariadb.service
           +-28726 /bin/sh /usr/bin/mysqld_safe --basedir=/usr
           +-28889 /usr/libexec/mysqld --basedir=/usr --datadir=/var/lib/mysql 

[root@idp-irs ~]# mysql -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 2
Server version: 5.5.60-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> 

MariaDB [(none)]> CREATE DATABASE radius;
Query OK, 1 row affected (0.00 sec)

MariaDB [(none)]> GRANT ALL ON radius.* TO radius@localhost IDENTIFIED BY "bDren@UgcPUST";
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> quit
Bye

Step-01: Apache installation

[root@eduroam ~]# yum -y install httpd httpd-devel

[root@eduroam ~]# systemctl enable httpd
Created symlink from /etc/systemd/system/multi-user.target.wants/httpd.service to /usr/lib/systemd/system/httpd.service.

[root@eduroam ~]# systemctl start httpd


### Installing php

# yum install php-pear php-pear-DB php-devel php-mysql php-common php-gd php-mbstring php-mcrypt php php-xml -y
 
## Installation and Configure Radius Server

### Add EPEL repo

```` bash 
# yum install epel-release
```` 
Step-01: Install Radius Server


Step-02: MariaDB Server Installation

```` bash 
[root@eduroam ~]# yum install -y mariadb-server mariadb
````
Enable and Start MariaDB Service

```` bash
[root@eduroam ~]# systemctl enable mariadb
Created symlink from /etc/systemd/system/multi-user.target.wants/mariadb.service to /usr/lib/systemd/system/mariadb.service.

[root@eduroam ~]# systemctl start mariadb

[root@eduroam ~]# systemctl status mariadb
● mariadb.service - MariaDB database server
   Loaded: loaded (/usr/lib/systemd/system/mariadb.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2019-07-18 16:08:53 +06; 11s ago
 Main PID: 22367 (mysqld_safe)
   CGroup: /system.slice/mariadb.service
           ├─22367 /bin/sh /usr/bin/mysqld_safe --basedir=/usr
           └─22529 /usr/libexec/mysqld --basedir=/usr --datadir=/var/lib/mysql --p.
````
Step-01: Apache installation

[root@eduroam ~]# yum -y install httpd httpd-devel

[root@eduroam ~]# systemctl enable httpd
Created symlink from /etc/systemd/system/multi-user.target.wants/httpd.service to /usr/lib/systemd/system/httpd.service.

[root@eduroam ~]# systemctl start httpd

> **ProTip:** You can disable any **Markdown extension** in the **File properties** dialog.
