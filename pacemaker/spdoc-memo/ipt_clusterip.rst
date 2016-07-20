=====================================================
ClusterIP / iptables mechanisms
=====================================================

In Active/Active mode, all the nodes in a cluster has the same IP address, namely the clusterIP, and the same MAC address. Requests to the clusterIP are forwarded to all nodes but the only one node replies to the requests. This behavior is achieved by the extension of iptables. Let's look into source codes of iptables and kernel where this functionality is defined/implemented.

Finding the files containing the key word, "CLUSTER" within the iptables source codes,
::

   [mihara@wlan-197-60 iptables-1.4.21]$ pwd
   /run/media/mihara/disk/sources/i/iptables/^rhel-server-7-2-source-iso/pre-build/iptables-1.4.21

   [mihara@wlan-197-60 iptables-1.4.21]$ grep -lr CLUSTER *
   extensions/libipt_CLUSTERIP.c
   extensions/libxt_cluster.c
   include/linux/netfilter/xt_cluster.h
   include/linux/netfilter_ipv4/ipt_CLUSTERIP.h
   tests/options-ipv4.rules

Looking into one of the file (include/linux/netfilter_ipv4/ipt_CLUSTERIP.h),
::
   
   20struct ipt_clusterip_tgt_info {
   21
   22        __u32 flags;
   23
   24        /* only relevant for new ones */
   25        __u8 clustermac[6];
   26        __u16 num_total_nodes;
   27        __u16 num_local_nodes;
   28        __u16 local_nodes[CLUSTERIP_MAX_NODES];
   29        __u32 hash_mode;
   30        __u32 hash_initval;
   31
   32        /* Used internally by the kernel */
   33        struct clusterip_config *config;
   34};

Let's find clusterip_config in kernel source coeds.
::

   1Directory /run/media/mihara/disk/sources/k/kernel/3.10.0-327.3.1.el7--srpm/pre-buil\
    d/kernel-3.10.0-327.3.1.el7/linux-3.10.0-327.3.1.el7.x86_64/
    
   ./net/ipv4/netfilter/ipt_CLUSTERIP.c:280:clusterip_responsible(const struct cluste\
      rip_config *config, u_int32_t hash)

clusterip_responsible in /net/ipv4/netfilter/ipt_CLUSTERIP.c:280 seems promising ...
::
   280clusterip_responsible(const struct clusterip_config *config, u_int32_t hash)
   281{
   282        return test_bit(hash - 1, &config->local_nodes);
   283}

The function clusterip_responsible is called in the same file within clusterip_tg function.
::
   290clusterip_tg(struct sk_buff *skb, const struct xt_action_param *par)
   291{
   .
   .
   .
   338        if (!clusterip_responsible(cipinfo->config, hash)) {
   339                pr_debug("not responsible\n");
   340                return NF_DROP;
   341        }
   342        pr_debug("responsible\n");
   343
   344        /* despite being received via linklayer multicast, this is
   345         * actually a unicast IP packet. TCP doesn't like PACKET_MULTICAST */
   346        skb->pkt_type = PACKET_HOST;
   347
   348        return XT_CONTINUE;

It's very likely that the function "clusterip_responsible" compares the hash value (probably calculated according to hash_mode) and the local_node's ID and determines whether the node should respond or not.

clusterip_tg is a member of the struct clusterip_tg_reg
::

   440static struct xt_target clusterip_tg_reg __read_mostly = {
   441        .name           = "CLUSTERIP",
   442        .family         = NFPROTO_IPV4,
   443        .target         = clusterip_tg,
   444        .checkentry     = clusterip_tg_check,
   445        .destroy        = clusterip_tg_destroy,
   446        .targetsize     = sizeof(struct ipt_clusterip_tgt_info),
   447#ifdef CONFIG_COMPAT
   448        .compatsize     = sizeof(struct compat_ipt_clusterip_tgt_info),
   449#endif /* CONFIG_COMPAT */
   450        .me             = THIS_MODULE
   451};

clusterip_tg_reg is loaded by xt_register_target in  __init clusterip_tg_init(void)
::

   701static int __init clusterip_tg_init(void)
   702{
   703        int ret;
   704
   705        ret = xt_register_target(&clusterip_tg_reg);
   706        if (ret < 0)
   707                return ret;
   708
   709        ret = nf_register_hook(&cip_arp_ops);
   710        if (ret < 0)
   711                goto cleanup_target;
   712
   713#ifdef CONFIG_PROC_FS
   714        clusterip_procdir = proc_mkdir("ipt_CLUSTERIP", init_net.proc_net);
   715        if (!clusterip_procdir) {
   716                pr_err("Unable to proc dir entry\n");
   717                ret = -ENOMEM;
   718                goto cleanup_hook;
   719        }
   720#endif /* CONFIG_PROC_FS */
   721
   722        pr_info("ClusterIP Version %s loaded successfully\n",
   723                CLUSTERIP_VERSION);
   724        return 0;
   725
   726#ifdef CONFIG_PROC_FS
   727cleanup_hook:
   728        nf_unregister_hook(&cip_arp_ops);
   729#endif /* CONFIG_PROC_FS */
   730cleanup_target:
   731        xt_unregister_target(&clusterip_tg_reg);
   732        return ret;
   733}

line#722 above, "ClusterIP Version %s loaded successfully\n" can be found in /var/log/messages of the cluster node like follows, 
::

   May 19 10:22:24 a1 kernel: ipt_CLUSTERIP: ClusterIP Version 0.8 loaded successfully
   
