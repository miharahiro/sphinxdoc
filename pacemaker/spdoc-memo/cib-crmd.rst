====================================
cib - crmd
====================================

- crmd内でcibとの接続は、fsa_cib_connで表現されている。

- crmdからcibへの問い合わせは多岐に渡っているが、fsa_cib_conn->cmds->... で実施されており、そのAPIは、下記で実装されている。
::

   include/crm/cib.h:85:typedef struct cib_api_operations_s

   85typedef struct cib_api_operations_s {
   86    int (*signon) (cib_t * cib, const char *name, enum cib_conn_type type);
   87    int (*signon_raw) (cib_t * cib, const char *name, enum cib_conn_type type, int *event_fd);
   88    int (*signoff) (cib_t * cib);
   89    int (*free) (cib_t * cib);
   90
   91    int (*set_op_callback) (cib_t * cib, void (*callback) (const xmlNode * msg, int callid,
   92                                                           int rc, xmlNode * output));
   93
   94    int (*add_notify_callback) (cib_t * cib, const char *event,
   95                                void (*callback) (const char *event, xmlNode * msg));
   96
   97    int (*del_notify_callback) (cib_t * cib, const char *event,
   98                                void (*callback) (const char *event, xmlNode * msg));
   .
   .
   .

crmdからcibにipc経由で送信されたリクエストは、cib内ではcib_common_callback()で処理される。
::

   cib/callbacks.c
   247cib_common_callback(qb_ipcs_connection_t * c, void *data, size_t size, gboolean privileged)
   248{
   249    uint32_t id = 0;
   250    uint32_t flags = 0;
   251    int call_options = 0;
   252    crm_client_t *cib_client = crm_client_get(c);
   253    xmlNode *op_request = crm_ipcs_recv(cib_client, data, size, &id, &flags);

最終的には、cib/callbacks.cの247cib_common_callback() -> 914cib_process_request() -> 1165cib_process_command()で処理される。

  

	    
