ceph中的RBD块设备可提供给OpenStack的块设备服务组件cinder作volume后端存储设备
cinder除可配置RBD外，还可支持nfs等其他共享存储配置访问方法。可为nova虚拟机提供卷
# 配置参数
## ceph配置
创建存储池  
```  
$ ceph osd pool create volumes  
$ ceph osd pool create images  
$ ceph osd pool create backups  
$ ceph osd pool create vms  
```  
初始化存储池为RBD类型  
```  
$ rbd pool init volumes  
$ rbd pool init images  
$ rbd pool init backups  
$ rbd pool init vms  
```  
创建提供给cinder用的访问用户和密钥  
```  
$ ceph auth get-or-create client.glance mon 'profile rbd' osd 'profile rbd pool=images' mgr 'profile rbd pool=images'  
$ ceph auth get-or-create client.cinder mon 'profile rbd' osd 'profile rbd pool=volumes, profile rbd pool=vms, profile rbd-read-only pool=images' mgr 'profile rbd pool=volumes, profile rbd pool=vms'  
$ ceph auth get-or-create client.cinder-backup mon 'profile rbd' osd 'profile rbd pool=backups' mgr 'profile rbd pool=backups'  
```
## openstack配置
```
$ vim /etc/glance/glance-api.conf
[glance_store]
stores = rbd
default_store = rbd
rbd_store_pool = images
rbd_store_user = glance
rbd_store_ceph_conf = /etc/ceph/ceph.conf
rbd_store_chunk_size = 8

$ vim /etc/cinder/cinder.conf
[DEFAULT]
...
enabled_backends = ceph
glance_api_version = 2
...
[ceph]
volume_driver = cinder.volume.drivers.rbd.RBDDriver
volume_backend_name = ceph
rbd_pool = volumes
rbd_ceph_conf = /etc/ceph/ceph.conf
rbd_flatten_volume_from_snapshot = false
rbd_max_clone_depth = 5
rbd_store_chunk_size = 4
rados_connect_timeout = -1
rbd_user = cinder
rbd_secret_uuid = 457eb676-33da-42ec-9a8c-9293d545c337
rados_connection_interval = 5  
rados_connection_retries = 3  
rbd_cluster_name = ceph  
replication_connect_timeout = 5


[libvirt]
...
rbd_user = cinder
rbd_secret_uuid = 457eb676-33da-42ec-9a8c-9293d545c337

```

# cinder组件
## cinder-api
![[Pasted image 20250529160224.png]]

作为一个Web Server Gateway Interface，向openstack集群内部的其他组件提供统一的RESTful接口，可处理虚拟机或其他上层组件对块设备的处理请求
## cinder-scheduler
上层对volume请求的调度器
## cinder-volume
对后端块设备管理，提供不同存储的访问dirver，对RBD则为rbd-iscsi
- Driver 框架

