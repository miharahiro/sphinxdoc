=============================
Fencing test
=============================

1. Testing fence agent 

   - List ::
     
       [root@a4 ~]# fence_xvm -o list -dd
       -- args @ 0x7fff2c2f8c50 --
       args->domain = (null)
       args->op = 6
       args->mode = 0
       args->debug = 2
       args->timeout = 30
       args->delay = 0
       args->retr_time = 20
       args->flags = 0
       args->net.addr = 225.0.0.12
       args->net.ipaddr = (null)
       args->net.key_file = /etc/cluster/fence_xvm.key
       args->net.port = 1229
       args->net.hash = 2
       args->net.auth = 2
       args->net.family = 2
       args->net.ifindex = 0
       args->serial.device = (null)
       args->serial.speed = 115200,8N1
       args->serial.address = 10.0.2.179
       -- end args --
       TCP Exchange + Authentication done... 
       pm_a1                            30ebed9e-0a9b-439b-81ac-197b4bc280c1 on
       pm_a2                            7f8b0d79-e3df-4252-994e-40e2c2284035 on
       pm_a3                            7a135be9-5e64-4f04-942b-a6def2819aa2 on
       pm_a4                            61553413-78ff-4ff9-8fa5-7814ad4f1a48 on

   - Reboot ::

       [root@a4 ~]# fence_xvm -o reboot -H pm_a1 -dd
       -- args @ 0x7ffc2f3519d0 --
       args->domain = pm_a1
       args->op = 2
       args->mode = 0
       args->debug = 2
       args->timeout = 30
       args->delay = 0
       args->retr_time = 20
       args->flags = 0
       args->net.addr = 225.0.0.12
       args->net.ipaddr = (null)
       args->net.key_file = /etc/cluster/fence_xvm.key
       args->net.port = 1229
       args->net.hash = 2
       args->net.auth = 2
       args->net.family = 2
       args->net.ifindex = 0
       args->serial.device = (null)
       args->serial.speed = 115200,8N1
       args->serial.address = 10.0.2.179
       -- end args --
       TCP Exchange + Authentication done... 

       
2. Fencing caused by events

   Events like follows cause the host being fenced / rebooted.
   
   - Disable high-availability communications ::

       [root@a2 ~]# firewall-cmd --remove-service=high-availability 

   - Stop NIC ::

       [root@a2 ~]# ifdown eth0

   - Suspend monitored processes (httpd) ::

       [root@a2 ~]# killall -STOP httpd

     If you try -KILL instead of -STOP, httpd processes will be re-started and the host is not fenced.
     
