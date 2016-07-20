======================================
CIB
======================================

The status shown by pcs command,
::

   [root@a1 ~]# pcs status
   Cluster name: test_cluster
   Last updated: Fri Jun 10 11:52:14 2016Last change: Wed Jun  8 18:26:16 2016 by root via cibadmin on a1
   Stack: corosync
   Current DC: a1 (version 1.1.13-10.el7_2.2-44eb2dd) - partition with quorum
   3 nodes and 6 resources configured

   Online: [ a1 a2 a3 ]

   Full list of resources:

   node1-xvm(stonith:fence_xvm):Started a1
   node2-xvm(stonith:fence_xvm):Started a2
   node3-xvm(stonith:fence_xvm):Started a3
   Clone Set: webserver-clone [webserver]
   Started: [ a1 a2 a3 ]

   PCSD Status:
   a1: Online
   a2: Online
   a3: Online

   Daemon Status:
   corosync: active/enabled
   pacemaker: active/enabled
   pcsd: active/enabled
		  
CIB can be dumped to a xml file like,
::

   [root@a1 ~]# pcs cluster cib cib-a1.xml

The cib-a1.xml file looks like

.. include:: data/cib-a1.xml
	     :literal:
	     
