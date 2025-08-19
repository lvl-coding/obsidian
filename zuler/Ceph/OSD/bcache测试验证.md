# 环境
广州1办公集群55节点，已踢出，使用离线部署脚本，部署ceph和openstack环境
设备规格：
- cpu：Intel(R) Xeon(R) Gold 6326 CPU @ 2.90GHz 64核
- 内存：377Gi
- ens1：1000Mb/s
## 磁盘
|          |     |            |             |
| -------- | --- | ---------- | ----------- |
| 盘符       | 类型  | 大小         | bcache      |
| /dev/sda | SSD | 894.25 GiB | bcache0 缓存盘 |
| /dev/sdb | SSD | 894.25 GiB |             |
| /dev/sdc | HDD | 3.64 TiB   | bcache0 后端盘 |
| /dev/sdd | HDD | 3.64 TiB   |             |
| /dev/sde | SSD | 1.75 TiB   |             |
| /dev/sdf | SSD | 1.75 TiB   |             |

# 测试方法 
在上述55节点上创建单节点的ceph集群和openstack集群，创建windows虚拟机实例，镜像文件为[Win10 21H2](http://192.168.103.32:8888/images/Windows_Desktop_10_21H2_v1.2.0.tar.gz)，ceph集群中固定sda和sdb两块SSD作为osd，后续使用sdc和sdd两块HDD与sde和sdf两块SSD依次做bcache和OSD的db设备两种场景创建OSD，将bcache/SSD/HDD类型的OSD在不同测试场景下加入到新建的数据盘存储池，openstack中添加该存储池作为volume后端服务
# 测试场景

## bcache+db SSD池性能
|          |     |            |                     |                   |
| -------- | --- | ---------- | ------------------- | ----------------- |
| 盘符       | 类型  | 大小         | bcache              | OSD               |
| /dev/sda | SSD | 894.25 GiB |                     | osd.0             |
| /dev/sdb | SSD | 894.25 GiB |                     | osd.1             |
| /dev/sdc | HDD | 3.64 TiB   | bcache0 后端盘         | osd.2             |
| /dev/sdd | HDD | 3.64 TiB   | bcache2 后端盘         | osd.3             |
| /dev/sde | SSD | 1.75 TiB   | bcache0/bcache2 缓存盘 |                   |
| /dev/sdf | SSD | 1.75 TiB   |                     | OSD.2-db，osd.3-db |
新增存储池的OSD为bcache和SSD的db创建。对虚拟机的实例中的数据盘进行fio读写性能验证

|            |         |       |     |
| ---------- | ------- | ----- | --- |
|            | bcache  |       |     |
| iops       | 带宽MiB/s | 延时 ms |     |
| 4M顺序读 avl  |         |       |     |
| 4M顺序写 avl  |         |       |     |
| 4M混合顺序读50% |         |       |     |
| 4M混合顺序写50% |         |       |     |
| 4k随机读      |         |       |     |
| 4k随机写      |         |       |     |
| 4K混合随机读50% |         |       |     |
| 4K混合随机写50% |         |       |     |
## SSD池RBD性能

|            |         |       |     |
| ---------- | ------- | ----- | --- |
|            | SSD     |       |     |
| iops       | 带宽MiB/s | 延时 ms |     |
| 4M顺序读 avl  |         |       |     |
| 4M顺序写 avl  |         |       |     |
| 4M混合顺序读50% |         |       |     |
| 4M混合顺序写50% |         |       |     |
| 4k随机读      |         |       |     |
| 4k随机写      |         |       |     |
| 4K混合随机读50% |         |       |     |
| 4K混合随机写50% |         |       |     |
## HDD+db SSD池性能

|            |         |       |     |
| ---------- | ------- | ----- | --- |
|            | HDD     |       |     |
| iops       | 带宽MiB/s | 延时 ms |     |
| 4M顺序读 avl  |         |       |     |
| 4M顺序写 avl  |         |       |     |
| 4M混合顺序读50% |         |       |     |
| 4M混合顺序写50% |         |       |     |
| 4k随机读      |         |       |     |
| 4k随机写      |         |       |     |
| 4K混合随机读50% |         |       |     |
| 4K混合随机写50% |         |       |     |
## 纯HDD池RBD性能
|            |          |       |     |
| ---------- | -------- | ----- | --- |
|            | HDD only |       |     |
| iops       | 带宽MiB/s  | 延时 ms |     |
| 4M顺序读 avl  |          |       |     |
| 4M顺序写 avl  |          |       |     |
| 4M混合顺序读50% |          |       |     |
| 4M混合顺序写50% |          |       |     |
| 4k随机读      |          |       |     |
| 4k随机写      |          |       |     |
| 4K混合随机读50% |          |       |     |
| 4K混合随机写50% |          |       |     |
## 纯bcache 池RBD性能
|            |             |       |     |
| ---------- | ----------- | ----- | --- |
|            | bcache only |       |     |
| iops       | 带宽MiB/s     | 延时 ms |     |
| 4M顺序读 avl  |             |       |     |
| 4M顺序写 avl  |             |       |     |
| 4M混合顺序读50% |             |       |     |
| 4M混合顺序写50% |             |       |     |
| 4k随机读      |             |       |     |
| 4k随机写      |             |       |     |
| 4K混合随机读50% |             |       |     |
| 4K混合随机写50% |             |       |     |
