============================================================================
Process of detaching/attaching ClusterIP in case of node/service failure
============================================================================
- Step 0: Initial condition

  - a1, a2, and a3 node are running in a test_cluster.
  - stonith is disabled.
  - webserver and ClusterIP resources are running on a2

::

   root@a1 ~]# pcs property list
   Cluster Properties:
   cluster-infrastructure: corosync
   cluster-name: my_cluster
   dc-version: 1.1.13-10.el7_2.2-44eb2dd
   have-watchdog: false
   last-lrm-refresh: 1463471186
   stonith-action: reboot
   stonith-enabled: false
	  
   [root@a2 ~]# pcs status
   Cluster name: test_cluster
   Last updated: Thu Jun  2 15:24:15 2016Last change: Thu Jun  2 15:24:12 2016 by root via cibadmin on a2
   Stack: corosync
   Current DC: a1 (version 1.1.13-10.el7_2.2-44eb2dd) - partition with quorum
   3 nodes and 5 resources configured
   
   Online: [ a1 a2 a3 ]

   Full list of resources:

   node1-xvm(stonith:fence_xvm):Started a1
   node2-xvm(stonith:fence_xvm):Started a2
   node3-xvm(stonith:fence_xvm):Started a3
   webserver(ocf::heartbeat:apache):Started a2
   ClusterIP(ocf::heartbeat:IPaddr2):Started a2

- Step1 : Stop httpd process on a2.
::

   [root@a2 ~]# killall -STOP httpd 


- Then the status of the cluster is like follows. CluserIP is now running on a1.  
::

   [root@a1 ~]# pcs status
   Cluster name: test_cluster
   Last updated: Thu Jun  2 15:40:29 2016Last change: Thu Jun  2 15:24:12 2016 by root via cibadmin on a2
   Stack: corosync
   Current DC: a1 (version 1.1.13-10.el7_2.2-44eb2dd) - partition with quorum
   3 nodes and 5 resources configured

   Online: [ a1 a2 a3 ]

   Full list of resources:

   node1-xvm(stonith:fence_xvm):Started a1
   node2-xvm(stonith:fence_xvm):Started a2
   node3-xvm(stonith:fence_xvm):Started a3
   webserver(ocf::heartbeat:apache):FAILED a2 (unmanaged)
   ClusterIP(ocf::heartbeat:IPaddr2):Started a1

   Failed Actions:
   * webserver_stop_0 on a2 'unknown error' (1): call=29, status=complete, exitreason='apache still running (3575). Killing pid failed.',
     last-rc-change='Thu Jun  2 15:26:25 2016', queued=0ms, exec=42205ms


     PCSD Status:
     a1: Online
     a2: Online
     a3: Online

     Daemon Status:
     corosync: active/enabled
     pacemaker: active/enabled
     pcsd: active/enabled

		  
