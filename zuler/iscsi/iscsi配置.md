# 服务端安装
```
$ apt-get install tgt -y
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