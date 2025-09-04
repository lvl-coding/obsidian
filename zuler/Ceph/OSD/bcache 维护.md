# 更换cache设备
1. 解绑cache设备，这个过程需要一定时间，cache会将脏数据刷写入后端设备，刷写完成后查看设备状态
 ```bash
$ echo 1 > /sys/block/bcache<id>/bcache/detach
$ bcache-super-show /dev/sdc
sb.magic		ok
sb.first_sector		8 [match]
sb.csum			D964FC89D79C4C4D [match]
sb.version		1 [backing device]

dev.label		(empty)
dev.uuid		77a61f14-cd8f-4723-bcca-41aa558a9d3d
dev.sectors_per_block	1
dev.sectors_per_bucket	4096
dev.data.first_sector	16
dev.data.cache_mode	1 [writeback]
dev.data.cache_state	0 [detached]

cset.uuid		00000000-0000-0000-0000-000000000000
 ```
2. 创建cache设备，已有则跳过步骤
```bash
$ make-bcache --bucket 2M --block 512 --writeback -C /dev/sde
```
3. 查看cache设备uuid
```bash
$ bcache-super-show /dev/sde
sb.magic		ok
sb.first_sector		8 [match]
sb.csum			FAB6746D39DA11E0 [match]
sb.version		3 [cache device]

dev.label		(empty)
dev.uuid		b7485749-8a88-40f7-b439-fbeee4cb57a3
dev.sectors_per_block	1
dev.sectors_per_bucket	4096
dev.cache.first_sector	4096
dev.cache.cache_sectors	3750744064
dev.cache.total_sectors	3750748160
dev.cache.ordered	yes
dev.cache.discard	no
dev.cache.pos		0
dev.cache.replacement	0 [lru]

cset.uuid		acf14f92-20fa-43f3-90b0-50ac9d1fec1f
```
4. 将cache设备的uuid与原有`bcache`后端设备绑定
```bash
$ echo "acf14f92-20fa-43f3-90b0-50ac9d1fec1f" > /sys/block/bcache<id>/bcache/attach
```
# 从一个丢失的cache设备启动bcache
 假如注册后端设备毫无帮助，事已至此，你只需强制它在无cache设备的情况下运行
 ```bash
 $ echo /dev/sdb1 > /sys/fs/bcache/register
[  119.844831] bcache: register_bcache() error opening /dev/sdb1: device already registered
 ```
下一步，如果有cache设备的话，你尽量注册它，然而假如没有，或是cache设备因为某种原因注册失败，在没有cache设备的情况下，你仍然能够启动bcache，像下面这样
```
$ echo 1 > /sys/block/bcache0/bcache/running
```
 注意如果当前在writeback模式下，这会导致数据丢失。