- See what happened on a2 in the corosync.log on a2.
::
     
   Jun 02 15:27:07 [3042] a2.pm.redhat.com       crmd:     info: do_lrm_rsc_op:    Performing key=17:7:0:403e6f8f-a4b3-4424-a62e-d8270e651055 op=ClusterIP_stop_0
   Jun 02 15:27:07 [3039] a2.pm.redhat.com       lrmd:    debug: process_lrmd_message:     Processed lrmd_rsc_exec operation from 34478fba-2787-4c64-b9fe-aa864a4b612f: rc=31, reply=1, notify=0, exit=4203648
   Jun 02 15:27:07 [3039] a2.pm.redhat.com       lrmd:     info: log_execute:      executing - rsc:ClusterIP action:stop call_id:31
   Jun 02 15:27:07 [3042] a2.pm.redhat.com       crmd:     info: process_lrm_event:        Operation ClusterIP_monitor_30000: Cancelled (node=a2, call=24, confirmed=true)
   Jun 02 15:27:07 [3042] a2.pm.redhat.com       crmd:    debug: update_history_cache:     Updating history for 'ClusterIP' with monitor op
   IPaddr2(ClusterIP)[4743]:       2016/06/02_15:27:07 INFO: IP status = ok, IP_CIP=
   Jun 02 15:27:07 [3039] a2.pm.redhat.com       lrmd:    debug: operation_finished:       ClusterIP_stop_0:4743 - exited with rc=0
   Jun 02 15:27:07 [3039] a2.pm.redhat.com       lrmd:    debug: operation_finished:       ClusterIP_stop_0:4743:stderr [ -- empty -- ]
   Jun 02 15:27:07 [3039] a2.pm.redhat.com       lrmd:    debug: operation_finished:       ClusterIP_stop_0:4743:stdout [ -- empty -- ]
   Jun 02 15:27:07 [3039] a2.pm.redhat.com       lrmd:     info: log_finished:     finished - rsc:ClusterIP action:stop call_id:31 pid:4743 exit-code:0 exec-time:39ms queue-time:0ms
   Jun 02 15:27:07 [3042] a2.pm.redhat.com       crmd:    debug: create_operation_update:  do_update_resource: Updating resource ClusterIP after stop op complete (interval=0)
   Jun 02 15:27:07 [3042] a2.pm.redhat.com       crmd:   notice: process_lrm_event:        Operation ClusterIP_stop_0: ok (node=a2, call=31, rc=0, cib-update=26, confirmed=true)
   Jun 02 15:27:07 [3042] a2.pm.redhat.com       crmd:    debug: update_history_cache:     Updating history for 'ClusterIP' with stop op
   
