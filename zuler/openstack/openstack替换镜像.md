# 登陆jenkins打包镜像
登陆[jenkins](http://192.168.3.151:10240)，选择对应job，比如[cinder-builder](http://192.168.3.151:10240/job/cinder-builder/)，选择已何如代码的分支，启动job打包
# 提取镜像
- cinder和nova等组件打包完成后被推送至192.186.3.151中
```bash
$ docker images
REPOSITORY                                                                    TAG                          IMAGE ID       CREATED         SIZE
172.16.21.71:7777/openstack/kolla/ubuntu-source-cinder-volume                 wallaby-release-3.0-250619   e4277602f45a   30 hours ago    1.42GB
172.16.21.71:7777/openstack/kolla/ubuntu-source-cinder-backup                 wallaby-release-3.0-250619   4280485e936b   30 hours ago    1.41GB
```
- 打包镜像文件，上传至所需环境中
```bash
$ docker save <REPOSITORY>:<TAG> -o <mytar>.tar.gz
$ scp <mytar>.tar.gz <user>@<target>:~
```
- 在所需环境中加载镜像
```
$ docker load -i <mytar>.tar.gz
$ docker tag <REPOSITORY>:<TAG> <local-REPOSITORY>:<TAG>
```
# 更新镜像
在部署节点上使用cmadmin部署用户
```bash
$ sudo su cmadmin
$ cd /mnt/kvirtdesk-offline/kolla-ansible
```
加载部署环境变量
```
$ source venv/bin/activate
```
添加对应服务tag
```
$ vim /etc/kolla/globals.yaml 
nova_tag: "wallaby-2.0"
ironic_tag: "wallaby-2.0"
<my-image>_tag: "<mytag>"
```

安装部署python库，安装出错时注意检查是否使用cmadmin部署用户
```bash
$ python3 setup.py install
```
更新指定节点，更新出错注意检查加载的是部署环境变量，而非cmadmin部署用户主目录下的环境变量
```
$ kolla-ansible -i multinode deploy -t <组件> --limit <主机名>
```
更新全部节点
```bash
$ kolla-ansible -i multinode deploy -t <组件>
```
[[openstack部署]]
openstack部署完成后将ocfs2的修改更新至集群中
# 部署ocfs2集群[[ocfs2]]
将对应设备挂载至`/var/opt/mnt/ocfs2/<ip-sd*>`目录下
```bash
$ mount /dev/sd* /var/opt/mnt/ocfs2/<ip-sd*>
```
# 修改ansible脚本
添加docker中对ocfs2挂载点配置，注意挂载点目录与其他目录不要有冲突
```
$ vi /etc/kolla/globals.yml +315
  - enable_cinder_ocfs2: "yes"
$ vi ansible/roles/cinder/defaults/main.yml +174
  - "{% if enable_cinder_ocfs2 | bool %}/var/opt/mnt/ocfs2:/var/lib/mnt/ocfs2{% endif %}"
$ vi ansible/roles/nova-cell/defaults/main.yml +362
  - "{% if enable_cinder_ocfs2 | bool %}/var/opt/mnt/ocfs2:/var/lib/mnt/ocfs2{% endif %}"
$ vi ansible/roles/nova-cell/defaults/main.yml +409
  - "{% if enable_cinder_ocfs2 | bool %}/var/opt/mnt/ocfs2:/var/lib/mnt/ocfs2{% endif %}"
```
# 修改集群配置文件
```bash
$ vim /etc/kolla/cinder-volume/cinder.conf
[DEFAULT]
enabled_backends = volumes,ocfs2

[ocfs2]
volume_driver = cinder.volume.drivers.ocfs2.OCFS2Driver
volume_backend_name = ocfs2
max_over_subscription_ratio = 2.0
ocfs2_qcow2_volumes = True
ocfs2_mount_point_base = /var/lib/mnt/ocfs2
ocfs2_snapshot_support = True

$ vim /etc/kolla/nova-compute/nova.conf
[libvirt]
ocfs2_mount_point_base = /var/lib/mnt/ocfs2

$ vim /etc/kolla/nova-libvirt/libvirtd.conf
ocfs2_mount_point_base = "/var/lib/mnt/ocfs2"
```