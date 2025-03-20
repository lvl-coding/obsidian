您可以使用 **lvcreate** 命令的 `-l` 参数来指定逻辑卷的大小（以扩展为单位）。您还可以使用此参数指定相关卷组、逻辑卷或物理卷组大小的百分比。
==后缀 %VG 表示卷组的总大小，
后缀 %FREE 表示卷组中剩余的空闲空间，
后缀 %PVS 表示指定的物理卷中的空闲空间。==
对于快照，其大小使用后缀 %ORIGIN 表示原始卷总大小的百分比，（100%ORIGIN 提供了整个原始卷的空间）。当以百分比表示时，该大小定义了新逻辑卷中逻辑扩展数的上限。在命令完成前，不会确定新 LV 中的逻辑扩展的确切数目。
批量删除lvm
```
$ lvs | awk '$2 ~ /^ceph-/ {print $2}'| xargs -I {} lvremove {} -y
ceph orch device ls | awk 'NR>1 && $NF == "No" {print $1, $2}' | while read -r hostname path; do ceph orch device zap "$hostname" "$path" --force; done
```