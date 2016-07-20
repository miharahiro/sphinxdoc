=====================================
Fencing case studies
=====================================

Block high-availability communications
----------------------------------------------

- Procedures

  - pcs status before blocking high-availability ports ::

      [root@a1 ~]# pcs status
      Cluster name: my_cluster
      Last updated: Wed May 11 17:38:05 2016		Last change: Wed May 11 17:29:43 2016 by hacluster via crmd on a3
      Stack: corosync
      Current DC: a3 (version 1.1.13-10.el7_2.2-44eb2dd) - partition with quorum
      4 nodes and 8 resources configured

      Online: [ a1 a2 a3 a4 ]

      Full list of resources:

      node1-xvm	(stonith:fence_xvm):	Started a1
      node2-xvm	(stonith:fence_xvm):	Started a2
      node3-xvm	(stonith:fence_xvm):	Started a3
      Clone Set: dlm-clone [dlm]
      Stopped: [ a1 a2 a3 a4 ]
      node4-xvm	(stonith:fence_xvm):	Started a4
      Resource Group: WEBSERVICE
      webserver	(ocf::heartbeat:apache):	Started a1
      VIP	(ocf::heartbeat:IPaddr2):	Started a1

      PCSD Status:
      a1: Online
      a2: Online
      a3: Online
      a4: Online

      Daemon Status:
      corosync: active/enabled
      pacemaker: active/enabled
      pcsd: active/enabled

    

  - block high-availability communications ports (2224/tcp, 3121/tcp, 5404/udp, 5405/udp, 21064/tcp)
  (ports are defined in  /usr/lib/firewalld/services/high-availability.xml)
  ::

     [root@a1 ~]# firewall-cmd --remove-service=high-availability

