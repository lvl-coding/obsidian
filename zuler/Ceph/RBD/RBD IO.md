[【Rados Block Device】三、Client内核RBD驱动分析－设备映射流程](https://rootw.github.io/2018/01/RBD-client-2/)
![[Pasted image 20250529112945.png]]
# RBD映射
rbd工具通过map子命令映射产生一个可使用的内核块设备，其本质原理是由rbd工具往sysfs(“/sys/bus/rbd/add”)内核文件接口中写入待创建块设备的信息(例如前文实例写入的内容为”9.22.115.154:6789 name=amdin,key=client, wbpool wb -“，其中9.22.115.154为主monitor的IP)，内核在处理这个写入请求时会调用由RBD驱动事先注册好的处理函数来生成一个内核块设备对象。
## RBD创建client连接
`rbd_get_client[*] \-rbd_client_create |-ceph_create_client | |-ceph_messenger_init | |-ceph_monc_init | \-ceph_osdc_init \_ceph_open_session |-ceph_monc_open_session | \-__open_session | |-ceph_con_open | |-ceph_auth_build_hello | \-__send_prepared_auth_request \-wait_event_interruptible_timeout`

```
rbd_get_client[*]
\-rbd_client_create
	|-ceph_create_client
	|  |-ceph_messenger_init
	|  |-ceph_monc_init
	|  \-ceph_osdc_init
	\_ceph_open_session
		|-ceph_monc_open_session
		|  \-__open_session
		|    |-ceph_con_open
		|    |-ceph_auth_build_hello
		|    \-__send_prepared_auth_request
		\-wait_event_interruptible_timeout
```
# mon与librbd客户端
![[Pasted image 20250529153953.png]]