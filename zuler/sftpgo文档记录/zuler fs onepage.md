# 背景
提供给被控网络磁盘功能，zuler fs虚拟出磁盘作为本地磁盘使用
![[Pasted image 20250521120937.png]]
# 服务实现
![[sftpgo组件.png]]
# 功能缺陷
## sftpgo后端对接s3特性缺陷
- 重命名文件操作不是原子的，服务端通过拷贝为新文件再删除原有文件来实现重命名操作
- 不支持对非空目录进行重命名，由于上述实现，非空目录重命名导致需要大量拷贝和删除目录下的所有文件，每个文件都要执行s3的api调用，耗时超出预期
- chtime操作不支持，chtime是修改文件access和mtime的操作接口
- 用户使用s3作为后端存储时根目录会保存部分临时文件
- 不支持需求特殊特性的文件系统，比如ssh fs
## zuler fs逻辑错误
- Zulerfs在上传大文件的过程中检测到文件被修改，则会rst掉正在上传的tcp连接，导致服务端可能由于丢包未收到tcp rst，导致文件读写io超时
![[Pasted image 20250521170002.png]]
```
{"level":"debug","time":"2025-04-30T03:32:33.988","sender":"DAV","connection_id":"DAV_d08pjk8f1osc73fea1ig","message":"connection added, local address \"172.18.0.2:10080\", remote address \"192.168.3.137:61636\", num open connections: 1"}
{"level":"info","time":"2025-04-30T03:32:33.989","sender":"webdavd","content_length":"0","depth":"0","elapsed_ms":0,"method":"PROPFIND","proto":"HTTP/1.1","remote_addr":"192.168.3.137:61636","request_id":"d08pjk8f1osc73fea1ig","uri":"http://192.168.3.2:10080/","user_agent":"Zuler-WebDAV-demo/10.0.19045"}
{"level":"debug","time":"2025-04-30T03:32:33.989","sender":"DAV","connection_id":"DAV_d08pjk8f1osc73fea1ig","message":"connection removed, local address \"172.18.0.2:10080\", remote address \"192.168.3.137:61636\" close fs error: <nil>, num open connections: 0"}
{"level":"debug","time":"2025-04-30T03:32:33.999","sender":"DAV","connection_id":"DAV_d08pjk8f1osc73fea1j0","message":"connection added, local address \"172.18.0.2:10080\", remote address \"192.168.3.137:61636\", num open connections: 1"}
{"level":"info","time":"2025-04-30T03:32:33.999","sender":"webdavd","content_length":"0","depth":"1","elapsed_ms":0,"method":"PROPFIND","proto":"HTTP/1.1","remote_addr":"192.168.3.137:61636","request_id":"d08pjk8f1osc73fea1j0","uri":"http://192.168.3.2:10080/","user_agent":"Zuler-WebDAV-demo/10.0.19045"}
{"level":"debug","time":"2025-04-30T03:32:33.999","sender":"DAV","connection_id":"DAV_d08pjk8f1osc73fea1j0","message":"connection removed, local address \"172.18.0.2:10080\", remote address \"192.168.3.137:61636\" close fs error: <nil>, num open connections: 0"}
{"level":"debug","time":"2025-04-30T03:32:34.063","sender":"DAV","connection_id":"DAV_d08pjkgf1osc73fea1jg","message":"connection added, local address \"172.18.0.2:10080\", remote address \"192.168.3.137:61636\", num open connections: 1"}
{"level":"info","time":"2025-04-30T03:33:34.278","sender":"Upload","local_addr":"172.18.0.2:10080","remote_addr":"192.168.3.137:61636","elapsed_ms":60199,"size_bytes":21686268,"username":"localtest","file_path":"/srv/sftpgo/data/localtest/targetfile1","connection_id":"DAV_d08pjkgf1osc73fea1jg","protocol":"DAV"}
{"level":"info","time":"2025-04-30T03:33:34.278","sender":"webdavd","content_length":"104857600","elapsed_ms":60214,"method":"PUT","proto":"HTTP/1.1","remote_addr":"192.168.3.137:61636","request_id":"d08pjkgf1osc73fea1jg","resp_status":405,"uri":"http://192.168.3.2:10080/targetfile1","user_agent":"Zuler-WebDAV-demo/10.0.19045","error":"read tcp 172.18.0.2:10080->192.168.3.137:61636: i/o timeout"}
{"level":"debug","time":"2025-04-30T03:33:34.278","sender":"DAV","connection_id":"DAV_d08pjkgf1osc73fea1jg","message":"connection removed, local address \"172.18.0.2:10080\", remote address \"192.168.3.137:61636\" close fs error: <nil>, num open connections: 0"}
```
- 1M块大小direct写多个4M文件开启crc32校验时会校验失败
```
$ fio -bs=1M -ioengine=windowsaio -iodepth=1 -numjobs=1 -direct=1 -rw=rw -thread -refill_buffers -randrepeat=0 -group_reporting -name=fio-readwrite-throughput -verify=crc32 -nrfiles=5 -filesize=4M -directory="Z\:targetdir"
fio-readwrite-throughput: (g=0): rw=rw, bs=(R) 1024KiB-1024KiB, (W) 1024KiB-1024KiB, (T) 1024KiB-1024KiB, ioengine=windowsaio, iodepth=1
fio-3.38
Starting 1 thread
verify: bad header rand_seed 6255214275624166944, wanted 5294777006438703540 at file Z:targetdir\fio-readwrite-throughput.0.0 offset 0, length 1048576 (requested block: offset=0, length=1048576)
fio: pid=12812, err=42/file:io_u.c:2290, func=io_u_queued_complete, error=Illegal byte sequence
```
# 性能验证分析
- 测试工具 fio  
- 测试客户端环境：上海办公环境虚拟机  
- 测试服务端环境：上海3.2-3.4三节点ceph存储集群，在3.2节点启动sftpgo服务，和s3对象存储服务
## 性能数据
针对上述io流程，使用ceph fs在windows下挂载的方式进行数据对比，流程组件如下
![[Pasted image 20250521152314.png]]
服务侧存在sftpgo与ceph RGW的io耗时，因此将sftpgo的后端存储设置为服务侧本地文件系统，对比s3交互产生的耗时
![[Pasted image 20250521153157.png]]
# 性能耗时分析
针对如上性能数据，目前网盘方案实现性能较差，初步分析性能耗时差异和原因
避免sftpgo后端使用S3协议耗时影响，因此采用sftpgo后端对接服务侧本地文件系统的方式进行分析
windows自身支持webdav协议的客户端挂载，可使用windows映射网络驱动器挂载与zuler fs的同一个服务端

