========================================================
Code Walk / Timeout of a resource
============================================================
Log
::

   [mihara@wlan-197-60 data]$ cat corosync-fencea3_a3.log
May 24 15:46:41 [29153] a3.pm.redhat.com       lrmd:  warning: child_timeout_callback:webserver_monitor_10000 process (PID 26685) timed out
May 24 15:46:41 [29153] a3.pm.redhat.com       lrmd:  warning: operation_finished:webserver_monitor_10000:26685 - timed out after 20000ms
May 24 15:46:41 [29156] a3.pm.redhat.com       crmd:    error: process_lrm_event:Operation webserver_monitor_10000: Timed Out (node=a3, call=36, timeout=20000ms)
May 24 15:46:41 [29151] a3.pm.redhat.com        cib:     info: cib_process_request:Forwarding cib_modify operation for section status to master (origin=local/crmd/72)
May 24 15:46:41 [29151] a3.pm.redhat.com        cib:     info: cib_perform_op:Diff: --- 0.242.50 2
May 24 15:46:41 [29151] a3.pm.redhat.com        cib:     info: cib_perform_op:Diff: +++ 0.242.51 (null)
May 24 15:46:41 [29151] a3.pm.redhat.com        cib:     info: cib_perform_op:+  /cib:  @num_updates=51
May 24 15:46:41 [29151] a3.pm.redhat.com        cib:     info: cib_perform_op:++ /cib/status/node_state[@id='3']/lrm[@id='3']/lrm_resources/lrm_resource[@id='webserver']:  <lrm_rsc_op id="webserver_last_failure_0" operation_key="webserver_monitor_10000" operation="monitor" crm-debug-origin="do_update_resource" crm_feature_set="3.0.10" transition-key="27:2:0:a459132e-e3b7-4093-91dc-e93cbe533358" transition-magic="2:1;27:2:0:a459132e-e3b7-4093-91dc-e93cbe533358" on_node="a3" call-id="36" rc-code="1" op-stat
May 24 15:46:41 [29151] a3.pm.redhat.com        cib:     info: cib_process_request:Completed cib_modify operation for section status: OK (rc=0, origin=a3/crmd/72, version=0.242.51)
May 24 15:46:41 [29156] a3.pm.redhat.com       crmd:     info: abort_transition_graph:Transition aborted by webserver_monitor_10000 'create' on a3: Old event (magic=2:1;27:2:0:a459132e-e3b7-4093-91dc-e93cbe533358, cib=0.242.51, source=process_graph_event:593, 1)
May 24 15:46:41 [29156] a3.pm.redhat.com       crmd:     info: update_failcount:Updating failcount for webserver on a3 after failed monitor: rc=1 (update=value++, time=1464072401)
May 24 15:46:41 [29156] a3.pm.redhat.com       crmd:     info: process_graph_event:Detected action (2.27) webserver_monitor_10000.36=unknown error: failed
May 24 15:46:41 [29156] a3.pm.redhat.com       crmd:   notice: do_state_transition:State transition S_IDLE -> S_POLICY_ENGINE [ input=I_PE_CALC cause=C_FSA_INTERNAL origin=abort_transition_graph ]
May 24 15:46:41 [29154] a3.pm.redhat.com      attrd:     info: attrd_client_update:Expanded fail-count-webserver=value++ to 1
May 24 15:46:41 [29154] a3.pm.redhat.com      attrd:     info: attrd_peer_update:Setting fail-count-webserver[a3]: (null) -> 1 from a3
May 24 15:46:41 [29154] a3.pm.redhat.com      attrd:     info: write_attribute:Sent update 9 with 1 changes for fail-count-webserver, id=<n/a>, set=(null)
May 24 15:46:41 [29154] a3.pm.redhat.com      attrd:     info: attrd_peer_update:Setting last-failure-webserver[a3]: (null) -> 1464072401 from a3
May 24 15:46:41 [29154] a3.pm.redhat.com      attrd:     info: write_attribute:Sent update 10 with 1 changes for last-failure-webserver, id=<n/a>, set=(null)
May 24 15:46:41 [29151] a3.pm.redhat.com        cib:     info: cib_process_request:Forwarding cib_modify operation for section status to master (origin=local/attrd/9)
May 24 15:46:41 [29151] a3.pm.redhat.com        cib:     info: cib_process_request:Forwarding cib_modify operation for section status to master (origin=local/attrd/10)
May 24 15:46:41 [29151] a3.pm.redhat.com        cib:     info: cib_perform_op:Diff: --- 0.242.51 2
May 24 15:46:41 [29151] a3.pm.redhat.com        cib:     info: cib_perform_op:Diff: +++ 0.242.52 (null)
May 24 15:46:41 [29151] a3.pm.redhat.com        cib:     info: cib_perform_op:+  /cib:  @num_updates=52
May 24 15:46:41 [29151] a3.pm.redhat.com        cib:     info: cib_perform_op:++ /cib/status/node_state[@id='3']/transient_attributes[@id='3']/instance_attributes[@id='status-3']:  <nvpair id="status-3-fail-count-webserver" name="fail-count-webserver" value="1"/>
May 24 15:46:41 [29151] a3.pm.redhat.com        cib:     info: cib_process_request:Completed cib_modify operation for section status: OK (rc=0, origin=a3/attrd/9, version=0.242.52)
May 24 15:46:41 [29151] a3.pm.redhat.com        cib:     info: cib_perform_op:Diff: --- 0.242.52 2
May 24 15:46:41 [29151] a3.pm.redhat.com        cib:     info: cib_perform_op:Diff: +++ 0.242.53 (null)
May 24 15:46:41 [29151] a3.pm.redhat.com        cib:     info: cib_perform_op:+  /cib:  @num_updates=53
May 24 15:46:41 [29151] a3.pm.redhat.com        cib:     info: cib_perform_op:++ /cib/status/node_state[@id='3']/transient_attributes[@id='3']/instance_attributes[@id='status-3']:  <nvpair id="status-3-last-failure-webserver" name="last-failure-webserver" value="1464072401"/>
May 24 15:46:41 [29154] a3.pm.redhat.com      attrd:     info: attrd_cib_callback:Update 9 for fail-count-webserver: OK (0)
May 24 15:46:41 [29154] a3.pm.redhat.com      attrd:     info: attrd_cib_callback:Update 9 for fail-count-webserver[a3]=1: OK (0)
May 24 15:46:41 [29151] a3.pm.redhat.com        cib:     info: cib_process_request:Completed cib_modify operation for section status: OK (rc=0, origin=a3/attrd/10, version=0.242.53)
May 24 15:46:41 [29154] a3.pm.redhat.com      attrd:     info: attrd_cib_callback:Update 10 for last-failure-webserver: OK (0)
May 24 15:46:41 [29154] a3.pm.redhat.com      attrd:     info: attrd_cib_callback:Update 10 for last-failure-webserver[a3]=1464072401: OK (0)
May 24 15:46:41 [29156] a3.pm.redhat.com       crmd:     info: abort_transition_graph:Transition aborted by status-3-fail-count-webserver, fail-count-webserver=1: Transient attribute change (create cib=0.242.52, source=abort_unless_down:319, path=/cib/status/node_state[@id='3']/transient_attributes[@id='3']/instance_attributes[@id='status-3'], 1)
May 24 15:46:41 [29156] a3.pm.redhat.com       crmd:     info: abort_transition_graph:Transition aborted by status-3-last-failure-webserver, last-failure-webserver=1464072401: Transient attribute change (create cib=0.242.53, source=abort_unless_down:319, path=/cib/status/node_state[@id='3']/transient_attributes[@id='3']/instance_attributes[@id='status-3'], 1)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: determine_online_status_fencing:Node a2 is active
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: determine_online_status:Node a2 is online
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: determine_online_status_fencing:Node a3 is active
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: determine_online_status:Node a3 is online
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: determine_online_status_fencing:Node a1 is active
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: determine_online_status:Node a1 is online
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:  warning: unpack_rsc_op_failure:Processing failed op monitor for webserver:1 on a3: unknown error (1)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: native_print:node1-xvm(stonith:fence_xvm):Started a1
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: native_print:node2-xvm(stonith:fence_xvm):Started a2
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: native_print:node3-xvm(stonith:fence_xvm):Started a3
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: clone_print: Clone Set: ClusterIP-clone [ClusterIP] (unique)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: native_print:     ClusterIP:0(ocf::heartbeat:IPaddr2):Started a1
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: native_print:     ClusterIP:1(ocf::heartbeat:IPaddr2):Started a2
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: native_print:     ClusterIP:2(ocf::heartbeat:IPaddr2):Started a3
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: clone_print: Clone Set: webserver-clone [webserver]
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: native_print:     webserver(ocf::heartbeat:apache):FAILED a3
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: short_print:     Started: [ a1 a2 ]
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: RecurringOp: Start recurring monitor (10s) for webserver:1 on a3
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: LogActions:Leave   node1-xvm(Started a1)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: LogActions:Leave   node2-xvm(Started a2)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: LogActions:Leave   node3-xvm(Started a3)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: LogActions:Leave   ClusterIP:0(Started a1)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: LogActions:Leave   ClusterIP:1(Started a2)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: LogActions:Leave   ClusterIP:2(Started a3)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: LogActions:Leave   webserver:0(Started a2)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:   notice: LogActions:Recover webserver:1(Started a3)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: LogActions:Leave   webserver:2(Started a1)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:   notice: process_pe_message:Calculated Transition 20: /var/lib/pacemaker/pengine/pe-input-238.bz2
May 24 15:46:41 [29156] a3.pm.redhat.com       crmd:     info: handle_response:pe_calc calculation pe_calc-dc-1464072401-80 is obsolete
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: determine_online_status_fencing:Node a2 is active
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: determine_online_status:Node a2 is online
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: determine_online_status_fencing:Node a3 is active
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: determine_online_status:Node a3 is online
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: determine_online_status_fencing:Node a1 is active
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: determine_online_status:Node a1 is online
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:  warning: unpack_rsc_op_failure:Processing failed op monitor for webserver:1 on a3: unknown error (1)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: native_print:node1-xvm(stonith:fence_xvm):Started a1
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: native_print:node2-xvm(stonith:fence_xvm):Started a2
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: native_print:node3-xvm(stonith:fence_xvm):Started a3
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: clone_print: Clone Set: ClusterIP-clone [ClusterIP] (unique)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: native_print:     ClusterIP:0(ocf::heartbeat:IPaddr2):Started a1
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: native_print:     ClusterIP:1(ocf::heartbeat:IPaddr2):Started a2
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: native_print:     ClusterIP:2(ocf::heartbeat:IPaddr2):Started a3
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: clone_print: Clone Set: webserver-clone [webserver]
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: native_print:     webserver(ocf::heartbeat:apache):FAILED a3
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: short_print:     Started: [ a1 a2 ]
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: get_failcount_full:webserver:0 has failed 1 times on a3
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: common_apply_stickiness:webserver-clone can fail 999999 more times on a3 before being forced off
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: get_failcount_full:webserver:1 has failed 1 times on a3
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: common_apply_stickiness:webserver-clone can fail 999999 more times on a3 before being forced off
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: get_failcount_full:webserver:2 has failed 1 times on a3
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: common_apply_stickiness:webserver-clone can fail 999999 more times on a3 before being forced off
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: RecurringOp: Start recurring monitor (10s) for webserver:1 on a3
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: LogActions:Leave   node1-xvm(Started a1)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: LogActions:Leave   node2-xvm(Started a2)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: LogActions:Leave   node3-xvm(Started a3)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: LogActions:Leave   ClusterIP:0(Started a1)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: LogActions:Leave   ClusterIP:1(Started a2)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: LogActions:Leave   ClusterIP:2(Started a3)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: LogActions:Leave   webserver:0(Started a2)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:   notice: LogActions:Recover webserver:1(Started a3)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:     info: LogActions:Leave   webserver:2(Started a1)
May 24 15:46:41 [29155] a3.pm.redhat.com    pengine:   notice: process_pe_message:Calculated Transition 21: /var/lib/pacemaker/pengine/pe-input-239.bz2
May 24 15:46:41 [29156] a3.pm.redhat.com       crmd:     info: do_state_transition:State transition S_POLICY_ENGINE -> S_TRANSITION_ENGINE [ input=I_PE_SUCCESS cause=C_IPC_MESSAGE origin=handle_response ]
May 24 15:46:41 [29156] a3.pm.redhat.com       crmd:     info: do_te_invoke:Processing graph 21 (ref=pe_calc-dc-1464072401-81) derived from /var/lib/pacemaker/pengine/pe-input-239.bz2
May 24 15:46:41 [29156] a3.pm.redhat.com       crmd:   notice: te_rsc_command:Initiating action 7: stop webserver_stop_0 on a3 (local)
May 24 15:46:41 [29153] a3.pm.redhat.com       lrmd:     info: cancel_recurring_action:Cancelling ocf operation webserver_monitor_10000
May 24 15:46:41 [29156] a3.pm.redhat.com       crmd:     info: do_lrm_rsc_op:Performing key=7:21:0:a459132e-e3b7-4093-91dc-e93cbe533358 op=webserver_stop_0
May 24 15:46:41 [29153] a3.pm.redhat.com       lrmd:     info: log_execute:executing - rsc:webserver action:stop call_id:38
May 24 15:46:41 [29156] a3.pm.redhat.com       crmd:     info: process_lrm_event:Operation webserver_monitor_10000: Cancelled (node=a3, call=36, confirmed=true)
apache(webserver)[26790]:2016/05/24_15:46:41 INFO: Attempting graceful stop of apache PID 29588
May 24 15:46:46 [29151] a3.pm.redhat.com        cib:     info: cib_process_ping:Reporting our current digest to a3: 545aa3f1cfa04d8cfcce59dadc291c92 for 0.242.53 (0x221d4f0 0)




::

   lib/services/services_linux.c
   690        mainloop_child_add_with_flags(op->pid,
   691                                      op->timeout,
   692                                      op->id,
   693                                      op,
   694                                      (op->flags & SVC_ACTION_LEAVE_GROUP) ? mainloop_leave_pid_group : 0,
   695                                      operation_finished);


   lib/common/mainloop.c:1093: mainloop_child_add_with_flags(pid_t pid, int timeout, const char *desc, void *privatedata, enum mainloop_child_flags flags,

   1110    if (timeout) {
   1111        child->timerid = g_timeout_add(timeout, child_timeout_callback, child);
   1112    }
   