Operation ClusterIP_stop_0 was executed and the Floating IP address was not on a2 anymore.
On the same time in a1's log,
::

   Jun 02 15:27:07 [3207] a1.pm.redhat.com    pengine: (    native.c:2243  )  notice: LogActions:  Move    ClusterIP       (Started a2 -> a1)
   Jun 02 15:27:07 [3207] a1.pm.redhat.com    pengine: (   pengine.c:179   )  notice: process_pe_message:  Calculated Transition 7: /var/lib/pacemaker/pengine/pe-input-431.bz2
   Jun 02 15:27:07 [3208] a1.pm.redhat.com       crmd: (       fsa.c:197   )   debug: s_crmd_fsa:  Processing I_PE_SUCCESS: [ state=S_POLICY_ENGINE cause=C_IPC_MESSAGE origin=h
   andle_response ]
   Jun 02 15:27:07 [3208] a1.pm.redhat.com       crmd: (       fsa.c:507   )    info: do_state_transition: State transition S_POLICY_ENGINE -> S_TRANSITION_ENGINE [ input=I_PE_
   SUCCESS cause=C_IPC_MESSAGE origin=handle_response ]
   Jun 02 15:27:07 [3208] a1.pm.redhat.com       crmd: (    unpack.c:230   )   debug: unpack_graph:        Unpacked transition 7: 4 actions in 4 synapses
   Jun 02 15:27:07 [3208] a1.pm.redhat.com       crmd: (   tengine.c:208   )    info: do_te_invoke:        Processing graph 7 (ref=pe_calc-dc-1464848827-57) derived from /var/l
   ib/pacemaker/pengine/pe-input-431.bz2
   Jun 02 15:27:07 [3208] a1.pm.redhat.com       crmd: (te_actions.c:422   )  notice: te_rsc_command:      Initiating action 17: stop ClusterIP_stop_0 on a2
   Jun 02 15:27:07 [3208] a1.pm.redhat.com       crmd: (     graph.c:343   )   debug: run_graph:   Transition 7 (Complete=0, Pending=1, Fired=1, Skipped=0, Incomplete=3, Source
   =/var/lib/pacemaker/pengine/pe-input-431.bz2): In-progress
   Jun 02 15:27:07 [3208] a1.pm.redhat.com       crmd: (te_actions.c:422   )  notice: te_rsc_command:      Initiating action 17: stop ClusterIP_stop_0 on a2
   Jun 02 15:27:07 [3208] a1.pm.redhat.com       crmd: (     graph.c:343   )   debug: run_graph:   Transition 7 (Complete=0, Pending=1, Fired=1, Skipped=0, Incomplete=3, Source
   =/var/lib/pacemaker/pengine/pe-input-431.bz2): In-progress
   Jun 02 15:27:07 [3203] a1.pm.redhat.com        cib: ( cib_utils.c:285   )   debug: cib_acl_enabled:     CIB ACL is disabled
   Jun 02 15:27:07 [3203] a1.pm.redhat.com        cib: (       xml.c:1660  )    info: cib_perform_op:      Diff: --- 0.301.9 2
   Jun 02 15:27:07 [3203] a1.pm.redhat.com        cib: (       xml.c:1662  )    info: cib_perform_op:      Diff: +++ 0.301.10 (null)
   Jun 02 15:27:07 [3203] a1.pm.redhat.com        cib: (       xml.c:1728  )    info: cib_perform_op:      +  /cib:  @num_updates=10
   Jun 02 15:27:07 [3203] a1.pm.redhat.com        cib: (       xml.c:1728  )    info: cib_perform_op:      +  /cib/status/node_state[@id='2']/lrm[@id='2']/lrm_resources/lrm_res
   ource[@id='ClusterIP']/lrm_rsc_op[@id='ClusterIP_last_0']:  @operation_key=ClusterIP_stop_0, @operation=stop, @transition-key=17:7:0:403e6f8f-a4b3-4424-a62e-d8270e651055, @t
   ransition-magic=0:0;17:7:0:403e6f8f-a4b3-4424-a62e-d8270e651055, @call-id=31, @last-run=1464848827, @last-rc-change=1464848827, @exec-time=39
   Jun 02 15:27:07 [3203] a1.pm.redhat.com        cib: ( callbacks.c:1079  )    info: cib_process_request: Completed cib_modify operation for section status: OK (rc=0, origin=a
   2/crmd/26, version=0.301.10)
   Jun 02 15:27:07 [3204] a1.pm.redhat.com stonith-ng: (       xml.c:2072  )   debug: xml_patch_version_check:     Can apply patch 0.301.10 to 0.301.9
   Jun 02 15:27:07 [3208] a1.pm.redhat.com       crmd: (te_callbacks:378   )   debug: te_update_diff:      Processing (cib_modify) diff: 0.301.9 -> 0.301.10 (S_TRANSITION_ENGIN
   E)
   Jun 02 15:27:07 [3208] a1.pm.redhat.com       crmd: ( te_events.c:387   )    info: match_graph_event:   Action ClusterIP_stop_0 (17) confirmed on a2 (rc=0)
   Jun 02 15:27:07 [3208] a1.pm.redhat.com       crmd: (te_actions.c:422   )  notice: te_rsc_command:      Initiating action 18: start ClusterIP_start_0 on a1 (local)
   Jun 02 15:27:07 [3208] a1.pm.redhat.com       crmd: (       lrm.c:2005  )   debug: do_lrm_rsc_op:       Stopped 0 recurring operations in preparation for ClusterIP_start_0
   Jun 02 15:27:07 [3208] a1.pm.redhat.com       crmd: (       lrm.c:2009  )    info: do_lrm_rsc_op:       Performing key=18:7:0:403e6f8f-a4b3-4424-a62e-d8270e651055 op=ClusterIP_start_0
   Jun 02 15:27:07 [3205] a1.pm.redhat.com       lrmd: (      lrmd.c:1686  )   debug: process_lrmd_message:        Processed lrmd_rsc_exec operation from 8bdea4e8-8d84-4061-9a55-888e70b87879: rc=28, reply=1, notify=0, exit=4203648
   Jun 02 15:27:07 [3205] a1.pm.redhat.com       lrmd: (      lrmd.c:131   )    info: log_execute: executing - rsc:ClusterIP action:start call_id:28
   Jun 02 15:27:07 [3205] a1.pm.redhat.com       lrmd: (services_lin:689   )   trace: services_os_action_execute:  Async waiting for 3882 - /usr/lib/ocf/resource.d/heartbeat/IPaddr2
   Jun 02 15:27:07 [3208] a1.pm.redhat.com       crmd: (te_actions.c:55    )   debug: te_pseudo_action:    Pseudo action 5 fired and confirmed
   Jun 02 15:27:07 [3208] a1.pm.redhat.com       crmd: (     graph.c:343   )   debug: run_graph:   Transition 7 (Complete=1, Pending=1, Fired=2, Skipped=0, Incomplete=1, Source=/var/lib/pacemaker/pengine/pe-input-431.bz2): In-progress
   Jun 02 15:27:07 [3208] a1.pm.redhat.com       crmd: (     graph.c:343   )   debug: run_graph:   Transition 7 (Complete=2, Pending=1, Fired=0, Skipped=0, Incomplete=1, Source=/var/lib/pacemaker/pengine/pe-input-431.bz2): In-progress
   IPaddr2(ClusterIP)[3882]:       2016/06/02_15:27:07 INFO: Adding inet address 192.168.110.120/24 with broadcast address 192.168.110.255 to device eth0
   IPaddr2(ClusterIP)[3882]:       2016/06/02_15:27:07 INFO: Bringing device eth0 up
   IPaddr2(ClusterIP)[3882]:       2016/06/02_15:27:07 INFO: /usr/libexec/heartbeat/send_arp -i 200 -r 5 -p /var/run/resource-agents/send_arp-192.168.110.120 eth0 192.168.110.120 auto not_used not_used
   Jun 02 15:27:07 [3205] a1.pm.redhat.com       lrmd: (services_lin:289   )   trace: operation_finished:  ClusterIP_start_0:3882 0xe99a10 0xe99990
   Jun 02 15:27:07 [3205] a1.pm.redhat.com       lrmd: (services_lin:294   )   trace: operation_finished:  ClusterIP_start_0:3882 dispatching stderr
   Jun 02 15:27:07 [3205] a1.pm.redhat.com       lrmd: (services_lin:92    )   trace: svc_read_output:     Reading ClusterIP_start_0 stderr
   Jun 02 15:27:07 [3205] a1.pm.redhat.com       lrmd: (services_lin:296   )   trace: operation_finished:  ClusterIP_start_0: (nil)
   Jun 02 15:27:07 [3205] a1.pm.redhat.com       lrmd: (services_lin:305   )   trace: operation_finished:  ClusterIP_start_0:3882 dispatching stdout
   Jun 02 15:27:07 [3205] a1.pm.redhat.com       lrmd: (services_lin:92    )   trace: svc_read_output:     Reading ClusterIP_start_0 stdout
   Jun 02 15:27:07 [3205] a1.pm.redhat.com       lrmd: (services_lin:307   )   trace: operation_finished:  ClusterIP_start_0: (nil)
   Jun 02 15:27:07 [3205] a1.pm.redhat.com       lrmd: (services_lin:143   )   trace: pipe_out_done:       0xe9cf70
   Jun 02 15:27:07 [3205] a1.pm.redhat.com       lrmd: (services_lin:327   )   debug: operation_finished:  ClusterIP_start_0:3882 - exited with rc=0
   Jun 02 15:27:07 [3205] a1.pm.redhat.com       lrmd: (services_lin:332   )   debug: operation_finished:  ClusterIP_start_0:3882:stderr [ -- empty -- ]
   Jun 02 15:27:07 [3205] a1.pm.redhat.com       lrmd: (services_lin:336   )   debug: operation_finished:  ClusterIP_start_0:3882:stdout [ -- empty -- ]
   Jun 02 15:27:07 [3205] a1.pm.redhat.com       lrmd: (      lrmd.c:113   )    info: log_finished:        finished - rsc:ClusterIP action:start call_id:28 pid:3882 exit-code:0 exec-time:46ms queue-time:0ms
   
Actually in this case, a1 was the DC. So the calculations by pengine were carried out on a1 before stopping ClusterIP on a2.
After stopping ClusterIP on a2, ClusterIP on a1 was started.
As a conclusion, pacemaker tried to remove(stop) ClusterIP on the failure node.
So even if stonith is disabled, ClusterIP on the failure node is removed (at least tried to be removed), then assigned to the new active node.
