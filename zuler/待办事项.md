- [ ] 


```shell
root@cluster90:~# sudo cephadm shell -- ceph-volume lvm list
Inferring fsid 960c0bc4-0433-11f0-b5c2-59cfc57e8ebc
Using recent ceph image ceph/daemon@sha256:261bbe628f4b438f5bf10de5a8ee05282f2697a5a2cb7ff7668f776b61b9d586


====== osd.0 =======

  [block]       /dev/ceph-f273a343-e084-43cf-86b1-06cd2225eee5/osd-block-36c1b631-26d1-4660-8c0e-79f839b1cbbd

      block device              /dev/ceph-f273a343-e084-43cf-86b1-06cd2225eee5/osd-block-36c1b631-26d1-4660-8c0e-79f839b1cbbd
      block uuid                2GFYPn-i6FD-p20B-xmvr-Z4ff-mOy1-cptshI
      cephx lockbox secret
      cluster fsid              960c0bc4-0433-11f0-b5c2-59cfc57e8ebc
      cluster name              ceph
      crush device class        None
      encrypted                 0
      osd fsid                  36c1b631-26d1-4660-8c0e-79f839b1cbbd
      osd id                    0
      osdspec affinity          None
      type                      block
      vdo                       0
      devices                   /dev/vdb,/dev/vdc

```