# bcache找不到cache
```
$ echo 0226553a-37cf-41d5-b3ce-8b1e944543a8 > /sys/block/bcache<id>/bcache/attach
[ 1933.455082] bcache: bch_cached_dev_attach() Couldn't find uuid for md5 in set
[ 1933.478179] bcache: __cached_dev_store() Can't attach 0226553a-37cf-41d5-b3ce-8b1e944543a8
[ 1933.478179] : cache set not found
```
这种情况，cache设备可能在系统启动时没有注册，或是消失后又回来了，需要再次注册一下
```
$ echo /dev/sdh2 > /sys/fs/bcache/register
```
# 没有bcache的情况下恢复数据
假如kernel中找不到bcache模块，后端设备的filesystem仍然可以在8KB偏移处找到。所以，要么通过后端设备的loopdev，使用--offset 8K生成的，或者是，当你最初使用make-cache来格式化bache时，--data-offset定义的其它值
比如：
```
$ losetup -o 8192 /dev/loop0 /dev/your_bcache_backing_dev
```
这应该会将后端设备中的数据原封不动地复制到/dev/loop0中。如果你的缓存是在writethrough模式下，你可以安全地放弃cache设备了，此时不会丢失任何数据。
# 擦除缓存设备
```
$ wipefs -a /dev/sdh
216 bytes were erased at offset 0x1018 (bcache)
they were: c6 85 73 f6 4e 1a 45 ca 82 65 f5 7f 48 ba 6d 81
```
在重启系统后，开启bcache，重建cache设备并开启它：
```
$ make-bcache -C /dev/sdh2
UUID:                   7be7e175-8f4c-4f99-94b2-9c904d227045
Set UUID:               5bc072a8-ab17-446d-9744-e247949913c1
version:                0
nbuckets:               106874
block_size:             1
bucket_size:            1024
nr_in_set:              1
nr_this_dev:            0
first_bucket:           1
[  650.511912] bcache: run_cache_set() invalidating existing data
[  650.549228] bcache: register_cache() registered cache device sdh2
```
在无cache的情况下，开启后端设备
```
$ echo 1 > /sys/block/md5/bcache/running
```
绑定新cache设备
```
$ echo 5bc072a8-ab17-446d-9744-e247949913c1 > attach
[  865.276616] bcache: bch_cached_dev_attach() Caching md5 as bcache0 on set 5bc072a8-ab17-446d-9744-e247949913c1
```
#  参数配置
bcache大部分参数都是开箱即用的，Bcache默认参数能覆盖90%以上的场景，不过有几个参数我们仍然可以进行调整。
```bash
$ echo "writeback" > /sys/block/bcache<id>/bcache/cache_mode
$ echo 0 > /sys/block/bcache<id>/bcache/sequential_cutoff
$ echo 40 > /sys/block/bcache<id>/bcache/writeback_percent
```
- `cache_mode`，设置`bcache`设备的缓存模式为"`writeback`"
- `sequential cutoff`设置`bcache`设备的顺序截断。顺序截断是指当连续IO操作的大小超过该阈值时，BCache会将这些IO操作视为顺序访问，而不会缓存到缓存中。这有助于避免将大量连续IO操作缓存到BCache中，保持缓存的有效性。这里的单位是Byte
- `writeback percent`设置BCache设备的写回百分比。写回百分比表示当缓存容量占用达到一定百分比时，BCache会开始执行写回操作。
## Backing device 块大小对齐
bucket和block大小与缓存设备的擦除大小和HDD盘的扇区大小一致，创建时通过参数指定后续可在cache的配置文件中查看
```bash
$ make-bcache --bucket 2M --block 512
$ cat /sys/fs/bcache/<cache-set-UUID>/block_size
$ cat /sys/fs/bcache/<cache-set-UUID>/bucket_size
```
## 写入模式
- writethrough 数据同时写入缓存和后端存储，两者都完成后才返回写操作成功。
- ==**writeback**== 数据先写入缓存，仅当缓存块被替换（淘汰）时，才将修改过的数据同步到后端存储。 
- writearound 数据直接写入后端存储，不写入缓存；仅当后续读取该数据时，才将其加载到缓存。
- none 无缓存效果
```bash
$ echo writeback > /sys/block/bcache0/bcache/cache_mode
$ cat /sys/block/bcache0/bcache/cache_mode
```
## 跳过顺序的IO
`sequential_cutoff`，为0时表示不跳过，默认参数4M，调整 backing 设备的连续 IO 阈值，表示 bcache0 设备的连续 IO 大于 4MB 时，大于 4MB 的部分不会走 SSD 设备，也不会缓存到 ssd，而是直接读写 backing 设备。
## 监控io耗时
bcache会跟踪每个IO，如果IO的时间超过阈值，则绕过cache设备，直接读写backing设备。为0时跳过跟踪
- `congested_read_threshold_us`
- `congested_write_threshold_us`
## cache设备异常
cache设备的io错误过多时自动切换至`writethrough`模式，关闭cache设备
## 后端设备参数
- `attach` `echo <cset-uuid>` 到此文件将缓存设备连接到后端设备
- `cache_mode` 写入模式
- `clear_stats` 重置统计数据
- `detach` 分离cache设备
- `dirty_data` `cache`设备中缓存的数据
- `label` 底层设备名称
- `readahead` 对`cache`未命中的数据预读大小
- `running` 置1后可以无`cache`设备的状态下运行
- `sequential_merge`
- `state`
	- `no cache`
	- `clean`
	- `dirty`
	- `inconsistent`
