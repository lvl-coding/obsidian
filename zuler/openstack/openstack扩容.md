# 配置文件
编辑 kvirtdesk-offline/conf/global.cfg，添加扩容的节点，已存在部署过的节点需被注释掉
```
$ vim kvirtdesk-offline/conf/global.cfg
##BASE_CONTROL是控制节点,初始化时部署naginx，keepalive
BASE_CONTROL=$DEPLOYMENT
##BASE_NON_DEPLOY是除去部署节点以外的所有节点，单节点时不需要写，BASE_NON_DEPLOY= 即可
BASE_NON_DEPLOY=cluster90,cluster91
```
切换部署cmadmin用户，加载环境变量
```bash
$ sudo su cmadmin
$ source ~/.venv/env.sh
$ cd /mnt/kvirtdesk-offline/python-venv/
$ ansible-playbook -i inventory local_play.yml # --skip-tags set_net
```
# 扩容节点
进入 kolla-ansible 目录，编辑 multinode 文件，添加相应新节点主机名
- 追加扩容cluster89和cluster90节点
- control节点需为奇数
- network中的已部署的节点不可删除，否则对已部署的vip可以ping通会导致部署出错
```ini
[control]
# These hostname must be resolvable from your deployment host
cluster89 ansible_user=cmadmin    ansible_become=true
#cluster90 ansible_user=cmadmin    ansible_become=true
#cluster91 ansible_user=cmadmin    ansible_become=true

# The above can also be specified as follows:
#control[01:03]     ansible_user=kolla

# The network nodes are where your l3-agent and loadbalancers will run
# This can be the same as a host in the control group
[network]
cluster89 ansible_user=cmadmin    ansible_become=true
cluster90 ansible_user=cmadmin    ansible_become=true
cluster91 ansible_user=cmadmin    ansible_become=true

[compute]
#cluster89 ansible_user=cmadmin    ansible_become=true
cluster90 ansible_user=cmadmin    ansible_become=true
cluster91 ansible_user=cmadmin    ansible_become=true

[monitoring]
cluster89 ansible_user=cmadmin    ansible_become=true

# When compute nodes and control nodes use different interfaces,
# you need to comment out "api_interface" and other interfaces from the globals.yml
# and specify like below:
#compute01 neutron_external_interface=eth0 api_interface=em1 storage_interface=em1 tunnel_interface=em1

[storage]
cluster89 ansible_user=cmadmin    ansible_become=true
cluster90 ansible_user=cmadmin    ansible_become=true
cluster91 ansible_user=cmadmin    ansible_become=true
```
指定扩容命令
```bash
$ kolla-ansible -i multinode bootstrap-servers --limit compute
$ kolla-ansible -i multinode prechecks --limit compute
$ kolla-ansible -i multinode deploy --limit compute
```
取消 multinode 文件注释，重新推监控配置文件
```bash
$ kolla-ansible -i multinode genconfig -t prometheus
```