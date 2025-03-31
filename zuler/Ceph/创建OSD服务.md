HDD盘使用两块SSD作为bulestore的db设备和wal设备
yml文件语法网页[【搞定yml文件】.yml文件基本用法汇总](https://zhuanlan.zhihu.com/p/493137181)
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
```bash
lvs | awk '$2 ~ /^ceph-/ {print $2}' | xargs -I {} lvremove -y {}
lvs | awk '$2 ~ /^ceph-/ && $6 < "7.28t" {print $1, $2}' | while read -r lv vg; do lvremove -y "$vg/$lv"; done
for i in {11..26}; do ceph orch device zap shsc002 /dev/sd$(printf "\\x$(printf %x $((96 + i)))") --force; done
```