- `stop`
- `writeback_delay`
- `writeback_percent`
- `writeback_rate`
- `writeback_running` 如果关闭，则不会发生脏数据的回写，模拟所有缓存命中的场景
## 后端设备状态
绕过缓存的 IO (读取和写入)量
- `bypassed`
部分 hit 会被计为 miss
- `cache_hits`
- `cache_misses`
- `cache_hit_ratio` 
绕过缓存的 IO 的 hit 和 miss 的计数
- `cache_bypass_hits`
- `cache_bypass_misses`
- `cache_miss_collisions`
预读次数计数
- `cache_readaheads`
btree中每个键的平均大小
- `average_key_size`
- `block_size`
- `btree_cache_size`
- `bucket_size`
- `cache_available_percent`
- `clear_stats`
- `dirty_data`
echo 一个大小到这个文件,（以人类可读的单位，`k/M/G`）创建一个由缓存集支持的精简卷
- `flash_vol_create`
cache设备io异常统计
- `io_error_halflife`
- `io_error_limit`
日志写入延迟几毫秒,除非缓存刷新发生得更早。默认值为100
- `journal_delay_ms`
正在使用的 root btree节点的百分比,如果这太高，节点会分裂，增加树的深度。(default:2)
- `root_usage_percent`
置1此文件，直到所有连接的后备设备都已关闭后关闭缓存集,
- `stop`
分离所有 `backing device `并关闭缓存设备,如果存在脏数据,将禁用回写式缓存并等待被刷写
- `unregister`
## cache设备状态
- `active_journal_entries`
- `btree_nodes`
- `btree_used_percent`
- `bset_tree_stats`
- `btree_cache_max_chain`
- `cache_read_races`
强制垃圾回收
- `trigger_gc`
- `CACHE DEVICE`
- `block_size`
- `btree_written`
- `bucket_size`
cache刷新策略
- `cache_replacement_policy`
- `discard`
- `freelist_percent`
- `io_errors`
- `metadata_written`
- `nbuckets`
- `priority_stats`
- `written`

# 内核版本
bcache在3.10版本开始合并到官方内核。在5.4内核版本中逐渐趋于稳定，在5.15和5.19版本中修复了部分问题。因此如果需要使用bcache建议选择这几个内核的最新版本。
# bcache设备盘符变化问题
有多个bcache设备的情况下，在系统重启后，bcache的盘符会发生变化，如bcache0重启后可能变为bcache1或bcache2。因此在创建好bcache设备后，不要直接mount bcache设备，而是应该通过bcache设备格式化后的文件系统uuid来进行mount。
```bash
$ blkid |grep bcache0
/dev/bcache0: UUID="ed31e3ca-9bec-44bb-aa1a-a2a3ed428cb2" TYPE="xfs"
$ mount -t ocfs2 UUID=ed31e3ca-9bec-44bb-aa1a-a2a3ed428cb2 /var/opt/mnt/ocfs2/192.168.10.89_sda -o noatime
```
## 垃圾回收（gc）对性能的影响
由于Bcache在存储数据和元数据时，均是追加写，因此需要及时进行垃圾回收。将不再使用的存储空间回收后，供后续申请使用。
每写入缓存总容量1/16的数据量后，会触发一次垃圾回收操作。bcache 自身的 gc 操作，会停止前端 io 的下发。可以通过以下方式观察bcache的gc操作
```bash
$ grep . /sys/block/bcache0/bcache/cache/internal/btree_gc_*
/sys/block/bcache0/bcache/cache/internal/btree_gc_average_duration_ms:1762
/sys/block/bcache0/bcache/cache/internal/btree_gc_average_frequency_sec:66602
/sys/block/bcache0/bcache/cache/internal/btree_gc_last_sec:16437
/sys/block/bcache0/bcache/cache/internal/btree_gc_max_duration_ms:3588
```
- `btree_gc_average_duration_ms`（平均持续时间）：
	- 值越小表示平均每次垃圾收集操作的执行速度越快。
	- 这个值表示每次B树垃圾收集操作的平均持续时间，单位是毫秒（ms）。
- `btree_gc_average_frequency_sec`（平均频率）：
	- 值越小表示平均垃圾收集操作执行的频率越低，系统压力越小。
	- 这个值表示B树垃圾收集操作的平均执行频率，单位是秒（sec）。
- `btree_gc_last_sec`（最近一次垃圾收集操作的持续时间）：
	- 它提供了最近一次垃圾收集操作的执行效率。
	- 这个值表示最近一次B树垃圾收集操作的持续时间，单位是秒（sec）。
- `btree_gc_max_duration_ms`（最大持续时间）：
	- 值越小表示没有出现非常耗时的垃圾收集操作。
	- 这个值表示所有垃圾收集操作中的最大持续时间，单位是毫秒（ms）。
