# ceph命令

|   |   |   |
|---|---|---|
|cephadm|sudo cephadm shell -- <后续指令>|cephadm宿主机上执行集群内指令|
||ceph --version|查看版本|
||ceph orch host ls|查看集群节点|
||ceph osd lspools|查看集群所有池|
||ceph osd pool stats||
||ceph osd pool create <poolname> pg_num pgp_num {replicated\|erasure} --autoscale_mode=off||
||ceph osd pool get ceph-demo size||
||ceph osd pool delete default.rgw.log default.rgw.log --yes-i-really-really-mean-it||
||ceph tell mon.\* injectargs '--mon-allow-pool-delete=true'|修改mon参数|
||ceph mgr services||
||ceph osd crush rule ls|查看规则名列表|
||ceph osd crush rule dump|查看规则详情|
||ceph osd crush rule rm test-hdd|删除规则|
||ceph osd erasure-code-profile set default-hdd crush-device-class=hdd k=2 m=1 crush-failure-domain=host|创建ecprofile文件|
||ceph osd crush rule create-erasure ec-hdd default-hdd|根据profile文件创建规则|
||rbd create -p ceph-demo --image rbd-demo.img --size 5G --image-feature exclusive-lock,journaling||
||rbd -p ceph-demo ls||
||rbd info ceph-demo/rbd-demo.img||
||rbd map ceph-demo/rbd-demo.img||
||rbd device list||
||mkfs.xfs /dev/rbd0||
||mkdir /mnt/rbd-demo||
||mount /dev/rbd0 /mnt/rbd-demo/||
|RGW|ceph orch ls||
||ceph orch rm rgw.sftpgo||
||ceph orch apply rgw foo '--placement=label:rgw count-per-host:2' --port=8000||
||ceph osd pool application enable {pool-name} {application-name}||