社区文档[Overview of OCFS2](https://www.ibm.com/docs/en/linux-on-systems?topic=ocfs2-overview)
[OCFS2 Users Guide](https://raw.githubusercontent.com/markfasheh/ocfs2-tools/master/documentation/users_guide.txt)
[心跳超时参数修改](https://www.cnblogs.com/tianlesoftware/archive/2009/11/13/3610355.html)
[Oracle集群文件系统（OCFS2）用户指南](https://blog.51cto.com/u_9748604/5572602)
[ocfs2: 搭建环境](https://www.cnblogs.com/shanno/p/3982195.html "发布于 2014-09-19 19:47")
[heartbeat.c](https://lxr.missinglinkelectronics.com/linux+xilinx-v2017.3/fs/ocfs2/cluster/heartbeat.c)

# 问题记录
- [ ] 怀疑追加ocfs2服务产生挂载点重复
- [ ] 同一iscsi多节点下盘符不一致时的ocfs2挂载的方法
- [ ] ocfs2部分节点mount失败
- [ ] 服务脚本需兼容或修改ocfs服务自启动方式
# 开启自启
```bash
$ /etc/init.d/o2cb configure
Load O2CB driver on boot (y/n) [n] : y
$ dpkg-reconfigure ocfs2-tools
```
或
```
$ /etc/init.d/o2cb configure  
Configuring the O2CB driver.  
This will configure the on-boot properties of the O2CB driver.  
The following questions will determine whether the driver is loaded on  
boot. The current values will be shown in brackets (’[]‘). Hitting  
without typing an answer will keep that current value. Ctrl-C  
will abort.  
Load O2CB driver on boot (y/n) [n]: y  
Cluster to start on boot (Enter “none” to clear) []: ocfs2  
Writing O2CB configuration: oK  
```
可使ocfs服务开启自启动，并制定所启动的集群
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
## 磁盘自动挂载
磁盘被手动[挂载](https://so.csdn.net/so/search?q=%E6%8C%82%E8%BD%BD&spm=1001.2101.3001.7020)之后都必须把挂载信息写入/etc/fstab这个文件中，否则下次开机启动时仍然需要重新挂载。系统开机时会主动读取/etc/fstab这个文件中的内容，根据文件里面的配置挂载磁盘。这样我们只需要将磁盘的挂载信息写入这个文件中我们就不需要每次开机启动之后手动进行挂载了。
```
/dev/dasdf1 /datadisk1 ocfs2 _netdev,datavolume,nointr 0 0 
/dev/dasdd1 /bin_disk ocfs2 _netdev 0 0
```
# 组件说明
## 配置文件
`/etc/ocfs2/cluster.conf`
## o2cb集群服务
ocfs2有自己的集群服务结构，叫做O2CB，它包括
- NM：节点管理器，它对cluster.conf文件中所有节点进行监控
- HB：心跳服务，它在节点离开或加入rac时提示up和down的消息
- TCP：控制节点间的通讯
- DLM：分布式锁管理器，它持续跟踪所有的锁、锁的所有者及状态
- CONFIGFS：用户配置文件系统驱动空间，挂载点是/config
- DLMFS：用户空间和内核空间DLM的借口
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
# 格式化
如果O2CB CLUSTER没有启动，首先启动它。格式化操作依赖于这些服务。同时它需要你确保所有的节点上都没有挂载需要格式化的设备。
你可以使用图形界面或者命令行来格式化分区。
如果你使用图形界面，选择task/Format来执行操作，在下拉列表中选择可用的设备。同时控制台会尽可能的列出设备上存在的文件系统。
为要格式化的设备输入一个标签，为了便于管理，我们建议你输入一个标签，你仍然可以在格式化之后修改它。
为设备选择一个集群单元大小（cluster size),它支持从4k到1M。如果这个设备用来存储数据文件或者大的文件，比较合适的大小是128K。
选择一个块单元大小（block size），它支持从512字节到4k，由于OCFS2在格式化的时候并不分配一个静态的inode 区域，所以4k是我们强烈推荐的大小，它几乎适合所有的磁盘大小，换句话说，即时这些磁盘支持512字节，但我们仍然不建议使用小的单元。
一旦格式化完毕，集群单元和块单元都不可以再改变。
输入一个节点槽位（node slots），这个数值决定了允许多少节点同时挂载这个设备。这个数值随后可以修改，但只能增加，不能减小。
点击ok按钮执行格式化操作。
