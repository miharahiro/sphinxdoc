======================================
Active/Active setup - VIP
======================================

Reference : 

http://clusterlabs.org/doc/en-US/Pacemaker/1.1/html/Clusters_from_Scratch/_clone_the_ip_address.html

Setup procedures
---------------------------

- After adding VIP and webserver resouces, they are running on different hosts. ::

    [root@a2 ~]# pcs resource 
    VIP	(ocf::heartbeat:IPaddr2):	Started a1
    webserver	(ocf::heartbeat:apache):	Started a3

- Collocate VIP and webserver resources to make them run on the same host ::

    [root@a2 ~]# pcs constraint colocation add webserver with VIP INFINITY

  Then they are running on the same host ::
    
    [root@a2 ~]# pcs resource 
    VIP	(ocf::heartbeat:IPaddr2):	Started a1
    webserver	(ocf::heartbeat:apache):	Started a1

- If both services need specific order in starting services, ::

    [root@a2 ~]# pcs constraint order VIP then webserver
    Adding VIP webserver (kind: Mandatory) (Options: first-action=start then-action=start)
    
- Clone VIP ::

    [root@a2 ~]# pcs cluster cib loadbalance_cfg
    [root@a2 ~]# pcs -f loadbalance_cfg resource clone VIP clone-max=4 clone-node-max=4 globally-unique=true

    [root@a2 ~]# pcs -f loadbalance_cfg resource update VIP clusterip_hash=sourceip

    [root@a2 ~]# pcs cluster cib-push loadbalance_cfg
    CIB updated

- Clone webserver ::

    [root@a2 ~]# pcs cluster cib active_cfg
    [root@a2 ~]# pcs -f active_cfg resource clone webserver
    [root@a2 ~]# pcs -f active_cfg constraint
    Location Constraints:
    Ordering Constraints:
    Colocation Constraints:
    webserver-clone with VIP-clone (score:INFINITY)
    [root@a2 ~]# pcs cluster cib-push active_cfg
    CIB updated[root@a2 ~]# pcs cluster cib-push active_cfg
    CIB updated

  Then both VIP and webserver are effective on all the hosts ::

    [root@a2 ~]# pcs resource 
    Clone Set: VIP-clone [VIP] (unique)
     VIP:0	(ocf::heartbeat:IPaddr2):	Started a1
     VIP:1	(ocf::heartbeat:IPaddr2):	Started a2
     VIP:2	(ocf::heartbeat:IPaddr2):	Started a3
     VIP:3	(ocf::heartbeat:IPaddr2):	Started a4
     Clone Set: webserver-clone [webserver]
     Started: [ a1 a2 a3 a4 ]


VIP mechanisms
-------------------------------------

- All VIP have the same MAC address

  Utilize arping (need to install net-tools) to look up MAC address of the VIP (192.168.110.120) ::

    [root@a1 ~]# arping 192.168.110.120 -c 1
    ARPING 192.168.110.120 from 192.168.110.121 eth0
    Unicast reply from 192.168.110.120 [21:37:6F:88:C2:A0]  0.850ms
    Unicast reply from 192.168.110.120 [21:37:6F:88:C2:A0]  1.271ms
    Unicast reply from 192.168.110.120 [21:37:6F:88:C2:A0]  1.643ms
    Sent 1 probes (1 broadcast(s))
    Received 3 response(s)

  host a2, a3, and a4 replied to the arping request from host a1. They all have the same MAC address.

