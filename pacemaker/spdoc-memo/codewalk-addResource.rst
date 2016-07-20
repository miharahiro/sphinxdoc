======================================
Code Walk / Adding resources
======================================

Add webserver resource like follows.
::

   [root@a1 ~]# pcs resource create webserver apache statusurl="http://localhost"

Log (/var/log/cluster/corosync.log)

.. include:: ../data/corosync-addresource-debug.log
	     :literal:

Pickup a log as an entry point,
::

   May 30 16:52:19 [2451] a1.pm.redhat.com       crmd: (te_actions.c:422   )  notice: te_rsc_command:	Initiating action 11: monitor webserver_monitor_0 on a3

The above log comes from
::

   crmd/te_actions.c:420:    crm_notice("Initiating action %d: %s %s on %s%s%s", 

                     424:    cmd = create_request(CRM_OP_INVOKE_LRM, rsc_op, router_node,                   |
		     425:                         CRM_SYSTEM_LRMD, CRM_SYSTEM_TENGINE, NULL);

		     444:        do_lrm_invoke(A_LRM_INVOKE, C_FSA_INTERNAL, fsa_state, I_NULL, &msg);

