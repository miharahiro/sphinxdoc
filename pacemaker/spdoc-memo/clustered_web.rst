======================================
Setup clustered web servers
======================================

- Prerequisites

  * httpd is installed on every host in the cluster
  * http port (=80) is reachable (not blocked)
  * httpd is "not" running.

- See :doc:`cluster-resource` for what is resource and how they are implemented.
    
- Create VirtualIP resource ::

    [root@a4 ~]# pcs resource create VIP IPaddr2 ip=192.168.110.120 cidr_netmask=24 op  monitor interval=30s
    [root@a4 ~]# pcs resource show 
    VIP	(ocf::heartbeat:IPaddr2):	Started a2

  Then, at host a2 you can find the Virtual IP (192.168.110.120) on eth0::

    [root@a2 ~]# ip addr show eth0
    2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 52:54:00:00:04:22 brd ff:ff:ff:ff:ff:ff
    inet 192.168.110.122/24 brd 192.168.110.255 scope global dynamic eth0
       valid_lft 2785sec preferred_lft 2785sec
    inet 192.168.110.120/24 brd 192.168.110.255 scope global secondary eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::5054:ff:fe00:422/64 scope link 
       valid_lft forever preferred_lft forever

- Create Web server resource ::

   [root@a4 ~]# pcs resource create webserver apache statusurl="http://localhost"
   [root@a4 ~]# pcs resource 
   VIP	(ocf::heartbeat:IPaddr2):	Started a2
   webserver	(ocf::heartbeat:apache):	Started a4

  In above case, virtual-IP and web-server are effective on different hosts, which causes problem in http access through virtual-IP. To resolve such cases, resource grouping is utilized.

- Resource grouping ::

    [root@a4 ~]# pcs resource group add WEBSERVICE webserver VIP
    [root@a4 ~]# pcs resource 
     Resource Group: WEBSERVICE
     webserver	(ocf::heartbeat:apache):	Started a4
     VIP	(ocf::heartbeat:IPaddr2):	Started a4

  After creating the group with resources, both VIP and webserver resources are effective on the same host. Resources in the same group start sequentially and stop in the reverse order.
    


  
