========================================================
Code-walk / Monitoring resources
========================================================

Recurring logs and monitoring
===============================================
After a resource is added, a recurring set of logs like follows are seen in the logs.
::

   May 26 18:46:26 [2281] a1.pm.redhat.com       lrmd: (services_lin:226   )   debug: recurring_action_timer:	Scheduling another invocation of webserver_monitor_10000
   May 26 18:46:26 [2281] a1.pm.redhat.com       lrmd: (services_lin:689   )   trace: services_os_action_execute:	Async waiting for 2629 - /usr/lib/ocf/resource.d/heartbeat/apache
   May 26 18:46:26 [2281] a1.pm.redhat.com       lrmd: (services_lin:289   )   trace: operation_finished:	webserver_monitor_10000:2629 0x13e1b30 0x13e3990
   May 26 18:46:26 [2281] a1.pm.redhat.com       lrmd: (services_lin:294   )   trace: operation_finished:	webserver_monitor_10000:2629 dispatching stderr
   May 26 18:46:26 [2281] a1.pm.redhat.com       lrmd: (services_lin:92    )   trace: svc_read_output:	Reading webserver_monitor_10000 stderr
   May 26 18:46:26 [2281] a1.pm.redhat.com       lrmd: (services_lin:296   )   trace: operation_finished:	webserver_monitor_10000: (nil)
   May 26 18:46:26 [2281] a1.pm.redhat.com       lrmd: (services_lin:305   )   trace: operation_finished:	webserver_monitor_10000:2629 dispatching stdout
   May 26 18:46:26 [2281] a1.pm.redhat.com       lrmd: (services_lin:92    )   trace: svc_read_output:	Reading webserver_monitor_10000 stdout
   May 26 18:46:26 [2281] a1.pm.redhat.com       lrmd: (services_lin:307   )   trace: operation_finished:	webserver_monitor_10000: (nil)
   May 26 18:46:26 [2281] a1.pm.redhat.com       lrmd: (services_lin:143   )   trace: pipe_out_done:	0x13e3060
   May 26 18:46:26 [2281] a1.pm.redhat.com       lrmd: (services_lin:327   )   debug: operation_finished:	webserver_monitor_10000:2629 - exited with rc=0
   May 26 18:46:26 [2281] a1.pm.redhat.com       lrmd: (services_lin:332   )   debug: operation_finished:	webserver_monitor_10000:2629:stderr [ -- empty -- ]
   May 26 18:46:26 [2281] a1.pm.redhat.com       lrmd: (services_lin:336   )   debug: operation_finished:	webserver_monitor_10000:2629:stdout [ -- empty -- ]
   May 26 18:46:26 [2281] a1.pm.redhat.com       lrmd: (      lrmd.c:113   )   debug: log_finished:	finished - rsc:webserver action:monitor call_id:21 pid:2629 exit-code:0 exec-time:0ms queue-time:0ms

   >>>>> After about 10 seconds, the same set of logs appear again <<<<<<<<<<
   
   May 26 18:46:36 [2281] a1.pm.redhat.com       lrmd: (services_lin:226   )   debug: recurring_action_timer:	Scheduling another invocation of webserver_monitor_10000
   May 26 18:46:36 [2281] a1.pm.redhat.com       lrmd: (services_lin:689   )   trace: services_os_action_execute:	Async waiting for 2664 - /usr/lib/ocf/resource.d/heartbeat/apache
   May 26 18:46:36 [2281] a1.pm.redhat.com       lrmd: (services_lin:289   )   trace: operation_finished:	webserver_monitor_10000:2664 0x13e3990 0x13e31b0
   May 26 18:46:36 [2281] a1.pm.redhat.com       lrmd: (services_lin:294   )   trace: operation_finished:	webserver_monitor_10000:2664 dispatching stderr
   May 26 18:46:36 [2281] a1.pm.redhat.com       lrmd: (services_lin:92    )   trace: svc_read_output:	Reading webserver_monitor_10000 stderr
   May 26 18:46:36 [2281] a1.pm.redhat.com       lrmd: (services_lin:296   )   trace: operation_finished:	webserver_monitor_10000: (nil)
   May 26 18:46:36 [2281] a1.pm.redhat.com       lrmd: (services_lin:305   )   trace: operation_finished:	webserver_monitor_10000:2664 dispatching stdout
   May 26 18:46:36 [2281] a1.pm.redhat.com       lrmd: (services_lin:92    )   trace: svc_read_output:	Reading webserver_monitor_10000 stdout
   May 26 18:46:36 [2281] a1.pm.redhat.com       lrmd: (services_lin:307   )   trace: operation_finished:	webserver_monitor_10000: (nil)
   May 26 18:46:36 [2281] a1.pm.redhat.com       lrmd: (services_lin:143   )   trace: pipe_out_done:	0x13e3060
   May 26 18:46:36 [2281] a1.pm.redhat.com       lrmd: (services_lin:327   )   debug: operation_finished:	webserver_monitor_10000:2664 - exited with rc=0
   May 26 18:46:36 [2281] a1.pm.redhat.com       lrmd: (services_lin:332   )   debug: operation_finished:	webserver_monitor_10000:2664:stderr [ -- empty -- ]
   May 26 18:46:36 [2281] a1.pm.redhat.com       lrmd: (services_lin:336   )   debug: operation_finished:	webserver_monitor_10000:2664:stdout [ -- empty -- ]
   May 26 18:46:36 [2281] a1.pm.redhat.com       lrmd: (      lrmd.c:113   )   debug: log_finished:	finished - rsc:webserver action:monitor call_id:21 pid:2664 exit-code:0 exec-time:0ms queue-time:0ms

	       
