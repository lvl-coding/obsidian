HDD盘使用两块SSD作为bulestore的db设备和wal设备

```yml
service_type: osd
service_id: osd_using_paths
placement:
  hosts:
    - shsc001
    - shsc002
    - shsc003
spec:
  data_devices:
    rotational: 1
  db_devices:
    paths:
    - /dev/sdb
  wal_devices:
    paths:
    - /dev/sdc
---
service_type: osd
service_id: osd_ssd
placement:
  host_pattern: '*'
spec:
  data_devices:
    paths:
    - /dev/sdd
    - /dev/sde
    - /dev/sdf
    - /dev/sdg
```
```bash
lvs | awk '$2 ~ /^ceph-/ {print $2}' | xargs -I {} lvremove -y {}
lvs | awk '$2 ~ /^ceph-/ && $6 < "7.28t" {print $1, $2}' | while read -r lv vg; do lvremove -y "$vg/$lv"; done
for i in {11..26}; do ceph orch device zap shsc002 /dev/sd$(printf "\\x$(printf %x $((96 + i)))") --force; done
```
# 查看可用存储设备
```
$ ceph orch device ls
HOST       PATH      TYPE  DEVICE ID              SIZE  AVAILABLE  REFRESHED  REJECT REASONS
cluster90  /dev/sr0  hdd   QEMU_DVD-ROM_QM00003  1202M  No         7m ago     Has a FileSystem, Insufficient space (<5GB)
cluster90  /dev/vdb  hdd                          200G  No         7m ago     Has a FileSystem, Insufficient space (<10 extents) on vgs, LVM detected
cluster90  /dev/vdc  hdd                          200G  No         7m ago     Has a FileSystem, Insufficient space (<10 extents) on vgs, LVM detected
cluster91  /dev/sr0  hdd   QEMU_DVD-ROM_QM00003  1202M  No         8m ago     Has a FileSystem, Insufficient space (<5GB)
cluster91  /dev/vdb  hdd                          200G  No         8m ago     Has a FileSystem, Insufficient space (<10 extents) on vgs, LVM detected
cluster91  /dev/vdc  hdd                          200G  No         8m ago     Has a FileSystem, Insufficient space (<10 extents) on vgs, LVM detected
cluster92  /dev/sr0  hdd   QEMU_DVD-ROM_QM00003  1202M  No         6m ago     Has a FileSystem, Insufficient space (<5GB)
cluster92  /dev/vdb  hdd                          200G  No         6m ago     Has a FileSystem, Insufficient space (<10 extents) on vgs, LVM detected
cluster92  /dev/vdc  hdd                          200G  No         6m ago     Has a FileSystem, Insufficient space (<10 extents) on vgs, LVM detected
```
## 可能需要预先清除旧的OSD数据
```
$ wipefs -a /dev/sdd
$ sudo ceph orch device zap cluster91 /dev/vdb --force
$ ceph orch device ls | awk 'NR>1 && $NF == "No" {print $1, $2}' | while read -r hostname path; do ceph orch device zap "$hostname" "$path" --force; done  #AI生成的自动脚本
```
# 自动创建OSD
## 编写模板文件

模版文件是通过ceph orch启动一个自动根据规则去创建OSD的服务进程，可通过ceph orch ls查看，使用yml文件语法[【搞定yml文件】.yml文件基本用法汇总](https://zhuanlan.zhihu.com/p/493137181)
字段说明：
- **service_type**创建的服务类型，mon、osd、rgw等  
- **service_id** ceph orch启动的服务名称，可通过ceph orch ls查看  
- **placement**服务运行的域，可指定启动服务的host主机  
- **data_devices**指定创建OSD使用的磁盘，可通过rotational为1表示携带磁道的HDD盘，0则表示ssd盘  
- **db_devices**指定OSD的bulestore使用的db设备，可以通过paths指定  
- **wal_devices**指定OSD的bulestore使用的wal设备，可以通过paths指定---多个配置文件和写到同一个yml文件内，用---分隔
expaml:
```yml
service_type: osd
service_id: osd_ssd
placement:
  hosts:
    - shsc001
    - shsc002
    - shsc003
spec:
  data_devices:
    paths:
    - /dev/sdd
    - /dev/sde
    - /dev/sdf
    - /dev/sdg
---
service_type: osd
service_id: osd_hdd_1
placement:
  hosts:
    - shsc001
spec:
  data_devices:
    paths:
    - /dev/sdh
    - /dev/sdi
    - /dev/sdj
    - /dev/sdk
    - /dev/sdl
    - /dev/sdm
    - /dev/sdn
    - /dev/sdo
    - /dev/sdp
    - /dev/sdq
    - /dev/sdr
    - /dev/sds
    - /dev/sdt
  db_devices:
    paths:
    - /dev/sdb
  wal_devices:
    paths:
    - /dev/sdc
---
service_type: osd
service_id: osd_hdd_2-1
placement:
  hosts:
    - shsc002
    - shsc003
spec:
  data_devices:
    paths:
    - /dev/sdo
    - /dev/sdp
    - /dev/sdq
    - /dev/sdr
  db_devices:
    paths:
    - /dev/sdb
  wal_devices:
    paths:
    - /dev/sdc
---
service_type: osd
service_id: osd_hdd_2-2
placement:
  hosts:
    - shsc002
    - shsc003
spec:
  data_devices:
    paths:
    - /dev/sds
    - /dev/sdt
    - /dev/sdu
    - /dev/sdv
  db_devices:
    paths:
    - /dev/sdk
  wal_devices:
    paths:
    - /dev/sdm
---
service_type: osd
service_id: osd_hdd_2-3
placement:
  hosts:
    - shsc002
    - shsc003
spec:
  data_devices:
    paths:
    - /dev/sdw
    - /dev/sdx
    - /dev/sdy
    - /dev/sdz
    - /dev/sdaa
  db_devices:
    paths:
    - /dev/sdl
  wal_devices:
    paths:
    - /dev/sdn
```
## 校验模板文件
```
$ ceph orch apply osd -i ssd_osd.yml --dry-run
WARNING! Dry-Runs are snapshots of a certain point in time and are bound
to the current inventory setup. If any on these conditions changes, the
preview will be invalid. Please make sure to have a minimal
timeframe between planning and applying the specs.
####################
SERVICESPEC PREVIEWS
####################
+---------+------+--------+-------------+
|SERVICE  |NAME  |ADD_TO  |REMOVE_FROM  |
+---------+------+--------+-------------+
+---------+------+--------+-------------+
################
OSDSPEC PREVIEWS
################
Preview data is being generated.. Please re-run this command in a bit.
```
## 加载模板文件
```
$ ceph orch apply osd -i ssd_osd.yml
Scheduled osd.osd_ssd update...
root@shsc001:~# ceph orch ls
NAME                 PORTS  RUNNING  REFRESHED  AGE  PLACEMENT
crash                           3/3  52s ago    2h   *
mgr                             2/2  51s ago    2h   count:2
mon                             3/5  52s ago    2h   count:5
osd.osd_ssd                     0/3  -          6s   *
osd.osd_using_paths           39/42  52s ago    31m  shsc001;shsc002;shsc003
```
# 手动添加OSD
```
$ sudo ceph orch daemon add osd cluster92:/dev/vdb,/dev/vdc --db-devices=
```



