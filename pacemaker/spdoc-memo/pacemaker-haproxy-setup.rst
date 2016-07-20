===============================================
Active/Passive HA proxy using Pacemaker setup
===============================================

Prerequisites
---------------------------

- pacemaker / corosync / pcsd installed and active.

- fence devices are properly configured and active.

Setup procedures
---------------------------

HA proxy setup
..................................
::

   [root@a5 ~]# yum -y install haproxy

Edit config file (/etc/haproxy/haproxy.cfg)

.. include:: data/haproxy.cfg
	     :literal:
   

Cluster setup
................................
ClusterIP resource setup
::
  
   [root@a4 ~]# pcs resource create ClusterIP IPaddr2 ip=192.168.110.120 cidr_netmask=24 op  monitor interval=30s

HA proxy resource setup
::
   
   [root@a4 ~]# pcs resource create haproxy systemd:haproxy op monitor interval=10s

Colocation setup
::

   [root@a4 ~]# pcs constraint colocation add haproxy with ClusterIP INFINITY

Then the pcs status shows,
::

   [root@a4 ~]# pcs status
   Cluster name: haproxy_cluster
   Last updated: Wed Jun  8 18:46:15 2016Last change: Wed Jun  8 18:27:13 2016 by root via cibadmin on a4
   Stack: corosync
   Current DC: a5 (version 1.1.13-10.el7_2.2-44eb2dd) - partition with quorum
   2 nodes and 4 resources configured

   Online: [ a4 a5 ]

   Full list of resources:

   node1-xvm(stonith:fence_xvm):Started a4
   node2-xvm(stonith:fence_xvm):Started a5
   ClusterIP(ocf::heartbeat:IPaddr2):Started a4
   haproxy(systemd:haproxy):Started a4

   PCSD Status:
   a4: Online
   a5: Online

   Daemon Status:
   corosync: active/enabled
   pacemaker: active/enabled
   pcsd: active/enabled
	  
