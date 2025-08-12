[Bcache详解及踩坑记录](https://zhuanlan.zhihu.com/p/682979765)

# 优势
- bcache的优势在于可以将SSD资源池化，一块SSD可对应多块HDD，形成一个缓存池
- 支持从缓存池中划出瘦分配的纯flash卷（thin-flash LUN）单独使用。
- 采用一棵相对标准的B+树作为索引，命中率会有很大提高
# 创建Bcache设备
创建bcache设备需要用到[bcache-tools](https://zhida.zhihu.com/search?content_id=239903397&content_type=Article&match_order=1&q=bcache-tools&zhida_source=entity)工具，ubuntu和suse中默认已安装bcache-tool工具。

```
root@ceph-1:~# apt install bcache-tools -y  ##安装bcache工具，ubuntu及suse默认已安装
root@ceph-1:~# modprobe bcache  ##导入模块
root@ceph-1:~# lsmod |grep bcache
bcache                245760  0
crc64                  16384  1 bcache
root@ceph-1:~# make-bcache -C /dev/nvme0n1 -B /dev/sdc --writeback ##-C指定s
```
通常系统会自动注册cache设备和backing设备并自动生成虚拟的bcache设备/dev/bcache0
- 如果udev规则没有安装，则需要手动注册设备
```text
root@ceph-1:~# echo /dev/nvme0n1 > /sys/fs/bcache/register

root@ceph-1:~# echo /dev/sdb > /sys/fs/bcache/register
```
- 如果udev规则没有安装，还需要手动将cache设备挂载到backing设备上，否则高速缓存不会介入。
```text
root@ceph-1:~# echo <cache-set-UUID> > /sys/block/bcache0/bcache/attach
```
`<cache-set-UUID>`是在格式化cache设备时为高速缓存设备生成的全局唯一标识，可以在/sys/fs/bcache/目录下查找到。
创建完成后可以看到有bcacheX命名的盘，bcache盘是一个裸设备，可以直接格式化使用。
```
root@yzcm003:/home/cmadmin# lsblk
NAME                      MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
loop0                       7:0    0  63.9M  1 loop /snap/core20/2105
loop1                       7:1    0  63.7M  1 loop /snap/core20/2496
loop2                       7:2    0    87M  1 loop /snap/lxd/27037
loop3                       7:3    0  89.4M  1 loop /snap/lxd/31333
loop4                       7:4    0  40.4M  1 loop /snap/snapd/20671
loop5                       7:5    0  44.4M  1 loop /snap/snapd/23771
sda                         8:0    0   3.6T  0 disk
└─bcache1                 252:128  0   3.6T  0 disk
sdb                         8:16   0   3.6T  0 disk
└─bcache0                 252:0    0   3.6T  0 disk
sdc                         8:32   0 222.6G  0 disk
├─sdc1                      8:33   0     1M  0 part
├─sdc2                      8:34   0     2G  0 part /boot
└─sdc3                      8:35   0 220.6G  0 part
  └─ubuntu--vg-ubuntu--lv 253:0    0 220.6G  0 lvm  /
sdd                         8:48   0 447.1G  0 disk
├─bcache4                 252:512  0   200G  0 disk
└─bcache5                 252:640  0   3.6T  0 disk
sde                         8:64   0 447.1G  0 disk
├─bcache1                 252:128  0   3.6T  0 disk
└─bcache2                 252:256  0   200G  0 disk
sdf                         8:80   0   3.6T  0 disk
└─bcache5                 252:640  0   3.6T  0 disk
sdg                         8:96   0 447.1G  0 disk
├─bcache0                 252:0    0   3.6T  0 disk
└─bcache3                 252:384  0   200G  0 disk
sdh                         8:112  0   3.6T  0 disk
sdi                         8:128  0 447.1G  0 disk
```
指定慢速盘查看bcache信息，其中dev.uuid是后端盘符的uuid，cset是前端加速盘的uuid
```
root@yzcm003:/home/cmadmin# bcache-super-show /dev/sda|grep cset
cset.uuid               12d63ac5-d0f0-47d3-95cc-051666638598
root@yzcm003:/home/cmadmin# echo "12d63ac5-d0f0-47d3-95cc-051666638598" > /sys/block/bcache1/bcache/detach
root@yzcm003:/home/cmadmin# echo 1 > /sys/fs/bcache/12d63ac5-d0f0-47d3-95cc-051666638598/unregister
root@yzcm003:/home/cmadmin# echo 1 > /sys/block/bcache1/bcache/stop
root@yzcm003:/home/cmadmin# lsblk
NAME                      MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
loop0                       7:0    0  63.9M  1 loop /snap/core20/2105
loop1                       7:1    0  63.7M  1 loop /snap/core20/2496
loop2                       7:2    0    87M  1 loop /snap/lxd/27037
loop3                       7:3    0  89.4M  1 loop /snap/lxd/31333
loop4                       7:4    0  40.4M  1 loop /snap/snapd/20671
loop5                       7:5    0  44.4M  1 loop /snap/snapd/23771
sda                         8:0    0   3.6T  0 disk
sdb                         8:16   0   3.6T  0 disk
└─bcache0                 252:0    0   3.6T  0 disk
sdc                         8:32   0 222.6G  0 disk
├─sdc1                      8:33   0     1M  0 part
├─sdc2                      8:34   0     2G  0 part /boot
└─sdc3                      8:35   0 220.6G  0 part
  └─ubuntu--vg-ubuntu--lv 253:0    0 220.6G  0 lvm  /
sdd                         8:48   0 447.1G  0 disk
├─bcache4                 252:512  0   200G  0 disk
└─bcache5                 252:640  0   3.6T  0 disk
sde                         8:64   0 447.1G  0 disk
sdf                         8:80   0   3.6T  0 disk
└─bcache5                 252:640  0   3.6T  0 disk
sdg                         8:96   0 447.1G  0 disk
├─bcache0                 252:0    0   3.6T  0 disk
└─bcache3                 252:384  0   200G  0 disk
sdh                         8:112  0   3.6T  0 disk
sdi                         8:128  0 447.1G  0 disk
```
# bcache清理
每一块低速盘(HDD)在经过make-bcache后, 都会对应一个bcacheX设备, 删除低速盘就是将对应的bcacheX设备停掉(删掉). 如果该bcacheX设备有绑定前端盘(SSD), 需要先解除与前端盘的关联。
```text
root@node3:~# lsblk |grep sda -A 1 ##确定要删除bcache盘的后端盘
sda                       8:0    0  14.6T  0 disk
└─bcache0               252:0    0  14.6T  0 disk
root@node3:~# bcache-super-show /dev/sda|grep cset  ##获取到cset
cset.uuid               ae6bf031-bd04-47f5-a273-48f2dd514268
root@node3:~# echo "ae6bf031-bd04-47f5-a273-48f2dd514268" > /sys/block/bcache0/bcache/detach  ##通过获取到的cset.uuid移除bcache0的绑定
root@node3:~# echo 1 >/sys/fs/bcache/ae6bf031-bd04-47f5-a273-48f2dd514268/unregister
root@node3:~# echo 1 > /sys/block/bcache0/bcache/stop
```
#  参数修改
bcache大部分参数都是开箱即用的，Bcache默认参数能覆盖90%以上的场景，不过有几个参数我们仍然可以进行调整。
```text
echo "writeback" > /sys/block/bcache$i/bcache/cache_mode
echo $((1024*1024)) > /sys/block/bcache$i/bcache/sequential_cutoff
echo 40 > /sys/block/bcache$i/bcache/writeback_percent
```
- cache_mode，设置BCache设备的缓存模式为"writeback"
- 设置BCache设备的顺序截断（sequential cutoff）。顺序截断是指当连续IO操作的大小超过该阈值时，BCache会将这些IO操作视为顺序访问，而不会缓存到缓存中。这有助于避免将大量连续IO操作缓存到BCache中，保持缓存的有效性。这里的1024*1024表示1MB。
- 设置BCache设备的写回百分比（writeback percent）。写回百分比表示当缓存容量占用达到一定百分比时，BCache会开始执行写回操作。


# tips
## 内核版本
bcache在3.10版本开始合并到官方内核。在5.4内核版本中逐渐趋于稳定，在5.15和5.19版本中修复了部分问题。因此如果需要使用bcache建议选择这几个内核的最新版本。
## bcache设备盘符变化问题
有多个bcache设备的情况下，在系统重启后，bcache的盘符会发生变化，如bcache0重启后可能变为bcache1或bcache2。因此在创建好bcache设备后，不要直接mount bcache设备，而是应该通过bcache设备格式化后的文件系统uuid来进行mount。
```text
root@node1:~# blkid |grep bcache0
/dev/bcache0: UUID="ed31e3ca-9bec-44bb-aa1a-a2a3ed428cb2" TYPE="xfs"
```
## 垃圾回收（gc）对性能的影响
由于Bcache在存储数据和元数据时，均是追加写，因此需要及时进行垃圾回收。将不再使用的存储空间回收后，供后续申请使用。
每写入缓存总容量1/16的数据量后，会触发一次垃圾回收操作。bcache 自身的 gc 操作，会停止前端 io 的下发。可以通过以下方式观察bcache的gc操作
```text
root@node1:~# grep . /sys/block/bcache0/bcache/cache/internal/btree_gc_*
/sys/block/bcache0/bcache/cache/internal/btree_gc_average_duration_ms:1762
/sys/block/bcache0/bcache/cache/internal/btree_gc_average_frequency_sec:66602
/sys/block/bcache0/bcache/cache/internal/btree_gc_last_sec:16437
/sys/block/bcache0/bcache/cache/internal/btree_gc_max_duration_ms:3588
```
- btree_gc_average_duration_ms（平均持续时间）：
- 较小的值表示平均每次垃圾收集操作的执行速度较快。
- 这个值表示每次B树垃圾收集操作的平均持续时间，单位是毫秒（ms）。
- btree_gc_average_frequency_sec（平均频率）：
- 较小的值表示平均垃圾收集操作执行的频率较低，系统压力较小。
- 这个值表示B树垃圾收集操作的平均执行频率，单位是秒（sec）。
- btree_gc_last_sec（最近一次垃圾收集操作的持续时间）：
- 它提供了最近一次垃圾收集操作的执行效率。
- 这个值表示最近一次B树垃圾收集操作的持续时间，单位是秒（sec）。
- btree_gc_max_duration_ms（最大持续时间）：
- 较小的值表示没有出现非常耗时的垃圾收集操作。
- 这个值表示所有垃圾收集操作中的最大持续时间，单位是毫秒（ms）。
## cache的容量设置为多少合适
需要根据具体的业务场景来看，cache内主要存储的是热数据，容量设置为backing设备的10% ~ 20%都是可以的。
## cache容量使用完后对性能的影响
cache容量不会被使用完，因为为了防止出现死锁，bcache限制了脏数据最多只能使用cache 70%的容量。随着容量的使用，对性能的影响有以下几个区别。
- 当脏数据达到writeback_percent设置的值时
writeback线程将启动，将cache设备上的脏数据写入到backing设备，并限制前端的写入速度。回写的带宽由软件模拟的比例微分积分控制器动态控制。脏数据越多，分配给回写线程的带宽越多，则业务IO的的性能会逐步降低。
- 当脏数据超过缓存设备容量40%时
部分IO请求会跳过缓存直接访问后端设备。这会进一步降低业务IO性能来确保回写线程能够处理更多脏数据。
- 当脏数据容量超过缓存容量70%时
后续业务IO将不再进入缓存设备，避免过多脏数据导致垃圾回收和空间分配算法死锁。
## cache设备故障如何修复
当cache对应的ssd或nvme设备损坏，且cache设备上还有脏数据未写回到backing设备时。这部分cache上的数据可能会丢失。因此为了保证数据安全，需要业务层具有相应的备份策略，例如ceph的副本策略等。
## bcache的性能是否和cache所用的ssd或nvme性能相当
大家可能会想，既然在writeback模式下，数据是直接写到cache设备中的，那是不是相当于我只要使用bcahe就能获得和ssd或nvme设备相当的性能呢。不是这样的，因为对于I/O请求，bcache都需要更新B+tree索引、缓存数据、写journal，可能还需要分裂B+tree节点，并且I/O过程中还会受到gc和脏数据写回的影响。因此比起ssd或nvme设备，bcache性能会差一些，但是比起纯HDD设备，也已经足够好了。
# 历史配置参考
以一对ssd+hdd为例，ssd盘符为`/dev/sda``hdd`盘符为`/dev/sdb`，生成的`bcache`设备为`bcache0`
```bash
$ sudo wipefs -a /dev/sda /dev/sdb
$ uuidgen #获取要设置的csetuuid
$ sudo make-bcache --bucket 2M --block 512 --writeback --cset-uuid 124d59e1-530a-4981-80a9-441c2cb7bfc0 -C /dev/sda-B /dev/sdb
$ sudo echo100G > /sys/fs/bcache/124d59e1-530a-4981-80a9-441c2cb7bfc0/flash_vol_create
$ echo 40 > /sys/block/bcache0/bcache/writeback_percent
$ echo 0 > /sys/block/bcache0/bcache/sequential_cutoff
```