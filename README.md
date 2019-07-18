# eduroamIRS Configuration

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
