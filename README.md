# eduroam Institutional Radius Server (IRS) Configuration

### Server Basic Configuration for IRS

#### Hostname Configuration:
```` bash 
# vim /etc/hostname

idp-irs.pust.ac.bd
````
```` bash 
# hostname idp-irs.pust.ac.bd
````
#### Check configuration:
```` bash 
# hostname

idp-irs.pust.ac.bd
````
```` bash 
# hostname -d
pust.ac.bd
````
### Disable Selinux:
```` bash
[root@pust ~]# vim /etc/selinux/config

SELINUX=disabled

# reboot
````

### Firewall Configuration 

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

#### Output:
```` bash
[root@pust ~]# firewall-cmd --zone=public --permanent --add-service=http
success

[root@pust ~]# firewall-cmd --zone=public --permanent --add-service=https
success

[root@pust ~]# firewall-cmd --zone=public --permanent --add-service=radius
success

[root@pust ~]# firewall-cmd --zone=public --permanent --add-port=1812/tcp
success

[root@pust ~]# firewall-cmd --zone=public --permanent --add-port=1812/udp
success

[root@pust ~]# firewall-cmd --zone=public --permanent --add-port=1813/tcp
success

[root@pust ~]# firewall-cmd --zone=public --permanent --add-port=1813/udp
success

[root@pust ~]# firewall-cmd --reload
success

[root@pust ~]# firewall-cmd --zone=public --permanent --list-all
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
