
|                                               |                                  |
| --------------------------------------------- | -------------------------------- |
| osd_max_backfills                             | 控制同时执行Recovery的PG个数              |
| osd_max_push_cost<br><br>osd_max_push_objects | OSD中push请求的最大字节数和对象数             |
| osd_recovery_max_active                       | 单个OSD允许同时执行Recovery的对象数          |
| osd_recovery_op_priority                      | Recovery op默认在op_shardedwq中的优先级  |
| osd_recovery_sleep                            | Recovery op在op_shardedwq中的预先休眠时间 |
|                                               |                                  |