- /var/log/messages on host a1

  - Active : resources (vip/webserver) were effective on this host

  - ports blocked on this host

  ::

    May 11 17:41:15 a1 corosync[1537]: [TOTEM ] A processor failed, forming new configuration.
    May 11 17:41:18 a1 stonith-ng[2056]:  notice: crm_update_peer_proc: Node a2[2] - state is now lost (was member)
    May 11 17:41:18 a1 stonith-ng[2056]:  notice: Removing a2/2 from the membership list
    May 11 17:41:18 a1 stonith-ng[2056]:  notice: Purged 1 peers with id=2 and/or uname=a2 from the membership cache
    May 11 17:41:18 a1 stonith-ng[2056]:  notice: crm_update_peer_proc: Node a3[3] - state is now lost (was member)
    May 11 17:41:18 a1 stonith-ng[2056]:  notice: Removing a3/3 from the membership list
    May 11 17:41:18 a1 stonith-ng[2056]:  notice: Purged 1 peers with id=3 and/or uname=a3 from the membership cache
    May 11 17:41:18 a1 stonith-ng[2056]:  notice: crm_update_peer_proc: Node a4[4] - state is now lost (was member)
    May 11 17:41:18 a1 stonith-ng[2056]:  notice: Removing a4/4 from the membership list
    May 11 17:41:18 a1 stonith-ng[2056]:  notice: Purged 1 peers with id=4 and/or uname=a4 from the membership cache
    May 11 17:41:18 a1 crmd[2079]:  notice: Membership 4472: quorum lost (1)
    May 11 17:41:18 a1 crmd[2079]:  notice: crm_reap_unseen_nodes: Node a4[4] - state is now lost (was member)
    May 11 17:41:18 a1 corosync[1537]: [TOTEM ] A new membership (192.168.110.121:4472) was formed. Members left: 2 3 4
    May 11 17:41:18 a1 corosync[1537]: [TOTEM ] Failed to receive the leave message. failed: 2 3 4
    May 11 17:41:18 a1 corosync[1537]: [QUORUM] This node is within the non-primary component and will NOT provide any services.
    May 11 17:41:18 a1 corosync[1537]: [QUORUM] Members[1]: 1
    May 11 17:41:18 a1 corosync[1537]: [MAIN  ] Completed service synchronization, ready to provide service.
    May 11 17:41:18 a1 attrd[2062]:  notice: crm_update_peer_proc: Node a2[2] - state is now lost (was member)
    May 11 17:41:18 a1 attrd[2062]:  notice: Removing all a2 (2) attributes for attrd_peer_change_cb
    May 11 17:41:18 a1 pacemakerd[1913]:  notice: Membership 4472: quorum lost (1)
    May 11 17:41:18 a1 pacemakerd[1913]:  notice: crm_reap_unseen_nodes: Node a3[3] - state is now lost (was member)
    May 11 17:41:18 a1 pacemakerd[1913]:  notice: crm_reap_unseen_nodes: Node a2[2] - state is now lost (was member)
    May 11 17:41:18 a1 pacemakerd[1913]:  notice: crm_reap_unseen_nodes: Node a4[4] - state is now lost (was member)
    May 11 17:41:18 a1 cib[2053]:  notice: crm_update_peer_proc: Node a2[2] - state is now lost (was member)
    May 11 17:41:18 a1 cib[2053]:  notice: Removing a2/2 from the membership list
    May 11 17:41:18 a1 cib[2053]:  notice: Purged 1 peers with id=2 and/or uname=a2 from the membership cache
    May 11 17:41:18 a1 cib[2053]:  notice: crm_update_peer_proc: Node a3[3] - state is now lost (was member)
    May 11 17:41:18 a1 cib[2053]:  notice: Removing a3/3 from the membership list
    May 11 17:41:18 a1 cib[2053]:  notice: Purged 1 peers with id=3 and/or uname=a3 from the membership cache
    May 11 17:41:18 a1 cib[2053]:  notice: crm_update_peer_proc: Node a4[4] - state is now lost (was member)
    May 11 17:41:18 a1 cib[2053]:  notice: Removing a4/4 from the membership list
    May 11 17:41:18 a1 crmd[2079]:  notice: crm_reap_unseen_nodes: Node a2[2] - state is now lost (was member)
    May 11 17:41:18 a1 crmd[2079]:  notice: crm_reap_unseen_nodes: Node a3[3] - state is now lost (was member)
    May 11 17:41:18 a1 crmd[2079]: warning: Our DC node (a3) left the cluster
    May 11 17:41:18 a1 crmd[2079]:  notice: State transition S_NOT_DC -> S_ELECTION [ input=I_ELECTION cause=C_FSA_INTERNAL origin=reap_dead_nodes ]
    May 11 17:41:18 a1 crmd[2079]:  notice: State transition S_ELECTION -> S_INTEGRATION [ input=I_ELECTION_DC cause=C_TIMER_POPPED origin=election_timeout_popped ]
    May 11 17:41:18 a1 attrd[2062]:  notice: Removing a2/2 from the membership list
    May 11 17:41:18 a1 attrd[2062]:  notice: Purged 1 peers with id=2 and/or uname=a2 from the membership cache
    May 11 17:41:18 a1 attrd[2062]:  notice: crm_update_peer_proc: Node a3[3] - state is now lost (was member)
    May 11 17:41:18 a1 attrd[2062]:  notice: Removing all a3 (3) attributes for attrd_peer_change_cb
    May 11 17:41:18 a1 attrd[2062]:  notice: Removing a3/3 from the membership list
    May 11 17:41:18 a1 attrd[2062]:  notice: Purged 1 peers with id=3 and/or uname=a3 from the membership cache
    May 11 17:41:18 a1 attrd[2062]:  notice: crm_update_peer_proc: Node a4[4] - state is now lost (was member)
    May 11 17:41:18 a1 attrd[2062]:  notice: Removing all a4 (4) attributes for attrd_peer_change_cb
    May 11 17:41:18 a1 attrd[2062]:  notice: Lost attribute writer a4
    May 11 17:41:18 a1 attrd[2062]:  notice: Removing a4/4 from the membership list
    May 11 17:41:18 a1 attrd[2062]:  notice: Purged 1 peers with id=4 and/or uname=a4 from the membership cache
    May 11 17:41:18 a1 cib[2053]:  notice: Purged 1 peers with id=4 and/or uname=a4 from the membership cache
    May 11 17:41:18 a1 crmd[2079]: warning: FSA: Input I_ELECTION_DC from do_election_check() received in state S_INTEGRATION
    May 11 17:41:18 a1 crmd[2079]:  notice: Notifications disabled
    May 11 17:41:19 a1 attrd[2062]:  notice: Update error (unknown peer uuid, retry will be attempted once uuid is discovered): a4[shutdown]=(null) failed (host=0x1da96b0)
    May 11 17:41:19 a1 attrd[2062]:  notice: Update error (unknown peer uuid, retry will be attempted once uuid is discovered): a3[shutdown]=(null) failed (host=0x1dcb830)
    May 11 17:41:19 a1 attrd[2062]:  notice: Update error (unknown peer uuid, retry will be attempted once uuid is discovered): a4[terminate]=(null) failed (host=0x1da96b0)
    May 11 17:41:19 a1 attrd[2062]:  notice: Update error (unknown peer uuid, retry will be attempted once uuid is discovered): a3[terminate]=(null) failed (host=0x1dcb830)
    May 11 17:41:19 a1 attrd[2062]:  notice: Update error (unknown peer uuid, retry will be attempted once uuid is discovered): a4[probe_complete]=(null) failed (host=0x1da96b0)
    May 11 17:41:19 a1 attrd[2062]:  notice: Update error (unknown peer uuid, retry will be attempted once uuid is discovered): a3[probe_complete]=(null) failed (host=0x1dcb830)
    May 11 17:41:20 a1 crmd[2079]: warning: No match for shutdown action on 2
    May 11 17:41:20 a1 crmd[2079]: warning: No match for shutdown action on 2
    May 11 17:41:20 a1 pengine[2072]:  notice: We do not have quorum - fencing and resource management disabled
    May 11 17:41:20 a1 pengine[2072]: warning: Node a3 is unclean because the node is no longer part of the cluster
    May 11 17:41:20 a1 pengine[2072]: warning: Node a3 is unclean
    May 11 17:41:20 a1 pengine[2072]: warning: Node a2 is unclean because node2-xvm is thought to be active there
    May 11 17:41:20 a1 pengine[2072]: warning: Processing failed op monitor for dlm:0 on a2: not installed (5)
    May 11 17:41:20 a1 pengine[2072]:  notice: Preventing dlm-clone from re-starting on a2: operation monitor failed 'not installed' (5)
    May 11 17:41:20 a1 pengine[2072]: warning: Processing failed op monitor for dlm:0 on a2: not installed (5)
    May 11 17:41:20 a1 pengine[2072]:  notice: Preventing dlm-clone from re-starting on a2: operation monitor failed 'not installed' (5)
    May 11 17:41:20 a1 pengine[2072]: warning: Processing failed op monitor for dlm:0 on a3: not installed (5)
    May 11 17:41:20 a1 pengine[2072]:  notice: Preventing dlm-clone from re-starting on a3: operation monitor failed 'not installed' (5)
    May 11 17:41:20 a1 pengine[2072]: warning: Processing failed op monitor for dlm:0 on a3: not installed (5)
    May 11 17:41:20 a1 pengine[2072]:  notice: Preventing dlm-clone from re-starting on a3: operation monitor failed 'not installed' (5)
    May 11 17:41:20 a1 pengine[2072]: warning: Node a4 is unclean because node4-xvm is thought to be active there
    May 11 17:41:20 a1 pengine[2072]: warning: Processing failed op monitor for dlm:0 on a4: not installed (5)
    May 11 17:41:20 a1 pengine[2072]:  notice: Preventing dlm-clone from re-starting on a4: operation monitor failed 'not installed' (5)
    May 11 17:41:20 a1 pengine[2072]: warning: Processing failed op monitor for dlm:0 on a4: not installed (5)
    May 11 17:41:20 a1 pengine[2072]:  notice: Preventing dlm-clone from re-starting on a4: operation monitor failed 'not installed' (5)
    May 11 17:41:20 a1 pengine[2072]:  notice: We can fence a1 without quorum because they're in our membership
    May 11 17:41:20 a1 pengine[2072]: warning: Processing failed op monitor for dlm:0 on a1: not installed (5)
    May 11 17:41:20 a1 pengine[2072]:  notice: Preventing dlm-clone from re-starting on a1: operation monitor failed 'not installed' (5)
    May 11 17:41:20 a1 pengine[2072]:  notice: We can fence a1 without quorum because they're in our membership
    May 11 17:41:20 a1 pengine[2072]: warning: Processing failed op monitor for dlm:0 on a1: not installed (5)
    May 11 17:41:20 a1 pengine[2072]:  notice: Preventing dlm-clone from re-starting on a1: operation monitor failed 'not installed' (5)
    May 11 17:41:20 a1 pengine[2072]: warning: Action node2-xvm_stop_0 on a2 is unrunnable (offline)
    May 11 17:41:20 a1 pengine[2072]: warning: Action node3-xvm_stop_0 on a3 is unrunnable (offline)
    May 11 17:41:20 a1 pengine[2072]: warning: Action node4-xvm_stop_0 on a4 is unrunnable (offline)
    May 11 17:41:20 a1 pengine[2072]: warning: Node a2 is unclean!
    May 11 17:41:20 a1 pengine[2072]: warning: Node a3 is unclean!
    May 11 17:41:20 a1 pengine[2072]: warning: Node a4 is unclean!
    May 11 17:41:20 a1 pengine[2072]:  notice: Cannot fence unclean nodes until quorum is attained (or no-quorum-policy is set to ignore)
    May 11 17:41:20 a1 pengine[2072]:  notice: Stop    node1-xvm#011(Started a1)
    May 11 17:41:20 a1 pengine[2072]:  notice: Stop    node2-xvm#011(Started a2 - blocked)
    May 11 17:41:20 a1 pengine[2072]:  notice: Stop    node3-xvm#011(Started a3 - blocked)
    May 11 17:41:20 a1 pengine[2072]:  notice: Stop    node4-xvm#011(Started a4 - blocked)
    May 11 17:41:20 a1 pengine[2072]:  notice: Stop    webserver#011(Started a1)
    May 11 17:41:20 a1 pengine[2072]:  notice: Stop    VIP#011(Started a1)
    May 11 17:41:20 a1 pengine[2072]: warning: Calculated Transition 0: /var/lib/pacemaker/pengine/pe-warn-13.bz2
    May 11 17:41:20 a1 crmd[2079]:  notice: Initiating action 7: stop node1-xvm_stop_0 on a1 (local)
    May 11 17:41:20 a1 crmd[2079]:  notice: Initiating action 24: stop VIP_stop_0 on a1 (local)
    May 11 17:41:20 a1 crmd[2079]:  notice: Operation node1-xvm_stop_0: ok (node=a1, call=38, rc=0, cib-update=51, confirmed=true)
    May 11 17:41:20 a1 IPaddr2(VIP)[6779]: INFO: IP status = ok, IP_CIP=
    May 11 17:41:20 a1 crmd[2079]:  notice: Operation VIP_stop_0: ok (node=a1, call=40, rc=0, cib-update=52, confirmed=true)
    May 11 17:41:20 a1 crmd[2079]:  notice: Initiating action 22: stop webserver_stop_0 on a1 (local)
    May 11 17:41:20 a1 apache(webserver)[6834]: INFO: Attempting graceful stop of apache PID 2660
    May 11 17:41:22 a1 apache(webserver)[6834]: INFO: apache stopped.
    May 11 17:41:22 a1 crmd[2079]:  notice: Operation webserver_stop_0: ok (node=a1, call=42, rc=0, cib-update=53, confirmed=true)
    May 11 17:41:22 a1 crmd[2079]:  notice: Transition 0 (Complete=6, Pending=0, Fired=0, Skipped=0, Incomplete=0, Source=/var/lib/pacemaker/pengine/pe-warn-13.bz2): Complete
    May 11 17:41:22 a1 crmd[2079]:  notice: State transition S_TRANSITION_ENGINE -> S_IDLE [ input=I_TE_SUCCESS cause=C_FSA_INTERNAL origin=notify_crmd ]

