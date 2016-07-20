===================================
Pacemaker / fencing setup / test2
===================================

0. OS setup

   - OS env [N*] ::

       [root@a4 ~]# uname -a
       Linux a4.pm.redhat.com 3.10.0-327.13.1.el7.x86_64 #1 SMP Mon Feb 29 13:22:02 EST 2016 x86_64 x86_64 x86_64 GNU/Linux
   
   - Subscribe [N*] ::

       [root@a4 ~]# subscription-manager register
       [root@a4 ~]# subscription-manager attach --auto
       [root@a4 ~]# subscription-manager repos --list-enabled 
       +----------------------------------------------------------+
       Available Repositories in /etc/yum.repos.d/redhat.repo
       +----------------------------------------------------------+
       Repo ID:   rhel-7-server-rpms
       Repo Name: Red Hat Enterprise Linux 7 Server (RPMs)
       Repo URL:  https://cdn.redhat.com/content/dist/rhel/server/7/$releasever/$basearch/os
       Enabled:   1

       Repo ID:   rhel-ha-for-rhel-7-server-rpms
       Repo Name: Red Hat Enterprise Linux High Availability (for RHEL 7 Server) (RPMs)
       Repo URL:  https://cdn.redhat.com/content/dist/rhel/server/7/$releasever/$basearch/highavailability/os
       Enabled:   1

1. HA setup
   
   - Package install and setup [N*] ::

       [root@a4 ~]# yum install pcs fence-agents-all
       [root@a4 ~]# passwd hacluster
       [root@a4 ~]# systemctl start pcsd.service
       [root@a4 ~]# systemctl enable pcsd.service

   - Edit hosts file [N*] ::

       [root@a4 ~]# less /etc/hosts
       127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
       ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
       192.168.110.121 a1 a1.pm.redhat.com
       192.168.110.122 a2 a2.pm.redhat.com
       192.168.110.123 a3 a3.pm.redhat.com
       192.168.110.124 a4 a4.pm.redhat.com

   - Authentication [N] ::

       [root@a4 ~]# pcs cluster auth a1 a2 a3 a4
       Username: hacluster
       Password: *******

   - Firewall settings [N*] ::

       [root@a4 ~]# firewall-cmd --permanent --add-port=1229/tcp
       [root@a4 ~]# firewall-cmd --permanent --add-service=high-availability
       [root@a4 ~]# firewall-cmd --reload

   - Cluster creation [N] ::

       [root@a4 ~]# pcs cluster setup --start --name my_cluster a1 a2 a3 a4

   - Start cluster [N] ::

       [root@a1 ~]# pcs cluster start --all

   - Enable cluster permanently [N] ::

       [root@a1 ~]# pcs cluster enable --all

2. Fencing setup 

   - create /etc/fence_virt.conf with interactive mode [H] ::

       [root@wlan-197-60 mihara]# fence_virtd -c
       Module search path [/usr/lib64/fence-virt]: 
       ...
       ...

     - Created configuration 

       .. literalinclude:: data/fence_virt.conf	 
       
   - Create / distribute fence_xvm.key [H] ::

       [root@wlan-197-60 mihara]# dd if=/dev/urandom of=/etc/cluster/fence_xvm.key bs=4k count=1
       [root@wlan-197-60 mihara]# scp /etc/cluster/fence_xvm.key 192.168.110.121:/etc/cluster/fence_xvm.key
       ...
       ...

   - Firewall settings [H] ::

       [root@wlan-197-60 mihara]# firewall-cmd --permanent --add-port=1229/tcp
       [root@wlan-197-60 mihara]# firewall-cmd --reload 

   - Permit fence_virtd connect tcp [H] ::
       
       [root@wlan-197-60 mihara]# setsebool -P fenced_can_network_connect 1

   - Start / enable fence_virtd service [H] ::

       [root@wlan-197-60 mihara]# systemctl start fence_virtd.service
       [root@wlan-197-60 mihara]# systemctl enable fence_virtd.service 
       
       
   - Fence agent setup [N] ::

       [root@a4 ~]# pcs cluster cib dump.xml
       [root@a4 ~]# pcs -f dump.xml stonith create node4-xvm fence_xvm pcmk_host_list="a4" port="pm_a4" delay=10
       [root@a4 ~]# pcs cluster cib-push dump.xml
       CIB updated



       
