# Backing device 块大小对齐
bucket和block大小与缓存设备的擦除大小和HDD盘的扇区大小一致
# 写入模式
- writethrough 数据同时写入缓存和后端存储，两者都完成后才返回写操作成功。
- ===**writeback**=== 数据先写入缓存，仅当缓存块被替换（淘汰）时，才将修改过的数据同步到后端存储。 
- writearound 数据直接写入后端存储，不写入缓存；仅当后续读取该数据时，才将其加载到缓存。
- none 无缓存效果
# 跳过顺序的IO
sequential_cutoff，为0时表示不跳过，默认参数4M，调整 backing 设备的连续 IO 阈值，表示 bcache0 设备的连续 IO 大于 4MB 时，大于 4MB 的部分不会走 SSD 设备，也不会缓存到 ssd，而是直接读写 backing 设备。
# 监控io耗时
bcache会跟踪每个IO，如果IO的时间超过阈值，则绕过cache设备，直接读写backing设备。
- `congested_read_threshold_us`
- `congested_write_threshold_us`
为0时跳过跟踪
# cache设备异常
cache设备的io错误过多时自动切换至writethrough模式，关闭cache设备
# 后端设备参数
- attach `echo <cset-uuid>` 到此文件将缓存设备连接到后端设备
- cache_mode 写入模式
- clear_stats 重置统计数据
- detach 分离cache设备
- dirty_data cache设备中缓存的数据
- label 底层设备名称
- readahead 对cache未命中的数据预读大小
- running 置1后可以无cache设备的状态下运行
- sequential_merge
- state
	- no cache
	- clean
	- dirty
	- inconsistent
- stop
- writeback_delay
- writeback_percent
- writeback_rate
- writeback_running 如果关闭，则不会发生脏数据的回写，模拟所有缓存命中的场景
## 后端设备状态
绕过缓存的 IO (读取和写入)量
- bypassed
部分 hit 会被计为 miss
- cache_hits
- cache_misses
- cache_hit_ratio 
绕过缓存的 IO 的 hit 和 miss 的计数
- cache_bypass_hits
- cache_bypass_misses

