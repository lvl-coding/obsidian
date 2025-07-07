# 服务端安装
```
$ apt-get install tgt -y
```
## 使用`tgtadm`生成临时iscsi 源
```bash
# 创建tgtadm （一般 -T 源自自身/etc/iscsi/initiatorname.iscsi配置 ）
tgtadm --lld iscsi --op new --mode target --tid 1 -T iqn.2005-03.org.open-iscsi:test
# 扩展
tgtadm --lld iscsi --op new --mode target --tid 2 -T iqn.2005-03.org.open-iscsi-2:test
# 添加硬盘
tgtadm --lld iscsi --op new --mode logicalunit --tid 1 --lun 1 -b /dev/mapper/ubuntu--vg-iscsi_test --bsoflags="sync"
# 添加网段访问权限
tgtadm --lld iscsi --op bind --mode target --tid 1 -I 192.168.10.0/24
# 设置可以访问的设备
tgtadm --lld iscsi --op bind --mode target --tid 1 -I ALL
# 查看
tgtadm --lld iscsi --op show --mode target
```
根据配置生成用户配置文件的文本写入配置文件使永久生效，但需将生成文本中的initiator-name替换为initiator-address，否则该配置不完整
```bash
$ tgt-admin --dump
<target iqn.2005-03.org.open-iscsi:test>
	backing-store /dev/mapper/lvmdisk-iscsi_test
	initiator-name ALL
</target>
```
## 直接编辑配置文件设置iscsi源
```
$ vim /etc/tgt/conf.d/iscsi.conf
# 定义LUN（逻辑单元号）的名称。
<target iqn.2021-03.bee.com:lun1>
    backing-store /dev/vdb
    initiator-address 192.168.10.90
</target>

$ systemctl restart tgt
$ tgtadm --mode target --op show
```
# 客户端安装
```
$ apt-get install open-iscsi -y
$ systemctl enable open-iscsi
$ systemctl restart open-iscsi
$ iscsiadm -m discovery -t st -p 192.168.10.89
$ ls /etc/iscsi/nodes/
$ vim /etc/iscsi/initiatorname.iscsi
InitiatorName=iqn.2021-03.bee.com:lun1.init1
$ vim /etc/iscsi/iscsid.conf
node.startup = automatic
$ systemctl restart open-iscsi iscsid
$ iscsiadm -m node -T iqn.2021-03.bee.com:lun1 -p 192.168.10.89 -l
$ fdisk -l
```