==================================================
Setting up clustered MariaDB with Pacemaker
==================================================

Install MariaDB
-----------------------
Install MariaDB
::
   
   # yum -y install mariadb-server

[N'*'] Configure [mysqld] section in /etc/my.cnf 
::

   log-bin=mariadb-bin
   server-id=1 <- (1,2,3...)
   
[N] Create replication user
----------------------------------------
Ref :

http://gmt-24.net/archives/1240

https://mariadb.com/kb/en/mariadb/setting-up-replication/

Start MariaDB
::

   # systemctl start mariadb.service

Create replication user
::

   [root@b1 heartbeat]# mysql -u root
   Welcome to the MariaDB monitor.  Commands end with ; or \g.
   Your MariaDB connection id is 2
   Server version: 5.5.47-MariaDB-log MariaDB Server

   Copyright (c) 2000, 2015, Oracle, MariaDB Corporation Ab and others.

   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

   MariaDB [(none)]> GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%' IDENTIFIED BY 'slavepass';

   MariaDB [(none)]> GRANT SUPER,REPLICATION SLAVE,REPLICATION CLIENT,PROCESS ON *.* TO 'repl'@'localhost' IDENTIFIED BY 'slavepass';
   Query OK, 0 rows affected (0.00 sec)

   MariaDB [(none)]> FLUSH PRIVILEGES;
   Query OK, 0 rows affected (0.00 sec)

Stop MariaDB
::

   [root@b1 ~]# systemctl stop mariadb.service
   
Copy MariaDB to other nodes.
On other nodes,
::

   [root@b2 ~]# rm -rf /var/lib/mysql/

Copy DB to other nodes,
::

   [root@b1 heartbeat]# tar cf - -C /var/lib mysql | ssh b2 tar xpvf - -C /var/lib/


Setup cluster resource
-----------------------------------
b2, b3 as slave nodes.
::

   [root@b1 ~]# pcs cluster standby b2
   [root@b1 ~]# pcs cluster standby b3

Register cluster resource
::

   pcs cluster cib mysql_repl
   pcs -f mysql_repl resource create mysql ocf:heartbeat:mysql
   pcs -f mysql_repl resource update mysql binary=/usr/bin/mysqld_safe
   pcs -f mysql_repl resource update mysql datadir=/var/lib/mysql
   pcs -f mysql_repl resource update mysql log=/var/log/mariadb/mariadb.log
   pcs -f mysql_repl resource update mysql pid=/run/mariadb/mariadb.pid
   pcs -f mysql_repl resource update mysql replication_user=repl
   pcs -f mysql_repl resource update mysql replication_passwd=slavepass
   pcs -f mysql_repl resource op add mysql notify interval=0 timeout=90s
   pcs cluster cib-push mysql_repl
   CIB updated

Clone the resource
::

   [root@b1 ~]# pcs resource master mysql-clone mysql master-max=1 master-node-max=1 clone-max=2 clone-node-max=1 notify=true

Unstandby b2
::

   [root@b1 ~]# pcs cluster unstandby b2

SELinux
-----------------------------------
Ref:

https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Security-Enhanced_Linux/sect-Security-Enhanced_Linux-Fixing_Problems-Allowing_Access_audit2allow.html

After above procedures, got errors (audit.log)
::

   type=AVC msg=audit(1466415987.567:366): avc:  denied  { read write } for  pid=2833 comm="mysqld" name="mariadb-bin.index" dev="dm-0" ino=17443052 scontext=system_u:system_r:mysqld_t:s0 tcontext=unconfined_u:object_r:var_lib_t:s0 tclass=file
   type=AVC msg=audit(1466415987.567:366): avc:  denied  { read write } for  pid=2833 comm="mysqld" name="mariadb-bin.index" dev="dm-0" ino=17443052 scontext=system_u:system_r:mysqld_t:s0 tcontext=unconfined_u:object_r:var_lib_t:s0 tclass=file

::

   [root@b2 ~]# audit2allow -a

   #============= mysqld_t ==============
   allow mysqld_t var_lib_t:file { read write getattr open lock };
   allow mysqld_t var_lib_t:sock_file { create unlink };
   
Create a custom module and apply the rule
::

   [root@b2 selinux]# audit2allow -a -M mariadb
   ******************** IMPORTANT ***********************
   To make this policy package active, execute:

   semodule -i mariadb.pp

   [root@b2 selinux]# ls
   configconfig~  mariadb.pp  mariadb.te  semanage.conftargeted
   [root@b2 selinux]# semodule -i mariadb.pp
   
   
