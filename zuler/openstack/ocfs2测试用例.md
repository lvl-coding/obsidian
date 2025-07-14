[Cinder/tested-3rdParty-drivers](https://wiki.openstack.org/wiki/Cinder/tested-3rdParty-drivers)
[cinder下driver开发指导](https://docs.openstack.org/cinder/latest/contributor/drivers.html#core-functionality)
[cinder New Driver Review Checklist](https://docs.openstack.org/cinder/latest/contributor/new_driver_checklist.html)

# 新增磁盘
- 格式化文件系统后挂载即可
```
$ mount | grep sdc
/dev/sdc on /var/opt/mnt/ocfs2/192.168.10.91_sda type ocfs2 (rw,relatime,_netdev,heartbeat=local,nointr,data=ordered,errors=remount-ro,atime_quantum=60,coherency=full,user_xattr,acl)
```
- cinder-volume创建卷时无法分配至新增挂载点，需重启cinder-volume服务
```
2025-06-25 16:38:14.396 70 WARNING cinder.volume.drivers.ocfs2 [req-ae391712-442b-4bf9-b885-f29c706b643a 5273db44a9cf42bbb456cb1d0709251a 5be4d7808b7b45cea69010428c166a48 - - -] OCFS2 share /var/lib/cinder/ocfs2/192.168.10.91_sda is not mounted.
2025-06-25 16:38:15.126 70 INFO cinder.volume.drivers.remotefs [req-ae391712-442b-4bf9-b885-f29c706b643a 5273db44a9cf42bbb456cb1d0709251a 5be4d7808b7b45cea69010428c166a48 - - -] casted to /var/lib/cinder/ocfs2/192.168.10.89_sda
2025-06-25 16:38:22.002 70 WARNING cinder.volume.drivers.remotefs [req-ae391712-442b-4bf9-b885-f29c706b643a 5273db44a9cf42bbb456cb1d0709251a 5be4d7808b7b45cea69010428c166a48 - - -] /var/lib/cinder/ocfs2/192.168.10.89_sda/volume-ce9ed6b9-2087-4aa2-bbb4-85dc182cf896 is being set with open permissions: ugo+rw
2025-06-25 16:38:23.049 70 INFO cinder.volume.flows.manager.create_volume [req-ae391712-442b-4bf9-b885-f29c706b643a 5273db44a9cf42bbb456cb1d0709251a 5be4d7808b7b45cea69010428c166a48 - - -] Volume volume-ce9ed6b9-2087-4aa2-bbb4-85dc182cf896 (ce9ed6b9-2087-4aa2-bbb4-85dc182cf896): created successfully
2025-06-25 16:38:23.449 70 INFO cinder.volume.manager [req-ae391712-442b-4bf9-b885-f29c706b643a 5273db44a9cf42bbb456cb1d0709251a 5be4d7808b7b45cea69010428c166a48 - - -] Created volume successfully.
2025-06-25 16:38:36.202 70 WARNING cinder.volume.drivers.ocfs2 [req-05f0ca3c-f859-4008-bec1-c220bbdb424a - - - - -] OCFS2 share /var/lib/cinder/ocfs2/192.168.10.91_sda is not mounted.
2025-06-25 16:39:36.203 70 WARNING cinder.volume.drivers.ocfs2 [req-05f0ca3c-f859-4008-bec1-c220bbdb424a - - - - -] OCFS2 share /var/lib/cinder/ocfs2/192.168.10.91_sda is not mounted.
2025-06-25 16:39:47.527 70 INFO cinder.volume.flows.manager.create_volume [req-446381eb-0cbf-4d34-874b-27be5875bea6 5273db44a9cf42bbb456cb1d0709251a 5be4d7808b7b45cea69010428c166a48 - - -] Volume 238586dd-da78-431e-b685-66aaae2e9fa3: being created as raw with specification: {'status': 'creating', 'volume_name': 'volume-238586dd-da78-431e-b685-66aaae2e9fa3', 'volume_size': 5}
2025-06-25 16:39:47.531 70 WARNING cinder.volume.drivers.ocfs2 [req-446381eb-0cbf-4d34-874b-27be5875bea6 5273db44a9cf42bbb456cb1d0709251a 5be4d7808b7b45cea69010428c166a48 - - -] OCFS2 share /var/lib/cinder/ocfs2/192.168.10.91_sda is not mounted.
2025-06-25 16:39:48.298 70 INFO cinder.volume.drivers.remotefs [req-446381eb-0cbf-4d34-874b-27be5875bea6 5273db44a9cf42bbb456cb1d0709251a 5be4d7808b7b45cea69010428c166a48 - - -] casted to /var/lib/cinder/ocfs2/192.168.10.90_sda
2025-06-25 16:39:56.734 70 WARNING cinder.volume.drivers.remotefs [req-446381eb-0cbf-4d34-874b-27be5875bea6 5273db44a9cf42bbb456cb1d0709251a 5be4d7808b7b45cea69010428c166a48 - - -] /var/lib/cinder/ocfs2/192.168.10.90_sda/volume-238586dd-da78-431e-b685-66aaae2e9fa3 is being set with open permissions: ugo+rw
2025-06-25 16:39:57.217 70 INFO cinder.volume.flows.manager.create_volume [req-446381eb-0cbf-4d34-874b-27be5875bea6 5273db44a9cf42bbb456cb1d0709251a 5be4d7808b7b45cea69010428c166a48 - - -] Volume volume-238586dd-da78-431e-b685-66aaae2e9fa3 (238586dd-da78-431e-b685-66aaae2e9fa3): created successfully
2025-06-25 16:39:57.300 70 INFO cinder.volume.manager [req-446381eb-0cbf-4d34-874b-27be5875bea6 5273db44a9cf42bbb456cb1d0709251a 5be4d7808b7b45cea69010428c166a48 - - -] Created volume successfully.
2025-06-25 16:40:36.204 70 WARNING cinder.volume.drivers.ocfs2 [req-05f0ca3c-f859-4008-bec1-c220bbdb424a - - - - -] OCFS2 share /var/lib/cinder/ocfs2/192.168.10.91_sda is not mounted.
```
# 删除o2cb节点
```bash
# 卸载已挂载设备
$ umount /dev/sd*
# 在所有节点上执行删除擦操作
$ o2cb remove-node <node-name>
$ o2cb stop-heartbeat <cluster-name>
$ /etc/init.d/o2cb unload
$ /etc/init.d/o2cb disable
$ /etc/init.d/o2cb stop
$ o2cb remove-cluster zuler
```
- 删除完成
```bash
$ mount /dev/sda /var/opt/mnt/ocfs2/192.168.10.89_sda
mount.ocfs2: Internal logic failure while trying to join the group
$ mount /dev/sdb /var/opt/mnt/ocfs2/192.168.10.91_sda
mount.ocfs2: Internal logic failure while trying to join the group

$ tail -f /var/log/kern.log
Jun 26 15:18:21 cluster90 kernel: [13817.485884] o2dlm: Leaving domain 6301B24E15244B98AD1E6B693AE92503
Jun 26 15:18:22 cluster90 kernel: [13818.190334] ocfs2: Unmounting device (8,0) on (node 1)
Jun 26 15:18:28 cluster90 kernel: [13824.301656] o2dlm: Leaving domain FCDBEE3B768D49F5ADE9BC8A81E19A3A
Jun 26 15:18:29 cluster90 kernel: [13825.100728] o2net: No longer connected to node cluster89 (num 0) at 192.168.10.89:17777
Jun 26 15:18:29 cluster90 kernel: [13825.100936] o2net: No longer connected to node cluster91 (num 2) at 192.168.10.91:17777
Jun 26 15:18:29 cluster90 kernel: [13825.102560] ocfs2: Unmounting device (8,16) on (node 1)

#其他节点日志
$ tail -f /var/log/kern.log
Jun 26 15:18:29 cluster89 kernel: [13824.881854] o2net: Connection to node cluster90 (num 1) at 192.168.10.90:17777 shutdown, state 8
Jun 26 15:18:29 cluster89 kernel: [13824.882045] o2net: No longer connected to node cluster90 (num 1) at 192.168.10.90:17777
```
# 新增o2cb节点
```bash
$ o2cb add-cluster <cluster-name>
# 根据已存在配置文件依次添加节点信息或直接拷贝配置文件/etc/ocfs/cluster.conf，最终添加新增的节点
$ o2cb add-node <cluster-name> <hostname> --ip <ip> --port <port>
# 将节点通过设备上的DLM信息添加到已有ocfs集群中
$ o2cb add-heartbeat <cluster-name> /dev/sd*
$ o2cb register-cluster zuler
$ o2cb start-heartbeat zuler
# 最终挂载成功
$ mount /dev/sd* <mount-point>
# 日志
$ tail -f /var/log/kern.log
Jun 26 16:01:43 cluster90 kernel: [16418.901356] ocfs2: Mounting device (8,0) on (node 1, slot 1) with ordered data mode.
Jun 26 16:02:04 cluster90 kernel: [16439.849420] o2dlm: Joining domain FCDBEE3B768D49F5ADE9BC8A81E19A3A
Jun 26 16:02:04 cluster90 kernel: [16439.849429] (
Jun 26 16:02:04 cluster90 kernel: [16439.849432] 0
Jun 26 16:02:04 cluster90 kernel: [16439.849435] 1
Jun 26 16:02:04 cluster90 kernel: [16439.849436] 2
Jun 26 16:02:04 cluster90 kernel: [16439.849437] ) 3 nodes
Jun 26 16:02:04 cluster90 kernel: [16439.862568] ocfs2: Mounting device (8,16) on (node 1, slot 1) with ordered data mode.
```
# 读写一致性
fio crc32数据读写校验，当前节点写入后校验数据，并在其他节点进行二次校验数据
```
root@cluster89:~# /usr/local/bin/fio -bs=4k -ioengine=libaio -iodepth=32 -numjobs=1 -direct=1 -rw=randrw -thread -time_based -runtime=180 -refill_buffers -randrepeat=0 -name=frw -size=1000M -verify=crc32 -verify_only -filename=/var/opt/mnt/ocfs2/192.168.10.90_sda/targetfile1
frw: (g=0): rw=randrw, bs=(R) 4096B-4096B, (W) 4096B-4096B, (T) 4096B-4096B, ioengine=libaio, iodepth=32
fio-3.40
Starting 1 thread
Jobs: 1 (f=0): [V(1)][26.7%][eta 02m:12s]
Jobs: 1 (f=0): [m(1)][41.1%][eta 01m:46s]
Jobs: 1 (f=0): [m(1)][100.0%][eta 00m:00s]
frw: (groupid=0, jobs=1): err= 0: pid=2950663: Fri Jun 27 10:38:57 2025
  read: IOPS=58.2k, BW=227MiB/s (238MB/s)(1000MiB/4402msec)
    slat (nsec): min=1954, max=3731.6k, avg=7590.76, stdev=15155.46
    clat (usec): min=437, max=64358M, avg=32117774744.50, stdev=32178868275.63
     lat (nsec): min=26, max=9431.0k, avg=450617.02, stdev=525290.90
    clat percentiles (usec):
     |  1.00th=[     545],  5.00th=[     635], 10.00th=[     701],
     | 20.00th=[     791], 30.00th=[     865], 40.00th=[     971],
     | 50.00th=[    3884], 60.00th=[17112761], 70.00th=[17112761],
     | 80.00th=[17112761], 90.00th=[17112761], 95.00th=[17112761],
     | 99.00th=[17112761], 99.50th=[17112761], 99.90th=[17112761],
     | 99.95th=[17112761], 99.99th=[17112761]
   bw (  KiB/s): min=1022056, max=1022056, per=100.00%, avg=1022056.00, stdev= 0.00, samples=1
   iops        : min=255514, max=255514, avg=255514.00, stdev= 0.00, samples=1
  lat (usec)   : 500=0.07%, 750=9.98%, 1000=17.71%
  lat (msec)   : 2=4.81%, 4=0.74%, 10=0.06%, >=2000=66.62%
  cpu          : usr=98.57%, sys=0.78%, ctx=37219, majf=0, minf=3507
  IO depths    : 1=66.6%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=33.4%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.1%, 64=0.0%, >=64=0.0%
     issued rwts: total=256000,128243,0,0 short=0,0,0,0 dropped=0,0,0,0

Run status group 0 (all jobs):
   READ: bw=227MiB/s (238MB/s), 227MiB/s-227MiB/s (238MB/s-238MB/s), io=1000MiB (1049MB), run=4402-4402msec

Disk stats (read/write):
  sdb: ios=120928/1, sectors=967419/1, merge=0/0, ticks=81543/1, in_queue=81544, util=93.01%

root@cluster91:~# /usr/local/bin/fio -bs=4k -ioengine=libaio -iodepth=32 -numjobs=1 -direct=1 -rw=randrw -thread -runtime=180 -refill_buffers -randrepeat=0 -name=frw -size=1000M -verify=crc32 -verify_only -filename=/var/opt/mnt/ocfs2/192.168.10.90_sda/targetfile1
frw: (g=0): rw=randrw, bs=(R) 4096B-4096B, (W) 4096B-4096B, (T) 4096B-4096B, ioengine=libaio, iodepth=32
fio-3.40
Starting 1 thread
Jobs: 1 (f=1): [V(1)][-.-%][r=133MiB/s][r=34.1k IOPS][eta 00m:00s]
frw: (groupid=0, jobs=1): err= 0: pid=862134: Fri Jun 27 10:44:46 2025
  read: IOPS=61.4k, BW=240MiB/s (251MB/s)(1000MiB/4172msec)
    slat (usec): min=2, max=1551, avg= 6.04, stdev= 7.98
    clat (usec): min=444, max=83800M, avg=41960085075.17, stdev=41899891605.58
     lat (nsec): min=26, max=11576k, avg=490115.97, stdev=554792.26
    clat percentiles (usec):
     |  1.00th=[     578],  5.00th=[     693], 10.00th=[     766],
     | 20.00th=[     889], 30.00th=[     988], 40.00th=[    1090],
     | 50.00th=[17112761], 60.00th=[17112761], 70.00th=[17112761],
     | 80.00th=[17112761], 90.00th=[17112761], 95.00th=[17112761],
     | 99.00th=[17112761], 99.50th=[17112761], 99.90th=[17112761],
     | 99.95th=[17112761], 99.99th=[17112761]
   bw (  KiB/s): min=1025472, max=1025472, per=100.00%, avg=1025472.00, stdev= 0.00, samples=1
   iops        : min=256368, max=256368, avg=256368.00, stdev= 0.00, samples=1
  lat (usec)   : 500=0.04%, 750=5.77%, 1000=15.08%
  lat (msec)   : 2=11.81%, 4=0.55%, 10=0.04%, 20=0.01%, >=2000=66.70%
  cpu          : usr=41.64%, sys=28.67%, ctx=41347, majf=0, minf=3496
  IO depths    : 1=66.7%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=33.3%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.1%, 64=0.0%, >=64=0.0%
     issued rwts: total=256000,127816,0,0 short=0,0,0,0 dropped=0,0,0,0

Run status group 0 (all jobs):
   READ: bw=240MiB/s (251MB/s), 240MiB/s-240MiB/s (251MB/s-251MB/s), io=1000MiB (1049MB), run=4172-4172msec

Disk stats (read/write):
  sdc: ios=126683/2, sectors=1013454/2, merge=0/0, ticks=113116/1, in_queue=113117, util=93.19%
```
# 卷备份
```bash
$ openstack volume backup create test5
+-------+--------------------------------------+
| Field | Value                                |
+-------+--------------------------------------+
| id    | 2405b790-4cf5-48ba-859c-11901ee890dd |
| name  | None                                 |
+-------+--------------------------------------+
```
# Create Volume
```
$ openstack volume create test4 --size 5 --type ocfs
+---------------------+--------------------------------------+
| Field               | Value                                |
+---------------------+--------------------------------------+
| attachments         | []                                   |
| availability_zone   | nova                                 |
| bootable            | false                                |
| consistencygroup_id | None                                 |
| created_at          | 2025-07-04T03:19:44.967428           |
| description         | None                                 |
| encrypted           | False                                |
| id                  | b24edb94-d129-4ed3-b0ce-f949318b0c18 |
| migration_status    | None                                 |
| multiattach         | False                                |
| name                | test4                                |
| properties          |                                      |
| replication_roles   | None                                 |
| replication_status  | None                                 |
| size                | 5                                    |
| snapshot_id         | None                                 |
| source_volid        | None                                 |
| status              | creating                             |
| type                | ocfs                                 |
| updated_at          | None                                 |
| user_id             | 5273db44a9cf42bbb456cb1d0709251a     |
+---------------------+--------------------------------------+
$ openstack volume show test4
+--------------------------------+--------------------------------------+
| Field                          | Value                                |
+--------------------------------+--------------------------------------+
| attachments                    | []                                   |
| availability_zone              | nova                                 |
| bootable                       | false                                |
| consistencygroup_id            | None                                 |
| created_at                     | 2025-07-04T03:19:44.000000           |
| description                    | None                                 |
| encrypted                      | False                                |
| id                             | b24edb94-d129-4ed3-b0ce-f949318b0c18 |
| migration_status               | None                                 |
| multiattach                    | False                                |
| name                           | test4                                |
| os-vol-host-attr:host          | sharehost@ocfs2#ocfs2                |
| os-vol-mig-status-attr:migstat | None                                 |
| os-vol-mig-status-attr:name_id | None                                 |
| os-vol-tenant-attr:tenant_id   | 5be4d7808b7b45cea69010428c166a48     |
| properties                     |                                      |
| replication_roles              | None                                 |
| replication_status             | None                                 |
| size                           | 5                                    |
| snapshot_id                    | None                                 |
| source_volid                   | None                                 |
| status                         | available                            |
| type                           | ocfs                                 |
| updated_at                     | 2025-07-04T03:19:47.000000           |
| user_id                        | 5273db44a9cf42bbb456cb1d0709251a     |
+--------------------------------+--------------------------------------+
```
# Delete Volume
```
$ openstack volume delete test4
$ openstack volume show test4
No volume with a name or ID of 'test4' exists.
```
# Attach Volume
```
$ openstack server add volume vmx86 test3
+-----------+--------------------------------------+
| Field     | Value                                |
+-----------+--------------------------------------+
| ID        | 4ef9061f-580a-4ae7-bef4-a9fc810d95a9 |
| Server ID | b5a82883-4160-4bb2-95d1-282255ca15c8 |
| Volume ID | 4ef9061f-580a-4ae7-bef4-a9fc810d95a9 |
| Device    | /dev/vdd                             |
+-----------+--------------------------------------+
```
# Detach Volume
```
$ openstack server remove volume vmx86 test3
$ openstack server show vmx86
+-------------------------------------+------------------------------------------------------------------------------------------------------------------------+
| Field                               | Value                                                                                                                  |
+-------------------------------------+------------------------------------------------------------------------------------------------------------------------+
| OS-DCF:diskConfig                   | AUTO                                                                                                                   |
| OS-EXT-AZ:availability_zone         | nova                                                                                                                   |
| OS-EXT-SRV-ATTR:host                | cluster89                                                                                                              |
| OS-EXT-SRV-ATTR:hypervisor_hostname | cluster89                                                                                                              |
| OS-EXT-SRV-ATTR:instance_name       | instance-00000007                                                                                                      |
| OS-EXT-STS:power_state              | Running                                                                                                                |
| OS-EXT-STS:task_state               | None                                                                                                                   |
| OS-EXT-STS:vm_state                 | active                                                                                                                 |
| OS-SRV-USG:launched_at              | 2025-07-03T03:00:22.000000                                                                                             |
| OS-SRV-USG:terminated_at            | None                                                                                                                   |
| accessIPv4                          |                                                                                                                        |
| accessIPv6                          |                                                                                                                        |
| addresses                           | VPNET-1=192.168.10.28                                                                                                  |
| config_drive                        | True                                                                                                                   |
| created                             | 2025-07-03T02:59:31Z                                                                                                   |
| flavor                              | test-simple (dca3ea9b-a626-4fe4-b272-80508776625f)                                                                     |
| hostId                              | 017164f06a8e59dc775c15e82b91f90a17c455e2b13f1a2a54df10e4                                                               |
| id                                  | b5a82883-4160-4bb2-95d1-282255ca15c8                                                                                   |
| image                               | N/A (booted from volume)                                                                                               |
| key_name                            | None                                                                                                                   |
| name                                | vmx86                                                                                                                  |
| progress                            | 0                                                                                                                      |
| project_id                          | 5be4d7808b7b45cea69010428c166a48                                                                                       |
| properties                          | disk_qos_specs='{"total_bytes_sec": 2516582400, "total_iops_sec": 4000}', genid='751c6f75-0836-4d8f-b9aa-8a72fec61a49' |
| security_groups                     | name='default'                                                                                                         |
| status                              | ACTIVE                                                                                                                 |
| updated                             | 2025-07-03T03:00:23Z                                                                                                   |
| user_id                             | 5273db44a9cf42bbb456cb1d0709251a                                                                                       |
| volumes_attached                    | id='b82ef3fa-24d4-47aa-9691-8cc8d3e7ec68'                                                                              |
|                                     | id='e7cc81b5-0bff-47d3-9efe-6d6d6d9b4125'                                                                              |
|                                     | id='5547c407-24b7-4d54-8abd-cf6393e62310'                                                                              |
+-------------------------------------+------------------------------------------------------------------------------------------------------------------------+
$ openstack volume show test3
+--------------------------------+--------------------------------------+
| Field                          | Value                                |
+--------------------------------+--------------------------------------+
| attachments                    | []                                   |
| availability_zone              | nova                                 |
| bootable                       | false                                |
| consistencygroup_id            | None                                 |
| created_at                     | 2025-07-03T10:42:44.000000           |
| description                    |                                      |
| encrypted                      | False                                |
| id                             | 4ef9061f-580a-4ae7-bef4-a9fc810d95a9 |
| migration_status               | None                                 |
| multiattach                    | False                                |
| name                           | test3                                |
| os-vol-host-attr:host          | sharehost@ocfs2#ocfs2                |
| os-vol-mig-status-attr:migstat | None                                 |
| os-vol-mig-status-attr:name_id | None                                 |
| os-vol-tenant-attr:tenant_id   | 5be4d7808b7b45cea69010428c166a48     |
| properties                     |                                      |
| replication_roles              | None                                 |
| replication_status             | None                                 |
| size                           | 5                                    |
| snapshot_id                    | None                                 |
| source_volid                   | None                                 |
| status                         | available                            |
| type                           | ocfs                                 |
| updated_at                     | 2025-07-04T03:33:14.000000           |
| user_id                        | 5273db44a9cf42bbb456cb1d0709251a     |
+--------------------------------+--------------------------------------+
```
# Extend Volume
```
$ cinder extend test3 10
WARNING:cinderclient.shell:API version 3.68 requested,
WARNING:cinderclient.shell:downgrading to 3.64 based on server support.
$ cinder show test3
WARNING:cinderclient.shell:API version 3.68 requested,
WARNING:cinderclient.shell:downgrading to 3.64 based on server support.
+--------------------------------+--------------------------------------+
| Property                       | Value                                |
+--------------------------------+--------------------------------------+
| attached_servers               | []                                   |
| attachment_ids                 | []                                   |
| availability_zone              | nova                                 |
| bootable                       | false                                |
| cluster_name                   | None                                 |
| consistencygroup_id            | None                                 |
| created_at                     | 2025-07-03T10:42:44.000000           |
| description                    |                                      |
| encrypted                      | False                                |
| group_id                       | None                                 |
| id                             | 4ef9061f-580a-4ae7-bef4-a9fc810d95a9 |
| metadata                       |                                      |
| migration_status               | None                                 |
| multiattach                    | False                                |
| name                           | test3                                |
| os-vol-host-attr:host          | sharehost@ocfs2#ocfs2                |
| os-vol-mig-status-attr:migstat | None                                 |
| os-vol-mig-status-attr:name_id | None                                 |
| os-vol-tenant-attr:tenant_id   | 5be4d7808b7b45cea69010428c166a48     |
| provider_id                    | None                                 |
| replication_roles              | None                                 |
| replication_status             | None                                 |
| service_uuid                   | 052cd38d-ea3d-4cda-86d4-697f239bc2ff |
| shared_targets                 | False                                |
| size                           | 10                                   |
| snapshot_id                    | None                                 |
| source_volid                   | None                                 |
| status                         | available                            |
| updated_at                     | 2025-07-04T03:36:31.000000           |
| user_id                        | 5273db44a9cf42bbb456cb1d0709251a     |
| volume_type                    | ocfs                                 |
| volume_type_id                 | 4ba9546e-2318-4355-b63d-7702fd69eb16 |
+--------------------------------+--------------------------------------+
```
# Create Snapshot
```
$ openstack volume snapshot create --volume test-img test-snp
+-------------+--------------------------------------+
| Field       | Value                                |
+-------------+--------------------------------------+
| created_at  | 2025-07-04T04:07:36.243283           |
| description | None                                 |
| id          | 3be8edc9-5706-4508-8120-595c59b52e58 |
| name        | test-snp                             |
| properties  |                                      |
| size        | 5                                    |
| status      | creating                             |
| updated_at  | None                                 |
| volume_id   | 6b267499-52d5-40a0-98bd-abf712ec4b50 |
+-------------+--------------------------------------+
(.venv) cmadmin@cluster89:~$ openstack volume snapshot list
+--------------------------------------+----------+-------------+-----------+------+
| ID                                   | Name     | Description | Status    | Size |
+--------------------------------------+----------+-------------+-----------+------+
| 3be8edc9-5706-4508-8120-595c59b52e58 | test-snp | None        | available |    5 |
+--------------------------------------+----------+-------------+-----------+------+
```
# Delete Snapshot
```
$ openstack volume snapshot delete snp-vo
$ openstack volume snapshot list
+--------------------------------------+----------+-------------+-----------+------+
| ID                                   | Name     | Description | Status    | Size |
+--------------------------------------+----------+-------------+-----------+------+
| 3be8edc9-5706-4508-8120-595c59b52e58 | test-snp | None        | available |    5 |
+--------------------------------------+----------+-------------+-----------+------+
```
# Create Volume from Snapshot
```
$ openstack volume create snp-volume --snapshot snp-vo
+---------------------+--------------------------------------+
| Field               | Value                                |
+---------------------+--------------------------------------+
| attachments         | []                                   |
| availability_zone   | nova                                 |
| bootable            | true                                 |
| consistencygroup_id | None                                 |
| created_at          | 2025-07-04T04:14:45.187041           |
| description         | None                                 |
| encrypted           | False                                |
| id                  | b8bf737f-7c25-4d8c-b037-2f8799b8762c |
| migration_status    | None                                 |
| multiattach         | False                                |
| name                | snp-volume                           |
| properties          |                                      |
| replication_roles   | None                                 |
| replication_status  | None                                 |
| size                | 5                                    |
| snapshot_id         | f68d7ba4-499d-459d-81a1-d703485a5e3b |
| source_volid        | None                                 |
| status              | creating                             |
| type                | ocfs                                 |
| updated_at          | None                                 |
| user_id             | 5273db44a9cf42bbb456cb1d0709251a     |
+---------------------+--------------------------------------+
$ openstack volume show snp-volume
+--------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Field                          | Value                                                                                                                                                                                                                                   |
+--------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| attachments                    | []                                                                                                                                                                                                                                      |
| availability_zone              | nova                                                                                                                                                                                                                                    |
| bootable                       | true                                                                                                                                                                                                                                    |
| consistencygroup_id            | None                                                                                                                                                                                                                                    |
| created_at                     | 2025-07-04T04:14:45.000000                                                                                                                                                                                                              |
| description                    | None                                                                                                                                                                                                                                    |
| encrypted                      | False                                                                                                                                                                                                                                   |
| id                             | b8bf737f-7c25-4d8c-b037-2f8799b8762c                                                                                                                                                                                                    |
| migration_status               | None                                                                                                                                                                                                                                    |
| multiattach                    | False                                                                                                                                                                                                                                   |
| name                           | snp-volume                                                                                                                                                                                                                              |
| os-vol-host-attr:host          | sharehost@ocfs2#ocfs2                                                                                                                                                                                                                   |
| os-vol-mig-status-attr:migstat | None                                                                                                                                                                                                                                    |
| os-vol-mig-status-attr:name_id | None                                                                                                                                                                                                                                    |
| os-vol-tenant-attr:tenant_id   | 5be4d7808b7b45cea69010428c166a48                                                                                                                                                                                                        |
| properties                     |                                                                                                                                                                                                                                         |
| replication_roles              | None                                                                                                                                                                                                                                    |
| replication_status             | None                                                                                                                                                                                                                                    |
| size                           | 5                                                                                                                                                                                                                                       |
| snapshot_id                    | f68d7ba4-499d-459d-81a1-d703485a5e3b                                                                                                                                                                                                    |
| source_volid                   | None                                                                                                                                                                                                                                    |
| status                         | available                                                                                                                                                                                                                               |
| type                           | ocfs                                                                                                                                                                                                                                    |
| updated_at                     | 2025-07-04T04:14:49.000000                                                                                                                                                                                                              |
| user_id                        | 5273db44a9cf42bbb456cb1d0709251a                                                                                                                                                                                                        |
| volume_image_metadata          | {'image_id': 'ea3b0130-7e56-4f18-89e7-6c242015d912', 'image_name': 'test86', 'checksum': '87617e24a5e30cb3b87fda8c0764838f', 'container_format': 'bare', 'disk_format': 'qcow2', 'min_disk': '5', 'min_ram': '500', 'size': '21692416'} |
+--------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```
# Create Volume from Volume (clone)
```
$ cinder create 10 --volume-type ocfs --source-volid 4ef9061f-580a-4ae7-bef4-a9fc810d95a9 --name testcp
WARNING:cinderclient.shell:API version 3.68 requested,
WARNING:cinderclient.shell:downgrading to 3.64 based on server support.
+--------------------------------+--------------------------------------+
| Property                       | Value                                |
+--------------------------------+--------------------------------------+
| attachments                    | []                                   |
| availability_zone              | nova                                 |
| bootable                       | false                                |
| cluster_name                   | None                                 |
| consistencygroup_id            | None                                 |
| created_at                     | 2025-07-04T03:53:55.000000           |
| description                    | None                                 |
| encrypted                      | False                                |
| group_id                       | None                                 |
| id                             | 1d5024c2-2881-4c4b-a8d4-7d6113829d91 |
| metadata                       | {}                                   |
| migration_status               | None                                 |
| multiattach                    | False                                |
| name                           | testcp                               |
| os-vol-host-attr:host          | None                                 |
| os-vol-mig-status-attr:migstat | None                                 |
| os-vol-mig-status-attr:name_id | None                                 |
| os-vol-tenant-attr:tenant_id   | 5be4d7808b7b45cea69010428c166a48     |
| provider_id                    | None                                 |
| replication_roles              | None                                 |
| replication_status             | None                                 |
| service_uuid                   | None                                 |
| shared_targets                 | True                                 |
| size                           | 10                                   |
| snapshot_id                    | None                                 |
| source_volid                   | 4ef9061f-580a-4ae7-bef4-a9fc810d95a9 |
| status                         | creating                             |
| updated_at                     | None                                 |
| user_id                        | 5273db44a9cf42bbb456cb1d0709251a     |
| volume_type                    | ocfs                                 |
| volume_type_id                 | 4ba9546e-2318-4355-b63d-7702fd69eb16 |
+--------------------------------+--------------------------------------+
$ cinder show testcp
WARNING:cinderclient.shell:API version 3.68 requested,
WARNING:cinderclient.shell:downgrading to 3.64 based on server support.
+--------------------------------+--------------------------------------+
| Property                       | Value                                |
+--------------------------------+--------------------------------------+
| attached_servers               | []                                   |
| attachment_ids                 | []                                   |
| availability_zone              | nova                                 |
| bootable                       | false                                |
| cluster_name                   | None                                 |
| consistencygroup_id            | None                                 |
| created_at                     | 2025-07-04T03:53:55.000000           |
| description                    | None                                 |
| encrypted                      | False                                |
| group_id                       | None                                 |
| id                             | 1d5024c2-2881-4c4b-a8d4-7d6113829d91 |
| metadata                       |                                      |
| migration_status               | None                                 |
| multiattach                    | False                                |
| name                           | testcp                               |
| os-vol-host-attr:host          | sharehost@ocfs2#ocfs2                |
| os-vol-mig-status-attr:migstat | None                                 |
| os-vol-mig-status-attr:name_id | None                                 |
| os-vol-tenant-attr:tenant_id   | 5be4d7808b7b45cea69010428c166a48     |
| provider_id                    | None                                 |
| replication_roles              | None                                 |
| replication_status             | None                                 |
| service_uuid                   | 052cd38d-ea3d-4cda-86d4-697f239bc2ff |
| shared_targets                 | False                                |
| size                           | 10                                   |
| snapshot_id                    | None                                 |
| source_volid                   | 4ef9061f-580a-4ae7-bef4-a9fc810d95a9 |
| status                         | available                            |
| updated_at                     | 2025-07-04T03:54:03.000000           |
| user_id                        | 5273db44a9cf42bbb456cb1d0709251a     |
| volume_type                    | ocfs                                 |
| volume_type_id                 | 4ba9546e-2318-4355-b63d-7702fd69eb16 |
+--------------------------------+--------------------------------------+
```
# Create Image from Volume
```
$ openstack image create --volume test-img volume-img
VolumeManager.upload_to_image() got an unexpected keyword argument 'visibility'
```
# Create Volume from Image
```
$ cinder create 5 --name test-img --image test86 --volume-type ocfs
WARNING:cinderclient.shell:API version 3.68 requested,
WARNING:cinderclient.shell:downgrading to 3.64 based on server support.
+--------------------------------+--------------------------------------+
| Property                       | Value                                |
+--------------------------------+--------------------------------------+
| attachments                    | []                                   |
| availability_zone              | nova                                 |
| bootable                       | false                                |
| cluster_name                   | None                                 |
| consistencygroup_id            | None                                 |
| created_at                     | 2025-07-04T03:42:00.000000           |
| description                    | None                                 |
| encrypted                      | False                                |
| group_id                       | None                                 |
| id                             | 6b267499-52d5-40a0-98bd-abf712ec4b50 |
| metadata                       | {}                                   |
| migration_status               | None                                 |
| multiattach                    | False                                |
| name                           | test-img                             |
| os-vol-host-attr:host          | None                                 |
| os-vol-mig-status-attr:migstat | None                                 |
| os-vol-mig-status-attr:name_id | None                                 |
| os-vol-tenant-attr:tenant_id   | 5be4d7808b7b45cea69010428c166a48     |
| provider_id                    | None                                 |
| replication_roles              | None                                 |
| replication_status             | None                                 |
| service_uuid                   | None                                 |
| shared_targets                 | True                                 |
| size                           | 5                                    |
| snapshot_id                    | None                                 |
| source_volid                   | None                                 |
| status                         | creating                             |
| updated_at                     | None                                 |
| user_id                        | 5273db44a9cf42bbb456cb1d0709251a     |
| volume_type                    | ocfs                                 |
| volume_type_id                 | 4ba9546e-2318-4355-b63d-7702fd69eb16 |
+--------------------------------+--------------------------------------+
$ cinder show test-img
WARNING:cinderclient.shell:API version 3.68 requested,
WARNING:cinderclient.shell:downgrading to 3.64 based on server support.
+--------------------------------+-------------------------------------------------+
| Property                       | Value                                           |
+--------------------------------+-------------------------------------------------+
| attached_servers               | []                                              |
| attachment_ids                 | []                                              |
| availability_zone              | nova                                            |
| bootable                       | true                                            |
| cluster_name                   | None                                            |
| consistencygroup_id            | None                                            |
| created_at                     | 2025-07-04T03:42:00.000000                      |
| description                    | None                                            |
| encrypted                      | False                                           |
| group_id                       | None                                            |
| id                             | 6b267499-52d5-40a0-98bd-abf712ec4b50            |
| metadata                       |                                                 |
| migration_status               | None                                            |
| multiattach                    | False                                           |
| name                           | test-img                                        |
| os-vol-host-attr:host          | sharehost@ocfs2#ocfs2                           |
| os-vol-mig-status-attr:migstat | None                                            |
| os-vol-mig-status-attr:name_id | None                                            |
| os-vol-tenant-attr:tenant_id   | 5be4d7808b7b45cea69010428c166a48                |
| provider_id                    | None                                            |
| replication_roles              | None                                            |
| replication_status             | None                                            |
| service_uuid                   | 052cd38d-ea3d-4cda-86d4-697f239bc2ff            |
| shared_targets                 | False                                           |
| size                           | 5                                               |
| snapshot_id                    | None                                            |
| source_volid                   | None                                            |
| status                         | available                                       |
| updated_at                     | 2025-07-04T03:42:06.000000                      |
| user_id                        | 5273db44a9cf42bbb456cb1d0709251a                |
| volume_image_metadata          | checksum : 87617e24a5e30cb3b87fda8c0764838f     |
|                                | container_format : bare                         |
|                                | disk_format : qcow2                             |
|                                | image_id : ea3b0130-7e56-4f18-89e7-6c242015d912 |
|                                | image_name : test86                             |
|                                | min_disk : 5                                    |
|                                | min_ram : 500                                   |
|                                | size : 21692416                                 |
| volume_type                    | ocfs                                            |
| volume_type_id                 | 4ba9546e-2318-4355-b63d-7702fd69eb16            |
+--------------------------------+-------------------------------------------------+
```
# Volume Migration (host assisted)
# 展示free_capacity_gb
```
$ cinder get-pools --detail
WARNING:cinderclient.shell:API version 3.68 requested,
WARNING:cinderclient.shell:downgrading to 3.64 based on server support.
+-----------------------------+------------------------------------------------------------------------------+
| Property                    | Value                                                                        |
+-----------------------------+------------------------------------------------------------------------------+
| allocated_capacity_gb       | 10                                                                           |
| backend_state               | up                                                                           |
| driver_version              | 1.2.0                                                                        |
| filter_function             | None                                                                         |
| free_capacity_gb            | 379.14                                                                       |
| goodness_function           | None                                                                         |
| location_info               | ceph:/etc/ceph/ceph.conf:c41486d4-4b61-11f0-8c60-f964ce3749b9:cinder:volumes |
| max_over_subscription_ratio | 2.0                                                                          |
| multiattach                 | True                                                                         |
| name                        | sharehost@volumes#volumes                                                    |
| replication_enabled         | False                                                                        |
| reserved_percentage         | 0                                                                            |
| storage_protocol            | ceph                                                                         |
| thin_provisioning_support   | True                                                                         |
| timestamp                   | 2025-07-07T07:46:32.187456                                                   |
| total_capacity_gb           | 379.72                                                                       |
| vendor_name                 | Open Source                                                                  |
| volume_backend_name         | volumes                                                                      |
+-----------------------------+------------------------------------------------------------------------------+
+-----------------------------+----------------------------+
| Property                    | Value                      |
+-----------------------------+----------------------------+
| QoS_support                 | False                      |
| allocated_capacity_gb       | 41                         |
| driver_version              | 1.4.0                      |
| filter_function             | None                       |
| free_capacity_gb            | 592.6661415100098          |
| goodness_function           | None                       |
| max_over_subscription_ratio | 2.0                        |
| name                        | sharehost@ocfs2#ocfs2      |
| provisioned_capacity_gb     | 0.0                        |
| reserved_percentage         | 0                          |
| sparse_copy_volume          | True                       |
| storage_protocol            | ocfs2                      |
| thick_provisioning_support  | False                      |
| thin_provisioning_support   | True                       |
| timestamp                   | 2025-07-07T07:45:52.079569 |
| total_capacity_gb           | 599.98828125               |
| vendor_name                 | Open Source                |
| volume_backend_name         | ocfs2                      |
+-----------------------------+----------------------------+
```
