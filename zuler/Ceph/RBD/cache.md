
# RBD 客户端cache
缓存在客户端的内存中，每个RBD映像都有自己的。由于缓存是客户端的本地缓存，如果有其他人访问图像，则没有一致性。在RBD上运行GFS或OCFS在启用缓存的情况下不起作用。
```
rbd_cache
rbd_cache_policy
rbd_cache_writethrough_until_flush
rbd_cache_size
rbd_cache_max_dirty
rbd_cache_target_dirty
rbd_cache_max_dirty_age
```

# RBD持久化只读缓存
对同一父镜像的克隆只读操作缓存
```ini
rbd parent cache enabled = true
rbd plugins = parent_cache
```
`ceph-immutable-object-cache`守护程序负责在其本地缓存目录中缓存父图像内容。使用固态硬盘作为底层存储，可以提供更好的性能。
- 基于域套接字的IPC
- 基于LRU的提升/降级策略：
- 基于文件的缓存存储