From recurring_action_timer(the 1st line) to services_os_action_execute(the 2nd line),
::
   
   lib/services/services_linux.c:222: recurring_action_timer(gpointer data)
   lib/services/services_linux.c:235: services_action_async(op, NULL);
   lib/services/services.c:609: services_action_async(svc_action_t * op, ...
   lib/services/services.c:630:    return action_async_helper(op);
   lib/services/services.c:585:action_async_helper(svc_action_t * op) {
   lib/services/services.c:597: res = services_os_action_execute(op, FALSE);
   lib/services/services_linux.c:594:services_os_action_execute(svc_action_t * op, ...

Then in services_os_action_execute() function, the cllback function (operation_finished) is registered by utilizing mainloop_child_add_with_flags() function.
::
   lib/services/services_linux.c:689-694:
   
   689        crm_trace("Async waiting for %d - %s", op->pid, op->opaque->exec);         |
   690        mainloop_child_add_with_flags(op->pid,                                     |
   691                                      op->timeout,                                 |
   692                                      op->id,                                      |
   693                                      op,                                          |
   694                                      (op->flags & SVC_ACTION_LEAVE_GROUP) ? mainl\|
      oop_leave_pid_group : 0,                                                           |
      695                                      operation_finished);   

In operation_finished() function, operation_finalize() function is called.
::

   lib/services/services_linux.c:339: operation_finalize(op);

In operation_finalize() function, recurring_action_timer() function is set to be called at regular intervals (op->interval) by utilizing GLib's g_timeout_add() function.
The "recurring_action_timer()" function is the first one to be called in the recurring log. (So we are back to the entry point of the repeating logs!) 
::

   lib/services/services_linux.c:251-252:
   251            op->opaque->repeat_timer = g_timeout_add(op->interval,                 |
   252                                                     recurring_action_timer, (void\|
       *)op);                                                               


Monitoring resources
-------------------------------------------
In above section, the recurring monitoring loop was investigated.How does pacemaker monitor each resource, then?
::

   lib/services/services_linux.c:222: recurring_action_timer(gpointer data)
   lib/services/services_linux.c:235: services_action_async(op, NULL);
   lib/services/services.c:609: services_action_async(svc_action_t * op, ...
   lib/services/services.c:630:    return action_async_helper(op);
   lib/services/services.c:585:action_async_helper(svc_action_t * op) {
   lib/services/services.c:597: res = services_os_action_execute(op, FALSE);
   lib/services/services_linux.c:594:services_os_action_execute(svc_action_t * op, ...

From recurring_action_timer() to services_os_action_execute() was referred in the previous section.
Continuing from services_os_action_execute(),
::

   lib/services/services_linux.c:665: action_launch_child(op);

   lib/services/services_linux.c:396: action_launch_child(svc_action_t *op)
     451    /* execute the RA */
     452    execvp(op->opaque->exec, op->opaque->args);

In the action_launch_child() function in lib/services/services_linux.c:452, execvp() is called and the script, or resource agent is executed.
In spite of the lack of logs, we know that the caller of execvp() is lrmd process.
So, the resource agent is called once in every recurring loop by lrmd process with args="monitor".

Detecting failure on resources
================================================

Case1: -KILL httpd -> recover without fence
----------------------------------------------------
Resource Group: WEBSERVICE (ClusterIP + webserver) is running on node a2. (Active/Passive)
::

   [root@a3 ~]# pcs status
   Cluster name: test_cluster
   Last updated: Thu Jun  2 17:11:41 2016Last change: Thu Jun  2 16:43:05 2016 by root via cibadmin on a2
   Stack: corosync
   Current DC: a2 (version 1.1.13-10.el7_2.2-44eb2dd) - partition with quorum
   3 nodes and 5 resources configured
   
   Online: [ a1 a2 a3 ]
   
   Full list of resources:
   
   node1-xvm(stonith:fence_xvm):Started a2
   node2-xvm(stonith:fence_xvm):Started a1
   node3-xvm(stonith:fence_xvm):Started a3
   Resource Group: WEBSERVICE
   ClusterIP(ocf::heartbeat:IPaddr2):Started a2
   webserver(ocf::heartbeat:apache):Started a2

On a2, kill httpd processes like follows,
::

   [root@a2 ~]# killall -KILL httpd

Right after the above command, httpd processes were disappeared on a2.
However, after a while, httpd processes were running again on a2.
Here are the logs on a2
::

   Jun 02 17:09:03 [3496] a2.pm.redhat.com       lrmd:    debug: recurring_action_timer:	Scheduling another invocation of webserver_monitor_10000
   Jun 02 17:09:03 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11035 - exited with rc=0
   Jun 02 17:09:03 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11035:stderr [ -- empty -- ]
   Jun 02 17:09:03 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11035:stdout [ -- empty -- ]
   Jun 02 17:09:03 [3496] a2.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:webserver action:monitor call_id:47 pid:11035 exit-code:0 exec-time:0ms queue-time:0ms
   Jun 02 17:09:13 [3496] a2.pm.redhat.com       lrmd:    debug: recurring_action_timer:	Scheduling another invocation of webserver_monitor_10000
   Jun 02 17:09:13 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11070 - exited with rc=0
   Jun 02 17:09:13 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11070:stderr [ -- empty -- ]
   Jun 02 17:09:13 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11070:stdout [ -- empty -- ]
   Jun 02 17:09:13 [3496] a2.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:webserver action:monitor call_id:47 pid:11070 exit-code:0 exec-time:0ms queue-time:0ms
   Jun 02 17:09:21 [3499] a2.pm.redhat.com       crmd:    debug: throttle_cib_load:	cib load: 0.000000 (0 ticks in 30s)
   Jun 02 17:09:21 [3499] a2.pm.redhat.com       crmd:    debug: throttle_load_avg:	Current load is 0.000000 (full: 0.00 0.01 0.05 2/131 11108)
   Jun 02 17:09:21 [3499] a2.pm.redhat.com       crmd:    debug: throttle_io_load:	Current IO load is 0.000000
   Jun 02 17:09:21 [3496] a2.pm.redhat.com       lrmd:    debug: recurring_action_timer:	Scheduling another invocation of ClusterIP_monitor_30000
   Jun 02 17:09:21 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	ClusterIP_monitor_30000:11109 - exited with rc=0
   Jun 02 17:09:21 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	ClusterIP_monitor_30000:11109:stderr [ -- empty -- ]
   Jun 02 17:09:21 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	ClusterIP_monitor_30000:11109:stdout [ -- empty -- ]
   Jun 02 17:09:21 [3496] a2.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:ClusterIP action:monitor call_id:45 pid:11109 exit-code:0 exec-time:0ms queue-time:0ms
   Jun 02 17:09:23 [3496] a2.pm.redhat.com       lrmd:    debug: recurring_action_timer:	Scheduling another invocation of webserver_monitor_10000
   Jun 02 17:09:23 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11154 - exited with rc=0
   Jun 02 17:09:23 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11154:stderr [ -- empty -- ]
   Jun 02 17:09:23 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11154:stdout [ -- empty -- ]
   Jun 02 17:09:23 [3496] a2.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:webserver action:monitor call_id:47 pid:11154 exit-code:0 exec-time:0ms queue-time:0ms
   Jun 02 17:09:33 [3496] a2.pm.redhat.com       lrmd:    debug: recurring_action_timer:	Scheduling another invocation of webserver_monitor_10000
   Jun 02 17:09:33 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11210 - exited with rc=0
   Jun 02 17:09:33 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11210:stderr [ -- empty -- ]
   Jun 02 17:09:33 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11210:stdout [ -- empty -- ]
   Jun 02 17:09:33 [3496] a2.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:webserver action:monitor call_id:47 pid:11210 exit-code:0 exec-time:0ms queue-time:0ms
   Jun 02 17:09:34 [3496] a2.pm.redhat.com       lrmd:    debug: log_execute:	executing - rsc:node1-xvm action:monitor call_id:27
   Jun 02 17:09:34 [3495] a2.pm.redhat.com stonith-ng:    debug: stonith_command:	Processing st_execute 39 from lrmd.3496 (               0)
   Jun 02 17:09:34 [3495] a2.pm.redhat.com stonith-ng:    debug: schedule_stonith_command:	Scheduling monitor on node1-xvm for 98d78c4d-ba12-46be-bed3-601a07ba7453 (timeout=20s)
   Jun 02 17:09:34 [3495] a2.pm.redhat.com stonith-ng:    debug: stonith_command:	Processed st_execute from lrmd.3496: Operation now in progress (-115)
   Jun 02 17:09:34 [3495] a2.pm.redhat.com stonith-ng:    debug: stonith_action_create:	Initiating action monitor for agent fence_xvm (target=(null))
   Jun 02 17:09:34 [3495] a2.pm.redhat.com stonith-ng:    debug: internal_stonith_action_execute:	forking
   Jun 02 17:09:34 [3495] a2.pm.redhat.com stonith-ng:    debug: internal_stonith_action_execute:	sending args
   Jun 02 17:09:34 [3495] a2.pm.redhat.com stonith-ng:    debug: stonith_device_execute:	Operation monitor on node1-xvm now running with pid=11245, timeout=20s
   Jun 02 17:09:43 [3496] a2.pm.redhat.com       lrmd:    debug: recurring_action_timer:	Scheduling another invocation of webserver_monitor_10000
   Jun 02 17:09:44 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11247 - exited with rc=0
   Jun 02 17:09:44 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11247:stderr [ -- empty -- ]
   Jun 02 17:09:44 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11247:stdout [ -- empty -- ]
   Jun 02 17:09:44 [3496] a2.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:webserver action:monitor call_id:47 pid:11247 exit-code:0 exec-time:0ms queue-time:0ms
   Jun 02 17:09:44 [3495] a2.pm.redhat.com stonith-ng:    debug: stonith_action_async_done:	Child process 11245 performing action 'monitor' exited with rc 0
   Jun 02 17:09:44 [3495] a2.pm.redhat.com stonith-ng:    debug: st_child_done:	Operation 'monitor' on 'node1-xvm' completed with rc=0 (0 remaining)
   Jun 02 17:09:44 [3495] a2.pm.redhat.com stonith-ng:    debug: log_operation:	Operation 'monitor' [11245] for device 'node1-xvm' returned: 0 (OK)
   Jun 02 17:09:44 [3496] a2.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:node1-xvm action:monitor call_id:27  exit-code:0 exec-time:10032ms queue-time:0ms
   Jun 02 17:09:51 [3499] a2.pm.redhat.com       crmd:    debug: throttle_cib_load:	cib load: 0.000000 (0 ticks in 30s)
   Jun 02 17:09:51 [3499] a2.pm.redhat.com       crmd:    debug: throttle_load_avg:	Current load is 0.000000 (full: 0.00 0.01 0.05 2/133 11281)
   Jun 02 17:09:51 [3499] a2.pm.redhat.com       crmd:    debug: throttle_io_load:	Current IO load is 0.000000
   Jun 02 17:09:51 [3496] a2.pm.redhat.com       lrmd:    debug: recurring_action_timer:	Scheduling another invocation of ClusterIP_monitor_30000
   Jun 02 17:09:51 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	ClusterIP_monitor_30000:11282 - exited with rc=0
   Jun 02 17:09:51 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	ClusterIP_monitor_30000:11282:stderr [ -- empty -- ]
   Jun 02 17:09:51 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	ClusterIP_monitor_30000:11282:stdout [ -- empty -- ]
   Jun 02 17:09:51 [3496] a2.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:ClusterIP action:monitor call_id:45 pid:11282 exit-code:0 exec-time:0ms queue-time:0ms
   Jun 02 17:09:54 [3496] a2.pm.redhat.com       lrmd:    debug: recurring_action_timer:	Scheduling another invocation of webserver_monitor_10000
   Jun 02 17:09:54 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11329 - exited with rc=0
   Jun 02 17:09:54 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11329:stderr [ -- empty -- ]
   Jun 02 17:09:54 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11329:stdout [ -- empty -- ]
   Jun 02 17:09:54 [3496] a2.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:webserver action:monitor call_id:47 pid:11329 exit-code:0 exec-time:0ms queue-time:0ms
   Jun 02 17:10:01 [3499] a2.pm.redhat.com       crmd:     info: crm_timer_popped:	PEngine Recheck Timer (I_PE_CALC) just popped (900000ms)
   Jun 02 17:10:01 [3499] a2.pm.redhat.com       crmd:    debug: s_crmd_fsa:	Processing I_PE_CALC: [ state=S_IDLE cause=C_TIMER_POPPED origin=crm_timer_popped ]
   Jun 02 17:10:01 [3499] a2.pm.redhat.com       crmd:   notice: do_state_transition:	State transition S_IDLE -> S_POLICY_ENGINE [ input=I_PE_CALC cause=C_TIMER_POPPED origin=crm_timer_popped ]
   Jun 02 17:10:01 [3499] a2.pm.redhat.com       crmd:     info: do_state_transition:	Progressed to state S_POLICY_ENGINE after C_TIMER_POPPED
   Jun 02 17:10:01 [3499] a2.pm.redhat.com       crmd:    debug: do_state_transition:	All 3 cluster nodes are eligible to run resources.
   Jun 02 17:10:01 [3499] a2.pm.redhat.com       crmd:    debug: do_pe_invoke:	Query 100: Requesting the current CIB: S_POLICY_ENGINE
   Jun 02 17:10:01 [3494] a2.pm.redhat.com        cib:    debug: cib_acl_enabled:	CIB ACL is disabled
   Jun 02 17:10:01 [3499] a2.pm.redhat.com       crmd:    debug: do_pe_invoke_callback:	Invoking the PE: query=100, ref=pe_calc-dc-1464855001-57, seq=10672, quorate=1
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:    debug: unpack_config:	STONITH timeout: 60000
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:    debug: unpack_config:	STONITH of failed nodes is enabled
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:    debug: unpack_config:	Stop all active resources: false
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:    debug: unpack_config:	Cluster is symmetric - resources can run anywhere by default
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:    debug: unpack_config:	Default stickiness: 0
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:    debug: unpack_config:	On loss of CCM Quorum: Stop ALL resources
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:    debug: unpack_config:	Node scores: 'red' = -INFINITY, 'yellow' = 0, 'green' = 0
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:     info: determine_online_status_fencing:	Node a1 is active
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:     info: determine_online_status:	Node a1 is online
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:     info: determine_online_status_fencing:	Node a2 is active
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:     info: determine_online_status:	Node a2 is online
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:     info: determine_online_status_fencing:	Node a3 is active
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:     info: determine_online_status:	Node a3 is online
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:     info: native_print:	node1-xvm	(stonith:fence_xvm):	Started a2
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:     info: native_print:	node2-xvm	(stonith:fence_xvm):	Started a1
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:     info: native_print:	node3-xvm	(stonith:fence_xvm):	Started a3
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:     info: group_print:	 Resource Group: WEBSERVICE
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:     info: native_print:	     ClusterIP	(ocf::heartbeat:IPaddr2):	Started a2
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:     info: native_print:	     webserver	(ocf::heartbeat:apache):	Started a2
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a2 to node1-xvm
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a1 to node2-xvm
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a3 to node3-xvm
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a2 to ClusterIP
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a2 to webserver
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:     info: LogActions:	Leave   node1-xvm	(Started a2)
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:     info: LogActions:	Leave   node2-xvm	(Started a1)
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:     info: LogActions:	Leave   node3-xvm	(Started a3)
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:     info: LogActions:	Leave   ClusterIP	(Started a2)
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:     info: LogActions:	Leave   webserver	(Started a2)
   Jun 02 17:10:01 [3498] a2.pm.redhat.com    pengine:   notice: process_pe_message:	Calculated Transition 4: /var/lib/pacemaker/pengine/pe-input-336.bz2
   Jun 02 17:10:01 [3499] a2.pm.redhat.com       crmd:    debug: s_crmd_fsa:	Processing I_PE_SUCCESS: [ state=S_POLICY_ENGINE cause=C_IPC_MESSAGE origin=handle_response ]
   Jun 02 17:10:01 [3499] a2.pm.redhat.com       crmd:     info: do_state_transition:	State transition S_POLICY_ENGINE -> S_TRANSITION_ENGINE [ input=I_PE_SUCCESS cause=C_IPC_MESSAGE origin=handle_response ]
   Jun 02 17:10:01 [3499] a2.pm.redhat.com       crmd:    debug: unpack_graph:	Unpacked transition 4: 0 actions in 0 synapses
   Jun 02 17:10:01 [3499] a2.pm.redhat.com       crmd:     info: do_te_invoke:	Processing graph 4 (ref=pe_calc-dc-1464855001-57) derived from /var/lib/pacemaker/pengine/pe-input-336.bz2
   Jun 02 17:10:01 [3499] a2.pm.redhat.com       crmd:    debug: print_graph:	Empty transition graph
   Jun 02 17:10:01 [3499] a2.pm.redhat.com       crmd:   notice: run_graph:	Transition 4 (Complete=0, Pending=0, Fired=0, Skipped=0, Incomplete=0, Source=/var/lib/pacemaker/pengine/pe-input-336.bz2): Complete
   Jun 02 17:10:01 [3499] a2.pm.redhat.com       crmd:    debug: te_graph_trigger:	Transition 4 is now complete
   Jun 02 17:10:01 [3499] a2.pm.redhat.com       crmd:    debug: notify_crmd:	Processing transition completion in state S_TRANSITION_ENGINE
   Jun 02 17:10:01 [3499] a2.pm.redhat.com       crmd:    debug: notify_crmd:	Transition 4 status: done - <null>
   Jun 02 17:10:01 [3499] a2.pm.redhat.com       crmd:    debug: s_crmd_fsa:	Processing I_TE_SUCCESS: [ state=S_TRANSITION_ENGINE cause=C_FSA_INTERNAL origin=notify_crmd ]
   Jun 02 17:10:01 [3499] a2.pm.redhat.com       crmd:     info: do_log:	FSA: Input I_TE_SUCCESS from notify_crmd() received in state S_TRANSITION_ENGINE
   Jun 02 17:10:01 [3499] a2.pm.redhat.com       crmd:   notice: do_state_transition:	State transition S_TRANSITION_ENGINE -> S_IDLE [ input=I_TE_SUCCESS cause=C_FSA_INTERNAL origin=notify_crmd ]
   Jun 02 17:10:01 [3499] a2.pm.redhat.com       crmd:    debug: do_state_transition:	Starting PEngine Recheck Timer
   Jun 02 17:10:01 [3499] a2.pm.redhat.com       crmd:    debug: crm_timer_start:	Started PEngine Recheck Timer (I_PE_CALC:900000ms), src=130
   Jun 02 17:10:04 [3496] a2.pm.redhat.com       lrmd:    debug: recurring_action_timer:	Scheduling another invocation of webserver_monitor_10000
   Jun 02 17:10:04 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11393 - exited with rc=0
   Jun 02 17:10:04 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11393:stderr [ -- empty -- ]
   Jun 02 17:10:04 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11393:stdout [ -- empty -- ]
   Jun 02 17:10:04 [3496] a2.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:webserver action:monitor call_id:47 pid:11393 exit-code:0 exec-time:0ms queue-time:0ms

Up until here, webserver is running properly. 
::
   
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:    debug: recurring_action_timer:	Scheduling another invocation of webserver_monitor_10000
   apache(webserver)[11437]:	2016/06/02_17:10:14 INFO: apache not running
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11437 - exited with rc=7
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11437:stderr [ -- empty -- ]
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11437:stdout [ -- empty -- ]
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:webserver action:monitor call_id:47 pid:11437 exit-code:7 exec-time:0ms queue-time:0ms

Got exit-code=7 on the last line. This might means, ENOMSG
::

   include/crm/comatibility.h
   103enum lrmd_errors {                                      
   104    lrmd_ok                      =  pcmk_ok,            
   105    lrmd_pending                 = -EINPROGRESS,        
   106    lrmd_err_generic             = -EPROTONOSUPPORT,    
   107    lrmd_err_internal            = -EPROTO,             
   108    lrmd_err_connection          = -ENOTCONN,           
   109    lrmd_err_missing             = -EINVAL,             
   110    lrmd_err_ipc                 = -ECOMM,              
   111    lrmd_err_peer                = -ENOMSG,             
   112    lrmd_err_unknown_operation   = -EOPNOTSUPP,         
   113    lrmd_err_unknown_rsc         = -ENODEV,             
   114    lrmd_err_no_metadata         = -EIO,                
   115    lrmd_err_stonith_connection  = -EUNATCH,            
   116    lrmd_err_provider_required   = -EINVAL,             
   117};   

Back to logs.
::

   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: create_operation_update:	do_update_resource: Updating resource webserver after monitor op complete (interval=10000)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:     info: process_lrm_event:	Operation webserver_monitor_10000: not running (node=a2, call=47, rc=7, cib-update=101, confirmed=false)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: update_history_cache:	Updating history for 'webserver' with monitor op

Now crmd knows that webserver is not running...
::

   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_process_request:	Forwarding cib_modify operation for section status to master (origin=local/crmd/101)
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:    debug: cib_acl_enabled:	CIB ACL is disabled
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	Diff: --- 0.306.124 2
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	Diff: +++ 0.306.125 (null)
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	+  /cib:  @num_updates=125
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	+  /cib/status/node_state[@id='2']:  @crm-debug-origin=do_update_resource
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	++ /cib/status/node_state[@id='2']/lrm[@id='2']/lrm_resources/lrm_resource[@id='webserver']:  <lrm_rsc_op id="webserver_last_failure_0" operation_key="webserver_monitor_10000" operation="monitor" crm-debug-origin="do_update_resource" crm_feature_set="3.0.10" transition-key="18:1:0:42f6807f-d8e5-4674-bd90-69eeaf74a2c7" transition-magic="0:7;18:1:0:42f6807f-d8e5-4674-bd90-69eeaf74a2c7" on_node="a2" call-id="47" rc-code="7" op-statu
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_process_request:	Completed cib_modify operation for section status: OK (rc=0, origin=a2/crmd/101, version=0.306.125)
   Jun 02 17:10:14 [3495] a2.pm.redhat.com stonith-ng:    debug: xml_patch_version_check:	Can apply patch 0.306.125 to 0.306.124
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: te_update_diff:	Processing (cib_modify) diff: 0.306.124 -> 0.306.125 (S_IDLE)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:     info: abort_transition_graph:	Transition aborted by webserver_monitor_10000 'create' on a2: Old event (magic=0:7;18:1:0:42f6807f-d8e5-4674-bd90-69eeaf74a2c7, cib=0.306.125, source=process_graph_event:593, 1)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:     info: update_failcount:	Updating failcount for webserver on a2 after failed monitor: rc=7 (update=value++, time=1464855014)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: attrd_update_delegate:	Sent update: fail-count-webserver=value++ for a2
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: attrd_update_delegate:	Sent update: last-failure-webserver=1464855014 for a2
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:     info: process_graph_event:	Detected action (1.18) webserver_monitor_10000.47=not running: failed
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: s_crmd_fsa:	Processing I_PE_CALC: [ state=S_IDLE cause=C_FSA_INTERNAL origin=abort_transition_graph ]
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:   notice: do_state_transition:	State transition S_IDLE -> S_POLICY_ENGINE [ input=I_PE_CALC cause=C_FSA_INTERNAL origin=abort_transition_graph ]
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: do_state_transition:	All 3 cluster nodes are eligible to run resources.
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: do_pe_invoke:	Query 102: Requesting the current CIB: S_POLICY_ENGINE
   Jun 02 17:10:14 [3497] a2.pm.redhat.com      attrd:     info: attrd_client_update:	Expanded fail-count-webserver=value++ to 1
   Jun 02 17:10:14 [3497] a2.pm.redhat.com      attrd:    debug: attrd_client_update:	Broadcasting fail-count-webserver[a2] = 1 (writer)
   Jun 02 17:10:14 [3497] a2.pm.redhat.com      attrd:    debug: attrd_client_update:	Broadcasting last-failure-webserver[a2] = 1464855014 (writer)
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:    debug: cib_acl_enabled:	CIB ACL is disabled
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: do_pe_invoke_callback:	Invoking the PE: query=102, ref=pe_calc-dc-1464855014-58, seq=10672, quorate=1

crmd asks pengine, our brain, to calculate the situation.
::

   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: unpack_config:	STONITH timeout: 60000
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: unpack_config:	STONITH of failed nodes is enabled
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: unpack_config:	Stop all active resources: false
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: unpack_config:	Cluster is symmetric - resources can run anywhere by default
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: unpack_config:	Default stickiness: 0
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: unpack_config:	On loss of CCM Quorum: Stop ALL resources
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: unpack_config:	Node scores: 'red' = -INFINITY, 'yellow' = 0, 'green' = 0
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: determine_online_status_fencing:	Node a1 is active
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: determine_online_status:	Node a1 is online
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: determine_online_status_fencing:	Node a2 is active
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: determine_online_status:	Node a2 is online
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: determine_online_status_fencing:	Node a3 is active
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: determine_online_status:	Node a3 is online
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: determine_op_status:	webserver_monitor_10000 on a2 returned 'not running' (7) instead of the expected value: 'ok' (0)
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:  warning: unpack_rsc_op_failure:	Processing failed op monitor for webserver on a2: not running (7)
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: native_print:	node1-xvm	(stonith:fence_xvm):	Started a2
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: native_print:	node2-xvm	(stonith:fence_xvm):	Started a1
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: native_print:	node3-xvm	(stonith:fence_xvm):	Started a3
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: group_print:	 Resource Group: WEBSERVICE
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: native_print:	     ClusterIP	(ocf::heartbeat:IPaddr2):	Started a2
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: native_print:	     webserver	(ocf::heartbeat:apache):	FAILED a2
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a2 to node1-xvm
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a1 to node2-xvm
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a3 to node3-xvm
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a2 to ClusterIP
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a2 to webserver
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: RecurringOp:	 Start recurring monitor (10s) for webserver on a2
   Jun 02 17:10:14 [3497] a2.pm.redhat.com      attrd:     info: attrd_peer_update:	Setting fail-count-webserver[a2]: (null) -> 1 from a2
   Jun 02 17:10:14 [3497] a2.pm.redhat.com      attrd:    debug: write_attribute:	Update: a1[fail-count-webserver]=(null) (1 1 1 a1)
   Jun 02 17:10:14 [3497] a2.pm.redhat.com      attrd:    debug: write_attribute:	Update: a2[fail-count-webserver]=1 (2 2 2 a2)
   Jun 02 17:10:14 [3497] a2.pm.redhat.com      attrd:    debug: write_attribute:	Update: a3[fail-count-webserver]=(null) (3 3 3 a3)
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: LogActions:	Leave   node1-xvm	(Started a2)
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: LogActions:	Leave   node2-xvm	(Started a1)
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: LogActions:	Leave   node3-xvm	(Started a3)
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: LogActions:	Leave   ClusterIP	(Started a2)
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:   notice: LogActions:	Recover webserver	(Started a2)
   Jun 02 17:10:14 [3497] a2.pm.redhat.com      attrd:     info: write_attribute:	Sent update 43 with 3 changes for fail-count-webserver, id=<n/a>, set=(null)
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:   notice: process_pe_message:	Calculated Transition 5: /var/lib/pacemaker/pengine/pe-input-337.bz2
   Jun 02 17:10:14 [3497] a2.pm.redhat.com      attrd:     info: attrd_peer_update:	Setting last-failure-webserver[a2]: (null) -> 1464855014 from a2
   Jun 02 17:10:14 [3497] a2.pm.redhat.com      attrd:    debug: write_attribute:	Update: a1[last-failure-webserver]=(null) (1 1 1 a1)
   Jun 02 17:10:14 [3497] a2.pm.redhat.com      attrd:    debug: write_attribute:	Update: a2[last-failure-webserver]=1464855014 (2 2 2 a2)
   Jun 02 17:10:14 [3497] a2.pm.redhat.com      attrd:    debug: write_attribute:	Update: a3[last-failure-webserver]=(null) (3 3 3 a3)
   Jun 02 17:10:14 [3497] a2.pm.redhat.com      attrd:     info: write_attribute:	Sent update 44 with 3 changes for last-failure-webserver, id=<n/a>, set=(null)
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_process_request:	Forwarding cib_modify operation for section status to master (origin=local/attrd/43)
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_process_request:	Forwarding cib_modify operation for section status to master (origin=local/attrd/44)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: s_crmd_fsa:	Processing I_PE_SUCCESS: [ state=S_POLICY_ENGINE cause=C_IPC_MESSAGE origin=handle_response ]
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:     info: do_state_transition:	State transition S_POLICY_ENGINE -> S_TRANSITION_ENGINE [ input=I_PE_SUCCESS cause=C_IPC_MESSAGE origin=handle_response ]
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: unpack_graph:	Unpacked transition 5: 8 actions in 8 synapses
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:     info: do_te_invoke:	Processing graph 5 (ref=pe_calc-dc-1464855014-58) derived from /var/lib/pacemaker/pengine/pe-input-337.bz2
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: te_pseudo_action:	Pseudo action 23 fired and confirmed
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:   notice: te_rsc_command:	Initiating action 4: stop webserver_stop_0 on a2 (local)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: stop_recurring_action_by_rsc:	Cancelling op 47 for webserver (webserver:47)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: cancel_op:	Cancelling op 47 for webserver (webserver:47)
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:     info: cancel_recurring_action:	Cancelling ocf operation webserver_monitor_10000
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:webserver action:monitor call_id:47  exit-code:7 exec-time:0ms queue-time:0ms
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:    debug: process_lrmd_message:	Processed lrmd_rsc_cancel operation from fbbf446c-cffa-49fd-9f9d-93bca81e3fb7: rc=0, reply=1, notify=0, exit=4203648
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:    debug: cib_acl_enabled:	CIB ACL is disabled
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:    debug: cib_process_modify:	Destroying /cib/status/node_state[1]/transient_attributes/instance_attributes/nvpair[3]
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:    debug: cib_process_modify:	Destroying /cib/status/node_state[3]/transient_attributes/instance_attributes/nvpair[3]
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: cancel_op:	Op 47 for webserver (webserver:47): cancelled
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: do_lrm_rsc_op:	Stopped 0 recurring operations in preparation for webserver_stop_0
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:     info: do_lrm_rsc_op:	Performing key=4:5:0:42f6807f-d8e5-4674-bd90-69eeaf74a2c7 op=webserver_stop_0
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:    debug: process_lrmd_message:	Processed lrmd_rsc_exec operation from fbbf446c-cffa-49fd-9f9d-93bca81e3fb7: rc=49, reply=1, notify=0, exit=4203648
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:     info: log_execute:	executing - rsc:webserver action:stop call_id:49
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: run_graph:	Transition 5 (Complete=0, Pending=1, Fired=2, Skipped=0, Incomplete=6, Source=/var/lib/pacemaker/pengine/pe-input-337.bz2): In-progress
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:     info: process_lrm_event:	Operation webserver_monitor_10000: Cancelled (node=a2, call=47, confirmed=true)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: update_history_cache:	Updating history for 'webserver' with monitor op
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: run_graph:	Transition 5 (Complete=1, Pending=1, Fired=0, Skipped=0, Incomplete=6, Source=/var/lib/pacemaker/pengine/pe-input-337.bz2): In-progress
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	Diff: --- 0.306.125 2
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	Diff: +++ 0.306.126 (null)
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	+  /cib:  @num_updates=126
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	++ /cib/status/node_state[@id='2']/transient_attributes[@id='2']/instance_attributes[@id='status-2']:  <nvpair id="status-2-fail-count-webserver" name="fail-count-webserver" value="1"/>
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_process_request:	Completed cib_modify operation for section status: OK (rc=0, origin=a2/attrd/43, version=0.306.126)
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:    debug: cib_acl_enabled:	CIB ACL is disabled
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:    debug: cib_process_modify:	Destroying /cib/status/node_state[1]/transient_attributes/instance_attributes/nvpair[3]
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:    debug: cib_process_modify:	Destroying /cib/status/node_state[3]/transient_attributes/instance_attributes/nvpair[3]
   Jun 02 17:10:14 [3495] a2.pm.redhat.com stonith-ng:    debug: xml_patch_version_check:	Can apply patch 0.306.126 to 0.306.125
   Jun 02 17:10:14 [3497] a2.pm.redhat.com      attrd:     info: attrd_cib_callback:	Update 43 for fail-count-webserver: OK (0)
   Jun 02 17:10:14 [3497] a2.pm.redhat.com      attrd:     info: attrd_cib_callback:	Update 43 for fail-count-webserver[a1]=(null): OK (0)
   Jun 02 17:10:14 [3497] a2.pm.redhat.com      attrd:     info: attrd_cib_callback:	Update 43 for fail-count-webserver[a2]=1: OK (0)
   Jun 02 17:10:14 [3497] a2.pm.redhat.com      attrd:     info: attrd_cib_callback:	Update 43 for fail-count-webserver[a3]=(null): OK (0)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: te_update_diff:	Processing (cib_modify) diff: 0.306.125 -> 0.306.126 (S_TRANSITION_ENGINE)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: update_abort_priority:	Abort priority upgraded from 0 to 1000000
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: update_abort_priority:	Abort action done superseded by restart: Transient attribute change
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:   notice: abort_transition_graph:	Transition aborted by status-2-fail-count-webserver, fail-count-webserver=1: Transient attribute change (create cib=0.306.126, source=abort_unless_down:319, path=/cib/status/node_state[@id='2']/transient_attributes[@id='2']/instance_attributes[@id='status-2'], 0)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: run_graph:	Transition 5 (Complete=1, Pending=1, Fired=0, Skipped=0, Incomplete=6, Source=/var/lib/pacemaker/pengine/pe-input-337.bz2): In-progress
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	Diff: --- 0.306.126 2
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	Diff: +++ 0.306.127 (null)
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	+  /cib:  @num_updates=127
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	++ /cib/status/node_state[@id='2']/transient_attributes[@id='2']/instance_attributes[@id='status-2']:  <nvpair id="status-2-last-failure-webserver" name="last-failure-webserver" value="1464855014"/>
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_process_request:	Completed cib_modify operation for section status: OK (rc=0, origin=a2/attrd/44, version=0.306.127)
   Jun 02 17:10:14 [3495] a2.pm.redhat.com stonith-ng:    debug: xml_patch_version_check:	Can apply patch 0.306.127 to 0.306.126
   Jun 02 17:10:14 [3497] a2.pm.redhat.com      attrd:     info: attrd_cib_callback:	Update 44 for last-failure-webserver: OK (0)
   Jun 02 17:10:14 [3497] a2.pm.redhat.com      attrd:     info: attrd_cib_callback:	Update 44 for last-failure-webserver[a1]=(null): OK (0)
   Jun 02 17:10:14 [3497] a2.pm.redhat.com      attrd:     info: attrd_cib_callback:	Update 44 for last-failure-webserver[a2]=1464855014: OK (0)
   Jun 02 17:10:14 [3497] a2.pm.redhat.com      attrd:     info: attrd_cib_callback:	Update 44 for last-failure-webserver[a3]=(null): OK (0)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: te_update_diff:	Processing (cib_modify) diff: 0.306.126 -> 0.306.127 (S_TRANSITION_ENGINE)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:     info: abort_transition_graph:	Transition aborted by status-2-last-failure-webserver, last-failure-webserver=1464855014: Transient attribute change (create cib=0.306.127, source=abort_unless_down:319, path=/cib/status/node_state[@id='2']/transient_attributes[@id='2']/instance_attributes[@id='status-2'], 0)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: run_graph:	Transition 5 (Complete=1, Pending=1, Fired=0, Skipped=0, Incomplete=6, Source=/var/lib/pacemaker/pengine/pe-input-337.bz2): In-progress
   apache(webserver)[11470]:	2016/06/02_17:10:14 INFO: apache is not running.
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_stop_0:11470 - exited with rc=0
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_stop_0:11470:stderr [ -- empty -- ]
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_stop_0:11470:stdout [ -- empty -- ]
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:     info: log_finished:	finished - rsc:webserver action:stop call_id:49 pid:11470 exit-code:0 exec-time:43ms queue-time:0ms
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: create_operation_update:	do_update_resource: Updating resource webserver after stop op complete (interval=0)
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_process_request:	Forwarding cib_modify operation for section status to master (origin=local/crmd/103)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:   notice: process_lrm_event:	Operation webserver_stop_0: ok (node=a2, call=49, rc=0, cib-update=103, confirmed=true)

Tried to stop webserver, anyway and succeeded.
::

   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: update_history_cache:	Updating history for 'webserver' with stop op
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:    debug: cib_acl_enabled:	CIB ACL is disabled
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	Diff: --- 0.306.127 2
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	Diff: +++ 0.306.128 (null)
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	+  /cib:  @num_updates=128
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	+  /cib/status/node_state[@id='2']/lrm[@id='2']/lrm_resources/lrm_resource[@id='webserver']/lrm_rsc_op[@id='webserver_last_0']:  @operation_key=webserver_stop_0, @operation=stop, @crm-debug-origin=do_update_resource, @transition-key=4:5:0:42f6807f-d8e5-4674-bd90-69eeaf74a2c7, @transition-magic=0:0;4:5:0:42f6807f-d8e5-4674-bd90-69eeaf74a2c7, @call-id=49, @last-run=1464855014, @last-rc-change=1464855014, @exec-time=43
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_process_request:	Completed cib_modify operation for section status: OK (rc=0, origin=a2/crmd/103, version=0.306.128)
   Jun 02 17:10:14 [3495] a2.pm.redhat.com stonith-ng:    debug: xml_patch_version_check:	Can apply patch 0.306.128 to 0.306.127
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: te_update_diff:	Processing (cib_modify) diff: 0.306.127 -> 0.306.128 (S_TRANSITION_ENGINE)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:     info: match_graph_event:	Action webserver_stop_0 (4) confirmed on a2 (rc=0)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: te_pseudo_action:	Pseudo action 24 fired and confirmed
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: te_pseudo_action:	Pseudo action 21 fired and confirmed
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: te_pseudo_action:	Pseudo action 7 fired and confirmed
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: run_graph:	Transition 5 (Complete=2, Pending=0, Fired=3, Skipped=1, Incomplete=3, Source=/var/lib/pacemaker/pengine/pe-input-337.bz2): In-progress
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:   notice: run_graph:	Transition 5 (Complete=5, Pending=0, Fired=0, Skipped=1, Incomplete=3, Source=/var/lib/pacemaker/pengine/pe-input-337.bz2): Stopped
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: te_graph_trigger:	Transition 5 is now complete
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: notify_crmd:	Processing transition completion in state S_TRANSITION_ENGINE
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: notify_crmd:	Transition 5 status: restart - Transient attribute change

Saying "restart", but what is going to be restarted?
::

   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: s_crmd_fsa:	Processing I_PE_CALC: [ state=S_TRANSITION_ENGINE cause=C_FSA_INTERNAL origin=notify_crmd ]
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:     info: do_state_transition:	State transition S_TRANSITION_ENGINE -> S_POLICY_ENGINE [ input=I_PE_CALC cause=C_FSA_INTERNAL origin=notify_crmd ]
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: do_state_transition:	All 3 cluster nodes are eligible to run resources.
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: do_pe_invoke:	Query 104: Requesting the current CIB: S_POLICY_ENGINE
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:    debug: cib_acl_enabled:	CIB ACL is disabled
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: do_pe_invoke_callback:	Invoking the PE: query=104, ref=pe_calc-dc-1464855014-60, seq=10672, quorate=1
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: unpack_config:	STONITH timeout: 60000
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: unpack_config:	STONITH of failed nodes is enabled
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: unpack_config:	Stop all active resources: false
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: unpack_config:	Cluster is symmetric - resources can run anywhere by default
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: unpack_config:	Default stickiness: 0
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: unpack_config:	On loss of CCM Quorum: Stop ALL resources
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: unpack_config:	Node scores: 'red' = -INFINITY, 'yellow' = 0, 'green' = 0
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: determine_online_status_fencing:	Node a1 is active
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: determine_online_status:	Node a1 is online
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: determine_online_status_fencing:	Node a2 is active
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: determine_online_status:	Node a2 is online
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: determine_online_status_fencing:	Node a3 is active
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: determine_online_status:	Node a3 is online
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: determine_op_status:	webserver_monitor_10000 on a2 returned 'not running' (7) instead of the expected value: 'ok' (0)
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:  warning: unpack_rsc_op_failure:	Processing failed op monitor for webserver on a2: not running (7)
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: native_print:	node1-xvm	(stonith:fence_xvm):	Started a2
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: native_print:	node2-xvm	(stonith:fence_xvm):	Started a1
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: native_print:	node3-xvm	(stonith:fence_xvm):	Started a3
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: group_print:	 Resource Group: WEBSERVICE
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: native_print:	     ClusterIP	(ocf::heartbeat:IPaddr2):	Started a2
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: native_print:	     webserver	(ocf::heartbeat:apache):	Stopped
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: get_failcount_full:	webserver has failed 1 times on a2
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: common_apply_stickiness:	webserver can fail 999999 more times on a2 before being forced off
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a2 to node1-xvm
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a1 to node2-xvm
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a3 to node3-xvm
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a2 to ClusterIP
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a2 to webserver
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: RecurringOp:	 Start recurring monitor (10s) for webserver on a2
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: LogActions:	Leave   node1-xvm	(Started a2)
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: LogActions:	Leave   node2-xvm	(Started a1)
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: LogActions:	Leave   node3-xvm	(Started a3)
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:     info: LogActions:	Leave   ClusterIP	(Started a2)
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:   notice: LogActions:	Start   webserver	(a2)
   Jun 02 17:10:14 [3498] a2.pm.redhat.com    pengine:   notice: process_pe_message:	Calculated Transition 6: /var/lib/pacemaker/pengine/pe-input-338.bz2
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: s_crmd_fsa:	Processing I_PE_SUCCESS: [ state=S_POLICY_ENGINE cause=C_IPC_MESSAGE origin=handle_response ]
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:     info: do_state_transition:	State transition S_POLICY_ENGINE -> S_TRANSITION_ENGINE [ input=I_PE_SUCCESS cause=C_IPC_MESSAGE origin=handle_response ]
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: unpack_graph:	Unpacked transition 6: 4 actions in 4 synapses
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:     info: do_te_invoke:	Processing graph 6 (ref=pe_calc-dc-1464855014-60) derived from /var/lib/pacemaker/pengine/pe-input-338.bz2
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: te_pseudo_action:	Pseudo action 20 fired and confirmed
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:   notice: te_rsc_command:	Initiating action 18: start webserver_start_0 on a2 (local)

Here comes "start webserver_start_0 on a2 (local)" !!!
::

   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: do_lrm_rsc_op:	Stopped 0 recurring operations in preparation for webserver_start_0
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:     info: do_lrm_rsc_op:	Performing key=18:6:0:42f6807f-d8e5-4674-bd90-69eeaf74a2c7 op=webserver_start_0
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:    debug: process_lrmd_message:	Processed lrmd_rsc_exec operation from fbbf446c-cffa-49fd-9f9d-93bca81e3fb7: rc=50, reply=1, notify=0, exit=4203648
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:     info: log_execute:	executing - rsc:webserver action:start call_id:50
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: run_graph:	Transition 6 (Complete=0, Pending=1, Fired=2, Skipped=0, Incomplete=2, Source=/var/lib/pacemaker/pengine/pe-input-338.bz2): In-progress
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: run_graph:	Transition 6 (Complete=1, Pending=1, Fired=0, Skipped=0, Incomplete=2, Source=/var/lib/pacemaker/pengine/pe-input-338.bz2): In-progress
   apache(webserver)[11504]:	2016/06/02_17:10:14 INFO: AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using a2.pm.redhat.com. Set the 'ServerName' directive globally to suppress this message
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_start_0:11504 - exited with rc=0
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_start_0:11504:stderr [ -- empty -- ]
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_start_0:11504:stdout [ -- empty -- ]
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:     info: log_finished:	finished - rsc:webserver action:start call_id:50 pid:11504 exit-code:0 exec-time:71ms queue-time:0ms

Succeeded in restarting webserver.
::

   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: create_operation_update:	do_update_resource: Updating resource webserver after start op complete (interval=0)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:     info: action_synced_wait:	Managed apache_meta-data_0 process 11553 exited with rc=0
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:   notice: process_lrm_event:	Operation webserver_start_0: ok (node=a2, call=50, rc=0, cib-update=105, confirmed=true)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: update_history_cache:	Updating history for 'webserver' with start op
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_process_request:	Forwarding cib_modify operation for section status to master (origin=local/crmd/105)
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:    debug: cib_acl_enabled:	CIB ACL is disabled
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	Diff: --- 0.306.128 2
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	Diff: +++ 0.306.129 (null)
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	+  /cib:  @num_updates=129
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	+  /cib/status/node_state[@id='2']/lrm[@id='2']/lrm_resources/lrm_resource[@id='webserver']/lrm_rsc_op[@id='webserver_last_0']:  @operation_key=webserver_start_0, @operation=start, @transition-key=18:6:0:42f6807f-d8e5-4674-bd90-69eeaf74a2c7, @transition-magic=0:0;18:6:0:42f6807f-d8e5-4674-bd90-69eeaf74a2c7, @call-id=50, @exec-time=71
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_process_request:	Completed cib_modify operation for section status: OK (rc=0, origin=a2/crmd/105, version=0.306.129)
   Jun 02 17:10:14 [3495] a2.pm.redhat.com stonith-ng:    debug: xml_patch_version_check:	Can apply patch 0.306.129 to 0.306.128
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: te_update_diff:	Processing (cib_modify) diff: 0.306.128 -> 0.306.129 (S_TRANSITION_ENGINE)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:     info: match_graph_event:	Action webserver_start_0 (18) confirmed on a2 (rc=0)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: te_pseudo_action:	Pseudo action 21 fired and confirmed
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:   notice: te_rsc_command:	Initiating action 19: monitor webserver_monitor_10000 on a2 (local)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:     info: do_lrm_rsc_op:	Performing key=19:6:0:42f6807f-d8e5-4674-bd90-69eeaf74a2c7 op=webserver_monitor_10000
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:    debug: process_lrmd_message:	Processed lrmd_rsc_exec operation from fbbf446c-cffa-49fd-9f9d-93bca81e3fb7: rc=51, reply=1, notify=0, exit=4203648
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:    debug: log_execute:	executing - rsc:webserver action:monitor call_id:51
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: run_graph:	Transition 6 (Complete=2, Pending=1, Fired=2, Skipped=0, Incomplete=0, Source=/var/lib/pacemaker/pengine/pe-input-338.bz2): In-progress
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: run_graph:	Transition 6 (Complete=3, Pending=1, Fired=0, Skipped=0, Incomplete=0, Source=/var/lib/pacemaker/pengine/pe-input-338.bz2): In-progress
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11565 - exited with rc=0
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11565:stderr [ -- empty -- ]
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11565:stdout [ -- empty -- ]
   Jun 02 17:10:14 [3496] a2.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:webserver action:monitor call_id:51 pid:11565 exit-code:0 exec-time:32ms queue-time:0ms
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: create_operation_update:	do_update_resource: Updating resource webserver after monitor op complete (interval=10000)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:     info: process_lrm_event:	Operation webserver_monitor_10000: ok (node=a2, call=51, rc=0, cib-update=106, confirmed=false)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: update_history_cache:	Updating history for 'webserver' with monitor op
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_process_request:	Forwarding cib_modify operation for section status to master (origin=local/crmd/106)
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:    debug: cib_acl_enabled:	CIB ACL is disabled
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	Diff: --- 0.306.129 2
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	Diff: +++ 0.306.130 (null)
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	+  /cib:  @num_updates=130
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_perform_op:	+  /cib/status/node_state[@id='2']/lrm[@id='2']/lrm_resources/lrm_resource[@id='webserver']/lrm_rsc_op[@id='webserver_monitor_10000']:  @crm-debug-origin=do_update_resource, @transition-key=19:6:0:42f6807f-d8e5-4674-bd90-69eeaf74a2c7, @transition-magic=0:0;19:6:0:42f6807f-d8e5-4674-bd90-69eeaf74a2c7, @call-id=51, @last-rc-change=1464855014, @exec-time=32
   Jun 02 17:10:14 [3494] a2.pm.redhat.com        cib:     info: cib_process_request:	Completed cib_modify operation for section status: OK (rc=0, origin=a2/crmd/106, version=0.306.130)
   Jun 02 17:10:14 [3495] a2.pm.redhat.com stonith-ng:    debug: xml_patch_version_check:	Can apply patch 0.306.130 to 0.306.129
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: te_update_diff:	Processing (cib_modify) diff: 0.306.129 -> 0.306.130 (S_TRANSITION_ENGINE)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:     info: match_graph_event:	Action webserver_monitor_10000 (19) confirmed on a2 (rc=0)
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:   notice: run_graph:	Transition 6 (Complete=4, Pending=0, Fired=0, Skipped=0, Incomplete=0, Source=/var/lib/pacemaker/pengine/pe-input-338.bz2): Complete
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: te_graph_trigger:	Transition 6 is now complete
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: notify_crmd:	Processing transition completion in state S_TRANSITION_ENGINE
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: notify_crmd:	Transition 6 status: done - <null>
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: s_crmd_fsa:	Processing I_TE_SUCCESS: [ state=S_TRANSITION_ENGINE cause=C_FSA_INTERNAL origin=notify_crmd ]
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:     info: do_log:	FSA: Input I_TE_SUCCESS from notify_crmd() received in state S_TRANSITION_ENGINE
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:   notice: do_state_transition:	State transition S_TRANSITION_ENGINE -> S_IDLE [ input=I_TE_SUCCESS cause=C_FSA_INTERNAL origin=notify_crmd ]
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: do_state_transition:	Starting PEngine Recheck Timer
   Jun 02 17:10:14 [3499] a2.pm.redhat.com       crmd:    debug: crm_timer_start:	Started PEngine Recheck Timer (I_PE_CALC:900000ms), src=140
   Jun 02 17:10:19 [3494] a2.pm.redhat.com        cib:    debug: cib_acl_enabled:	CIB ACL is disabled
   Jun 02 17:10:19 [3494] a2.pm.redhat.com        cib:     info: cib_process_ping:	Reporting our current digest to a2: c9458dd4a096678343279ba0a7f5e5f6 for 0.306.130 (0x1903a30 0)
   Jun 02 17:10:21 [3499] a2.pm.redhat.com       crmd:    debug: throttle_cib_load:	cib load: 0.000333 (1 ticks in 30s)
   Jun 02 17:10:21 [3499] a2.pm.redhat.com       crmd:    debug: throttle_load_avg:	Current load is 0.080000 (full: 0.08 0.03 0.05 3/134 11602)
   Jun 02 17:10:21 [3499] a2.pm.redhat.com       crmd:    debug: throttle_io_load:	Current IO load is 0.000000
   Jun 02 17:10:21 [3496] a2.pm.redhat.com       lrmd:    debug: recurring_action_timer:	Scheduling another invocation of ClusterIP_monitor_30000
   Jun 02 17:10:21 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	ClusterIP_monitor_30000:11603 - exited with rc=0
   Jun 02 17:10:21 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	ClusterIP_monitor_30000:11603:stderr [ -- empty -- ]
   Jun 02 17:10:21 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	ClusterIP_monitor_30000:11603:stdout [ -- empty -- ]
   Jun 02 17:10:21 [3496] a2.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:ClusterIP action:monitor call_id:45 pid:11603 exit-code:0 exec-time:0ms queue-time:0ms

From here, the recurring monitor action start again.
::

   Jun 02 17:10:24 [3496] a2.pm.redhat.com       lrmd:    debug: recurring_action_timer:	Scheduling another invocation of webserver_monitor_10000
   Jun 02 17:10:24 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11652 - exited with rc=0
   Jun 02 17:10:24 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11652:stderr [ -- empty -- ]
   Jun 02 17:10:24 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11652:stdout [ -- empty -- ]
   Jun 02 17:10:24 [3496] a2.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:webserver action:monitor call_id:51 pid:11652 exit-code:0 exec-time:0ms queue-time:0ms
   Jun 02 17:10:34 [3496] a2.pm.redhat.com       lrmd:    debug: recurring_action_timer:	Scheduling another invocation of webserver_monitor_10000
   Jun 02 17:10:34 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11695 - exited with rc=0
   Jun 02 17:10:34 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11695:stderr [ -- empty -- ]
   Jun 02 17:10:34 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11695:stdout [ -- empty -- ]
   Jun 02 17:10:34 [3496] a2.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:webserver action:monitor call_id:51 pid:11695 exit-code:0 exec-time:0ms queue-time:0ms
   Jun 02 17:10:44 [3496] a2.pm.redhat.com       lrmd:    debug: recurring_action_timer:	Scheduling another invocation of webserver_monitor_10000
   Jun 02 17:10:44 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11742 - exited with rc=0
   Jun 02 17:10:44 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11742:stderr [ -- empty -- ]
   Jun 02 17:10:44 [3496] a2.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:11742:stdout [ -- empty -- ]
   Jun 02 17:10:44 [3496] a2.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:webserver action:monitor call_id:51 pid:11742 exit-code:0 exec-time:0ms queue-time:0ms


Case2: -STOP httpd -> recover with fence-reboot
----------------------------------------------------
This time STOP httpd instead of KILL httpd.
::

   [root@a2 ~]# killall -KILL httpd
      
Here are the logs.
::

   Jun 02 16:53:06 [5853] a3.pm.redhat.com       lrmd:    debug: recurring_action_timer:	Scheduling another invocation of webserver_monitor_10000
   Jun 02 16:53:06 [5853] a3.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:9123 - exited with rc=0
   Jun 02 16:53:06 [5853] a3.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:9123:stderr [ -- empty -- ]
   Jun 02 16:53:06 [5853] a3.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:9123:stdout [ -- empty -- ]
   Jun 02 16:53:06 [5853] a3.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:webserver action:monitor call_id:31 pid:9123 exit-code:0 exec-time:0ms queue-time:0ms
   Jun 02 16:53:15 [5853] a3.pm.redhat.com       lrmd:    debug: recurring_action_timer:	Scheduling another invocation of ClusterIP_monitor_30000
   Jun 02 16:53:15 [5853] a3.pm.redhat.com       lrmd:    debug: operation_finished:	ClusterIP_monitor_30000:9193 - exited with rc=0
   Jun 02 16:53:15 [5853] a3.pm.redhat.com       lrmd:    debug: operation_finished:	ClusterIP_monitor_30000:9193:stderr [ -- empty -- ]
   Jun 02 16:53:15 [5853] a3.pm.redhat.com       lrmd:    debug: operation_finished:	ClusterIP_monitor_30000:9193:stdout [ -- empty -- ]
   Jun 02 16:53:15 [5853] a3.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:ClusterIP action:monitor call_id:29 pid:9193 exit-code:0 exec-time:0ms queue-time:0ms
   Jun 02 16:53:16 [5853] a3.pm.redhat.com       lrmd:    debug: recurring_action_timer:	Scheduling another invocation of webserver_monitor_10000
   Jun 02 16:53:16 [5853] a3.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:9239 - exited with rc=0
   Jun 02 16:53:16 [5853] a3.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:9239:stderr [ -- empty -- ]
   Jun 02 16:53:16 [5853] a3.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:9239:stdout [ -- empty -- ]
   Jun 02 16:53:16 [5853] a3.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:webserver action:monitor call_id:31 pid:9239 exit-code:0 exec-time:0ms queue-time:0ms
   Jun 02 16:53:17 [5853] a3.pm.redhat.com       lrmd:    debug: log_execute:	executing - rsc:node2-xvm action:monitor call_id:25
   Jun 02 16:53:17 [5852] a3.pm.redhat.com stonith-ng:    debug: stonith_command:	Processing st_execute 23 from lrmd.5853 (               0)
   Jun 02 16:53:17 [5852] a3.pm.redhat.com stonith-ng:    debug: schedule_stonith_command:	Scheduling monitor on node2-xvm for b1490462-38e2-4492-98f2-78d7c360ab64 (timeout=20s)
   Jun 02 16:53:17 [5852] a3.pm.redhat.com stonith-ng:    debug: stonith_command:	Processed st_execute from lrmd.5853: Operation now in progress (-115)
   Jun 02 16:53:17 [5852] a3.pm.redhat.com stonith-ng:    debug: stonith_action_create:	Initiating action monitor for agent fence_xvm (target=(null))
   Jun 02 16:53:17 [5852] a3.pm.redhat.com stonith-ng:    debug: internal_stonith_action_execute:	forking
   Jun 02 16:53:17 [5852] a3.pm.redhat.com stonith-ng:    debug: internal_stonith_action_execute:	sending args
   Jun 02 16:53:17 [5852] a3.pm.redhat.com stonith-ng:    debug: stonith_device_execute:	Operation monitor on node2-xvm now running with pid=9274, timeout=20s
   Jun 02 16:53:21 [5856] a3.pm.redhat.com       crmd:    debug: throttle_cib_load:	cib load: 0.000000 (0 ticks in 30s)
   Jun 02 16:53:21 [5856] a3.pm.redhat.com       crmd:    debug: throttle_load_avg:	Current load is 0.000000 (full: 0.00 0.02 0.05 2/135 9274)
   Jun 02 16:53:21 [5856] a3.pm.redhat.com       crmd:    debug: throttle_io_load:	Current IO load is 0.000000
   Jun 02 16:53:22 [5852] a3.pm.redhat.com stonith-ng:    debug: stonith_action_async_done:	Child process 9274 performing action 'monitor' exited with rc 0
   Jun 02 16:53:22 [5852] a3.pm.redhat.com stonith-ng:    debug: st_child_done:	Operation 'monitor' on 'node2-xvm' completed with rc=0 (0 remaining)
   Jun 02 16:53:22 [5852] a3.pm.redhat.com stonith-ng:    debug: log_operation:	Operation 'monitor' [9274] for device 'node2-xvm' returned: 0 (OK)
   Jun 02 16:53:22 [5853] a3.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:node2-xvm action:monitor call_id:25  exit-code:0 exec-time:5025ms queue-time:1ms
   Jun 02 16:53:26 [5853] a3.pm.redhat.com       lrmd:    debug: recurring_action_timer:	Scheduling another invocation of webserver_monitor_10000
   Jun 02 16:53:26 [5853] a3.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:9275 - exited with rc=0
   Jun 02 16:53:26 [5853] a3.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:9275:stderr [ -- empty -- ]
   Jun 02 16:53:26 [5853] a3.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:9275:stdout [ -- empty -- ]
   Jun 02 16:53:26 [5853] a3.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:webserver action:monitor call_id:31 pid:9275 exit-code:0 exec-time:0ms queue-time:0ms
   Jun 02 16:53:36 [5853] a3.pm.redhat.com       lrmd:    debug: recurring_action_timer:	Scheduling another invocation of webserver_monitor_10000
   Jun 02 16:53:45 [5853] a3.pm.redhat.com       lrmd:    debug: recurring_action_timer:	Scheduling another invocation of ClusterIP_monitor_30000
   Jun 02 16:53:45 [5853] a3.pm.redhat.com       lrmd:    debug: child_waitpid:	wait(9312) = 0: Resource temporarily unavailable (11)
   Jun 02 16:53:45 [5853] a3.pm.redhat.com       lrmd:    debug: operation_finished:	ClusterIP_monitor_30000:9355 - exited with rc=0
   Jun 02 16:53:45 [5853] a3.pm.redhat.com       lrmd:    debug: operation_finished:	ClusterIP_monitor_30000:9355:stderr [ -- empty -- ]
   Jun 02 16:53:45 [5853] a3.pm.redhat.com       lrmd:    debug: operation_finished:	ClusterIP_monitor_30000:9355:stdout [ -- empty -- ]
   Jun 02 16:53:45 [5853] a3.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:ClusterIP action:monitor call_id:29 pid:9355 exit-code:0 exec-time:0ms queue-time:0ms
   Jun 02 16:53:51 [5856] a3.pm.redhat.com       crmd:    debug: throttle_cib_load:	cib load: 0.000333 (1 ticks in 30s)
   Jun 02 16:53:51 [5856] a3.pm.redhat.com       crmd:    debug: throttle_load_avg:	Current load is 0.000000 (full: 0.00 0.02 0.05 1/138 9407)
   Jun 02 16:53:51 [5856] a3.pm.redhat.com       crmd:    debug: throttle_io_load:	Current IO load is 0.000000
   Jun 02 16:53:56 [5853] a3.pm.redhat.com       lrmd:    debug: child_kill_helper:	Kill pid 9312's group
   Jun 02 16:53:56 [5853] a3.pm.redhat.com       lrmd:  warning: child_timeout_callback:	webserver_monitor_10000 process (PID 9312) timed out
   Jun 02 16:53:56 [5853] a3.pm.redhat.com       lrmd:  warning: operation_finished:	webserver_monitor_10000:9312 - timed out after 20000ms
   Jun 02 16:53:56 [5853] a3.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:9312:stderr [ -- empty -- ]
   Jun 02 16:53:56 [5853] a3.pm.redhat.com       lrmd:    debug: operation_finished:	webserver_monitor_10000:9312:stdout [ -- empty -- ]
   Jun 02 16:53:56 [5853] a3.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:webserver action:monitor call_id:31 pid:9312 exit-code:1 exec-time:0ms queue-time:0ms
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: create_operation_update:	do_update_resource: Updating resource webserver after monitor op Timed Out (interval=10000)
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    error: process_lrm_event:	Operation webserver_monitor_10000: Timed Out (node=a3, call=31, timeout=20000ms)
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: update_history_cache:	Updating history for 'webserver' with monitor op

Monitoring webserver returned error.
::

   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:     info: cib_process_request:	Forwarding cib_modify operation for section status to master (origin=local/crmd/155)
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:    debug: cib_acl_enabled:	CIB ACL is disabled
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:     info: cib_perform_op:	Diff: --- 0.306.56 2
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:     info: cib_perform_op:	Diff: +++ 0.306.57 (null)
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:     info: cib_perform_op:	+  /cib:  @num_updates=57
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:     info: cib_perform_op:	+  /cib/status/node_state[@id='3']:  @crm-debug-origin=do_update_resource
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:     info: cib_perform_op:	++ /cib/status/node_state[@id='3']/lrm[@id='3']/lrm_resources/lrm_resource[@id='webserver']:  <lrm_rsc_op id="webserver_last_failure_0" operation_key="webserver_monitor_10000" operation="monitor" crm-debug-origin="do_update_resource" crm_feature_set="3.0.10" transition-key="21:15:0:e7d10a75-f5ba-444c-9af4-0c170bcad4df" transition-magic="2:1;21:15:0:e7d10a75-f5ba-444c-9af4-0c170bcad4df" on_node="a3" call-id="31" rc-code="1" op-sta
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:     info: cib_process_request:	Completed cib_modify operation for section status: OK (rc=0, origin=a3/crmd/155, version=0.306.57)
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: te_update_diff:	Processing (cib_modify) diff: 0.306.56 -> 0.306.57 (S_IDLE)
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:     info: abort_transition_graph:	Transition aborted by webserver_monitor_10000 'create' on a3: Old event (magic=2:1;21:15:0:e7d10a75-f5ba-444c-9af4-0c170bcad4df, cib=0.306.57, source=process_graph_event:593, 1)
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:     info: update_failcount:	Updating failcount for webserver on a3 after failed monitor: rc=1 (update=value++, time=1464854036)
   Jun 02 16:53:56 [5852] a3.pm.redhat.com stonith-ng:    debug: xml_patch_version_check:	Can apply patch 0.306.57 to 0.306.56
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: attrd_update_delegate:	Sent update: fail-count-webserver=value++ for a3
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: attrd_update_delegate:	Sent update: last-failure-webserver=1464854036 for a3
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:     info: process_graph_event:	Detected action (15.21) webserver_monitor_10000.31=unknown error: failed
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: s_crmd_fsa:	Processing I_PE_CALC: [ state=S_IDLE cause=C_FSA_INTERNAL origin=abort_transition_graph ]
   Jun 02 16:53:56 [5854] a3.pm.redhat.com      attrd:     info: attrd_client_update:	Expanded fail-count-webserver=value++ to 1
   Jun 02 16:53:56 [5854] a3.pm.redhat.com      attrd:    debug: attrd_client_update:	Broadcasting fail-count-webserver[a3] = 1
   Jun 02 16:53:56 [5854] a3.pm.redhat.com      attrd:    debug: attrd_client_update:	Broadcasting last-failure-webserver[a3] = 1464854036
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:   notice: do_state_transition:	State transition S_IDLE -> S_POLICY_ENGINE [ input=I_PE_CALC cause=C_FSA_INTERNAL origin=abort_transition_graph ]
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: do_state_transition:	All 3 cluster nodes are eligible to run resources.
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: do_pe_invoke:	Query 156: Requesting the current CIB: S_POLICY_ENGINE
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:    debug: cib_acl_enabled:	CIB ACL is disabled
   Jun 02 16:53:56 [5854] a3.pm.redhat.com      attrd:     info: attrd_peer_update:	Setting fail-count-webserver[a3]: (null) -> 1 from a3
   Jun 02 16:53:56 [5854] a3.pm.redhat.com      attrd:     info: attrd_peer_update:	Setting last-failure-webserver[a3]: (null) -> 1464854036 from a3
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: do_pe_invoke_callback:	Invoking the PE: query=156, ref=pe_calc-dc-1464854036-133, seq=10664, quorate=1
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:    debug: cib_acl_enabled:	CIB ACL is disabled
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:    debug: cib_process_modify:	Destroying /cib/status/node_state[1]/transient_attributes/instance_attributes/nvpair[3]
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:     info: cib_perform_op:	Diff: --- 0.306.57 2
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:     info: cib_perform_op:	Diff: +++ 0.306.58 (null)
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:     info: cib_perform_op:	+  /cib:  @num_updates=58
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:     info: cib_perform_op:	++ /cib/status/node_state[@id='3']/transient_attributes[@id='3']/instance_attributes[@id='status-3']:  <nvpair id="status-3-fail-count-webserver" name="fail-count-webserver" value="1"/>
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:     info: cib_process_request:	Completed cib_modify operation for section status: OK (rc=0, origin=a2/attrd/23, version=0.306.58)
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:    debug: cib_acl_enabled:	CIB ACL is disabled
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:    debug: cib_process_modify:	Destroying /cib/status/node_state[1]/transient_attributes/instance_attributes/nvpair[3]
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:     info: cib_perform_op:	Diff: --- 0.306.58 2
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:     info: cib_perform_op:	Diff: +++ 0.306.59 (null)
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:     info: cib_perform_op:	+  /cib:  @num_updates=59
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:     info: cib_perform_op:	++ /cib/status/node_state[@id='3']/transient_attributes[@id='3']/instance_attributes[@id='status-3']:  <nvpair id="status-3-last-failure-webserver" name="last-failure-webserver" value="1464854036"/>
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: unpack_config:	STONITH timeout: 60000
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: te_update_diff:	Processing (cib_modify) diff: 0.306.57 -> 0.306.58 (S_POLICY_ENGINE)
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: unpack_config:	STONITH of failed nodes is enabled
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:     info: abort_transition_graph:	Transition aborted by status-3-fail-count-webserver, fail-count-webserver=1: Transient attribute change (create cib=0.306.58, source=abort_unless_down:319, path=/cib/status/node_state[@id='3']/transient_attributes[@id='3']/instance_attributes[@id='status-3'], 1)
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: unpack_config:	Stop all active resources: false
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: unpack_config:	Cluster is symmetric - resources can run anywhere by default
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: unpack_config:	Default stickiness: 0
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: unpack_config:	On loss of CCM Quorum: Stop ALL resources
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: unpack_config:	Node scores: 'red' = -INFINITY, 'yellow' = 0, 'green' = 0
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: s_crmd_fsa:	Processing I_PE_CALC: [ state=S_POLICY_ENGINE cause=C_FSA_INTERNAL origin=abort_transition_graph ]
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: do_pe_invoke:	Query 157: Requesting the current CIB: S_POLICY_ENGINE
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: determine_online_status_fencing:	Node a1 is active
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: determine_online_status:	Node a1 is online
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: determine_online_status_fencing:	Node a2 is active
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: determine_online_status:	Node a2 is online
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: determine_online_status_fencing:	Node a3 is active
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: determine_online_status:	Node a3 is online
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:     info: cib_process_request:	Completed cib_modify operation for section status: OK (rc=0, origin=a2/attrd/24, version=0.306.59)
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:    debug: cib_acl_enabled:	CIB ACL is disabled
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:  warning: unpack_rsc_op_failure:	Processing failed op monitor for webserver on a3: unknown error (1)
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: native_print:	node1-xvm	(stonith:fence_xvm):	Started a2
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: native_print:	node2-xvm	(stonith:fence_xvm):	Started a3
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: native_print:	node3-xvm	(stonith:fence_xvm):	Started a1
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: group_print:	 Resource Group: WEBSERVICE
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: native_print:	     ClusterIP	(ocf::heartbeat:IPaddr2):	Started a3
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: native_print:	     webserver	(ocf::heartbeat:apache):	FAILED a3
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: te_update_diff:	Processing (cib_modify) diff: 0.306.58 -> 0.306.59 (S_POLICY_ENGINE)
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:     info: abort_transition_graph:	Transition aborted by status-3-last-failure-webserver, last-failure-webserver=1464854036: Transient attribute change (create cib=0.306.59, source=abort_unless_down:319, path=/cib/status/node_state[@id='3']/transient_attributes[@id='3']/instance_attributes[@id='status-3'], 1)
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: s_crmd_fsa:	Processing I_PE_CALC: [ state=S_POLICY_ENGINE cause=C_FSA_INTERNAL origin=abort_transition_graph ]
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a2 to node1-xvm
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a3 to node2-xvm
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a1 to node3-xvm
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a3 to ClusterIP
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a3 to webserver
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: do_pe_invoke:	Query 158: Requesting the current CIB: S_POLICY_ENGINE
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: RecurringOp:	 Start recurring monitor (10s) for webserver on a3
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: LogActions:	Leave   node1-xvm	(Started a2)
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: LogActions:	Leave   node2-xvm	(Started a3)
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: LogActions:	Leave   node3-xvm	(Started a1)
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: LogActions:	Leave   ClusterIP	(Started a3)
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:   notice: LogActions:	Recover webserver	(Started a3)
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:   notice: process_pe_message:	Calculated Transition 18: /var/lib/pacemaker/pengine/pe-input-342.bz2
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:     info: handle_response:	pe_calc calculation pe_calc-dc-1464854036-133 is obsolete
   Jun 02 16:53:56 [5851] a3.pm.redhat.com        cib:    debug: cib_acl_enabled:	CIB ACL is disabled
   Jun 02 16:53:56 [5852] a3.pm.redhat.com stonith-ng:    debug: xml_patch_version_check:	Can apply patch 0.306.58 to 0.306.57
   Jun 02 16:53:56 [5852] a3.pm.redhat.com stonith-ng:    debug: xml_patch_version_check:	Can apply patch 0.306.59 to 0.306.58
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: do_pe_invoke_callback:	Invoking the PE: query=158, ref=pe_calc-dc-1464854036-134, seq=10664, quorate=1
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: unpack_config:	STONITH timeout: 60000
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: unpack_config:	STONITH of failed nodes is enabled
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: unpack_config:	Stop all active resources: false
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: unpack_config:	Cluster is symmetric - resources can run anywhere by default
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: unpack_config:	Default stickiness: 0
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: unpack_config:	On loss of CCM Quorum: Stop ALL resources
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: unpack_config:	Node scores: 'red' = -INFINITY, 'yellow' = 0, 'green' = 0
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: determine_online_status_fencing:	Node a1 is active
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: determine_online_status:	Node a1 is online
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: determine_online_status_fencing:	Node a2 is active
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: determine_online_status:	Node a2 is online
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: determine_online_status_fencing:	Node a3 is active
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: determine_online_status:	Node a3 is online
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:  warning: unpack_rsc_op_failure:	Processing failed op monitor for webserver on a3: unknown error (1)
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: native_print:	node1-xvm	(stonith:fence_xvm):	Started a2
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: native_print:	node2-xvm	(stonith:fence_xvm):	Started a3
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: native_print:	node3-xvm	(stonith:fence_xvm):	Started a1
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: group_print:	 Resource Group: WEBSERVICE
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: native_print:	     ClusterIP	(ocf::heartbeat:IPaddr2):	Started a3
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: native_print:	     webserver	(ocf::heartbeat:apache):	FAILED a3
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: get_failcount_full:	webserver has failed 1 times on a3
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: common_apply_stickiness:	webserver can fail 999999 more times on a3 before being forced off
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a2 to node1-xvm
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a3 to node2-xvm
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a1 to node3-xvm
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a3 to ClusterIP
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:    debug: native_assign_node:	Assigning a3 to webserver
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: RecurringOp:	 Start recurring monitor (10s) for webserver on a3
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: LogActions:	Leave   node1-xvm	(Started a2)
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: LogActions:	Leave   node2-xvm	(Started a3)
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: LogActions:	Leave   node3-xvm	(Started a1)
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:     info: LogActions:	Leave   ClusterIP	(Started a3)
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:   notice: LogActions:	Recover webserver	(Started a3)
   Jun 02 16:53:56 [5855] a3.pm.redhat.com    pengine:   notice: process_pe_message:	Calculated Transition 19: /var/lib/pacemaker/pengine/pe-input-343.bz2
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: s_crmd_fsa:	Processing I_PE_SUCCESS: [ state=S_POLICY_ENGINE cause=C_IPC_MESSAGE origin=handle_response ]
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:     info: do_state_transition:	State transition S_POLICY_ENGINE -> S_TRANSITION_ENGINE [ input=I_PE_SUCCESS cause=C_IPC_MESSAGE origin=handle_response ]
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: unpack_graph:	Unpacked transition 19: 8 actions in 8 synapses
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:     info: do_te_invoke:	Processing graph 19 (ref=pe_calc-dc-1464854036-134) derived from /var/lib/pacemaker/pengine/pe-input-343.bz2
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: te_pseudo_action:	Pseudo action 23 fired and confirmed
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:   notice: te_rsc_command:	Initiating action 6: stop webserver_stop_0 on a3 (local)

Tried to stop webserver.
::

   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: stop_recurring_action_by_rsc:	Cancelling op 31 for webserver (webserver:31)
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: cancel_op:	Cancelling op 31 for webserver (webserver:31)
   Jun 02 16:53:56 [5853] a3.pm.redhat.com       lrmd:     info: cancel_recurring_action:	Cancelling ocf operation webserver_monitor_10000
   Jun 02 16:53:56 [5853] a3.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:webserver action:monitor call_id:31  exit-code:1 exec-time:0ms queue-time:0ms
   Jun 02 16:53:56 [5853] a3.pm.redhat.com       lrmd:    debug: process_lrmd_message:	Processed lrmd_rsc_cancel operation from 147ad94a-3be8-4079-ae61-325a8e89f9ba: rc=0, reply=1, notify=0, exit=4203648
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: cancel_op:	Op 31 for webserver (webserver:31): cancelled
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: do_lrm_rsc_op:	Stopped 0 recurring operations in preparation for webserver_stop_0
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:     info: do_lrm_rsc_op:	Performing key=6:19:0:e7d10a75-f5ba-444c-9af4-0c170bcad4df op=webserver_stop_0
   Jun 02 16:53:56 [5853] a3.pm.redhat.com       lrmd:    debug: process_lrmd_message:	Processed lrmd_rsc_exec operation from 147ad94a-3be8-4079-ae61-325a8e89f9ba: rc=33, reply=1, notify=0, exit=4203648
   Jun 02 16:53:56 [5853] a3.pm.redhat.com       lrmd:     info: log_execute:	executing - rsc:webserver action:stop call_id:33
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: run_graph:	Transition 19 (Complete=0, Pending=1, Fired=2, Skipped=0, Incomplete=6, Source=/var/lib/pacemaker/pengine/pe-input-343.bz2): In-progress
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:     info: process_lrm_event:	Operation webserver_monitor_10000: Cancelled (node=a3, call=31, confirmed=true)
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: update_history_cache:	Updating history for 'webserver' with monitor op
   Jun 02 16:53:56 [5856] a3.pm.redhat.com       crmd:    debug: run_graph:	Transition 19 (Complete=1, Pending=1, Fired=0, Skipped=0, Incomplete=6, Source=/var/lib/pacemaker/pengine/pe-input-343.bz2): In-progress
   apache(webserver)[9420]:	2016/06/02_16:53:56 INFO: Attempting graceful stop of apache PID 6404
   Jun 02 16:54:01 [5851] a3.pm.redhat.com        cib:    debug: cib_acl_enabled:	CIB ACL is disabled
   Jun 02 16:54:01 [5851] a3.pm.redhat.com        cib:     info: cib_process_ping:	Reporting our current digest to a3: 56e6b5e2c41c502903935288ab4c48dd for 0.306.59 (0x2818ac0 0)
   Jun 02 16:54:15 [5853] a3.pm.redhat.com       lrmd:    debug: recurring_action_timer:	Scheduling another invocation of ClusterIP_monitor_30000
   Jun 02 16:54:15 [5853] a3.pm.redhat.com       lrmd:    debug: child_waitpid:	wait(9420) = 0: Resource temporarily unavailable (11)
   Jun 02 16:54:15 [5853] a3.pm.redhat.com       lrmd:    debug: operation_finished:	ClusterIP_monitor_30000:9520 - exited with rc=0
   Jun 02 16:54:15 [5853] a3.pm.redhat.com       lrmd:    debug: operation_finished:	ClusterIP_monitor_30000:9520:stderr [ -- empty -- ]
   Jun 02 16:54:15 [5853] a3.pm.redhat.com       lrmd:    debug: operation_finished:	ClusterIP_monitor_30000:9520:stdout [ -- empty -- ]
   Jun 02 16:54:15 [5853] a3.pm.redhat.com       lrmd:    debug: log_finished:	finished - rsc:ClusterIP action:monitor call_id:29 pid:9520 exit-code:0 exec-time:0ms queue-time:0ms
   Jun 02 16:54:21 [5856] a3.pm.redhat.com       crmd:    debug: throttle_cib_load:	cib load: 0.000333 (1 ticks in 30s)
   Jun 02 16:54:21 [5856] a3.pm.redhat.com       crmd:    debug: throttle_load_avg:	Current load is 0.000000 (full: 0.00 0.02 0.05 2/136 9584)
   Jun 02 16:54:21 [5856] a3.pm.redhat.com       crmd:    debug: throttle_io_load:	Current IO load is 0.000000
   Jun 02 16:54:22 [5853] a3.pm.redhat.com       lrmd:    debug: log_execute:	executing - rsc:node2-xvm action:monitor call_id:25
   Jun 02 16:54:22 [5852] a3.pm.redhat.com stonith-ng:    debug: stonith_command:	Processing st_execute 24 from lrmd.5853 (               0)
   Jun 02 16:54:22 [5852] a3.pm.redhat.com stonith-ng:    debug: schedule_stonith_command:	Scheduling monitor on node2-xvm for b1490462-38e2-4492-98f2-78d7c360ab64 (timeout=20s)
   Jun 02 16:54:22 [5852] a3.pm.redhat.com stonith-ng:    debug: stonith_command:	Processed st_execute from lrmd.5853: Operation now in progress (-115)
   Jun 02 16:54:22 [5852] a3.pm.redhat.com stonith-ng:    debug: stonith_action_create:	Initiating action monitor for agent fence_xvm (target=(null))
   Jun 02 16:54:22 [5852] a3.pm.redhat.com stonith-ng:    debug: internal_stonith_action_execute:	forking
   Jun 02 16:54:22 [5852] a3.pm.redhat.com stonith-ng:    debug: internal_stonith_action_execute:	sending args
   Jun 02 16:54:22 [5852] a3.pm.redhat.com stonith-ng:    debug: stonith_device_execute:	Operation monitor on node2-xvm now running with pid=9585, timeout=20s
