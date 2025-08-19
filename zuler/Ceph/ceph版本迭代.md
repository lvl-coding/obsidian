每年一个新版本，预计3月份发布，按照字母排序
![[Pasted image 20250807102031.png]]
R版本21年3月份发布，社区已于停止更新提交
最新的release版本是S版本
# 推荐配置
![[Pasted image 20250807170235.png]]
# SQUID
## RGW
- PutObjectLockConfiguration现在可用于在未启用对象锁定的情况下创建的现有版本化桶上启用S3对象锁定。
- STS [CVE-2023-43040]中的身份验证旁路漏洞已修复。
- S3策略现在强制执行基于ARN的条件。
- 将对象复制到自身不再导致数据丢失。ETIMEDOUT（默认情况下未启用）的潜在损坏也已修复。
- 此热修复版本解决了使用CopyObject将对象复制到自身时的RGW数据丢失错误。当S3客户端想要更改现有对象的元数据时，他们通常会这样做。由于之前对[https://tracker.ceph.com/issues/66286](https://tracker.ceph.com/issues/66286)的修复导致回归，任何与此类对象相关的尾部对象都被错误地标记为垃圾收集。鼓励Squid上的RGW部署尽快升级，以尽量减少损失。实验性的rgw-gap-list工具可以帮助识别损坏的物体。
- 用户帐户功能解锁了几个新的AWS兼容IAM API，用于用户、密钥、组、角色、策略等的自助管理。
## RADOS
- BlueStore已经过优化，在快照密集型工作负载中提供了更好的性能。
- BlueStore RocksDB LZ4压缩现在默认启用，以提高平均性能和“快速设备”空间使用。
- 其他改进包括更灵活的EC配置、帮助调试mgr模块问题的OpTracker，以及更好的清理调度。
## RBD
- 不再允许将作为组成员的image移动到trash。rbd trash mv命令现在与在这种情况下的rbd rm行为相同
- diff-iterate现在可以在本地执行，为QEMU实时磁盘同步和备份用例带来了显著的性能提升。
- 增加了对非用户类型快照克隆的支持。
- rbd-wnbd驱动程序获得了多路image映射的能力。
## MGR
- MGR的始终打开的模块/插件现在可以强制禁用。当coresponding Ceph服务停机/退化时，需要防止MGR被模块命令太多而阻塞的情况下
- RADOS：平衡器mgr模块中的性能瓶颈已修复。
    相关跟踪器：[https://tracker.ceph.com/issues/68657](https://tracker.ceph.com/issues/68657)
# reef
- v18.2.4引入由于线程创建方法的不同，v18.2.4容器映像现在基于CentOS 9，在旧内核（例如Ubuntu 18.04）上可能不兼容。
## ceph-volume 
- 与cryptsetup版本处理相关的错误已经修复。相关跟踪器：[https://tracker.ceph.com/issues/66393](https://tracker.ceph.com/issues/66393)
- 与IPv6支持相关的错误现已修复。相关跟踪器：[https://tracker.ceph.com/issues/67517](https://tracker.ceph.com/issues/67517)
## RBD
- rbd-nbd的`try-netlink`映射选项已成为默认选项，现已弃用。如果内核不支持NBD netlink接口，则使用传统的ioctl接口重新重新对映。
- RADOS：添加了一个新的命令ceph osd rm-pg-upmap-primary-all，允许用户在需要时清除osdmap中的所有pg-upmap-primary映射。
	相关追踪者：
	- [https://tracker.ceph.com/issues/67179](https://tracker.ceph.com/issues/67179) 
	- [Https://tracker.ceph.com/issues/66867](https://tracker.ceph.com/issues/66867)
- 增加了对分层客户端加密的支持。
## rados
- 此版本修复了一个错误（[https://tracker.ceph.com/issues/61948](https://tracker.ceph.com/issues/61948)），尽管用户设置了require-min-compat-client=reef，但允许pre-reef客户端连接到pg-upmap-primary（[https://docs.ceph.com/en/reef/rados/operations/read-balancer/](https://docs.ceph.com/en/reef/rados/operations/read-balancer/)）接口，导致在osds和mons中出现断言。如果您在18.2.4之前的Reef版本中容易出现此错误，如果1）您正在使用带有-read选项的离线osdmaptool生成的osdmap，或者2）您已使用CLI命令显式生成pg-upmap-primary映射。请注意，修复是最小的，并且不解决角落案例，例如在升级过程中或部分升级集群中添加映射（相关跟踪器链接在[https://tracker.ceph.com/issues/61948中](https://tracker.ceph.com/issues/61948)）。因此，我们建议删除任何现有的pg-upmap-primary映射，直到未来的点版本解决剩余问题。有关如何删除现有pg-upmap-primary映射的说明，请参阅[https://tracker.ceph.com/issues/61948#note-32](https://tracker.ceph.com/issues/61948#note-32)。
- RADOS：如果应用程序未为池启用，无论池是否正在使用，现在都会报告POOL_APP_NOT_ENABLED健康警告。始终使用ceph osd pool application enable命令用应用程序标记池，以避免报告该池的POOL_APP_NOT_ENABLED。用户可能会使用ceph健康静音POOL_APP_NOT_ENABLED暂时静音此警告。
- 不支持FileStore
- RocksDB已升级到7.9.2版本。
- RocksDB迭代开销和性能有了显著的改进。
- Perf转储和perf模式命令已被弃用，以支持新的计数器转储和计数器模式命令。
- 缓存分层现已弃用。
- 一项新功能“读取平衡器”现已可用，它允许用户在集群上平衡每个池的主要PG。
- 
## RGW
- 使用服务器端加密的S3多部分上传现在在多站点部署中正确复制。以前，此类对象的副本在解密时会损坏。一个新的命令，radosgw-admin bucket resync encrypted multipart，可用于识别这些原始的多部分上传。任何已识别对象的LastModified时间戳都增加1ns，以导致对等区域再次复制它。对于使用服务器端加密的多站点部署，我们建议在所有区域升级后，针对每个区域的每个存储桶运行此命令。
- 在radosgw-admin中添加了用于识别和纠正版本化桶索引问题的新工具。版本桶索引事务工作流程的历史错误使索引能够积累多余的“簿记”olh（对象逻辑头）条目和普通占位符条目。在某些特定场景中，当客户端提出引用同一对象密钥的并发请求时，可能会积累大量额外的索引条目。当大量这些条目出现在单个桶索引碎片中时，它们可能会导致高桶列表延迟和生命周期处理失败。为了检查版本桶是否有不必要的olh条目，用户现在可以运行radosgw-admin bucket check olh。如果使用--fix标志，多余的条目将被安全地删除。与迄今为止描述的问题不同，一些版本桶也可能维护额外的未链接对象，这些对象无法从S3/Swift API中列出。这些额外的对象通常是PUT请求在存储桶索引事务的中间异常退出的结果——因此客户端不会收到成功的响应。先前版本中的错误使这些未链接的对象很容易通过在正在积极重新分片的存储桶上提出的任何PUT请求来重现。除了这些隐藏的、未链接的对象消耗的额外空间外，在某些场景中还可能存在另一个副作用，这是由于产生它们的故障模式的性质造成的，即作为此错误受害者的桶的客户端可能会发现与密钥关联的对象处于不一致的状态。要检查版本桶是否有未链接的条目，用户现在可以运行radosgw-admin桶检查未链接的条目。如果使用--fix标志，未链接的对象将被安全地删除。最后，第三个问题使版本桶指数统计数据被不准确地核算。重新计算版本桶统计数据的工具也有一个错误，以前无法修复这些不准确。此版本解决了这些问题，用户现在可以期待现有的radosgw-admin桶检查命令将产生正确的结果。我们建议拥有版本桶的用户，特别是以前版本中存在的桶的用户，使用这些新工具来检查他们的桶是否受到影响，并相应地清理它们。
- 多站点配置现在支持存储桶重新分级。
- 多站点复制的稳定性和一致性有了显著改善。
- 现在支持使用服务器端加密上传的对象进行压缩。
## dashboard
- 修复了新仪表板页面周围的一些错误和问题，包括破损的布局、一些指标给出错误的值，并引入了一个弹出窗口，以便在有HEALTH_WARN或HEALTH_ERR时显示详细信息。
- 修复了Rook支持的集群上的Ceph仪表板中的几个问题，并改善了Rook环境中的用户体验。
- 有一个改进布局的新仪表板页面。活跃的警报和一些重要的图表现在显示在卡片内。
# QUINCY
## rados
- 此版本修复了版本17.2.8（#63122）中的关键BlueStore回归。建议运行此发布的用户尽早升级。
## RBD
- 选项`--image-id`已添加到rbd子CLI命令中，因此可以在垃圾箱中为图像运行
- RBD_IMAGE_OPTION_CLONE_FORMAT选项已通过克隆、deep_copy和migration_prepare方法的克隆_format可选参数在Python绑定中公开。
- RBD_IMAGE_OPTION_FLATTEN选项已通过扁平化可选参数到deep_copy和migration_prepare方法在Python绑定中公开。
## MGR
- Ceph mgr转储命令现在在顶层输出last_failure_osd_epoch和active_clients字段。以前，这些字段在always_on_modules字段下输出。
# mClock调度器：
mClock调度器（昆西的默认调度器）已经经历了显著的可用性和设计改进，以解决缓慢的回填问题。一些重要的变化是：

“平衡”配置文件被设置为默认的mClock配置文件，因为它代表了优先级客户端IO或恢复IO之间的折衷方案。然后，用户可以选择“high_client_ops”配置文件来优先考虑客户端IO或“high_recovery_ops”配置文件来优先考虑恢复IO。

包括保留和限制在内的QoS参数现在以OSD IOPS容量的一小部分（范围：0.0到1.0）来指定。

成本参数（osd_mclock_cost_per_io_usec_*和osd_mclock_cost_per_byte_usec_*）已被删除。操作成本现在使用OSD底层设备的随机IOPS和最大顺序带宽能力来确定。

与错位对象恢复相比，退化对象恢复被赋予更高的优先级，因为退化对象存在数据安全问题，而只是放错的对象不存在。因此，使用“balanced”和“high_client_ops”mClock配置文件的回填操作可能比使用“WeightedPriorityQueue”（WPQ）调度程序看到的进度慢。

所有mClock配置文件中的QoS分配都根据上述修复和增强功能进行了优化。

有关更多详细信息，请参阅：https://docs.ceph.com/en/quincy/rados/configuration/mclock-config-ref/

## RGW
- 使用服务器端加密的S3多部分上传现在在多站点中正确复制。以前，此类对象的副本在解密时会损坏。一个新工具，radosgw-admin bucket resync encrypted multipart，可用于识别这些原始的多部分上传。任何已识别对象的LastModified时间戳都增加1纳秒，以导致对等区域再次复制它。对于使用服务器端加密的多站点部署，我们建议在所有区域升级后，针对每个区域的每个存储桶运行此命令。
## dashboard
- 有一个新的仪表板页面，布局改进了。活跃的警报和一些重要的图表现在显示在卡片内。可以通过设置ceph仪表板功能禁用仪表板来禁用这个新的仪表板，并恢复旧的布局。
## ceph-volume
- Bea9f4b中引入的ceph体积回归已经修复，该回归使激活过程需要很长时间才能完成。
	- Related tracker: [https://tracker.ceph.com/issues/57627](https://tracker.ceph.com/issues/57627)
## OSD
- 修复了恢复/回填操作期间CPU利用率高的问题。有关更多详细信息，请参阅：https://tracker.ceph.com/issues/56530。
- Octopus将SnapMapper密钥格式从`<LEGACY_MAPPING_PREFIX><snapid>_<shardid>_<hobject_t::to_str()>修改为<MAPPING_PREFIX><pool>_<snapid>_<shardid>_<hobject_t::to_str()>。当引入此更改时，94ebe0e还引入了一个带有关键错误的转换，该错误通过将遗留密钥映射到<MAPPING_PREFIX><poolid>_<snapid>_`而没有对象唯一后缀，基本上销毁了遗留密钥。转换在此版本中是固定的。相关跟踪器：https://tracker.ceph.com/issues/56147
## RADOS
- “BlueStore零块检测”功能（首次在https://github.com/ceph/ceph/pull/43337中引入昆西）已默认关闭，并有一个名为bluestore_zero_block_detection的新全局选项。此功能用于大规模合成测试，与一些RBD和CephFS功能不能很好地交互。如果配置选项保持设置为false，则在以前的Quincy版本中遇到的任何副作用将不再发生。相关跟踪器：https://tracker.ceph.com/issues/55521
- 在ceph-objectstore-tool中添加离线dup op修剪能力。相关跟踪器：https://tracker.ceph.com/issues/53729
- 修复了日志轮换后不填充群集日志的错误。相关跟踪器：https://tracker.ceph.com/issues/55383