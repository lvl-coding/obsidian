[[集群pg状态]]
# PG作用
1. 连接客户端和objectstorage的桥梁，对OP请求进行转换，实现OSD之间的数据同步
2. 组成存储池的基本单位：副本、纠删码等数据备份策略依托PG实现
3. 允许用户自定义故障域的备份策略，实现分布式数据一致性
# PG优点
1. 能够对数量可用的PG进行精准的资源分配
2. 以PG为单位的数据同步、迁移、一致性校验相比于对象更易操作
# PG状态机
1. Initial
2. Reset-根据PG是否会进入新的Interval来确认是否需要重启Peering。重启则状态机进入Reset状态-[查看OSDMap信息](https://www.cnblogs.com/sisimi/p/7737177.html)
3. Started
	1. Start
	2. Primary
		1. Peering
			1. GetInfo-对象元数据信息，主要描述一个interval内的对象操作信息
			2. GetLog-对象元数据信息，描述PG一段时间内所有对象的操作信息
			3. GetMissing
			4. WaitUpThru
			5. Down-Peering过程中一个PG interval中存活的副本不足以完成数据恢复
			6. Incomplete-Peering过程中无法选出权威日志
		2. WaitActingChange
		3. Active
			1. Activating
			2. Recovering-按照Peering结果进行降级对象的修复(增量同步)
			3. WaitLocalRecoveryReserved-等待本地recovery资源预留
			4. WaitRemoteRecoveryReserved-等待副本recovery资源预留
			5. NotBackfilling
			6. Backfilling
			7. WaitLocalBackfillReserved
			8. WaitRemoteBackfillReserved
			9. Recovered
			10. Clean
	3. Stray-副本所在的OSD不是当前的up或者acting成员
	4. ReplicaActive
		1. RepNotRecovering
		2. RepWaitRecoveryReserved
		3. RepWaitBackfillReserved
# 状态迁移
[ceph存储 PG的状态机 源码分析](https://vigourtyy-zhg.blog.csdn.net/article/details/104309646)
## 创建pg
1. Epoch检查，保证OSDMap同步
2. 单个OP创建多个PG
3. 常规检查
	1. 存储池
    2. Primary
    3. Up Set和Acting Set
4. 更新心跳检测OSD列表
5. 检查PG是否已存在
6. 检查PG是否在异步删除队列中
7. handle_pg_peering_evt通过NullEvt触发初始化创建
    1. 调用事务接口，创建Collection和元数据对象
    2. 创建PG内存结构，并加入到OSD的全局pg_map
    3. 填充Info，写入到元数据中的omap
    4. 向状态机投递Initialize和ActMap
    5. 如果是主OSD，设置状态为Creating，状态机进入Started/Primary/Perring/GetInfo状态，准备peering
    6. OS执行事务，并派发消息
8. 将peering事件加入PG的peering_queue队列，并把PG加入到OSD的peering_wq队列，等待处理
## Perring
### 常用概念
- acting set-按照一定的规则选择出来，保存某个interval的PG副本集合
- up set-基于Crush算法计算出来的副本集合
- pg temp-临时的PG，将其加入acting，用来保存当acting和up不一致是，保持acting为能接受IO的副本集合，知道up中的副本完全恢复
- current interval-当前PG的acting和up未发生变化的epoch序列
- past interval-上一次的acting和up未发生变化的epoch序列
- pg log-PG内所有更新的日志操作记录
- pg info-关于PG在OSD上的元数据信息，主要是一个interval内的对象操作信息
- Authoritative History-权威日志，代码中使用olog表示；主要通过状态机GetLog事件获取
### 整体简述
1. 生成past_interval，要根据interval确定每个interval期间的OSD集合
2. 根据past_interval选取参与Peering过程的OSD集合build_prior
3. 拉取prior集合中所有OSD的pg_info
4. 拉取权威的OSD
5. 拉取权威OSD的log与info，与本地log合并，形成本地missing结构
6. 拉取其他OSD的log与info，与auth log对比，将差异log发送给peer，并且在本地形成peer_missing结构
7. 根据missing和peer_missing结构可知丢失数据定位
8. peering处理成功，开始数据的修复
### 流程
1. osd_tp线程会并发处理PG
2. 创建RecoveryCtx，批量收集所有PG的Query Info Log Notify等
3. 针对每个PG
    1. 获取最新的OSD Map，检查PG是否需要更新，不需要则直接在状态机中处理
    2. 在advance_pg内部更新完成后再次加入peering_wq
        1. 如果更新版本号过多，会分多次进行更新
        2. 每更新一次OSDMap，都会同步向状态机中投递一个AdvMap事件，包括新老OSDMap和PG的Up/ActingSet信息。根据PG是否会进入新的Interval来确认是否需要重启Peering。重启则状态机进入Reset状态，对PG进行去活，暂停Scrub Recovery 丢弃未完成的OP等，清理Active Recovering等状态，准备执行Peering。advance_pg完成同步OSDMap后，投递一个ActMap事件，PG可以开始Peering 
4. 派发所有PG的Query Notify消息
#### past_intervals
- intervals与Acting Set Acting Primary Up Set Up Primary相关，有变化则是一个新的intervals。
- maybe_want_rw通过Acting Primary Acting Set和up_thru判断是true或false。
    - up_thru参数会在每当PG完成peering时，其归属OSD在OSDMonitor中设置。
- 更新完成past_intervals后，PG会同步更新Info中的same_interval_since为最新的Interval，并和past_intervals一起写入本地磁盘
#### GetInfo
- 状态机在Reset下收到ActMap事件即可正式开始Peering。
    - PG根据在OSDMap中的身份向状态机发送MakePrimary，状态机则进入Started Primary Peering GetInfo状态
    - 或者发送或者MakeStray事件，状态机进入Started Stray状态，PG实例需要当前Primary根据peering进一步确认其身份
    - Primary根据past_intervals收集Peering所需的全部OSD信息，加入到PriorSet集合中。
    - interval要在last_epoch_started之后
        - 当Peering结束前，Primary会通知所有副本更新Info中的last_epoch_started为本次Peering完成的epoch，固化至本地磁盘。Primary在Info属性之下记录的是Primary发出的最终epoch，在History之下的epoch则是所有副本更新完成的记录。所有副本记录完成后，Primary会设置PG为Active状态。Acting Set不满足则是Peered状态。History中的记录也就是PG最近能够接受读写请求的epoch，核心是为了更新info日志
    - Interval中的maybe_went_rw不能为false
    - Interval中Acting Set的OSD不足以完成数据修复，所有副本都离线或纠删码的OSD数量不足，则PG为Down状态，否则PriorSet则成功，继续进行Peering
    - 从所有past_intervals中有效的Interval中的新日志组成权威日志进行数据同步。
        - pg日志更新时会记录log_tail和last_update信息
        - Primary首先会向PriorSet中的OSD发送Query消息，拉取Info来获取日志的概要信息。
            - Primary共会向Stray发送Query Info Log三种类型消息。Query仅收集Info和Log
### GetLog
- Primary向状态机发送GotInfo事件，跳转至Started Primary Peering GetLog状态，开始日志同步
    1. 优先选取内容最新的日志
    2. 再优先选取日志条目多的日志
    3. 再优先选取当前的Primary
- 选取失败则进入Started Primary Peering Incomplete状态
- 首先确认PriorSet中要同步的OSD，choose_acting
    - 首先选择当前Up Set中的副本。如果当前的副本必须通过Backfill同步，则所需时间会较久。而Primary承载读写IO，当Primary需要Backfill时则需要生成PG Temp进行过度。
        - PG Temp替换PG对于CRUSH计算出的Acting Set。而PG Temp失效则使用原始计算结果。当ActingSet和Up Set由于PG Temp的替换而不一致被称为Remapped。Up Set中的部分OSD在Backfill，和Acting Set中所有OSD组成的集合为ActingBackfill。
        - 客户端的读写IO由Acting Set中的第一个OSD即Acting Primary承载。PG Temp完成peering之后将读写IO重定向到作为新Primary的PG Temp上。当Up Set完成数据同步时，可通知OSDMonitor取消PG Temp，使Acting Set和Up Set一致，读写IO切回计算出的Primary
            - 如果写请求所在的对象正在Backfill，则阻塞，正在运行的OSD完成这个对象的Backfill后则正常执行事务，其他没有开始Backfill该对象的OSD则记录一个空事务，用于更新日志统计等元数据信息
- choose_acting选出的结果是want_acting。
    - Up Set中的Primary能够修复或自身即是权威日志。否则选择权威日志作为Primary，并加入到want_acting
    - 从Up Set中所有其他OSD，如果还能修复则加入want_acting列表，否则加入Backfill列表
    - want_acting大小满足存储池副本数则停止，否则从Acting Set中选择能够基于日志修复的OSD加入列表
    - 若还不满足，则从所有返回过Info的OSD选取能够基于日志修复的OSD
    - 如果选不出来足够的副本数，则PG进入Incomplete状态
    - 选出来的want_acting和当前的Acting Set不一致则需要PG Temp过渡，Primary向Monitor发送设置PG Temp请求，并向状态机投递NeedActingChange事件，状态机被设置为Started Primary WaitActingChange状态。
        - Prime PG Temp对PG Temp预填充。OSDMonitor在OSDMap变化时根据两次不同的PG映射结果判断是否PG会Remapping，有则预先填充对应的PG Temp。如果预先的PG Temp不相符Primary仍可发送请求到OSDMonitor进行修改
    - PG Temp生效或者Acting Set 和Up Set 一致时，Primary进行日志同步，如果权威日志不在本地则先拉取，仅拉取所有Acting Set中最小的last_update到最新的日志。
    - 权威日志与本地日志修复
        - 更老的日志则追加到本地日志的尾部
        - 更新的日志则追加到本地日志头部
            - 存在日志分歧
    - Primary本地有对象需要修复则加入missing列表，完成合并后则发送GotLog事件，切换至Started Primary Peering GetMissing状态，固化日志和missing列表。向所有能通过Recovery回复的日志发送Query消息。获取日志与权威日志比对构建所有Peer的missing列表，准备执行Recovery
### GetMissing
missing列表记录了need和have两个版本号
- 分歧日志按照对象分类在同一个队列中
    - 本地日志有分歧日志，更新的日志则将对象加入missing列表，have为0，need为权威日志版本号
    - 对象之前不存在则直接删除对象
    - 对象在missing列表之中，则需要对象回滚到prior_version即分歧日志生效前的版本，need设置为prior_version，如果have为prior_version则对象可直接从missing列表移除。
    - 对象不在missing列表且所有分歧日志都可以回滚则进行回滚
    - 对象不在missing列表有的分歧日志不能回滚则删除本地对象，加入到missing列表，need设置为prior_version，have为0；
- Primary生成所有Peering的missing列表后向状态机投递Activate事件，进入Started Primary Active Activating状态。
### Activate
固化Peering的成果
- Info和History子属性下的last_epoch_started分别记录了Primary自身Peering完成的epoch和所有副本Activate完成后的版本
- 根据ActingBackfill中的实际情况分别发送Info或者Log消息转化为Started ReplicaActive状态，并参与到后续客户端读写 Recovery或Backfill中来
    - Peer不需要修复则直接发送Info更新last_epoch_started即可
    - Peer需要Backfill，设置Info中的last_backfill为空，发送Log消息，指示后续进行Backfill
    - Peer可以Recovery，发送Log消息，携带需要的那部分日志
- Peer收到Log消息后首先检查需要重启Backfill初始化Backfill设置。可以Recovery则合并生成完整日志并构建missing列表。向状态机投递Activate事件，将自身状态机从Started Stray切换为Started ReplicaActive状态，本地OS固化Info和日志信息
- Peer收到Info消息则说明自身数据已经和权威日志同步，但可能有分歧日志。向状态机投递Activate事件，进入Started ReplicaActive状态开始固化自身Info，本地事务成功后向Primary回复Info消息表明固化成功
- 修复对象需要记录所有missing列表中的条目和所在位置信息，即MissingLoc
- MissingLoc内部分为need_recovery_map和missing_loc，missing_loc中内个待修复对象可能存在于多个PG实例之上，因为是这些PG的集合，
- MissingLoc由Primary负生成，将所有Peer的missing中的条目加入到need_recovery_map中，并根据Peer的Info和missing列表将对象目标版本的位置信息填充到MissingLoc中
    - 对象need比Peer的last_update还大则Peer中不存在，跳过
    - Peer需要或继续执行Backfill，但还没到对象，直接跳过
    - 对象也在Peer的missing列表中，跳过
    - 将Peer加入Missing_loc
- 生成MissingLoc后，needs_recovery_map有对象Primary设置自身状态为Degraded Activating，Monitor只收集Primary的状态作为PG的状态。Primary 检测当前Acting Set不满足副本数则设置Undersized状态，通过OS接口固化Peering结果，等待其他Peer的Peering完成。
- 所有的Activate操作完成后，Primary向状态机投递一个AllReplicasActivated事件来清除自身的Activating状态和Creating状态，如果PG此时的Acting Set不满足副本数，则将PG状态设为Peered并停止处理，否则设置为Active并接受之前阻塞的客户端OP。Recovery和Backfill可以切换至后台进行
## Recovery
Primary向状态机投递DoRecovery事件，切换至Started Primary Active WaitLocalRecoveryReserved状态，PG进入Recovery_wait状态
- OSD中有对Recovery速率限制参数
- 首先向主OSD申请Recovery资源预留，将PG加入到OSD的异步资源预留队列实现，达到上限则排队，指定回调函数
- Primary本地Recovery资源预留成功，向状态机投递LocalRecoveryReserved事件，切换至Started Primary Active WaitRemoteRecoveryReserved状态，并通知所有参与Recovery的Peer即ActingBackfill中除Primary外的OSD进行资源预留，Peer资源预留操作与Primary类同，Primary检测所有Peer预留完成后，投递AllRemoteReserved事件，进入Started Primary Active Recovering状态，开始Recovery，PG状态从Recovery_wait切换到Recovering
- Recovery的PG需要进入OSD的op_shardedwq队列与读写op竞争，可通过优先级或Recovery的Qos进行限制
- Primary可通过Pull从missing_loc中拉取指定版本对象到本地，向将本地修复完成后及时响应客户端的读请求
    - 日志中last_requested为Activate过程中升成的本次Recovery的起始版本号
    - 将所有修复对象按照日志版本号排列，从不小于last_requested的对象开始，记录其当前日志版本号和待修复的版本号
    - 如果不是head对象，需要先检查是否优先修复head对象或者snapdir对象
    - 根据PGBackend生成Pull op
    - 更新last_updated为v
    - 没有被限速则继续修复下一个对象，上限后则批量发送Pull消息，Pull消息随机发送到missing_loc中的PG集合中。
- Primary自我修复完成后，通过Push主动推送每个Replica中需要的指定版本对象，对Peplica进行修复
- Recovery的所有操作为对全对象拷贝修复，因为PG日志信息没有对修改的详细描述信息
- 所有对象修复完成后，Primary通知所有Replica释放占用的Recovery资源
- 如果要进行Backfill，则Primary无需释放自身占用的资源。直接投递RequestBackfill事件，清除Recovering状态，状态机切换至Started Primary Active WaitRemoteBackfillReserved状态，准备Backfill

|                                             |                                  |
| ------------------------------------------- | -------------------------------- |
| osd_max_backfills                           | 控制同时执行Recovery的PG个数              |
| osd_max_push_cost  <br>osd_max_push_objects | OSD中push请求的最大字节数和对象数             |
| osd_recovery_max_active                     | 单个OSD允许同时执行Recovery的对象数          |
| osd_recovery_op_priority                    | Recovery op默认在op_shardedwq中的优先级  |
| osd_recovery_sleep                          | Recovery op在op_shardedwq中的预先休眠时间 |
|                                             |                                  |
### Recovery综述
1. 基于日志pglog对数据进行修复，最终将PG变为active+clean
2. 当Peering完成时，Primary检测到ActingBackfill中任意一个副本存在降级对象，那么需要尝试通过日志进行修复
### 过程说明
1. 资源预留
	1. 核心是通过工作队列使用QOS对recovery的IOPS进行限制，防止对正常业务造成冲击
	2. Primary主导发起资源预留
	3. 加入全局异步资源预留队列
	4. 如果资源分配达到上限，则会进行请求排队，否则直接取出队头PG，分配资源
	5. Primary预留成功后，通知参与recovery的副本进行资源预留，直至所有副本预留成功再执行下一步
2. 对象修复
	1. 方式
		1. pull-Primary自身存在降级对象，有Primary按照missing_loc选择合适的副本去拉取降级的对象到本地进行修复
		2. push-Primary感知到一个或多个副本存在降级，主动推送每个降级对象权威版本至相应副本进行修复
	2. 内容-依据pg_log_entry_t(包含再GetLog中获取的olog)
		1. MODIFY-删除之外的写操作(包括创建)
		2. CLONE-处理head对象时内部产生的克隆操作
		3. DELETE-删除对象
		4. LOST_REVERT&LOST_DELETE-`ceph pg <pgid> mark_unfound_lost revert|delete`
			1. revert之后会为对象生成新日志，回滚对象为能找到的最新版本
			2. delete生成新的LOST_DELETE日志
	3. 实现
		1. 整对象拷贝
			1. 拷贝内容
				1. 数据
				2. 扩展属性
				3. omap
			2. 拷贝过程
				1. 当目标对象内容较多，包含大量扩展属性时很难一次性交互完成
				2. 目的端建立一个临时对象
				3. 当对象拷贝完再删除本地同名对象+重命名临时对象
			3. 缺点
				1. 效率低下-仅仅修改对象的某一个扩展属性也可能导致拷贝全部内容
				2. 会导致OSD残留大量临时对象，上电时予以清除，造成上电时间过长，会增加数据损坏风险
		2. 删除对象-删除对象的方式进行修复
3. 降级对象访问
	1. 客户端发起读请求，待访问对象处于降级
		1. 待访问对象处在Primary，则需等待Primary Pull修复完再处理
		2. 待访问对象在副本上则无影响
	2. 客户端发起写请求，待访问对象处于降级-必须先修复该对象的所有降级副本才能处理写请求，修复方式：Primary主导，先修复Primary，再修复其他副本
### 缺陷
1. 修复手段单一且粗糙，整对象拷贝，消耗大量网络带宽和磁盘带宽
2. 客户端访问降级对象，需完全修复降级对象才能继续处理客户端请求
### 缺陷改进思路
1. 改进日志系统，使之能够回溯历史操作的详细信息，从而修复时只修复降级对象与其权威版本之间差异的部分
## Backfill
- Primary通知所有参与的PG进行Backfill资源预留，等待的过程为Backfill_wait状态
- 资源预留成功则向状态机投递AllBackfills-Reserved事件，Primary将内外状态都切换为Backfilling。
- 任意PG实例预留资源的OSD空间不足则将流程挂起，并释放之前申请的预留资源，进入Backfill_toofull状态。向定时器注册回调函数，定时重新尝试预留资源，知道可以进行Backfill为止
- Backfill根据对象哈希值排序全量拷贝对象写入目标PG中，同步的时间较长，需要使用PG Temp临时接受客户端的读写请求。
- 由于同步的数据量大，完成一个对象后同步更新Info中last_backfill的属性记录进度
- Backfill完成后，Primary向状态机投递Backfill事件，状态机切换至Started Primary Active Recovered状态
    - 清除Backfilling状态，向Monitor发送空PG Temp消息，同步Acting Set回Up Set
    - 向状态机发送GoClean事件，状态机切换至Started Primary Active Clean状态
- 空PG Temp在OSDMap生效后，PG关联的Acting Set 和Up Set一致，再次经过Peering PG进入Active+Clean状态，可以删除不必要的Stray副本