[Bcache详解及踩坑记录](https://zhuanlan.zhihu.com/p/682979765)
[bcache 状态/配置 文件详细介绍(翻译自官网）](https://www.cnblogs.com/zimufeng/p/6640439.html "发布于 2017-03-29 16:40")
[bcache实现详解](https://developer.aliyun.com/article/60734)
# 验证总结
- 在bcache缓存未满的情况下，性能对比HDD有较大提升，小块提升效果明显，读约有20倍的提升，大块读的提升效果为2.5倍。
- 当bcache缓存较小，达到限制是，小块和大块的提升效果有所下降，小块读为HDD的1.5倍，大块为1.2倍。
- 由于采用的写回模式，当缓存到达限制时小块的写入加速效果并没有明显衰降
# 优势
- bcache的优势在于可以将SSD资源池化，一块SSD可对应多块HDD，形成一个缓存池
- 支持从缓存池中划出瘦分配的纯flash卷（thin-flash LUN）单独使用。
- 采用一棵相对标准的B+树作为索引，命中率会有很大提高
# 创建Bcache设备
创建bcache设备需要用到[bcache-tools](https://zhida.zhihu.com/search?content_id=239903397&content_type=Article&match_order=1&q=bcache-tools&zhida_source=entity)工具，ubuntu和suse中默认已安装bcache-tool工具。
```bash
$ apt install bcache-tools -y  #安装bcache工具，ubuntu及suse默认已安装
$ modprobe bcache  #导入模块
$ lsmod |grep bcache
bcache                323584  8
crc64                  16384  1 bcache
```
创建设备
```
$ wipefs -a /dev/sda /dev/sdb
$ uuidgen #获取要设置的 csetuuid
$ make-bcache --bucket 2M --block 512 --writeback --cset-uuid 124d59e1-530a-4981-80a9-441c2cb7bfc0 -C /dev/sda -B /dev/sdb
$ echo 40 > /sys/block/bcache0/bcache/writeback_percent
$ echo 0 > /sys/block/bcache0/bcache/sequential_cutoff
```
在本例中，使用了512B和2M的块和桶大小。块大小应与支持设备扇区大小相匹配，通常为512或4k。存储桶大小应与缓存设备的擦除块大小相匹配，目的是减少写入放大。例如使用具有4k扇区的HDD和具有2MB擦除块大小的SSD
通常系统会自动注册cache设备和backing设备并自动生成虚拟的bcache设备/dev/bcache0
- 如果udev规则没有安装，则需要手动注册设备
```text
$ echo /dev/nvme0n1 > /sys/fs/bcache/register
$ echo /dev/sdb > /sys/fs/bcache/register
```
- 如果udev规则没有安装，还需要手动将cache设备挂载到backing设备上，否则高速缓存不会介入。
```text
$ echo <cache-set-UUID> > /sys/block/bcache0/bcache/attach
```
`<cache-set-UUID>`是在格式化cache设备时为高速缓存设备生成的全局唯一标识，可以在/sys/fs/bcache/目录下查找到。
创建完成后可以看到有bcacheX命名的盘，bcache盘是一个裸设备，可以直接格式化使用。
```
root@yzcm003:/home/cmadmin# lsblk
NAME                      MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda                         8:0    0   3.6T  0 disk
└─bcache0                 252:128  0   3.6T  0 disk
sdb                         8:16   0   3.6T  0 disk
└─bcache0                 252:0    0   3.6T  0 disk
```
指定慢速盘查看bcache信息，其中dev.uuid是后端盘符的uuid，cset是前端加速盘的uuid
```
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

cset.uuid		00000000-0000-0000-0000-000000000000```
```
# bcache清理
每一块低速盘(HDD)在经过make-bcache后, 都会对应一个bcacheX设备, 删除后端盘就是将对应的bcacheX设备停掉(删掉). 如果该bcacheX设备有绑定前端盘(SSD), 需要先解除与前端盘的关联。
1. 确定要删除bcache盘的后端盘
```bash
$ lsblk |grep sda -A 1
sda                       8:0    0  14.6T  0 disk
└─bcache0               252:0    0  14.6T  0 disk
```
2. 获取到cset
```bash
$ bcache-super-show /dev/sda|grep cset  
cset.uuid               ae6bf031-bd04-47f5-a273-48f2dd514268
```
3. 通过获取到的`cset.uuid`移除`bcache0`的绑定
```
$ echo "ae6bf031-bd04-47f5-a273-48f2dd514268" > /sys/block/bcache0/bcache/detach  
$ echo 1 >/sys/fs/bcache/ae6bf031-bd04-47f5-a273-48f2dd514268/unregister
$ echo 1 > /sys/block/bcache0/bcache/stop
$ wipefs -a /dev/sda
```
对磁盘进行清理时如果提示busy，说明磁盘中的分区信息又再次被探测到了，需要重新执行unregister或stop后再次清理磁盘
