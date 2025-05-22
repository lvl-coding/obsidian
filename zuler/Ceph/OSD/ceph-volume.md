OSD启动docker命令
```
$ ceph-volume activate --osd-id 1 --osd-uuid 58367491-e0bc-44bf-887e-e0338bdeb63d --no-systemd --no-tmpfs
```
支持使用lvm部署OSD
OSD启动失败时的重试参数
- CEPH_VOLUME_SYSTEMD_TRIES，默认30
- CEPH_VOLUME_SYSTEMD_INTERVAL，默认5
# prepare
仅使用LVM tag添加元数据到逻辑卷中
```
ceph-volume lvm prepare --bluestore --data vg/lv
```
`ceph-volume lvm`不对整个磁盘做分区。若磁盘已有分区，需要含有`PARTUUID`能被blkid识别
元数据类型：
- ## `type`
- `cluster_fsid`
- `data_device`
- `data_uuid`
- `journal_device`
- `journal_uuid`
- `encrypted`    
- `osd_fsid`    
- `osd_id`    
- `crush_device_class`
- `block_device`    
- `block_uuid`    
- `db_device`    
- `db_uuid`    
- `wal_device`    
- `wal_uuid`
- `vdo`
流程：
1. Accepts raw physical devices, partitions on physical devices or logical volumes as arguments.    
2. Creates logical volumes on any raw physical devices.    
3. Generate a UUID for the OSD    
4. Ask the monitor get an OSD ID reusing the generated UUID    
5. OSD data directory is created on a tmpfs mount.    
6. `block`, `block.wal`, and `block.db` are symlinked if defined.    
7. monmap is fetched for activation    
8. Data directory is populated by `ceph-osd`    
9. Logical Volumes are assigned all the Ceph metadata using lvm tags
```
ceph.<tag name>=<tag value>
```
# activate
1. Require both [OSD id](https://docs.ceph.com/en/reef/glossary/#term-OSD-ID) and [OSD uuid](https://docs.ceph.com/en/reef/glossary/#term-OSD-UUID)
2. Enable the system unit with matching id and uuid    
3. Create the `tmpfs` mount at the OSD directory in `/var/lib/ceph/osd/$cluster-$id/`    
4. Recreate all the files needed with `ceph-bluestore-tool prime-osd-dir` by pointing it to the OSD `block` device.
5. The systemd unit will ensure all devices are ready and linked    
6. The matching `ceph-osd` systemd unit will get started
# 批量自动创建OSD的默认规则
1. Devices are all spinning HDDs: 1 OSD is created per device
2. Devices are all SSDs: 2 OSDs are created per device
3. Devices are a mix of HDDs and SSDs: data is placed on the spinning device, the `block.db` is created on the SSD, as large as possible.
# create
上述`prepare`和`activate`合二为一
# systemd
 systemd 部分由 ceph-volume lvm 触发器子命令处理，该命令仅负责解析来自 systemd 和启动的元数据，然后调度到 ceph-volume lvm activate 继续执行激活操作
# `list`
展示OSD和设备的信息及日志设备，依赖元数据中的`PARTUUID`
# `zap`
清除OSD已使用的lvs、分区或设备，逻辑卷支持vg/lv格式，需先停止OSD服务进程
# `migrate`
数据迁移，迁移完成后删除源卷