==================================
Cluster Resources
==================================

What is a Cluster Resource
------------------------------

- quote from clusterlabs
  http://clusterlabs.org/doc/en-US/Pacemaker/1.0/html/Pacemaker_Explained/ch-resources.html#s-resource-intro

::
  
  The role of a resource agent is to abstract the service it provides and present a consistent view to the cluster, which allows the cluster to be agnostic about the resources it manages. The cluster doesn't need to understand how the resource works because it relies on the resource agent to do the right thing when given a start, stop or monitor command.

Resource behavior 
-----------------------------

is implemented in scripts under /usr/lib/ocf/resource.d/ . For example, the implementation of IPaddr2 is in /usr/lib/ocf/resource.d/heartbeat/IPaddr2 . Some of the parameters are defined in CIB. The followings are configs related to Virtual-IP. ::

        <primitive class="ocf" id="VIP" provider="heartbeat" type="IPaddr2">
          <instance_attributes id="VIP-instance_attributes">
            <nvpair id="VIP-instance_attributes-ip" name="ip" value="192.168.110.120"/>
            <nvpair id="VIP-instance_attributes-cidr_netmask" name="cidr_netmask" value="24"/>
          </instance_attributes>
          <operations>
            <op id="VIP-start-interval-0s" interval="0s" name="start" timeout="20s"/>
            <op id="VIP-stop-interval-0s" interval="0s" name="stop" timeout="20s"/>
            <op id="VIP-monitor-interval-30s" interval="30s" name="monitor"/>
          </operations>
        </primitive>

