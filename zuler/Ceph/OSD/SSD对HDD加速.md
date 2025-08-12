BlueStore WAL/DB 分离
将block.db和block.wal存放至快速设备SSD上
- Ceph 官方推荐
- 加速元数据和小 I/O，不影响数据路径安全性
- 只对小 I/O 提升明显，对大块顺序 I/O 提升有限。
