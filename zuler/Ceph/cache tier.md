
在R版本已弃用
# cache模式
- **writeback**
`osd_tier_default_cache_min_write_recency_for_promote`参数判断，当满足条件时将数据提升至cache池
- **readproxy**
- **readonly**
# 警告
cache池的提升效果依赖业务数据比较集中的场景，并且需求cache池足够大
- 表现较好的场景：
	- RGW场景下以最近时间为热度的业务数据
- 不利的场景
	- 副本cache和纠删码的RBD
	- 纠删码的cache和RBD

# 操作指令
## 设置缓存池与后段池绑定
```bash
$ ceph osd tier add {storagepool} {cachepool}
$ ceph osd tier cache-mode {cachepool} {cache-mode}
$ ceph osd tier set-overlay {storagepool} {cachepool}
```
## 设置缓存参数
使用bloom过滤去配置hit_set_type
```bash
$ ceph osd pool set {cachepool} hit_set_type bloom
```
`hit_set_count`和`hit_set_period`定义了要存储多少个此类HitSet，以及每个HitSet应该覆盖多少时间
```bash
$ ceph osd pool set {cachepool} hit_set_count 12
$ ceph osd pool set {cachepool} hit_set_period 14400
$ ceph osd pool set {cachepool} target_max_bytes 1000000000000
```
`min_read_recency_for_promote`和`min_write_recency_for_promote`决定了数据是否被提升至缓存层
### 缓存池大小
- **Flushing**
- **Evicting**
设置最大大小
```bash
$ ceph osd pool set {cachepool} target_max_bytes {#bytes}
$ ceph osd pool set {cachepool} target_max_objects {#objects}
```
设置比例，开始flush
```
$ ceph osd pool set {cachepool} cache_target_dirty_ratio {0.0..1.0}
```
到达比例后以更早速度flush
```
$ ceph osd pool set {cachepool} cache_target_dirty_high_ratio {0.0..1.0}
```
缓存留存时间
```bash
$ ceph osd pool set {cachepool} cache_min_flush_age {#seconds}
```
最小的留存时间
```bash
$ ceph osd pool {cache-tier} cache_min_evict_age {#seconds}
```
## 删除缓存池
### 只读缓存
```bash
$ ceph osd tier cache-mode {cachepool} none
$ ceph osd tier remove {storagepool} {cachepool}
```
### 回写缓存
```bash
$ ceph osd tier cache-mode {cachepool} proxy
```
确保缓存池已刷新。这可能需要几分钟时间
如果缓存池中仍有对象，您可以手动刷新它们
```
$ rados -p {cachepool} ls
$ rados -p {cachepool} cache-flush-evict-all
```
删除叠加层，这样客户端就不会将流量引导到缓存
最后，从备份存储池中删除缓存层池。
```
$ ceph osd tier remove-overlay {storagetier}
$ ceph osd tier remove {storagepool} {cachepool}
```