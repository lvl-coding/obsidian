[cinder help](https://docs.openstack.org/python-cinderclient/latest/cli/details.html)
[cinder-manage](https://docs.openstack.org/cinder/latest/cli/cinder-manage.html)
[Cinder组件流程解析](https://www.cnblogs.com/luohaixian/p/8134967.html "发布于 2017-12-28 13:05")
[OpenStack源码分析-Cinder创建卷流程](https://yikun.github.io/2016/02/14/OpenStack源码分析-Cinder创建卷流程/)

![[Pasted image 20250702153853.png]]
![[Pasted image 20250707172540.png]]
![[Pasted image 20250708142027.png]]

```
VolumeManager._report_driver_status.<locals>.get_stats
console_scripts = 
   cinder-api = cinder.cmd.api:main
   cinder-backup = cinder.cmd.backup:main
   cinder-manage = cinder.cmd.manage:main
   cinder-rootwrap = oslo_rootwrap.cmd:main
   cinder-rtstool = cinder.cmd.rtstool:main
   cinder-scheduler = cinder.cmd.scheduler:main
   cinder-volume = cinder.cmd.volume:main
   cinder-volume-usage-audit = cinder.cmd.volume_usage_audit:main
```

# cinder-api
cinder-api对外提供REST API、响应请求、调用cinder-volume，是整个cinder组件的门户，进入Cinder的HTTP接口。具体来说cinder-api的任务就是：
- 检查客户端传入的参数是否有效
- 调用cinder其他子组件处理客户端请求
- 将其他子组件处理的结果返回客户端
## 启动流程
1. register_all函数是导入/cinder/object目录下的多个模块
2. 接着是加载配置文件、设置中间件和初始化日志模块
3. 初始化rpc模块，用于与其它服务进行通信
4. 启动wsgi服务，监听客户端发送的请求
# cinder-volume
cinder-volume运行在存储节点上，负责管理具体存储设备的存储空间。每个存储节点都会运行cinder-volume服务，多个存储节点便组成了一个存储资源池。
cinder-volume运行在存储节点，通过OpenStack平台对volume的指令最后都会由cinder-volume完成。但是cinder-volume自身并不是真正的存储设备，真正的存储设备是volume provider，cinder-volume与volume provider一起实现了对volume全生命周期的管理。市面上有这么多volume provider，是将cinder-volume设计成与volume provider一一对应的形式吗，但这样就要设计很多代码相似度很高的cinder-volume。在这个问题上，cinder也运用了和neutron同样的思路。
通过驱动架构，cinder-volume为volume provider定义了统一的接口，对于volume provider来说，只要实现这些接口就可以以驱动的形式像插件一样插入OpenStack系统中。
# cinder-scheduler
当用户向cinder-api发出请求创建一个volume时，如果用户已经指定存储节点，则cinder-api会直接调用cinder-volume去创建volume；当用户没有指定节点时，cinder-api会将请求发送给cinder-scheduler，然后cinder-scheduler会根据调度算法选择最合适的存储节点创建volume。根据预定的策略选择合适的cinder-volume节点来处理用户的请求。
# volume provider
volume provider是数据存储设备，为volume提供物理存储空间。cinder-volume支持多种volume provider，每种volume provider通过自己的驱动与cinder-volume协同工作。
# cinder DB
cinder在部署前也需要在数据库中创建一个名为cinder的database。
# cinder-backup
用于提供存储卷的备份功能，支持将块存储卷备份到OpenStack备份存储后端，比如Swift、Ceph等。以后可以通过restone操作恢复。
cinder-backup跟volume做快照还是有很大区别的，以下列出主要的几点区别：
- 快照依赖于源volume，如果源volume被删除了，那快照是无用的，但backup是不依赖于源volume的，因为源volume也被备份到备份存储设备上去了，通过restone操作可以完全恢复。
- 快照和源volume通常放在一起，由同一个volume provider管理，而backup存放在独立的备份设备上，有自己的备份方案和实现，和volume provider没有关系。
- cinder-backup是具有容灾功能的，但因此备份往往需要较大的空间，而快照snapshot则提供volume provider内快捷的回溯功能。
我们可以通过使用cinder backup-create命令创建某个卷的备份，通过cinder backup-list命令查看创建的备份。
# cinder-manage
用于cinder管理的命令行接口。
# cinder-rtstool
伴随LIO（Linux-IO Target）支持而增加的工具。
# cinder-volume-usage-audit
用于卷使用情况统计。