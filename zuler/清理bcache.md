参考网页https://zhuanlan.zhihu.com/p/682979765
```
root@yzcm003:/home/cmadmin# lsblk
NAME                      MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
loop0                       7:0    0  63.9M  1 loop /snap/core20/2105
loop1                       7:1    0  63.7M  1 loop /snap/core20/2496
loop2                       7:2    0    87M  1 loop /snap/lxd/27037
loop3                       7:3    0  89.4M  1 loop /snap/lxd/31333
loop4                       7:4    0  40.4M  1 loop /snap/snapd/20671
loop5                       7:5    0  44.4M  1 loop /snap/snapd/23771
sda                         8:0    0   3.6T  0 disk
└─bcache1                 252:128  0   3.6T  0 disk
sdb                         8:16   0   3.6T  0 disk
└─bcache0                 252:0    0   3.6T  0 disk
sdc                         8:32   0 222.6G  0 disk
├─sdc1                      8:33   0     1M  0 part
├─sdc2                      8:34   0     2G  0 part /boot
└─sdc3                      8:35   0 220.6G  0 part
  └─ubuntu--vg-ubuntu--lv 253:0    0 220.6G  0 lvm  /
sdd                         8:48   0 447.1G  0 disk
├─bcache4                 252:512  0   200G  0 disk
└─bcache5                 252:640  0   3.6T  0 disk
sde                         8:64   0 447.1G  0 disk
├─bcache1                 252:128  0   3.6T  0 disk
└─bcache2                 252:256  0   200G  0 disk
sdf                         8:80   0   3.6T  0 disk
└─bcache5                 252:640  0   3.6T  0 disk
sdg                         8:96   0 447.1G  0 disk
├─bcache0                 252:0    0   3.6T  0 disk
└─bcache3                 252:384  0   200G  0 disk
sdh                         8:112  0   3.6T  0 disk
sdi                         8:128  0 447.1G  0 disk
root@yzcm003:/home/cmadmin# bcache-super-show /dev/sda|grep cset
cset.uuid               12d63ac5-d0f0-47d3-95cc-051666638598
root@yzcm003:/home/cmadmin# echo "12d63ac5-d0f0-47d3-95cc-051666638598" > /sys/block/bcache1/bcache/detach
root@yzcm003:/home/cmadmin# echo 1 > /sys/fs/bcache/12d63ac5-d0f0-47d3-95cc-051666638598/unregister
root@yzcm003:/home/cmadmin# echo 1 > /sys/block/bcache1/bcache/stop
root@yzcm003:/home/cmadmin# lsblk
NAME                      MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
loop0                       7:0    0  63.9M  1 loop /snap/core20/2105
loop1                       7:1    0  63.7M  1 loop /snap/core20/2496
loop2                       7:2    0    87M  1 loop /snap/lxd/27037
loop3                       7:3    0  89.4M  1 loop /snap/lxd/31333
loop4                       7:4    0  40.4M  1 loop /snap/snapd/20671
loop5                       7:5    0  44.4M  1 loop /snap/snapd/23771
sda                         8:0    0   3.6T  0 disk
sdb                         8:16   0   3.6T  0 disk
└─bcache0                 252:0    0   3.6T  0 disk
sdc                         8:32   0 222.6G  0 disk
├─sdc1                      8:33   0     1M  0 part
├─sdc2                      8:34   0     2G  0 part /boot
└─sdc3                      8:35   0 220.6G  0 part
  └─ubuntu--vg-ubuntu--lv 253:0    0 220.6G  0 lvm  /
sdd                         8:48   0 447.1G  0 disk
├─bcache4                 252:512  0   200G  0 disk
└─bcache5                 252:640  0   3.6T  0 disk
sde                         8:64   0 447.1G  0 disk
sdf                         8:80   0   3.6T  0 disk
└─bcache5                 252:640  0   3.6T  0 disk
sdg                         8:96   0 447.1G  0 disk
├─bcache0                 252:0    0   3.6T  0 disk
└─bcache3                 252:384  0   200G  0 disk
sdh                         8:112  0   3.6T  0 disk
sdi                         8:128  0 447.1G  0 disk
```