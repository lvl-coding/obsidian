# 重启OSD服务
```
$ sudo ls /etc/systemd/system/ceph-cd041c6a-fe49-11ef-b20e-131e900def78.target.wants/
ceph-cd041c6a-fe49-11ef-b20e-131e900def78@crash.shsc003.service  ceph-cd041c6a-fe49-11ef-b20e-131e900def78@osd.21.service  ceph-cd041c6a-fe49-11ef-b20e-131e900def78@osd.25.service  ceph-cd041c6a-fe49-11ef-b20e-131e900def78@osd.29.service
ceph-cd041c6a-fe49-11ef-b20e-131e900def78@mon.shsc003.service    ceph-cd041c6a-fe49-11ef-b20e-131e900def78@osd.22.service  ceph-cd041c6a-fe49-11ef-b20e-131e900def78@osd.26.service  ceph-cd041c6a-fe49-11ef-b20e-131e900def78@osd.30.service
ceph-cd041c6a-fe49-11ef-b20e-131e900def78@osd.19.service         ceph-cd041c6a-fe49-11ef-b20e-131e900def78@osd.23.service  ceph-cd041c6a-fe49-11ef-b20e-131e900def78@osd.27.service  ceph-cd041c6a-fe49-11ef-b20e-131e900def78@osd.31.service
ceph-cd041c6a-fe49-11ef-b20e-131e900def78@osd.20.service         ceph-cd041c6a-fe49-11ef-b20e-131e900def78@osd.24.service  ceph-cd041c6a-fe49-11ef-b20e-131e900def78@osd.28.service
$ sudo systemctl restart ceph-cd041c6a-fe49-11ef-b20e-131e900def78@osd.30
```
# OSD服务组件
cephadm下OSD进程运行在docker内，OSD docker如下
```
CONTAINER ID   IMAGE                                     COMMAND                  CREATED          STATUS          PORTS     NAMES
d7fb9792cb92   192.168.10.92:5000/ceph                   "/usr/bin/ceph-osd -…"   3 days ago       Up 3 days                 ceph-2a4e1392-0abd-11f0-95f6-e7d0a414706b-osd-0
06ce38864d07   192.168.10.92:5000/ceph                   "/usr/bin/ceph-osd -…"   8 days ago       Up 8 days                 ceph-2a4e1392-0abd-11f0-95f6-e7d0a414706b-osd-1
```
