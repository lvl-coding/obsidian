# 准备
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

# 测试场景

## bcache+db SSD池性能
|          |     |            |                     |                   |
| -------- | --- | ---------- | ------------------- | ----------------- |
| 盘符       | 类型  | 大小         | bcache              | OSD               |
| /dev/sda | SSD | 894.25 GiB |                     | OSD.2-db，osd.3-db |
| /dev/sdb | SSD | 894.25 GiB |                     |                   |
| /dev/sdc | HDD | 3.64 TiB   | bcache0 后端盘         | osd.2             |
| /dev/sdd | HDD | 3.64 TiB   | bcache2 后端盘         | osd.3             |
| /dev/sde | SSD | 1.75 TiB   | bcache0/bcache2 缓存盘 |                   |
| /dev/sdf | SSD | 1.75 TiB   |                     |                   |


## SSD池RBD性能
## HDD+db SSD池性能
## 纯HDD池RBD性能
## 纯bcache 池RBD性能