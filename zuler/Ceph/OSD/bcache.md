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

```
root@ceph-1:~# apt install bcache-tools -y  ##安装bcache工具，ubuntu及suse默认已安装
root@ceph-1:~# modprobe bcache  ##导入模块
root@ceph-1:~# lsmod |grep bcache
bcache                245760  0
crc64                  16384  1 bcache
root@ceph-1:~# make-bcache -C /dev/nvme0n1 -B /dev/sdc --writeback ##-C指定s
```
在本例中，使用了512B和128kB的默认块和桶大小。块大小应与支持设备扇区大小相匹配，通常为512或4k。存储桶大小应与缓存设备的擦除块大小相匹配，目的是减少写入放大。例如，使用具有4k扇区的HDD和具有2MB擦除块大小的SSD，此命令看起来像
```
$ make-bcache --block 4k --bucket 2M -C /dev/nvme0n1 -B /dev/sdc
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
$ wipefs -a /dev/sda # 对磁盘进行清理
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

