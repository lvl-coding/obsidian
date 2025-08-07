# 确认现有QOS
```bash
$ openstack volume qos list
```
# 修改QOS
修改HDD池读带宽为100MiB，写带宽为100MiB，读iops为1300，写iops为3000
```bash
$ openstack volume qos set HDD --property read_bytes_sec=104857600 --property read_iops_sec=1300 --property write_bytes_sec=104857600 --property write_iops_sec=3000
$ openstack volume qos list #确认修改后的结果
```

# 搁置实例
搁置实例再取消搁置使修改后的QOS生效
```bash
$ openstack server shelve <实例id>
$ openstack server show <实例id>
$ openstack server unshelve <实例id>
$ openstack server show <实例id>
```
或openstack管理页面操作

# 确认QOS生效
进入实例所在主机
进入nova_libvirt 服务docker
```bash
$ docker exec -it -u root nova_libvirt bash
$ virsh list
$ virsh dumpxml <实例id>
```
