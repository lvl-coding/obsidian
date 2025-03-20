1.  停止验证的目标OSD服务

```
$ sudo systemctl stop ceph-976f532a-03cc-11f0-a751-3cecef812104@osd.19.service
```

2.  对新加盘/dev/vdc初始化为物理卷，以便LVM使用

3.  查看环境中lvm设备的信息

4.  向需要扩容的OSD LVM卷组添加物理卷，动态扩展LVM卷组增加容量

5.  调整OSD LVM逻辑卷空间大小，增长为占用所有可用空间

6.  进入集群docker环境，并将待处理的OSD 运行文件映射到docker目录内

7.  Bluefs刷新检查其块设备的可用大小，如果块设备被扩容则可以使用新加入的空间

8.  查看OSD Bluestore和Bluefs的信息，空间大小已更新

9.  重新启动OSD服务，确认OSD服务正常启动，空间已扩展

```
$ sudo pvcreate /dev/vdc
  Physical volume "/dev/vdc" successfully created.

kvirtadm@cluster90:~$ sudo vgextend ceph-f273a343-e084-43cf-86b1-06cd2225eee5 /dev/vdc
  Volume group "ceph-f273a343-e084-43cf-86b1-06cd2225eee5" successfully extended

root@cluster90:~# lvresize -l +100%FREE /dev/ceph-f273a343-e084-43cf-86b1-06cd2225eee5/osd-block-36c1b631-26d1-4660-8c0e-79f839b1cbbd
  Size of logical volume ceph-f273a343-e084-43cf-86b1-06cd2225eee5/osd-block-36c1b631-26d1-4660-8c0e-79f839b1cbbd changed from <200.00 GiB (51199 extents) to 399.99 GiB (102398 extents).
  Logical volume ceph-f273a343-e084-43cf-86b1-06cd2225eee5/osd-block-36c1b631-26d1-4660-8c0e-79f839b1cbbd successfully resized.

root@cluster90:~# cephadm shell --mount /var/lib/ceph/960c0bc4-0433-11f0-b5c2-59cfc57e8ebc/osd.0:/var/lib/ceph/osd/ceph-0/
Inferring fsid 960c0bc4-0433-11f0-b5c2-59cfc57e8ebc
Using recent ceph image ceph/daemon@sha256:261bbe628f4b438f5bf10de5a8ee05282f2697a5a2cb7ff7668f776b61b9d586

root@cluster90:~# ceph-bluestore-tool bluefs-bdev-expand --path /var/lib/ceph/osd/ceph-0/
inferring bluefs devices from bluestore path
1 : device size 0x63ff800000 : using 0x32655f6000(202 GiB)
Expanding DB/WAL...
1 : expanding  from 0x31ffc00000 to 0x63ff800000
2025-03-20T02:48:25.489+0000 7feb55fed3c0 -1 bluestore(/var/lib/ceph/osd/ceph-0) _read_bdev_label failed to read from /var/lib/ceph/osd/ceph-0: (21) Is a directory
2025-03-20T02:48:25.489+0000 7feb55fed3c0 -1 bluestore(/var/lib/ceph/osd/ceph-0) unable to read label for /var/lib/ceph/osd/ceph-0: (21) Is a directory
root@cluster90:~# ceph-bluestore-tool show-label --path /var/lib/ceph/osd/ceph-0/
inferring bluefs devices from bluestore path
{
    "/var/lib/ceph/osd/ceph-0/block": {
        "osd_uuid": "36c1b631-26d1-4660-8c0e-79f839b1cbbd",
        "size": 214744170496,
        "btime": "2025-03-18T20:14:21.274474+0000",
        "description": "main",
        "bfm_blocks": "104855552",
        "bfm_blocks_per_key": "128",
        "bfm_bytes_per_block": "4096",
        "bfm_size": "429488340992",
        "bluefs": "1",
        "ceph_fsid": "960c0bc4-0433-11f0-b5c2-59cfc57e8ebc",
        "kv_backend": "rocksdb",
        "magic": "ceph osd volume v026",
        "mkfs_done": "yes",
        "osd_key": "AQAa1Nln/wibFxAASnRZtne6ULKjnxBgnigmPA==",
        "osdspec_affinity": "None",
        "ready": "ready",
        "require_osd_release": "16",
        "whoami": "0"
    }
}
root@cluster90:~# ceph-bluestore-tool bluefs-stats --path /var/lib/ceph/osd/ceph-0/
1 : device size 0x63ff800000 : using 0x336e6000(823 MiB)
wal_total:0, db_total:408013923942, slow_total:0

root@cluster90:~# systemctl restart ceph-960c0bc4-0433-11f0-b5c2-59cfc57e8ebc@osd.0.service
root@cluster90:~# ceph osd df tree
ID  CLASS  WEIGHT   REWEIGHT  SIZE     RAW USE  DATA     OMAP     META     AVAIL    %USE  VAR   PGS  STATUS  TYPE NAME
-1         0.58589         -  800 GiB  4.0 GiB  2.3 GiB    3 KiB  1.6 GiB  796 GiB  0.50  1.00    -          root default
-3         0.19530         -  400 GiB  824 MiB  800 MiB    3 KiB   24 MiB  399 GiB  0.20  0.40    -              host cluster90
 0    hdd  0.19530   1.00000  400 GiB  824 MiB  800 MiB    3 KiB   24 MiB  399 GiB  0.20  0.40  129      up          osd.0
-5         0.19530         -  200 GiB  1.6 GiB  800 MiB      0 B  823 MiB  198 GiB  0.79  1.59    -              host cluster91
 1    hdd  0.19530   1.00000  200 GiB  1.6 GiB  800 MiB      0 B  823 MiB  198 GiB  0.79  1.59  129      up          osd.1
-7         0.19530         -  200 GiB  1.6 GiB  800 MiB      0 B  827 MiB  198 GiB  0.79  1.60    -              host cluster92
 2    hdd  0.19530   1.00000  200 GiB  1.6 GiB  800 MiB      0 B  827 MiB  198 GiB  0.79  1.60  129      up          osd.2
                       TOTAL  800 GiB  4.0 GiB  2.3 GiB  3.2 KiB  1.6 GiB  796 GiB  0.50
MIN/MAX VAR: 0.40/1.60  STDDEV: 0.30

root@cluster90:~# sudo cephadm shell -- ceph-volume lvm list
Inferring fsid 960c0bc4-0433-11f0-b5c2-59cfc57e8ebc
Using recent ceph image ceph/daemon@sha256:261bbe628f4b438f5bf10de5a8ee05282f2697a5a2cb7ff7668f776b61b9d586


====== osd.0 =======

  [block]       /dev/ceph-f273a343-e084-43cf-86b1-06cd2225eee5/osd-block-36c1b631-26d1-4660-8c0e-79f839b1cbbd

      block device              /dev/ceph-f273a343-e084-43cf-86b1-06cd2225eee5/osd-block-36c1b631-26d1-4660-8c0e-79f839b1cbbd
      block uuid                2GFYPn-i6FD-p20B-xmvr-Z4ff-mOy1-cptshI
      cephx lockbox secret
      cluster fsid              960c0bc4-0433-11f0-b5c2-59cfc57e8ebc
      cluster name              ceph
      crush device class        None
      encrypted                 0
      osd fsid                  36c1b631-26d1-4660-8c0e-79f839b1cbbd
      osd id                    0
      osdspec affinity          None
      type                      block
      vdo                       0
      devices                   /dev/vdb,/dev/vdc

```