![[Pasted image 20250529161833.png]]
### cinder对RBD接口
![[Pasted image 20250530150501.png]]
#### `cinder.volume.drivers.rbd.RBDDriver`
import了ceph的librados库ceph/src/pybind/rados/rados.pyx
- RBDVolumeProxy 管理RBD卷的上下文管理器。其主要目的是简化 RBD 卷的连接、打开、关闭等生命周期管理
- RADOSClient 管理 Ceph RADOS 连接的上下文管理器。简化 Ceph 集群连接和断开的流程，并自动处理相关资源的释放
- RBDDriver 从driver.BaseVD中继承基本方法
	- get_driver_options 获取基本参数
	- do_setup 创建复制配置，获取目标端及相关参数
	- check_for_setup_error预先检查，检查完成后启动后台任务
		- `_trash_purge` 后台循环执行trash清理，默认60s
	- RBDProxy 线程包装RBD
	- `_connect_to_rados`失败后重试，间隔5s，次数3次
		- Rados.connect()
	- `_disconnect_from_rados` 关闭上下文，关闭rados连接
	- `_get_backup_snaps` 获取后段ceph集群中的卷快照
	- `_update_volume_stats`
		- `_get_fsid`
		- `_get_pool_stats` ceph df获取存储池使用情况
		- `_get_usage_info` RBD size()获取卷空间使用情况
	- create_cloned_volume
		- `_extend_if_required` 扩展卷的容量
		- RBDProxy().clone()
		- `_get_clone_depth`递归获取克隆深度
			- `_get_clone_info`判断卷是否为克隆卷，是则返回其父卷和快照的信息
			- `_get_clone_depth`
		- `_setup_volume` 根据当前卷状态的需要，动态配置卷的复制和多挂载这两种互斥特性
		- `_extend_if_required`
	- `_enable_replication` 开启卷复制功能
	- `_disable_replication`
	- `_is_replicated_type`
	- `_enable_multiattach` 开启卷多挂载功能
	- `_disable_multiattach`
	- `_is_multiattach_type`
	- create_volume
		- `_create_encrypted_volume` 创建加密卷
		- RBDProxy().create
		- `_setup_volume`
		- RBDProxy().remove 抛出异常则删除创建的资源
	- create_volume_from_snapshot
		- `_clone`
			- `_get_stripe_unit` 确定克隆卷（cloned volume）应使用的条带单元（stripe unit）大小
			- RBDProxy().clone
			- `_setup_volume`
			- RBDProxy().remove
		- `_flatten` 将所有数据块从父镜像复制到当前子镜像，使其不再依赖父镜像。这样做可以提升独立性，但会占用更多存储空间。
		- `_calculate_new_size` Image.size()
		- `_resize` Image.resize()
		- `_show_msg_check_clone_v2_api`
	- delete_volume
		- `_delete_backup_snaps`
			- `_get_backup_snaps` 获取rbd img的所有快照
			- Image.remove_snap()
		- `_get_clone_info`
		- if
			- `_try_remove_volume`
				- RBDProxy().remove
				- RBDProxy().trash_move
			- `_delete_clone_parent_refs` 
				- `_get_clone_info`
				- RBDProxy().trash_move/RBDProxy().remove
				- `_delete_clone_parent_refs` 递归调用
		- else
			- RBDProxy().rename
	- create_snapshot 
		- Image.create_snap()
		- Image.protect_snap()
	- delete_snapshot
		- Image.unprotect_snap()
		- `_get_children_info` Image.list_children()
		- Image.remove_snap()
	- revert_to_snapshot Image.rollback_to_snap()
	- retype `_setup_volume`
	- demote_volume
		- `_exec_on_local_volume`
	- promote_volume
		- `_exec_on_local_volume`
	- failover_host
		- failover
			- `_get_failover_target_config`
				- `_get_target_config`
			- `_demote_volumes`
				- `_exec_on_volume`
			- `_failover_volume`
				- `_exec_on_volume`
		- failover_completed
			- `_get_failover_target_config`
				- `_get_target_config`
	- initialize_connection
		- `_get_mon_addrs` ceph mon dump获取mon服务信息
		- `_get_config_tuple`
	- clone_image
		- `_is_cloneable`
			- `_parse_location`
			- `_clone`
			- `_resize`
	- copy_image_to_encrypted_volume
		- `_copy_image_to_volume`
			- image_conversion_dir
			- `_encrypt_image`
				- check_encryption_provider
				- image_conversion_dir
				- convert_image
				- os.rename()
				- delete_if_exists
			- delete_volume
			- `_resize`
			- `_setup_volume`
	- copy_image_to_volume
		- `_copy_image_to_volume`
	- copy_volume_to_image
		- `_get_fsid`
		- `_copy_voulme_and_create_snap`
			- Image.copy()
			- Image.create_snap()
			- Image.protect_snap()
		- image_conversion_dir
		- `_try_execute`
		- upload_volume
	- extend_volume
		- `_resize`
	- manage_existing
		- Image.rename()
	- manage_existing_get_size
		- Image.size()
		- Image.close()
	- get_manageable_volumes
		- RBD.list()
		- extract_id_from_volume_name
		- `_get_image_status`
		- paginate_entries_list
	- update_migrated_volume
		- RBD.rename()
	- migrate_volume
		- RBD.copy()
		- delete_volume
	- manage_existing_snapshot_get_size
		- rename_snap
	- manage_existing_snapshot
		- protect_snap
	- get_manageable_snapshots
		- RBD.list()
		- extract_id_from_snapshot_name
	- unmanage_snapshot
	- get_backup_device
#### `cinder.volume.drivers.ceph.rbd_iscsi.RBDISCSIDriver`
- 使用rbd_iscsi_client库创建RBD client
- do_setup通过配置文件获取client和api
- find_client_luns 从initiator扫描lun
- initialize_connection 将卷连接到主机
	- create_client从网关查找client，没有则创建
	- create_disk 卷不存在则创建卷
	- register_disk/unregister_disk 向网关注册卷/取消注册
	- export_disk/unexport_disk 导出卷到initiator/从initiator移除卷
- terminate_connection
	- `_terminate_all`
		- `_get_disks`
		- `_terminate_connection`
		- `_delete_disk`
	- `_terminate_connection`
		- unexport_disk
		- unregister_disk
		- `_get_config`
		- find_client_luns
		- client.delete_client
### Plugin 框架
![[Pasted image 20250529162016.png]]
## cinder-backup
提供 Volume 的备份功能
![[Pasted image 20250529162609.png]]
## Volume Provider
![[Pasted image 20250529162717.png]]
## 中间件
- **Messaging queue**：Routes information between the Block Storage processes.
- **DB**：sql database for data storage. Used by all components (LINKS NOT SHOWN).
