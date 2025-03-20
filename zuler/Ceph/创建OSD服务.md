HDD盘使用两块SSD作为bulestore的db设备和wal设备
```yml
root@shsc001:~# cat hhd_osd.yml
service_type: osd
service_id: osd_using_paths
placement:
  hosts:
    - shsc001
    - shsc002
    - shsc003
spec:
  data_devices:
    rotational: 1
  db_devices:
    paths:
    - /dev/sdb
  wal_devices:
    paths:
    - /dev/sdc
root@shsc001:~# cat ssd_osd.yml
service_type: osd
service_id: osd_ssd
placement:
  host_pattern: '*'
spec:
  data_devices:
    paths:
    - /dev/sdd
    - /dev/sde
    - /dev/sdf
    - /dev/sdg
```