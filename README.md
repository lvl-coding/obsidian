# obsidian
my obsidian note

```
(.venv) root@cqhczy001:/home/kvirtadm# cinder get-pools --detail
WARNING:cinderclient.shell:API version 3.68 requested, 
WARNING:cinderclient.shell:downgrading to 3.64 based on server support.
+-----------------------------+------------------------------------------------------------------------------+
| Property                    | Value                                                                        |
+-----------------------------+------------------------------------------------------------------------------+
| allocated_capacity_gb       | 15056                                                                        |
| backend_state               | up                                                                           |
| driver_version              | 1.2.0                                                                        |
| filter_function             | None                                                                         |
| free_capacity_gb            | 2204.35                                                                      |
| goodness_function           | None                                                                         |
| location_info               | ceph:/etc/ceph/ceph.conf:ad4ea722-055c-11f0-8287-9bfcdd72e8ac:cinder:volumes |
| max_over_subscription_ratio | 4.5                                                                          |
| multiattach                 | True                                                                         |
| name                        | sharehost@volumes#volumes                                                    |
| replication_enabled         | False                                                                        |
| reserved_percentage         | 0                                                                            |
| storage_protocol            | ceph                                                                         |
| thin_provisioning_support   | True                                                                         |
| timestamp                   | 2025-06-16T09:48:18.325851                                                   |
| total_capacity_gb           | 28158.62                                                                     |
| vendor_name                 | Open Source                                                                  |
| volume_backend_name         | volumes                                                                      |
+-----------------------------+------------------------------------------------------------------------------+
+-----------------------------+---------------------------------------------------------------------------------+
| Property                    | Value                                                                           |
+-----------------------------+---------------------------------------------------------------------------------+
| allocated_capacity_gb       | 65710                                                                           |
| backend_state               | up                                                                              |
| driver_version              | 1.2.0                                                                           |
| filter_function             | None                                                                            |
| free_capacity_gb            | 15246.95                                                                        |
| goodness_function           | None                                                                            |
| location_info               | ceph:/etc/ceph/ceph.conf:ad4ea722-055c-11f0-8287-9bfcdd72e8ac:cinder:hdd-volume |
| max_over_subscription_ratio | 4.5                                                                             |
| multiattach                 | True                                                                            |
| name                        | sharehost@hdd-volume#hdd-volume                                                 |
| replication_enabled         | False                                                                           |
| reserved_percentage         | 0                                                                               |
| storage_protocol            | ceph                                                                            |
| thin_provisioning_support   | True                                                                            |
| timestamp                   | 2025-06-16T09:48:40.263714                                                      |
| total_capacity_gb           | 59966.7                                                                         |
| vendor_name                 | Open Source                                                                     |
| volume_backend_name         | hdd-volume                                                                      |
+-----------------------------+---------------------------------------------------------------------------------+
(.venv) root@cqhczy001:/home/kvirtadm# cephadm shell -- ceph df 
Inferring fsid ad4ea722-055c-11f0-8287-9bfcdd72e8ac
Using recent ceph image 10.103.227.1:7777/ceph/ceph@sha256:268cdf6d6ead311e76e4393384f0063869e2f5201e5bed0a02c58c3008a3cd0f
--- RAW STORAGE ---
CLASS     SIZE   AVAIL    USED  RAW USED  %RAW USED
lhd     96 TiB  52 TiB  44 TiB    44 TiB      45.58
lsd     38 TiB  11 TiB  28 TiB    28 TiB      72.39
TOTAL  134 TiB  63 TiB  72 TiB    72 TiB      53.24
 
--- POOLS ---
POOL                   ID   PGS   STORED  OBJECTS     USED  %USED  MAX AVAIL
device_health_metrics   1     1   14 MiB       70   42 MiB      0    2.2 TiB
backups                 2    64      0 B        0      0 B      0    2.2 TiB
images                  3   128  817 GiB  198.47k  2.4 TiB  27.03    2.2 TiB
vms                     4   128   25 MiB      523   78 MiB      0    2.2 TiB
volumes                 5   512  8.4 TiB    2.36M   25 TiB  79.69    2.2 TiB
hdd-volume              6  1024   15 TiB    2.46M   44 TiB  49.44     15 TiB

```