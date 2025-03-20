ceph config dump 查看配置参数  
ceph config set修改参数后可能未生效 #question  
ceph dashboard set-rgw-api-port * 修改dashboard管理rgw服务的端口  
ceph orch apply rgw 会重新启动其他进程，并将原有的进程推出