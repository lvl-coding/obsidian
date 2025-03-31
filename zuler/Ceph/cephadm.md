[https://docs.ceph.com/en/pacific/cephadm/install/](https://docs.ceph.com/en/pacific/cephadm/install/)  
cephadm bootstrap --mon-ip *<mon-ip>*  
此命令将：

- 在本地主机上为新集群创建监视和管理守护进程。
- 为 Ceph 集群生成新的 SSH 密钥并将其添加到 root 用户的/root/.ssh/authorized_keys文件中。
- 将公钥的副本写入/etc/ceph/ceph.pub。
- 将最小配置文件写入/etc/ceph/ceph.conf。此文件是与新集群进行通信所必需的。
- 将client.admin管理密钥写入/etc/ceph/ceph.client.admin.keyring。
- 将_admin标签添加到引导设备。一般其他_admin的设备也将获得/etc/ceph/ceph.conf和/etc/ceph/ceph.client.admin.keyring的复制
 
--log-to-file  
指定日志文件，默认会输入到标准输入输出或journald中
 
--cluster-network  
指定CIDR
 
--output-dir  
指定ceph集群文件存放路径
 
--config  
指定配置文件
 
--ssh-user  
指定ssh用户，密钥填写在==/home/*<user>*/.ssh/authorized_keys==.中，该用户须有不用密码的sudo权限
 
--registry-json  
下载docker的配置文件eg: {"url":"REGISTRY_URL", "username":"REGISTRY_USERNAME", "password":"REGISTRY_PASSWORD"}￼  
cephadm shell  
会在安装了所有 Ceph 软件包的容器中启动 bash shell，会在/etc/ceph目录下找到密钥和配置文件，由此进入docker环境中。在mon节点上运行时则会在mon容器中获取配置，如果指定了--mount参数，则路径会挂在到docker中的/mnt路径下
	--images 指定cephadm进入shell环境时的ceph 镜像版本
 
# 解散集群