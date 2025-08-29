[Command List](https://docs.openstack.org/python-openstackclient/ussuri/cli/command-list.html)
```
$ docker exec -it -u root nova_libvirt bash
$ virsh list
$ virsh dumpxml
$ qemu-img snapshot -a openstack vda.qcow2
$ openstack aggregate list
$ openstack aggregate create --zone test test
$ openstack aggregate add host test cluster91
$ openstack aggregate remove host test cluster90
$ 
```

