# 网络配置
RBD image跨集群同步需要目标端集群能够访问源端集群，访问的途径为ceph.conf，通过特定集群名对应的conf文件，比如cephA.conf或cephB.conf，重命名拷贝过来的ceph.conf即可
```ini
# minimal ceph.conf for 2a4e1392-0abd-11f0-95f6-e7d0a414706b
[global]
        fsid = 2a4e1392-0abd-11f0-95f6-e7d0a414706b
        mon_host = [v2:<mon1-ip>:3300/0,v1:<mon1-ip>:6789/0] [v2:<mon2-ip>:3300/0,v1:<mon2-ip>:6789/0] [v2:<mon3-ip>:3300/0,v1:<mon3-ip>:6789/0]
```
通信的依赖为集群的fsid，和mon的ip及端口号，目标端集群在通过ceph.conf配置能够访问源集群的mon的mon节点。
## ipvs转发
当源端集群和目标端集群分别在不同的内网时，则需要通过公网ip映射的方式使目标集群通过ceph.conf内的mon ip信息访问源集群，可通过ipvs做ip映射转发实现，在目标端将源端的mon ip映射为其所在公网ip，在源端将其公网ip转发为目标端ip。所需转发端口有三类
 1. mon的v2版本ip端口，即3300
 2. mon的v1版本ip端口，即6789，当前版本已不适用，可忽略
 3. RBD mirror数据传输的端口，为一个区间内按需自动分配，从6800到7300
目标段ipvs配置说明
- 设置ipvs服务的流量转发持久化，此参数为特定客户端的流量在制定时间内转发给同一服务端
- 需要转发的端口有3300、6789和6800-7300，因此在允许的情况直接做全端口转发，比如将流向192.168.12.2 ip的流量转发至58.220.67.199 ip中
- 目标集群中的RBD mirror服务需要访问源端集群的所有节点，因此需在目标集群的每个节点都设置所有源端集群的ip到源端所在公网ip的转发
```bash
$ ipvsadm -A -t <源集群mon1-ip>:0 -p 86400 -s rr
$ ipvsadm -a -t <源集群mon1-ip>:0 -r <源集群节点1公网ip>:0 -m
$ ipvsadm -A -t <源集群mon2-ip>:0 -p 86400 -s rr
$ ipvsadm -a -t <源集群mon2-ip>:0 -r <源集群节点2公网ip>:0 -m
$ ipvsadm -A -t <源集群mon3-ip>:0 -p 86400 -s rr
$ ipvsadm -a -t <源集群mon3-ip>:0 -r <源集群节点3公网ip>:0 -m
$ ipvsadm -L -n
```
- RBD迁移完成后需将添加的ipvs服务配置删除
```bash
$ ipvsadm -L -n
$ ipvsadm -D -t <源集群mon1-ip>:0
$ ipvsadm -D -t <源集群mon2-ip>:0
$ ipvsadm -D -t <源集群mon3-ip>:0
$ ipvsadm -L -n
```
源端目标配置说明
- 设置ipvs服务的流量转发持久化，此参数为特定客户端的流量在制定时间内转发给同一服务端
- 需要转发的端口有3300、6789和6800-7300，因此在允许的情况直接做全端口转发，比如将流向58.220.67.200 ip的流量转发至192.168.12.3:0 ip中
- 目标集群中的RBD mirror服务需要访问源端集群的所有节点，因此需在源集群的每个节点都设置自身公网的ip到自身mon ip的转发
- RBD迁移完成后需将添加的ipvs服务配置删除
```bash
$ ipvsadm -A -t <源集群节点公网ip>:0 -p 86400 -s rr
$ ipvsadm -A -t <源集群节点公网ip>:0 -r <源集群mon-ip>:0 -m
$ ipvsadm -a -t <源集群节点公网ip>:0 -r <源集群mon-ip>:0 -m
$ ipvsadm -L -n

$ ipvsadm -D -t <源集群节点公网ip>:0
$ ipvsadm -L -n
```
## iptables转发
ipvs转发也可切换为使用iptables转发，按需调整

> [!important]
> 端口转发必须为一一对应，即对应转发端口需长期稳定，否则导致集群间通信中断

