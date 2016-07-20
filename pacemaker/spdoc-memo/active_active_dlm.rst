======================================
Active/Active setup
======================================

Reference : 

http://clusterlabs.org/doc/en-US/Pacemaker/1.1/html/Clusters_from_Scratch/ch09.html

Setup procedures
---------------------------

- Need "Red Hat Enterprise Linux Resilient Storage" included in Red Hat Enterprise Linux Developer Suite

  - Find the appropriate pool-id for the subscription ::

      [root@a2 ~]# subscription-manager list --available

  - Subscribe / enable rhel-rs ::
    
      [root@a4 ~]# subscription-manager subscribe --pool=xxxxxxxxxxxx

- Install Cluster Filesystem Software ::

   [root@a4 ~]# yum install -y gfs2-utils dlm

- Configure the Cluster for the DLM ::

    [root@a2 ~]# pcs cluster cib dlm_cfg
    [root@a2 ~]# pcs -f dlm_cfg resource create dlm ocf:pacemaker:controld op monitor interval=60s
    [root@a2 ~]# pcs -f dlm_cfg resource clone dlm clone-max=4 clone-node-max=1
    [root@a2 ~]# pcs cluster cib-push dlm_cfg
    CIB updated
    
    [root@a2 ~]# pcs status
    Cluster name: my_cluster
    Last updated: Thu May 12 16:03:26 2016		Last change: Thu May 12 16:03:02 2016 by root via cibadmin on a2
    Stack: corosync
    Current DC: a1 (version 1.1.13-10.el7_2.2-44eb2dd) - partition with quorum
    4 nodes and 10 resources configured

    Online: [ a1 a2 a3 a4 ]

    Full list of resources:

    node1-xvm	(stonith:fence_xvm):	Started a1
    node2-xvm	(stonith:fence_xvm):	Started a2
    node3-xvm	(stonith:fence_xvm):	Started a3
    node4-xvm	(stonith:fence_xvm):	Started a4
    Resource Group: WEBSERVICE
     webserver	(ocf::heartbeat:apache):	Started a1
     VIP	(ocf::heartbeat:IPaddr2):	Started a1
     Clone Set: dlm-clone [dlm]
     Started: [ a1 a2 a3 a4 ]

    PCSD Status:
    a1: Online
    a2: Online
    a3: Online
    a4: Online
    
    Daemon Status:
    corosync: active/enabled
    pacemaker: active/enabled
    pcsd: active/enabled


   