- What happens when the http requests are sent to the VIP?

  As all the VIPs on the hosts have the same MAC address, the requests are sent to all hosts with VIP. Only one of the hosts (a4 in the following case) replies to the request. According to the documentations of Pacemaker, the host to reply is calculated by the hash value created by the IP address and port of the client sending the request. 
  
  host a4, which receives the request and replies to it ::

    [root@a4 ~]# tcpdump 'tcp port 80'
    tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
    listening on eth0, link-type EN10MB (Ethernet), capture size 65535 bytes


    11:20:58.940199 IP gw.pm.redhat.com.33382 > a4.pm.redhat.com.http: Flags [S], seq 1112197014, win 29200, options [mss 1460,sackOK,TS val 4019272 ecr 0,nop,wscale 7], length 0
    11:20:58.940286 IP a4.pm.redhat.com.http > gw.pm.redhat.com.33382: Flags [S.], seq 1671459417, ack 1112197015, win 28960, options [mss 1460,sackOK,TS val 3884336 ecr 4019272,nop,wscale 7], length 0
    11:20:58.941460 IP gw.pm.redhat.com.33382 > a4.pm.redhat.com.http: Flags [.], ack 1, win 229, options [nop,nop,TS val 4019273 ecr 3884336], length 0
    11:20:58.941637 IP gw.pm.redhat.com.33382 > a4.pm.redhat.com.http: Flags [P.], seq 1:80, ack 1, win 229, options [nop,nop,TS val 4019273 ecr 3884336], length 79
    11:20:58.941700 IP a4.pm.redhat.com.http > gw.pm.redhat.com.33382: Flags [.], ack 80, win 227, options [nop,nop,TS val 3884337 ecr 4019273], length 0
    11:20:58.941951 IP a4.pm.redhat.com.http > gw.pm.redhat.com.33382: Flags [P.], seq 1:298, ack 80, win 227, options [nop,nop,TS val 3884338 ecr 4019273], length 297
    11:20:58.942042 IP gw.pm.redhat.com.33382 > a4.pm.redhat.com.http: Flags [.], ack 298, win 237, options [nop,nop,TS val 4019274 ecr 3884338], length 0
    11:20:58.942176 IP gw.pm.redhat.com.33382 > a4.pm.redhat.com.http: Flags [F.], seq 80, ack 298, win 237, options [nop,nop,TS val 4019274 ecr 3884338], length 0
    11:20:58.942242 IP a4.pm.redhat.com.http > gw.pm.redhat.com.33382: Flags [F.], seq 298, ack 81, win 227, options [nop,nop,TS val 3884338 ecr 4019274], length 0
    11:20:58.942369 IP gw.pm.redhat.com.33382 > a4.pm.redhat.com.http: Flags [.], ack 299, win 237, options [nop,nop,TS val 4019274 ecr 3884338], length 0

  other hosts a1, a2, and a3, which receive the request but don't reply ::

    [root@a1 ~]# tcpdump 'tcp port 80'
    [ 4151.180262] device eth0 entered promiscuous mode
    tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
    listening on eth0, link-type EN10MB (Ethernet), capture size 65535 bytes


    11:20:59.412011 IP gw.pm.redhat.com.33382 > a1.pm.redhat.com.http: Flags [S], seq 1112197014, win 29200, options [mss 1460,sackOK,TS val 4019272 ecr 0,nop,wscale 7], length 0
    11:20:59.412908 IP gw.pm.redhat.com.33382 > a1.pm.redhat.com.http: Flags [.], ack 1671459418, win 229, options [nop,nop,TS val 4019273 ecr 3884336], length 0
    11:20:59.413261 IP gw.pm.redhat.com.33382 > a1.pm.redhat.com.http: Flags [P.], seq 0:79, ack 1, win 229, options [nop,nop,TS val 4019273 ecr 3884336], length 79
    11:20:59.413637 IP gw.pm.redhat.com.33382 > a1.pm.redhat.com.http: Flags [.], ack 298, win 237, options [nop,nop,TS val 4019274 ecr 3884338], length 0
    11:20:59.413663 IP gw.pm.redhat.com.33382 > a1.pm.redhat.com.http: Flags [F.], seq 79, ack 298, win 237, options [nop,nop,TS val 4019274 ecr 3884338], length 0
    11:20:59.413859 IP gw.pm.redhat.com.33382 > a1.pm.redhat.com.http: Flags [.], ack 299, win 237, options [nop,nop,TS val 4019274 ecr 3884338], length 0
