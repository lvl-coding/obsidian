在ceph分布式存储集群内，由于存储长时间运行，及磁盘的使用寿命，因此会有OSD所在磁盘损坏需要更换的情况
1. 确认OSD所在坏盘
- 通过demsg查看
- lsblk查看磁盘和OSD的lvm关系
```
$root@cluster90:/home/kvirtadm#$ lsblk
NAME                                                                                                  MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
vda                                                                                                   252:0    0  250G  0 disk
├─vda1                                                                                                252:1    0    1M  0 part
├─vda2                                                                                                252:2    0    1G  0 part /boot
└─vda3                                                                                                252:3    0  249G  0 part
  └─ubuntu--vg-ubuntu--lv                                                                             253:0    0  249G  0 lvm  /
vdb                                                                                                   252:16   0  200G  0 disk
└─ceph--de42fa19--bddf--4ff3--89fe--7f549ad07525-osd--block--9e6c7b02--69d9--48f1--ba05--37e151b8d147 253:2    0  200G  0 lvm
vdc                                                                                                   252:32   0  200G  0 disk
└─ceph--8649f7af--ef2f--4ad3--a100--4154a42868c3-osd--block--58367491--e0bc--44bf--887e--e0338bdeb63d 253:1    0  200G  0 lvm
```
- ceph-volume lvm list查看OSD和使用的磁盘
```
$ ceph-volume lvm list
====== osd.1 =======

  [block]       /dev/ceph-8649f7af-ef2f-4ad3-a100-4154a42868c3/osd-block-58367491-e0bc-44bf-887e-e0338bdeb63d

      block device              /dev/ceph-8649f7af-ef2f-4ad3-a100-4154a42868c3/osd-block-58367491-e0bc-44bf-887e-e0338bdeb63d
      block uuid                5RgG1l-kClJ-lHoB-aXbY-zA6g-dDNX-1isZuG
      cephx lockbox secret
      cluster fsid              2a4e1392-0abd-11f0-95f6-e7d0a414706b
      cluster name              ceph
      crush device class
      encrypted                 0
      osd fsid                  58367491-e0bc-44bf-887e-e0338bdeb63d
      osd id                    1
      osdspec affinity          None
      type                      block
      vdo                       0
      devices                   /dev/vdc

====== osd.6 =======

  [block]       /dev/ceph-de42fa19-bddf-4ff3-89fe-7f549ad07525/osd-block-9e6c7b02-69d9-48f1-ba05-37e151b8d147

      block device              /dev/ceph-de42fa19-bddf-4ff3-89fe-7f549ad07525/osd-block-9e6c7b02-69d9-48f1-ba05-37e151b8d147
      block uuid                HBLbJk-Vehi-YPII-DBpj-KEul-jPnP-1sOpkD
      cephx lockbox secret
      cluster fsid              2a4e1392-0abd-11f0-95f6-e7d0a414706b
      cluster name              ceph
      crush device class
      encrypted                 0
      osd fsid                  9e6c7b02-69d9-48f1-ba05-37e151b8d147
      osd id                    6
      osdspec affinity          None
      type                      block
      vdo                       0
      devices                   /dev/vdb
```
2. 设置集群noout、nobackfill、norecover、norebalance，避免在进行OSD操作时导致数据震荡
```
$ ceph osd set noout
$ ceph osd set nobackfill
$ ceph osd set norebalance
$ ceph osd set norecover

$ ceph osd unset noout
$ ceph osd unset nobackfill
$ ceph osd unset norebalance
$ ceph osd unset norecover
```
3. 删除故障OSD
	- OUT出osd
	- 在对应节点上停止OSD服务
	- crush map中删除osd
	- 彻底清楚OSD服务文件
```
$ ceph osd out osd.1
$ systemctl stop ceph-2a4e1392-0abd-11f0-95f6-e7d0a414706b@osd.1.service
$ ceph osd crush remove osd.1
$ ceph auth del osd.1
$ ceph osd rm osd.1
$ ceph orch daemon rm osd.1 --force
```
4. 确认新加入磁盘自动添加为OSD的服务是否启动，若存在自动添加新加入磁盘为OSD的服务则需确认添加OSD是否符合预期
5. 手动将新加入磁盘添加为OSD，需添加db设备或wal设备则在参数中指定
```
$ ceph orch daemon add osd cluster92:/dev/vdb,/dev/vdc --db-devices=<>
```
6. 根据环境调整集群修复参数

|                                             |                                  |
| ------------------------------------------- | -------------------------------- |
| osd_max_backfills                           | 控制同时执行Recovery的PG个数              |
| osd_max_push_cost  <br>osd_max_push_objects | OSD中push请求的最大字节数和对象数             |
| osd_recovery_max_active                     | 单个OSD允许同时执行Recovery的对象数          |
| osd_recovery_op_priority                    | Recovery op默认在op_shardedwq中的优先级  |
| osd_recovery_sleep                          | Recovery op在op_shardedwq中的预先休眠时间 |

