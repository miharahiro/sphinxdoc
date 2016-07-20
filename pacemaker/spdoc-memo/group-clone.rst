=====================================================
Clone resources / clone groups
=====================================================

Tips of cloning groups to realize Active/Active cluster.
As an initial phase, webserver and ClusterIP resources are defined and running on different nodes.
::

   [root@a1 ~]# pcs status
   Cluster name: test_cluster
   Last updated: Thu May 19 18:29:23 2016Last change: Thu May 19 18:27:17 2016 by root via cibadmin on a1
   Stack: corosync
   Current DC: a1 (version 1.1.13-10.el7_2.2-44eb2dd) - partition with quorum
   3 nodes and 5 resources configured

   Online: [ a1 a2 a3 ]

   Full list of resources:

   node1-xvm(stonith:fence_xvm):Started a1
   node2-xvm(stonith:fence_xvm):Started a2
   node3-xvm(stonith:fence_xvm):Started a3
   webserver(ocf::heartbeat:apache):Started a1
   ClusterIP(ocf::heartbeat:IPaddr2):Started a2

   PCSD Status:
   a1: Online
   a2: Online
   a3: Online

   Daemon Status:
   corosync: active/enabled
   pacemaker: active/enabled
   pcsd: active/enabled

After grouping ClusterIP and webserver, both resources run on the same node.
::

   [root@a1 ~]# pcs resource group add WEBSERVICE ClusterIP webserver
   [root@a1 ~]# pcs status
   Cluster name: test_cluster
   Last updated: Thu May 19 18:30:45 2016Last change: Thu May 19 18:30:40 2016 by root via cibadmin on a1
   Stack: corosync
   Current DC: a1 (version 1.1.13-10.el7_2.2-44eb2dd) - partition with quorum
   3 nodes and 5 resources configured

   Online: [ a1 a2 a3 ]

   Full list of resources:

   node1-xvm(stonith:fence_xvm):Started a1
   node2-xvm(stonith:fence_xvm):Started a2
   node3-xvm(stonith:fence_xvm):Started a3
   Resource Group: WEBSERVICE
   ClusterIP(ocf::heartbeat:IPaddr2):Started a2
   webserver(ocf::heartbeat:apache):Started a2

Then clone the group (WEBSERVICE).
::

   [root@a1 ~]# pcs cluster cib lb-group_cfg
   [root@a1 ~]# pcs -f lb-group_cfg resource clone WEBSERVICE clone-max=3 clone-node-max=2 globally-unique=true
   [root@a1 ~]# pcs cluster cib-push lb-group_cfg
   CIB updated
   
As the default value of clone-node-max is 1, it has to be explicitly defined as 2 or greater value for allocating 2 or more ClusterIP in case of nodes failure. After cloning the group,
::

   [root@a3 ~]# pcs status
   Cluster name: test_cluster
   Last updated: Fri May 20 10:04:43 2016Last change: Fri May 20 09:26:34 2016 by root via cibadmin on a1
   Stack: corosync
   Current DC: a1 (version 1.1.13-10.el7_2.2-44eb2dd) - partition with quorum
   3 nodes and 9 resources configured

   Online: [ a1 a2 a3 ]

   Full list of resources:

   node1-xvm(stonith:fence_xvm):Started a1
   node2-xvm(stonith:fence_xvm):Started a2
   node3-xvm(stonith:fence_xvm):Started a3
   Clone Set: WEBSERVICE-clone [WEBSERVICE] (unique)
   Resource Group: WEBSERVICE:0
      ClusterIP:0(ocf::heartbeat:IPaddr2):Started a1
      webserver:0(ocf::heartbeat:apache):Started a1
   Resource Group: WEBSERVICE:1
     ClusterIP:1(ocf::heartbeat:IPaddr2):Started a2
     webserver:1(ocf::heartbeat:apache):Started a2
   Resource Group: WEBSERVICE:2
     ClusterIP:2(ocf::heartbeat:IPaddr2):Started a3
     webserver:2(ocf::heartbeat:apache):Started a3

   PCSD Status:
     a1: Online
     a2: Online
     a3: Online

   Daemon Status:
     corosync: active/disabled
     pacemaker: active/disabled
     pcsd: active/enabled

Then stop(suspend) httpd processes on node a3 
::

   [root@a3 ~]# killall -STOP httpd

After a while, node a3 was fenced. The cluster status was as follows,
::

   [root@a1 ~]# pcs status
   Cluster name: test_cluster
   Last updated: Fri May 20 10:59:25 2016Last change: Fri May 20 10:31:28 2016 by root via cibadmin on a1
   Stack: corosync
   Current DC: a2 (version 1.1.13-10.el7_2.2-44eb2dd) - partition with quorum
   3 nodes and 9 resources configured

   Online: [ a1 a2 ]
   OFFLINE: [ a3 ]

   Full list of resources:

   node1-xvm(stonith:fence_xvm):Started a1
   node2-xvm(stonith:fence_xvm):Started a2
   node3-xvm(stonith:fence_xvm):Started a1
   Clone Set: WEBSERVICE-clone [WEBSERVICE] (unique)
   Resource Group: WEBSERVICE:0
   ClusterIP:0(ocf::heartbeat:IPaddr2):Started a1
   webserver:0(ocf::heartbeat:apache):Started a1
   Resource Group: WEBSERVICE:1
   ClusterIP:1(ocf::heartbeat:IPaddr2):Started a2
   webserver:1(ocf::heartbeat:apache):Started a2
   Resource Group: WEBSERVICE:2
   ClusterIP:2(ocf::heartbeat:IPaddr2):Started a2
   webserver:2(ocf::heartbeat:apache):Started a2
   
   PCSD Status:
   a1: Online
   a2: Online
   a3: Online

   Daemon Status:
   corosync: active/enabled
   pacemaker: active/enabled
   pcsd: active/enabled
	    
Resource Group: WEBSERVICE:2 is now running on node a2 (it was on node a3 before a3 being fenced.)
http request from host computer was redirected to a3, which is now redirected to a2.
::

   [mihara@wlan-197-60 ~]$ curl http://192.168.110.120
   <html><body><h1>a2</h1></body></html>
   