- cache_miss_collisions
预读次数计数
- cache_readaheads
btree中每个键的平均大小
- average_key_size
- block_size
- btree_cache_size
- bucket_size
- cache_available_percent
- clear_stats
- dirty_data
echo 一个大小到这个文件,（以人类可读的单位，k/M/G）创建一个由缓存集支持的精简卷
- flash_vol_create
- io_error_halflife
- io_error_limit
日志写入延迟几毫秒,除非缓存刷新发生得更早。默认值为100
- journal_delay_ms
正在使用的 root btree节点的百分比,如果这太高，节点会分裂，增加树的深度。(default:2)
- root_usage_percent
置1此文件，直到所有连接的后备设备都已关闭后关闭缓存集,
- stop
分离所有 backing device 并关闭缓存设备,如果存在脏数据,将禁用回写式缓存并等待被刷写
- unregister
## cache设备状态
- active_journal_entries
- btree_nodes
- btree_used_percent
- bset_tree_stats
- btree_cache_max_chain
- cache_read_races
强制垃圾回收
- trigger_gc
- CACHE DEVICE
- block_size
- btree_written
- bucket_size
cache刷新策略
- cache_replacement_policy
- discard
- freelist_percent
- io_errors
- metadata_written
- nbuckets
- priority_stats
- written



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
 
 # 奇技淫巧Howto/cookbook

 A)从一个丢失的caching设备启动bcache

 假如注册后端设备毫无帮助，事已至此，你只需强制它在无cache设备的情况下运行

 host:~# echo /dev/sdb1 > /sys/fs/bcache/register[ 119.844831] bcache: register_bcache() error opening /dev/sdb1: device already registered

 下一步，如果有cache设备的话，你尽量注册它，然而假如没有，或是cache设备因为某种原因注册失败，在没有cache设备的情况下，你仍然能够启动bcache，像下面这样

 host:/sys/block/sdb/sdb1/bcache# echo 1 > running

 注意如果当前在writeback模式下，这会导致数据丢失。

 
 

 B)bcache找不到cache

 host:/sys/block/md5/bcache# echo 0226553a-37cf-41d5-b3ce-8b1e944543a8 > attach[ 1933.455082] bcache: bch_cached_dev_attach() Couldn't find uuid for md5 in set[ 1933.478179] bcache: __cached_dev_store() Can't attach 0226553a-37cf-41d5-b3ce-8b1e944543a8[ 1933.478179] : cache set not found

 这种情况，cache设备可能在系统启动时没有注册，或是消失后又回来了，需要再次注册一下

 host:/sys/block/md5/bcache# echo /dev/sdh2 > /sys/fs/bcache/register

 
 

 C)损坏的bcache在设备注册期间将kernel搞崩溃

 这不会发生，如果真发生了，那就是bug！请报告给bcache开发小组:

 linux-bcache@vger.kernel.org

 务必提供更多的信息，包括kernel dmesg输出，以便我们可以参考

 
 

 D)没有bcache的情况下恢复数据

 假如kernel中找不到bcache模块，后端设备的filesystem仍然可以在8KB偏移处找到。所以，要么通过后端设备的loopdev，使用--offset 8K生成的，或者是，当你最初使用make-cache来格式化bache时，--data-offset定义的其它值

 比如：

 losetup -o 8192 /dev/loop0 /dev/your_bcache_backing_dev

 这应该会将后端设备中的数据原封不动地复制到/dev/loop0中。

 
 

 如果你的缓存是在writethrough模式下，你可以安全地扔掉cache设备了，此时不会丢失任何数据。

 
 

 E)擦除缓存设备

 ::host:~# wipefs -a /dev/sdh216 bytes were erased at offset 0x1018 (bcache)they were: c6 85 73 f6 4e 1a 45 ca 82 65 f5 7f 48 ba 6d 81

 在重启系统后，开启bcache，重建cache设备并开启它：

 host:~# make-bcache -C /dev/sdh2UUID: 7be7e175-8f4c-4f99-94b2-9c904d227045Set UUID: 5bc072a8-ab17-446d-9744-e247949913c1version: 0nbuckets: 106874block_size: 1bucket_size: 1024nr_in_set: 1nr_this_dev: 0first_bucket: 1[ 650.511912] bcache: run_cache_set() invalidating existing data[ 650.549228] bcache: register_cache() registered cache device sdh2

 在无cache的情况下，开启后端设备

 host:/sys/block/md5/bcache# echo 1 > running

 绑定新cache设备

 host:/sys/block/md5/bcache# echo 5bc072a8-ab17-446d-9744-e247949913c1 > attach[ 865.276616] bcache: bch_cached_dev_attach() Caching md5 as bcache0 on set 5bc072a8-ab17-446d-9744-e247949913c1

 
 

 
 

 F)移除或是替换缓存设备

 host:/sys/block/sda/sda7/bcache# echo 1 > detach[ 695.872542] bcache: cached_dev_detach_finish() Caching disabled for sda7host:~# wipefs -a /dev/nvme0n1p4wipefs: error: /dev/nvme0n1p4: probing initialization failed: Device or resource busyOoops, it's disabled, but not unregistered, so it's still protected我们需要取消注册host:/sys/fs/bcache/b7ba27a1-2398-4649-8ae3-0959f57ba128# ls -l cache0lrwxrwxrwx 1 root root 0 Feb 25 18:33 cache0 -> ../../../devices/pci0000:00/0000:00:1d.0/0000:70:00.0/nvme/nvme0/nvme0n1/nvme0n1p4/bcache/host:/sys/fs/bcache/b7ba27a1-2398-4649-8ae3-0959f57ba128# echo 1 > stopkernel: [ 917.041908] bcache: cache_set_free() Cache set b7ba27a1-2398-4649-8ae3-0959f57ba128 unregistered现在我们才能清除数据host:~# wipefs -a /dev/nvme0n1p4/dev/nvme0n1p4: 16 bytes were erased at offset 0x00001018 (bcache): c6 85 73 f6 4e 1a 45 ca 82 65 f5 7f 48 ba 6d 81

 
 

 G)dm-crypt和bcache

 首先搭建非加密的bcache，然后在/dev/bcache<N>上层安装dmcrypt，这会更快，假如你dmcrypt后端和caching设备的时候，同时在顶层安装bcache。[需要benchmarks?]

 
 

 H)停止并释放一个注册过的bcache，擦除之后重新生成

 假如你需要释放所有的bcache引用，以便你能运行fdisk并重新注册一个变动的分区表，假如上面有任何活跃的backing或是caching设备，这都不会成功

 1)/dev/bcache*有东西吗？(有时没有)

 假如存在，很简单：

 host:/sys/block/bcache0/bcache# echo 1 > stop

 2)但是假如你的后端设备不在，这不会成功:

 host:/sys/block/bcache0# cd bcachebash: cd: bcache: No such file or directory

 这种情况，你或许必须注销引用该待释放bcache的dmcrypt的块设备

 host:~# dmsetup remove oldds1bcache: bcache_device_free() bcache0 stoppedbcache: cache_set_free() Cache set 5bc072a8-ab17-446d-9744-e247949913c1 unregistered

 这导致后端bcache从/sys/fs/bcache中移除，然后再次重用，This would be true of any block device stacking where bcache is a lower device.

 
 

 3)另一方面，你也能看看/sys/fs/bcache/

 host:/sys/fs/bcache# 
 ls -l */{cache?,bdev?}lrwxrwxrwx 1 root root 0 Mar 5 09:39 0226553a-37cf-41d5-b3ce-8b1e944543a8/bdev1 -> ../../../devices/virtual/block/dm-1/bcache/lrwxrwxrwx 1 root root 0 Mar 5 09:39 0226553a-37cf-41d5-b3ce-8b1e944543a8/cache0 -> ../../../devices/virtual/block/dm-4/bcache/lrwxrwxrwx 1 root root 0 Mar 5 09:39 5bc072a8-ab17-446d-9744-e247949913c1/cache0 -> ../../../devices/pci0000:00/0000:00:01.0/0000:01:00.0/ata10/host9/target9:0:0/9:0:0:0/block/sdl/sdl2/bcache/

 设备名将显示哪个UUID是相关的，在该目录下cd，然后stop the cache:

 host:/sys/fs/bcache/5bc072a8-ab17-446d-9744-e247949913c1# echo 1 > stop

 这将会释放bcache引用，让你重用其它目的的分区 