## cache的容量设置为多少合适
需要根据具体的业务场景来看，cache内主要存储的是热数据，容量设置为backing设备的10% ~ 20%都是可以的。
## cache容量使用完后对性能的影响
cache容量不会被使用完，因为为了防止出现死锁，bcache限制了脏数据最多只能使用cache 70%的容量。随着容量的使用，对性能的影响有以下几个区别。
- 当脏数据达到writeback_percent设置的值时
writeback线程将启动，将cache设备上的脏数据写入到backing设备，并限制前端的写入速度。回写的带宽由bcache动态控制。脏数据越多，分配给回写线程的带宽越多，则业务IO的的性能会逐步降低。
- 当脏数据超过缓存设备容量40%时
部分IO请求会跳过缓存直接访问后端设备。这会进一步降低业务IO性能来确保回写线程能够处理更多脏数据。
- 当脏数据容量超过缓存容量70%时
后续业务IO将不再进入缓存设备，避免过多脏数据导致垃圾回收和空间分配算法死锁。
## cache设备故障如何修复
当cache对应的ssd或nvme设备损坏，且cache设备上还有脏数据未写回到backing设备时。这部分cache上的数据可能会丢失。因此为了保证数据安全，需要业务层具有相应的备份策略，例如ceph的副本策略等。
## bcache的性能是否和cache所用的ssd或nvme性能相当
既然在writeback模式下，数据是直接写到cache设备中的，那是不是相当于只要使用bcahe就能获得和ssd或nvme设备相当的性能呢。不是这样的，因为对于I/O请求，bcache都需要更新B+tree索引、缓存数据、写journal，可能还需要分裂B+tree节点，并且I/O过程中还会受到gc和脏数据写回的影响。因此比起ssd或nvme设备，bcache性能会差一些，但是比起纯HDD设备，也已经足够好了。
# 其他问题
## 损坏的bcache在设备注册期间将kernel搞崩溃
这不会发生，如果真发生了，那就是bug！请报告给bcache开发小组:`linux-bcache@vger.kernel.org`务必提供更多的信息，包括kernel dmesg输出，以便参考
## dm-crypt和bcache
首先搭建非加密的bcache，然后在`/dev/bcache<N>`上层安装`dmcrypt`，这比在后端和caching设备同时安装`dmcrypt`更快
## 重建bcache
假如你需要释放bcache，然后重新修改分区表，上面有任何后端或cache设备都会冲突失败
1. `/dev/bcache*`如果存在则很简单：
```bash
$ echo 1 > /sys/block/bcache0/bcache/stop
```
2. 但是如果后端设备不存在
```bash
$ cd /sys/block/bcache0/bcache
bash: cd: bcache: No such file or directory
```
这种情况，你或许必须注销引用待释放bcache的块设备
```bash
$ dmsetup remove old ds1
bcache: bcache_device_free() bcache0 stopped
bcache: cache_set_free() Cache set 5bc072a8-ab17-446d-9744-e247949913c1 unregistered
```
这导致后端bcache从/sys/fs/bcache中移除，然后再次重用，This would be true of any block device stacking where bcache is a lower device.
3. 另一方面，你也能看看`/sys/fs/bcache/`
```bash
$ ls -l /sys/fs/bcache/*/{cache?,bdev?}
lrwxrwxrwx 1 root root 0 Mar  5 09:39 0226553a-37cf-41d5-b3ce-8b1e944543a8/bdev1 -> ../../../devices/virtual/block/dm-1/bcache/
lrwxrwxrwx 1 root root 0 Mar  5 09:39 0226553a-37cf-41d5-b3ce-8b1e944543a8/cache0 -> ../../../devices/virtual/block/dm-4/bcache/
lrwxrwxrwx 1 root root 0 Mar  5 09:39 5bc072a8-ab17-446d-9744-e247949913c1/cache0 -> ../../../devices/pci0000:00/0000:00:01.0/0000:01:00.0/ata10/host9/target9:0:0/9:0:0:0/block/sdl/sdl2/bcache/
```
设备名将显示哪个UUID是相关的，在该目录下cd，然后stop the cache:
```bash
$ echo 1 > /sys/fs/bcache/5bc072a8-ab17-446d-9744-e247949913c1/stop
```
 这将会释放bcache引用，让你重用其它目的的分区
 