```bash
$ for i in {6800..7300};do iptables -t nat -A OUTPUT -d <源集群mon1-ip> -p tcp  --dport $i -j DNAT --to-destination  <源集群节点1公网ip>:$((i+10000));done
$ for i in {6800..7300};do iptables -t nat -A OUTPUT -d <源集群mon2-ip> -p tcp  --dport $i -j DNAT --to-destination  <源集群节点2公网ip>:$((i+20000));done
$ for i in {6800..7300};do iptables -t nat -A OUTPUT -d <源集群mon3-ip> -p tcp  --dport $i -j DNAT --to-destination  <源集群节点3公网ip>:$((i+30000));done
```
## 防火墙配置
源端和目标端均需开启上述三类端口的防火墙规则，源端需针对目标端所在公网ip开启对应端口，目标端需针对源端所在公网ip开启对应端口
```bash
$ ufw allow from <目标集群外访公网ip> to any port 6800:7300 proto tcp
$ ufw allow from <目标集群外访公网ip> to any port 3300
```
## 配置完成确认
mon访问正常
```bash
$ ceph --cluster site-remote mon stat
e3: 3 mons at {vswsercer1=[v2:172.16.208.251:3300/0,v1:172.16.208.251:6789/0],vswsercer2=[v2:172.16.208.252:3300/0,v1:172.16.208.252:6789/0],vswsercer3=[v2:172.16.208.253:3300/0,v1:172.16.208.253:6789/0]}, election epoch 16, leader 0 vswsercer1, quorum 0,1,2 vswsercer1,vswsercer2,vswsercer3
```
RBD访问正常
```
$ rbd list volumes --cluster site-src
volume-0992e622-2d98-4fea-9145-6d595ab684a2
volume-1075a66f-fe33-4757-a2cd-5abc900b5385
volume-16e4407e-fe96-493a-ae26-7c3c0f7e5d97
volume-19727084-964f-4ea2-9bc7-5719e55fef17
volume-1ea7d776-ac15-4db3-ae26-ccab87b8c5fe
volume-21d6c1f8-e0d7-41c8-b72d-e6f174ba0f53
volume-238dc0dd-9786-46ab-a9f7-f9ad78f8eb75
volume-3bdfdcc4-33f7-4ade-be47-3d82ea3e4924
```
# 迁移方案操作步骤
- [ ] 检查是否有同名的池，rbd mirror需使用同名的pool进行image同步
pool池镜像功能分为两种image与pool：
- image模式下则需手动针对每个img使能镜像同步
- pool模式下存储池所有开journaling特性的img都会自动启用镜像同步
这里选择pool模式，方便迁移池中的多个image
```bash
$ rbd mirror pool enable --site-name site-src volumes pool //源端
$ rbd mirror pool enable --site-name site-dst volumes pool //目标端
```
建立本端与对端集群的peer关联，这里采用引导式的方法，避免了配置文件向cephadm docker内的映射和拷贝操作
源端
```bash
$ rbd mirror pool peer bootstrap create --site-name site-src volumes
eyJmc2lkIjoiOTIzMzFhMDUtNWZjOC00ZjZiLTk5YjgtM2Q4Y2Y5MGYwYjY5IiwiY2xpZW50X2lkIjoicmJkLW1pcnJvci1wZWVyIiwia2V5IjoiQVFBNkJPMW5reGl3TWhBQXkySlljd0NPNzRCVnNpa3dlRktrV3c9PSIsIm1vbl9ob3N0IjoiW3YyOjE5Mi4xNjguNy4xMDozMzAwLzAsdjE6MTkyLjE2OC43LjEwOjY3ODkvMF0ifQ==
```
目标端
```bash
$ cat <<EOF > token
eyJmc2lkIjoiOWY1MjgyZGItYjg5OS00NTk2LTgwOTgtMzIwYzFmYzM5NmYzIiwiY2xpZW50X2lkIjoicmJkLW1pcnJvci1wZWVyIiwia2V5IjoiQVFBUnczOWQwdkhvQmhBQVlMM1I4RmR5dHNJQU50bkFTZ0lOTVE9PSIsIm1vbl9ob3N0IjoiW3YyOjE5Mi4xNjguMS4zOjY4MjAsdjE6MTkyLjE2OC4xLjM6NjgyMV0ifQ==
EOF
$ rbd mirror pool peer bootstrap import --site-name site-dst volumes token
```
查看peer建立信息，源端和目标端均可查看
```bash
$ rbd mirror pool info volumes
Mode: pool
Site Name: site-src

Peer Sites:

UUID: f65724c7-7d92-4b45-829d-5b5b210d509a
Name: site-3
Mirror UUID: e4a5a544-31e1-46da-a37c-818c768f03cc
Direction: rx-tx
Client: client.rbd-mirror-peer

$ rbd mirror pool info volumes
Mode: pool
Site Name: site-dst

Peer Sites:

UUID: 7f6b0c3d-db9b-42fa-8bb3-0ea011e2778e
Name: site-149
Mirror UUID: 5c428b96-be36-4e54-ad2a-3abc23c55575
Direction: rx-tx
Client: client.rbd-mirror-peer

```
由于RBD的分层机制，数据卷，快照，克隆默认下采用写时复制，RBD image同步基于快照的克隆卷时，需把父卷先同步至源端
查看父卷信息，parent字段及为父卷及其快照信息
```bash
$ rbd info volumes/volume-84273017-9891-48ce-82f0-33fb71620eb9
rbd image 'volume-84273017-9891-48ce-82f0-33fb71620eb9':
	size 300 GiB in 38400 objects
	order 23 (8 MiB objects)
	snapshot_count: 1
	id: 08e7146a9a53e5
	block_name_prefix: rbd_data.08e7146a9a53e5
	format: 2
	features: layering, exclusive-lock, object-map, fast-diff, deep-flatten, journaling
	op_features:
	flags:
	create_timestamp: Wed Jun  4 12:15:57 2025
	access_timestamp: Tue Jun 10 12:59:59 2025
	modify_timestamp: Tue Jun 10 12:45:09 2025
	parent: volumes/volume-16e4407e-fe96-493a-ae26-7c3c0f7e5d97@snapshot-4e5eda8b-7332-4326-a761-c389015c6277
	overlap: 300 GiB
	journal: 08e7146a9a53e5
	mirroring state: enabled
	mirroring mode: journal
	mirroring global id: 5df3838f-45d9-4e2b-acb6-6cb3e3c0557e
	mirroring primary: true
```
image克隆可能存在嵌套，因此需确认父image信息，image克隆存在嵌套时从最初始的image开始同步
```bash
$ rbd info volumes/volume-16e4407e-fe96-493a-ae26-7c3c0f7e5d97
rbd image 'volume-16e4407e-fe96-493a-ae26-7c3c0f7e5d97':
	size 300 GiB in 38400 objects
	order 23 (8 MiB objects)
	snapshot_count: 1
	id: c3cb5d35f7cbd4
	block_name_prefix: rbd_data.c3cb5d35f7cbd4
	format: 2
	features: layering, exclusive-lock, object-map, fast-diff, deep-flatten, journaling
	op_features:
	flags:
	create_timestamp: Mon Feb 17 07:31:27 2025
	access_timestamp: Tue Jun 10 12:53:49 2025
	modify_timestamp: Mon Feb 17 07:31:27 2025
	parent: images/047ccbe6-675d-48d3-8d61-9d9ae2b60c7d@snap
	overlap: 300 GiB
	journal: c3cb5d35f7cbd4
	mirroring state: enabled
	mirroring mode: journal
	mirroring global id: 825c17f7-a1ea-4041-a5d4-f97f88023419
	mirroring primary: true
$ rbd info images/047ccbe6-675d-48d3-8d61-9d9ae2b60c7d
rbd image '047ccbe6-675d-48d3-8d61-9d9ae2b60c7d':
	size 300 GiB in 38400 objects
	order 23 (8 MiB objects)
	snapshot_count: 1
	id: c3c701b2d4ec94
	block_name_prefix: rbd_data.c3c701b2d4ec94
	format: 2
	features: layering, exclusive-lock, object-map, fast-diff, deep-flatten, journaling
	op_features:
	flags:
	create_timestamp: Mon Feb 17 07:29:26 2025
	access_timestamp: Tue Jun 10 12:53:49 2025
	modify_timestamp: Mon Feb 17 07:30:27 2025
	journal: c3c701b2d4ec94
	mirroring state: enabled
	mirroring mode: journal
	mirroring global id: 34fe1421-762b-42c4-960d-91025163f5b0
	mirroring primary: true
```
其嵌套父卷为images池的卷，因此需针对images设置镜像同步，操作步骤同上
由于images仅有一个卷需同步，因此可针对images启用image模式
```bash
$ rbd mirror pool enable --site-name site-src images image //源端
$ rbd mirror pool enable --site-name site-dst images image //目标端
```
使用引导模式建立peer信息，生成token与volumes一致
```bash
$ rbd mirror pool peer bootstrap create --site-name site-src images
eyJmc2lkIjoiOTIzMzFhMDUtNWZjOC00ZjZiLTk5YjgtM2Q4Y2Y5MGYwYjY5IiwiY2xpZW50X2lkIjoicmJkLW1pcnJvci1wZWVyIiwia2V5IjoiQVFBNkJPMW5reGl3TWhBQXkySlljd0NPNzRCVnNpa3dlRktrV3c9PSIsIm1vbl9ob3N0IjoiW3YyOjE5Mi4xNjguNy4xMDozMzAwLzAsdjE6MTkyLjE2OC43LjEwOjY3ODkvMF0ifQ==
```
可复用目标端的token文件
```bash
$ rbd mirror pool peer bootstrap import --site-name site-dst images token
```
确认池的peer信息
```bash
$ rbd mirror pool info images
Mode: image
Site Name: site-src

Peer Sites:

UUID: 727763fe-1955-4249-9b3d-0b96827b9856
Name: site-59
Mirror UUID:
Direction: rx-tx
Client: client.rbd-mirror-peer
```
启用目标image的journaling特性，该特性为rbd开启mirror时的必需。
在存储池开启了pool模式的功能时，rbd image开启journaling特性时会自动使能镜像同步
当存储池的镜像为image模式时，rbd image除开启journaling特性外，还需使能mirror操作才会开始同步
```bash
$ rbd feature enable volumes/volume-img journaling
$ rbd mirror image enable volumes/volume-img
```
查看image同步状态
```bash
$ rbd mirror image status volumes/volume-img
```
在目标端启动同步进程
在目标端集群启动rbd-mirror守护进程前，需要创建rbd-mirror使用的同步用户
```bash
$ ceph auth get-or-create client.rbd-mirror-peer mon 'profile rbd-mirror-peer' osd 'profile rbd'
```
在目标端集群启动rbd-mirror守护进程
```bash
$ ceph orch apply rbd-mirror
```
查看同步进度，可观察replay的流量大小，由于源设备一直被使用，因此同步状态持续为replaying回放日志，当bytes_per_second的流量不大和entries_behind_primary基本为0时，表示基本同步完成。当需求镜像完全同步，则需求上述两个数值全部为0
```bash
$ rbd mirror image status volumes/volume-84273017-9891-48ce-82f0-33fb71620eb9
volume-84273017-9891-48ce-82f0-33fb71620eb9:
  global_id:   5df3838f-45d9-4e2b-acb6-6cb3e3c0557e
  state:       up+replaying
  description: replaying, {"bytes_per_second":0.0,"entries_behind_primary":0,"entries_per_second":0.0,"non_primary_position":{"entry_tid":3,"object_number":31,"tag_tid":2},"primary_position":{"entry_tid":3,"object_number":31,"tag_tid":2}}
  service:     vswsercer1.qvocbo on vswsercer1
  last_update: 2025-06-11 02:22:53
```
当image为克隆卷时，bootstrapping的进度条不准确，可通过image使用大小对比查看进度
源端
```bash
$ rbd du images/227a3a14-2cdf-45b3-9dfe-8444dbfb3687
NAME                                                                                                                        PROVISIONED  USED
227a3a14-2cdf-45b3-9dfe-8444dbfb3687@snap                                                                                       300 GiB  58 GiB
227a3a14-2cdf-45b3-9dfe-8444dbfb3687@.rbd-mirror.25b0ef26-02e8-472e-95b4-328b1f601d10.bd833eed-8dbe-4b32-9549-8999eb686853      300 GiB     0 B
227a3a14-2cdf-45b3-9dfe-8444dbfb3687                                                                                            300 GiB     0 B
<TOTAL>                                                                                                                         300 GiB  58 GiB
```
目标端
```bash
$ rbd du images/227a3a14-2cdf-45b3-9dfe-8444dbfb3687
NAME                                                                                                                        PROVISIONED  USED
227a3a14-2cdf-45b3-9dfe-8444dbfb3687@snap                                                                                       300 GiB  18 GiB
227a3a14-2cdf-45b3-9dfe-8444dbfb3687@.rbd-mirror.25b0ef26-02e8-472e-95b4-328b1f601d10.bd833eed-8dbe-4b32-9549-8999eb686853      300 GiB     0 B
227a3a14-2cdf-45b3-9dfe-8444dbfb3687                                                                                            300 GiB     0 B
<TOTAL>
```
# 切换主备
当image同步完成后，可通过切换主备的方式将备份到目标端的image启用，但切换主备会导致源集群中的image变为只读。若需始终保持源集群中image保持在线则跳过源端降级为备的操作，将目标端提升为主时追加--force参数
首先在源端将image降级为备
```bash
$ rbd mirror image demote volumes/volume-84273017-9891-48ce-82f0-33fb71620eb9
Image demoted to non-primary
$ rbd mirror image status volumes/volume-84273017-9891-48ce-82f0-33fb71620eb9
volume-84273017-9891-48ce-82f0-33fb71620eb9:
  global_id:   5df3838f-45d9-4e2b-acb6-6cb3e3c0557e
  state:       down+unknown
  description: status not found
  last_update:
  peer_sites:
    name: site-149
    state: up+stopped
    description: local image is primary
    last_update: 2025-06-11 08:29:54
```
在目标端将同步过来的备image提升为主，则可在目标端对image进行访问
```bash
$ rbd mirror image promote volumes/volume-84273017-9891-48ce-82f0-33fb71620eb9
Image promoted to primary
$ rbd mirror image status volumes/volume-84273017-9891-48ce-82f0-33fb71620eb9
volume-84273017-9891-48ce-82f0-33fb71620eb9:
  global_id:   5df3838f-45d9-4e2b-acb6-6cb3e3c0557e
  state:       up+stopped
  description: local image is primary
  service:     vswsercer1.qvocbo on vswsercer1
  last_update: 2025-06-11 08:29:54
```
停止同步镜像主测的journaling特性
```bash
$ rbd feature disable volumes/volume-img journaling
```
提升目标端同步的image为主
```bash
$ rbd mirror image promote volumes/volume-img
```
停止同步镜像源端的journaling特性
```bash
$ rbd feature disable volumes/volume-img journaling
```
# 停止同步并清理镜像配置
当image迁移完成后，仅剩迁移image所需的父image保留在同步状态，需把依赖的父image在目标集群中提升为主完成主备切换的操作后再做最后的进程和配置清理
停止目标端rbd-mirror守护进程，停止进程后镜像同步即暂停。
```bash
$ ceph orch rm rbd-mirror
```
删除存储的peer信息，源端和目标端都需进行
```bash
$ rbd mirror pool info volumes
Mode: pool
Site Name: site-src

Peer Sites:

UUID: f65724c7-7d92-4b45-829d-5b5b210d509a
Name: site-3
Mirror UUID: e4a5a544-31e1-46da-a37c-818c768f03cc
Direction: rx-tx
Client: client.rbd-mirror-peer
$ rbd mirror pool peer remove volumes f65724c7-7d92-4b45-829d-5b5b210d509a
$ rbd mirror pool info volumes
Mode: pool
Site Name: site-src

Peer Sites: none
```
关闭源端和目标端存储池的镜像功能
```bash
$ rbd mirror pool disable volumes
```
# 覆盖新集群中的image
虚拟机跨OpenStack集群迁移，因此新集群中创建的虚拟机使用的rbd设备名会变更，需手动将同步过来的旧虚拟机的rbd设备覆盖新虚拟机使用的rbd设备，同一个存储池中的RBD可采用删除新image设备后，rename修改image名字
```bash
$ rbd rename volumes/volume-img volumes/volume-img-new
```
## 集群内的跨池rbd迁移
迁移预准备，目标image必须是虚拟机已经创建并使用的rbd镜像，可使用预操作确认
```bash
$ rbd migration prepare volumes/volume-a6a03256-a5c4-4da1-90e8-7f6f0e6fcff7 hdd_volume/volume-83b7aef5-4a9f-42b9-83c4-56a66e6bb811
rbd: preparing migration failed: (17) File exists
```
删除虚拟机创建出的image，将迁移过来的rbd块设备重命名或迁移为虚拟机创建出的image，达到数据覆盖的效果
```bash
$ rbd rm hdd_volume/volume-83b7aef5-4a9f-42b9-83c4-56a66e6bb811
Removing image: 100% complete...done.
```
预迁移完成后，可查看确认目标image的状态
```bash
$ rbd migration prepare volumes/volume-a6a03256-a5c4-4da1-90e8-7f6f0e6fcff7 hdd_volume/volume-83b7aef5-4a9f-42b9-83c4-56a66e6bb811
$ rbd status hdd_volume/volume-83b7aef5-4a9f-42b9-83c4-56a66e6bb811
Watchers: none
Migration:
source: volumes/volume-a6a03256-a5c4-4da1-90e8-7f6f0e6fcff7 (2b0213e84df47)
destination: hdd_volume/volume-83b7aef5-4a9f-42b9-83c4-56a66e6bb811 (2a61b305a95ef)
state: prepared
```
执行迁移操作
```bash
$ rbd migration execute hdd_volume/volume-83b7aef5-4a9f-42b9-83c4-56a66e6bb811
Image migration: 100% complete...done.
```
迁移完成后执行确认操作，确认操作完成后自动会将源image删除
```bash
$ rbd migration commit hdd_volume/volume-ac2bf1d5-c6cb-43bd-8d0a-ef07c8e4780f
Commit image migration: 100% complete...done.
```

