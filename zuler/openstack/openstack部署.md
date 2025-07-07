# 参考文档
[生产环境组件升级操作](https://wiki.todesk.com/pages/viewpage.action?pageId=100863523)  
[云桌面部署手册](http://192.168.103.32:8888/离线部署仓库/云桌面产品部署手册-V2.0.10.pdf)
# 环境
- 广州虚拟机3台计算节点
- 系统盘250G
# 安装包准备
- 离线安装包[kvirtdesk-offline-v1.0-rc1-22.04.4-250603.tar.gz](http://192.168.103.32:8888/%E7%A6%BB%E7%BA%BF%E9%83%A8%E7%BD%B2%E4%BB%93%E5%BA%93/kvirtdesk-offline-v1.0-rc1-22.04.4-250603.tar.gz "kvirtdesk-offline-v1.0-rc1-22.04.4-250603.tar.gz")
	- 将压缩包解压至/mnt/目录下
- docker资源压缩包[image-data-wallaby2.0.7-ha.tar.gz](http://192.168.103.32:8888/离线部署仓库/image-data-wallaby2.0.7-ha.tar.gz)
	- 压缩包保存在/mnt/kvirtdesk-offline/server/docker-registry 目录下
# 主节点用户初始化
# 基础服务初始化
编辑`conf/global.cfg`配置文件，指定当前主机ip和网卡名
## 配置网络
网络配置仅可执行一次，会将配置文件中的网卡更名为ens1和ens2
## 配置源及资源初始化
离线部署包仅提供了本地的apt源，因此在内核版本较新时会有软件包由于版本不兼容安装失败的情况，因此需在失败后将脚本注释的默认系统源开放
```
$ cp /etc/apt/sources.2025-06-13_08-17-02 /etc/apt/sources.list.d/sources-packages.list
```
# Ansible 多节点部署
## 配置文件编辑
编辑`conf/global.cfg`配置文件
```ini
BASE_CONTROL=$DEPLOYMENT
##BASE_NON_DEPLOY是除去部署节点以外的所有节点，单节点时不需要写，BASE_NON_DEPLOY= 即可
BASE_NON_DEPLOY=cluster90,cluster91
```
编辑/etc/hosts文件
```
127.0.0.1 localhost

192.168.10.89 cluster89
192.168.10.90 cluster90
192.168.10.91 cluster91
```
## 引导服务
安装ansible 虚拟环境及生成 inventory
## 服务部署
多节点部署时脚本在其他节点时的部署节点名会识别失败，需手动将节点名修改为ip
```bash
_setup_apt() {
	if [ -e /etc/apt/sources.list ];then
		mv /etc/apt/sources.list /etc/apt/sources."$(date +%Y-%m-%d_%H-%M-%S)"
	fi
	cat <<EOF > /etc/apt/sources.list.d/debian-packages.list
	deb http://<DEPLOY-ip>:7776/ focal main
EOF
	apt-key add ./public.key
	apt-get update
}
```
# 存储服务
修改ceph下磁盘类型和OSD设备列表
```ini
#自定义磁盘类型名
DEVICE_CLASS="lhd"    
#原始磁盘类型名
SRCDEV_CLASS="hdd"    
```
# 虚拟化配置
配置默认仅在控制节点上安装openstack集群，当期望部署其他多个节点时需修改配置文件如下字段
```ini
# 云管服务
## 多节点时加入其他节点<,主机名>,如CONTROL=$BASE_CONTROL,vm001
CONTROL=$BASE_CONTROL
NETWORK=$BASE_CONTROL
COMPUTE=$BASE_CONTROL,cluster90,cluster91
MONITORING=$BASE_CONTROL
STORAGE=$BASE_CONTROL
```