- /var/log/messages on host a2

  - Passive : one of the host in the cluster
  ::
     
   May 11 17:41:18 a2 corosync[1589]: [TOTEM ] A new membership (192.168.110.122:4472) was formed. Members left: 1
   May 11 17:41:18 a2 corosync[1589]: [TOTEM ] Failed to receive the leave message. failed: 1
   May 11 17:41:18 a2 attrd[2292]:  notice: crm_update_peer_proc: Node a1[1] - state is now lost (was member)
   May 11 17:41:18 a2 attrd[2292]:  notice: Removing all a1 (1) attributes for attrd_peer_change_cb
   May 11 17:41:18 a2 attrd[2292]:  notice: Removing a1/1 from the membership list
   May 11 17:41:18 a2 attrd[2292]:  notice: Purged 1 peers with id=1 and/or uname=a1 from the membership cache
   May 11 17:41:18 a2 stonith-ng[2290]:  notice: crm_update_peer_proc: Node a1[1] - state is now lost (was member)
   May 11 17:41:18 a2 stonith-ng[2290]:  notice: Removing a1/1 from the membership list
   May 11 17:41:18 a2 stonith-ng[2290]:  notice: Purged 1 peers with id=1 and/or uname=a1 from the membership cache
   May 11 17:41:18 a2 cib[2289]:  notice: crm_update_peer_proc: Node a1[1] - state is now lost (was member)
   May 11 17:41:18 a2 cib[2289]:  notice: Removing a1/1 from the membership list
   May 11 17:41:18 a2 cib[2289]:  notice: Purged 1 peers with id=1 and/or uname=a1 from the membership cache
   May 11 17:41:18 a2 pacemakerd[2166]:  notice: crm_reap_unseen_nodes: Node a1[1] - state is now lost (was member)
   May 11 17:41:18 a2 corosync[1589]: [QUORUM] Members[3]: 2 3 4
   May 11 17:41:18 a2 corosync[1589]: [MAIN  ] Completed service synchronization, ready to provide service.
   May 11 17:41:18 a2 crmd[2294]:  notice: crm_reap_unseen_nodes: Node a1[1] - state is now lost (was member)
   May 11 17:41:21 a2 corosync[1589]: [TOTEM ] A new membership (192.168.110.122:4476) was formed. Members
   May 11 17:41:21 a2 corosync[1589]: [QUORUM] Members[3]: 2 3 4
   May 11 17:41:21 a2 corosync[1589]: [MAIN  ] Completed service synchronization, ready to provide service.
   May 11 17:41:24 a2 corosync[1589]: [TOTEM ] A new membership (192.168.110.122:4480) was formed. Members
   May 11 17:41:24 a2 corosync[1589]: [QUORUM] Members[3]: 2 3 4
   May 11 17:41:24 a2 corosync[1589]: [MAIN  ] Completed service synchronization, ready to provide service.
   May 11 17:41:24 a2 stonith-ng[2290]:  notice: node3-xvm can not fence (reboot) a1: static-list
   May 11 17:41:24 a2 stonith-ng[2290]:  notice: node2-xvm can not fence (reboot) a1: static-list
   May 11 17:41:24 a2 stonith-ng[2290]:  notice: node4-xvm can not fence (reboot) a1: static-list
   May 11 17:41:24 a2 stonith-ng[2290]:  notice: node1-xvm can fence (reboot) a1: static-list
   May 11 17:41:24 a2 crmd[2294]:  notice: Operation node2-xvm_stop_0: ok (node=a2, call=34, rc=0, cib-update=22, confirmed=true)
   May 11 17:41:24 a2 stonith-ng[2290]:  notice: node3-xvm can not fence (reboot) a1: static-list
   May 11 17:41:24 a2 stonith-ng[2290]:  notice: node2-xvm can not fence (reboot) a1: static-list
   May 11 17:41:24 a2 stonith-ng[2290]:  notice: node4-xvm can not fence (reboot) a1: static-list
   May 11 17:41:24 a2 stonith-ng[2290]:  notice: node1-xvm can fence (reboot) a1: static-list
   May 11 17:41:27 a2 corosync[1589]: [TOTEM ] A new membership (192.168.110.122:4484) was formed. Members
   May 11 17:41:27 a2 corosync[1589]: [QUORUM] Members[3]: 2 3 4
   May 11 17:41:27 a2 corosync[1589]: [MAIN  ] Completed service synchronization, ready to provide service.
   May 11 17:41:29 a2 corosync[1589]: [TOTEM ] A new membership (192.168.110.122:4488) was formed. Members
   May 11 17:41:29 a2 corosync[1589]: [QUORUM] Members[3]: 2 3 4
   May 11 17:41:29 a2 corosync[1589]: [MAIN  ] Completed service synchronization, ready to provide service.
   May 11 17:41:30 a2 crmd[2294]:  notice: Operation node1-xvm_start_0: ok (node=a2, call=35, rc=0, cib-update=23, confirmed=true)
   May 11 17:41:32 a2 corosync[1589]: [TOTEM ] A new membership (192.168.110.122:4492) was formed. Members
   May 11 17:41:32 a2 corosync[1589]: [QUORUM] Members[3]: 2 3 4
   May 11 17:41:32 a2 corosync[1589]: [MAIN  ] Completed service synchronization, ready to provide service.
   May 11 17:41:35 a2 stonith-ng[2290]:  notice: Operation 'reboot' [3067] (call 2 from crmd.2322) for host 'a1' with device 'node1-xvm' returned: 0 (OK)
   May 11 17:41:35 a2 corosync[1589]: [TOTEM ] A new membership (192.168.110.122:4496) was formed. Members
   May 11 17:41:35 a2 corosync[1589]: [QUORUM] Members[3]: 2 3 4
   May 11 17:41:35 a2 corosync[1589]: [MAIN  ] Completed service synchronization, ready to provide service.
   May 11 17:41:35 a2 stonith-ng[2290]:  notice: Operation reboot of a1 by a2 for crmd.2322@a3.e7006050: OK
   May 11 17:41:35 a2 crmd[2294]:  notice: Peer a1 was terminated (reboot) by a2 for a3: OK (ref=e7006050-8c70-465e-a5d2-b5f1340d118b) by client crmd.2322
   May 11 17:41:36 a2 apache(webserver)[3093]: INFO: AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using a2.pm.redhat.com. Set the 'ServerName' directive globally to suppress this message
   May 11 17:41:36 a2 crmd[2294]:  notice: Operation webserver_start_0: ok (node=a2, call=37, rc=0, cib-update=24, confirmed=true)
   May 11 17:41:36 a2 IPaddr2(VIP)[3157]: INFO: Adding inet address 192.168.110.120/24 with broadcast address 192.168.110.255 to device eth0
   May 11 17:41:36 a2 IPaddr2(VIP)[3157]: INFO: Bringing device eth0 up
   May 11 17:41:36 a2 IPaddr2(VIP)[3157]: INFO: /usr/libexec/heartbeat/send_arp -i 200 -r 5 -p /var/run/resource-agents/send_arp-192.168.110.120 eth0 192.168.110.120 auto not_used not_used
   May 11 17:41:36 a2 crmd[2294]:  notice: Operation VIP_start_0: ok (node=a2, call=39, rc=0, cib-update=26, confirmed=true)
   May 11 17:41:49 a2 corosync[1589]: [TOTEM ] A new membership (192.168.110.121:4500) was formed. Members joined: 1
   May 11 17:41:49 a2 corosync[1589]: [QUORUM] Members[4]: 1 2 3 4
   May 11 17:41:49 a2 corosync[1589]: [MAIN  ] Completed service synchronization, ready to provide service.
   May 11 17:41:49 a2 crmd[2294]:  notice: pcmk_quorum_notification: Node a1[1] - state is now member (was lost)
   May 11 17:41:49 a2 pacemakerd[2166]:  notice: pcmk_quorum_notification: Node a1[1] - state is now member (was lost)
   May 11 17:41:49 a2 attrd[2292]:  notice: crm_update_peer_proc: Node a1[1] - state is now member (was (null))
   May 11 17:41:49 a2 stonith-ng[2290]:  notice: crm_update_peer_proc: Node a1[1] - state is now member (was (null))
   May 11 17:41:49 a2 cib[2289]:  notice: crm_update_peer_proc: Node a1[1] - state is now member (was (null))


Code walk
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- The very first log after the ports blocking on the active host (a1) was ::

    May 11 17:41:15 a1 corosync[1537]: [TOTEM ] A processor failed, forming new configuration.

  Above log comes from
    - exec/totemsrp.c func:timer_function_orf_token_timeout(data) line:1765
      - timer_function_heartbeat_timeout
	- reset_heartbeat_timeout
	  