由于父快照不存在导致同步出错
```
391 2025-06-10T07:07:13.397+0000 7fb1a51b3700 15 rbd::mirror::ImageReplayer: 0x558c6b68a500 [5/f816b4f2-f29c-453d-89fc-b99a4854ed9c] set_mirror_image_status_update: status={state=up+starting_replay, description=starting replay, last_update=0.000000]}
392 2025-06-10T07:07:13.397+0000 7fb1a51b3700 15 rbd::mirror::MirrorStatusUpdater 0x558c6c43b9e0 set_mirror_image_status: global_image_id=f816b4f2-f29c-453d-89fc-b99a4854ed9c, mirror_image_site_status={state=up+starting_replay, description=starting replay, last_update=0.000000]}
393 2025-06-10T07:07:13.397+0000 7fb1a51b3700 15 rbd::mirror::MirrorStatusUpdater 0x558c6b6ba120 set_mirror_image_status: global_image_id=f816b4f2-f29c-453d-89fc-b99a4854ed9c, mirror_image_site_status={state=up+starting_replay, description=starting replay, last_update=0.000000]}
394 2025-06-10T07:07:13.429+0000 7fb197998700 10 rbd::mirror::image_replayer::CreateImageRequest: 0x558c6d2285a0 handle_get_parent_global_image_id: r=-2
395 2025-06-10T07:07:13.429+0000 7fb197998700 10 rbd::mirror::image_replayer::CreateImageRequest: 0x558c6d2285a0 handle_get_parent_global_image_id: parent image c3cb5d35f7cbd4 not mirrored
396 2025-06-10T07:07:13.429+0000 7fb197998700 10 rbd::mirror::image_replayer::CreateImageRequest: 0x558c6d2285a0 finish: r=-2
397 2025-06-10T07:07:13.429+0000 7fb197998700 10 rbd::mirror::image_replayer::journal::CreateLocalImageRequest: 0x558c6d24f320 handle_create_local_image: r=-2
398 2025-06-10T07:07:13.429+0000 7fb197998700 10 rbd::mirror::image_replayer::journal::CreateLocalImageRequest: 0x558c6d24f320 handle_create_local_image: parent image does not exist
```