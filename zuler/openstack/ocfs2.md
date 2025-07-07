社区文档[Overview of OCFS2](https://www.ibm.com/docs/en/linux-on-systems?topic=ocfs2-overview)
[OCFS2 Users Guide](https://raw.githubusercontent.com/markfasheh/ocfs2-tools/master/documentation/users_guide.txt)
[心跳超时参数修改](https://www.cnblogs.com/tianlesoftware/archive/2009/11/13/3610355.html)
[Oracle集群文件系统（OCFS2）用户指南](https://blog.51cto.com/u_9748604/5572602)
[ocfs2: 搭建环境](https://www.cnblogs.com/shanno/p/3982195.html "发布于 2014-09-19 19:47")
[heartbeat.c](https://lxr.missinglinkelectronics.com/linux+xilinx-v2017.3/fs/ocfs2/cluster/heartbeat.c)
[Oracle Cluster File System Version 2](https://docs.oracle.com/en/operating-systems/oracle-linux/6/admin/ol_ocfs2.html)
[[ocfs2测试用例]]


# 问题记录
- [x] 怀疑追加ocfs2服务产生挂载点重复
```bash
$ mount | grep "/var/opt/mnt/ocfs2/192.168.10.89_sda" | wc -l
4096
$ mount | grep "/var/lib/docker/volumes/cinder/_data/ocfs2/192.168.10.89_sda" | wc -l
2048
```
- [ ] 同一iscsi多节点下盘符不一致时的ocfs2挂载的方法
- [x] ocfs2部分节点mount失败
- [ ] 服务脚本需兼容或修改ocfs服务自启动方式
- [x] docker重启时ocfs2的挂载目录残留
# ocfs2介绍
OCFS2是一个Linux上的共享磁盘文件系统又叫集群文件系统（Cluster File System），是专门构建在网络共享的磁盘上的文件系统。网络共享磁盘通过SAN（Storage Area Network）被多台主机共同访问，和磁盘文件系统相比，共享磁盘文件系统除了要解决磁盘空间的有效管理问题之外，还要解决文件系统被多台主机同时访问的并发修改问题。因此分布式锁机制是共享磁盘文件系统共有的机制。
OCFS2使用分布式锁管理器来组织访问和网络互连，在文件更改时在集群服务器之间共享信息。当文件驻留在集群中服务器之一的内存中时，其文件内容视图不会改变，并且该版本可以写入共享文件。
OCFS2允许不同服务器上的多个用户同时读取和写入同一物理磁盘上的相同文件。对共享文件的访问是互斥的，因此在任何时候只有一台服务器实际上写入文件块。保持写入、文件内容和文件系统结构的一致性。
OCFS2文件系统的操作使用O2CB中的几项服务进行管理。在使用OCFS2集群文件系统执行任何操作之前，O2CB必须加载、启动并已上线。
共享磁盘文件系统也用于创建高可用性（HA）系统，其中冗余访问存储中的数据或可执行文件提供了对SAN存储或本地磁盘存储的故障转移访问。
## 组件
OCFS2的独特之处在于它由五个文件系统组件组成。O2CB工具管理服务器集群中对共享文件的访问，尽管这些服务在设置完成后对用户透明，但O2CB工具必须在系统重新启动时加载并上线，之后OCFS2文件看起来和其他文件一样。
O2CB有这五个服务组件：
- Node manager (NM)：节点管理器，它对cluster.conf文件中所有节点进行监控，跟踪集群中的所有节点。
- Heart beat (HB)： 心跳服务，当节点加入或离开集群时，发出节点up和down通知。
- TCP： 提供节点之间的通信。
- DLM：分布式锁管理器，持续跟踪所有的锁、锁的所有者及状态
- DLMFS：在Linux内核中为DLM提供用户空间接口，和DLM一起确保了集群文件系统的一致性
- CONFIGFS：对内核对象执行基于文件系统的管理，称为config_items。挂载点是目录/config。
O2CB心跳是由集群成员定期写入共享控制区域创建的，使用指定的互连，作为节点活着的证明。默认的心跳速率是每两秒钟一次心跳。即使在只有一个节点的集群文件系统中，心跳也会得到维持。
节点管理器协调心跳过程，并在节点加入、放弃、失去通信或更改集群内状态时做出响应，无论是否有意。节点管理器可以触发恢复。节点管理器的一个关键功能是使节点掉线时能够释放资源。
分布式锁定系统是OCFS2协调多个节点对存储的共享访问的关键。DLS是一个分层锁定系统，允许从高级到精细的资源进行队列。锁定由DLM管理。
通过选择集群>配置节点，在安装程序上创建OCFS2集群。这会构建/etc/ocfs2/cluster.conf文件。要么接受集群的默认名称，即OCFS2，要么选择一个名称。
从“任务”菜单中，选择“添加”，并为每个节点键入节点名称、OCFS2要使用的IP地址和端口号7777，该端口被认为是群集通信的默认端口。
节点名称必须与主机名匹配，但不需要扩展域名。例如，使用myserver而不是myserver.xyz.com。IP地址不需要是与该主机名关联的IP地址。可以使用该节点上的任何有效IP地址。OCFS2将节点名称（主机名）与指定的IP地址不匹配。
# 服务管理
## 配置文件
ocfs集群依赖配置文件`/etc/ocfs2/cluster.conf`，内容如下
```
cluster:
	name = zuler
	heartbeat_mode = local
	node_count = 3

node:
	cluster = zuler
	number = 0
	ip_port = 17777
	ip_address = 192.168.10.89
	name = cluster89

node:
	cluster = zuler
	number = 2
	ip_port = 17777
	ip_address = 192.168.10.91
	name = cluster91

node:
	cluster = zuler
	number = 1
	ip_port = 17777
	ip_address = 192.168.10.90
	name = cluster90
```
可通过o2cb命令对配置文件进行集群和节点的配置信息管理
```bash
$ o2cb -h
usage: o2cb [--config-file=path] [-h|--help] [-v|--verbose] [-V|--version] COMMAND [ARGS]

The commands are:
  add-cluster         Add cluster to the config file.
  remove-cluster      Removes cluster from the config file.
  add-node            Adds a node to the cluster in the config file.
  remove-node         Removes a node from the cluster in the config file.
  add-heartbeat       Adds a heartbeat region to the cluster in the config file.
  remove-heartbeat    Removes a heartbeat region from the cluster in the config file.
  heartbeat-mode      Toggles the heartbeat mode between global and local.
  list-clusters       Lists all the cluster names in the config file.
  list-cluster        Lists all the nodes and heartbeat regions associated with the cluster in the config file.
  list-nodes          Lists all the nodes associated with the cluster in the config file.
  list-heartbeats     Lists all the heartbeat regions associated with the cluster in the config file.
  register-cluster    Registers the cluster with configfs.
  unregister-cluster  Unregisters the cluster from configfs.
  start-heartbeat     Starts global heartbeat.
  stop-heartbeat      Stops global heartbeat.
  cluster-status      Returns 0 if cluster online, 1 otherwise.
```
## 集群服务管理
/etc/init.d/o2cb用于管理集群服务，也称为O2CB stack。
```
$ /etc/init.d/o2cb -h
Usage: /etc/init.d/o2cb {start|stop|restart|force-reload|enable|disable|configure|load|unload|online|offline|force-offline|status|online-status}
```
可以通过以下方式检查所有服务的状态：
```
# /etc/init.d/o2cb status
Module “configfs”: Not loaded
Filesystem “configfs”: Not mounted
Module “ocfs2_nodemanager”: Not loaded
Module “ocfs2_dlm”: Not loaded
Module “ocfs2_dlmfs”: Not loaded
Filesystem “ocfs2_dlmfs”: Not mounted
```
通过以下方式启动所有服务：
```
# /etc/init.d/o2cb load
Loading module “configfs”: OK
Mounting configfs filesystem at /config: OK
Loading module “ocfs2_nodemanager”: OK
Loading module “ocfs2_dlm”: OK
Loading module “ocfs2_dlmfs”: OK
Mounting ocfs2_dlmfs filesystem at /dlm: OK
```
通过以下方式使cluster ocfs2 启动：
```
# /etc/init.d/o2cb online ocfs2
Starting cluster ocfs2: OK
```
通过以下方式停止cluster ocfs2:
```
# /etc/init.d/o2cb offline ocfs2
Cleaning heartbeat on ocfs2: OK
Stopping cluster ocfs2: OK
```
通过以下方式停止所有服务：
```
# /etc/init.d/o2cb unload
Unmounting ocfs2_dlmfs filesystem:
Unloading module “ocfs2_dlmfs”: OK
Unmounting configfs filesystem: OK
Unloading module “configfs”: OK
```
开启自启，可使ocfs服务开启自启动，并制定所启动的集群
```bash
$ /etc/init.d/o2cb configure
$ dpkg-reconfigure ocfs2-tools
```
如果cluster已经被配置为系统启动时加载，可以通过以下方式启动和停止它：
```
$ /etc/init.d/o2cb load  
Loading module “configfs”: OK  
Mounting configfs filesystem at /config: OK  
Loading module “ocfs2_nodemanager”: OK  
Loading module “ocfs2_dlm”: OK  
Loading module “ocfs2_dlmfs”: OK  
Mounting ocfs2_dlmfs filesystem at /dlm: OK
```
# 磁盘格式化与挂载
## 磁盘命名一致
使用san存储通过iscsi挂载多主机上作为磁盘，存在多个iscsi设备时可[使磁盘在系统中的命名始终一致](https://www.ibm.com/docs/en/linux-on-systems?topic=ocfs2-managing-shared-disks#l0wocf00_ocfs2_managing_shared_disks)
## 格式化
> [!important]
> 如果O2CB CLUSTER没有启动，首先启动它。
格式化操作依赖于这些服务。同时它需要你确保所有的节点上都没有挂载需要格式化的设备。
- 为要格式化的设备输入一个标签，为了便于管理，建议输入一个标签，仍然可以在格式化之后修改它。
- 为设备选择一个集群单元大小（cluster size),它支持从4k到1M。如果这个设备用来存储数据文件或者大的文件，比较合适的大小是128K。
- 选择一个块单元大小（block size），它支持从512字节到4k，由于OCFS2在格式化的时候并不分配一个静态的inode 区域，所以4k是强烈推荐的大小，它几乎适合所有的磁盘大小，换句话说，即时这些磁盘支持512字节，但仍然不建议使用小的单元。一旦格式化完毕，集群单元和块单元都不可以再改变。
- 输入一个节点槽位（node slots），这个数值决定了允许多少节点同时挂载这个设备。这个数值随后可以修改，但只能增加，不能减小。
## 磁盘自动挂载
磁盘被手动挂载之后都必须把挂载信息写入/etc/fstab这个文件中，否则下次开机启动时仍然需要重新挂载。系统开机时会主动读取/etc/fstab这个文件中的内容，根据文件里面的配置挂载磁盘。这样我们只需要将磁盘的挂载信息写入这个文件中我们就不需要每次开机启动之后手动进行挂载了。
```
/dev/dasdf1 /datadisk1 ocfs2 _netdev,datavolume,nointr 0 0 
/dev/dasdd1 /bin_disk ocfs2 _netdev 0 0
```
## 跳过磁盘扫描
由于磁盘过多导致开启缓慢，根据需要[跳过开启时的磁盘扫描](https://www.ibm.com/docs/en/linux-on-systems?topic=ocfs2-too-many-dasds-accessible-linux-read)




