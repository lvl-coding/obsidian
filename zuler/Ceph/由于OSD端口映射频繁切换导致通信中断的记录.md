mon节点信息可正常访问
```bash
$ ceph --cluster zz59 mon stat
e3: 3 mons at {qshc01=[v2:172.16.126.11:3300/0,v1:172.16.126.11:6789/0],qshc02=[v2:172.16.126.12:3300/0,v1:172.16.126.12:6789/0],qshc03=[v2:172.16.126.13:3300/0,v1:172.16.126.13:6789/0]}, election epoch 94, leader 0 qshc01, quorum 0,1,2 qshc01,qshc02,qshc03
```
osd 模块信息交互错误，极少情况下能够偶现成功
```bash
$ ceph --cluster zz59 rbd perf image stats
2025-06-09T03:44:47.758+0000 7f8701046700 -1 --2- 60.188.123.152:0/2423682416 >> [v2:172.16.126.12:6880/2015646848,v1:172.16.126.12:6881/2015646848] conn(0x7f86e808ede0 0x7f86e8091220 unknown :-1 s=BANNER_CONNECTING pgs=0 cs=0 l=1 rev1=0 rx=0 tx=0)._handle_peer_banner peer [v2:172.16.126.12:6880/2015646848,v1:172.16.126.12:6881/2015646848] is using msgr V1 protocol
2025-06-09T03:45:11.922+0000 7f8701046700 -1 --2- 60.188.123.152:0/2423682416 >> [v2:172.16.126.12:6880/2015646848,v1:172.16.126.12:6881/2015646848] conn(0x7f86e808ede0 0x7f86e8091220 unknown :-1 s=BANNER_CONNECTING pgs=0 cs=0 l=1 rev1=0 rx=0 tx=0)._handle_peer_banner peer [v2:172.16.126.12:6880/2015646848,v1:172.16.126.12:6881/2015646848] is using msgr V1 protocol
```
rbd命令hang住不返回，极少情况下能够偶现成功
```bash
$ rbd --cluster zz59 list volumes
```
上海192.168.3.2-4的存储集群未开防火墙，访问郑州启硕集群中的OSD模块同样不成功。
抓包对比，接收端与服务端报文一致，无报文缺失或被拦截
日志记录
```log
 319384 2025-06-09T08:40:55.406+0000 7f81fca47700 10 mon.qshc01@0(leader) e3 handle_auth_request con 0x5643ff56a000 (more) method 2 payload 36
 319385 2025-06-09T08:40:55.406+0000 7f81fca47700 10 mon.qshc01@0(leader) e3 ms_handle_authentication session 0x5643f9814b40 con 0x5643ff56a000 addr - MonSession(unknown.0  is open , features 0x0 (unknown))
 319386 2025-06-09T08:40:55.418+0000 7f81f7a3d700 10 mon.qshc01@0(leader) e3 handle_auth_request con 0x56441f4dd800 (start) method 2 payload 23
 319387 2025-06-09T08:40:55.418+0000 7f81f7a3d700 10 mon.qshc01@0(leader).auth v27123 _assign_global_id 207701775 (max 207944096)
 319388 2025-06-09T08:40:55.418+0000 7f81f7a3d700 10 mon.qshc01@0(leader) e3 handle_auth_request con 0x56441f4dd800 (more) method 2 payload 36
 319389 2025-06-09T08:40:55.418+0000 7f81f7a3d700 10 mon.qshc01@0(leader) e3 ms_handle_authentication session 0x5643f5895d40 con 0x56441f4dd800 addr - MonSession(unknown.0  is open , features 0x0 (unknown))
 319390 2025-06-09T08:40:55.418+0000 7f81f8a3f700 10 mon.qshc01@0(leader) e3 ms_handle_reset 0x56441f4dd800 -
 319391 2025-06-09T08:40:55.418+0000 7f81f8a3f700 10 mon.qshc01@0(leader) e3 reset/close on session unknown.0
 319392 2025-06-09T08:40:55.422+0000 7f81f7a3d700 10 mon.qshc01@0(leader) e3 handle_auth_request con 0x56441f4dd800 (start) method 2 payload 23
 319393 2025-06-09T08:40:55.422+0000 7f81f7a3d700 10 mon.qshc01@0(leader).auth v27123 _assign_global_id 207701778 (max 207944096)
 319394 2025-06-09T08:40:55.422+0000 7f81f7a3d700 10 mon.qshc01@0(leader) e3 handle_auth_request con 0x56441f4dd800 (more) method 2 payload 36
 319395 2025-06-09T08:40:55.422+0000 7f81f7a3d700 10 mon.qshc01@0(leader) e3 ms_handle_authentication session 0x5643f1096000 con 0x56441f4dd800 addr - MonSession(unknown.0  is open , features 0x0 (unknown))
 319396 2025-06-09T08:40:55.422+0000 7f81f8a3f700 10 mon.qshc01@0(leader) e3 ms_handle_reset 0x56441f4dd800 -
 319397 2025-06-09T08:40:55.422+0000 7f81f8a3f700 10 mon.qshc01@0(leader) e3 reset/close on session unknown.0
 319398 2025-06-09T08:40:55.426+0000 7f81f8a3f700 10 mon.qshc01@0(leader) e3 ms_handle_accept con 0x5643ff56a000 session 0x5643f9814b40 registering session for 60.188.123.152:0/3876206180
 319399 2025-06-09T08:40:55.450+0000 7f81f8a3f700 20 mon.qshc01@0(leader) e3 _ms_dispatch existing session 0x5643f9814b40 for client.?
 319400 2025-06-09T08:40:55.450+0000 7f81f8a3f700 20 mon.qshc01@0(leader) e3  entity_name client.admin global_id 207701772 (new_ok) caps allow *
 319401 2025-06-09T08:40:55.450+0000 7f81f8a3f700 10 mon.qshc01@0(leader) e3 handle_mon_get_map
 319402 2025-06-09T08:40:55.450+0000 7f81f8a3f700 20 mon.qshc01@0(leader) e3 _ms_dispatch existing session 0x5643f9814b40 for client.?
 319403 2025-06-09T08:40:55.450+0000 7f81f8a3f700 20 mon.qshc01@0(leader) e3  entity_name client.admin global_id 207701772 (new_ok) caps allow *
 319404 2025-06-09T08:40:55.450+0000 7f81f8a3f700 10 mon.qshc01@0(leader) e3 handle_subscribe mon_subscribe({config=0+,monmap=0+}) v3
 319405 2025-06-09T08:40:55.450+0000 7f81f8a3f700 10 mon.qshc01@0(leader).config check_sub next 0 have 89485
 319406 2025-06-09T08:40:55.450+0000 7f81f8a3f700 10 mon.qshc01@0(leader).config refresh_config crush_location for remote_host vswsercer1 is {}
 319407 2025-06-09T08:40:55.450+0000 7f81f8a3f700 20 mon.qshc01@0(leader).config refresh_config client.admin crush {} device_class
 319408 2025-06-09T08:40:55.450+0000 7f81f8a3f700 10 mon.qshc01@0(leader).config maybe_send_config to client.? (changed)
 319409 2025-06-09T08:40:55.450+0000 7f81f8a3f700 10 mon.qshc01@0(leader).config send_config to client.?
 319410 2025-06-09T08:40:55.450+0000 7f81f8a3f700 10 mon.qshc01@0(leader).monmap v3 check_sub monmap next 0 have 3
 319411 2025-06-09T08:40:55.466+0000 7f81f8a3f700 20 mon.qshc01@0(leader) e3 _ms_dispatch existing session 0x564410ab4d80 for client.?
 319412 2025-06-09T08:40:55.466+0000 7f81f8a3f700 20 mon.qshc01@0(leader) e3  entity_name client.cinder global_id 207701769 (new_ok) caps profile rbd
 319413 2025-06-09T08:40:55.466+0000 7f81f8a3f700  0 mon.qshc01@0(leader) e3 handle_command mon_command({"prefix": "df", "format": "json"} v 0) v1
```
```log
 319490 2025-06-09T08:40:55.478+0000 7f81f8a3f700 10 mon.qshc01@0(leader) e3 ms_handle_reset 0x5643ff56a000 60.188.123.152:0/3876206180
 319491 2025-06-09T08:40:55.478+0000 7f81f8a3f700 10 mon.qshc01@0(leader) e3 reset/close on session client.? 60.188.123.152:0/3876206180
 319492 2025-06-09T08:40:55.478+0000 7f81f8a3f700 10 mon.qshc01@0(leader) e3 remove_session 0x5643f9814b40 client.? 60.188.123.152:0/3876206180 features 0x3f01cfb9fffdffff
```