1. ==Ceph 数据IO全栈流程-源码分析== <[https://blog.51cto.com/wendashuai/2497104](https://blog.51cto.com/wendashuai/2497104)>
2. ==C====eph 写流程== <[http://bean-li.github.io/ceph-write-flow/](http://bean-li.github.io/ceph-write-flow/)>

==client只会和Primary OSD之间有消息交互，至于其它副本的数据写入，要靠Primary OSD发送请求通知它们，当然当Replica OSD写完之后，需要发送消息告知Primary OSD， Primary OSD汇总各个OSD的写入情况，在合适的时机给Client发送响应信息。==  
==注意，无论是Primary OSD还是 Replica OSD，写入都分成2个阶段，第一阶段是写入Journal，写入Journal成功，第一阶段的任务就算完成了，当Replica OSD如果完成了第一阶段的任务，写入了Journal，就会给Primary OSD发送第一个消息，表示第一阶段的任务完成了。如果Primary同时收到了所有的OSD的消息，确认所有OSD上的第一阶段任务完成，就会给Client回复第一个消息，表示写入已经完成。==  
==注意，第一阶段是写入Journal，因此，Journal如果是SSD这种比较快速的设备，会极大地改善写入请求的处理速度。==  
==每一个Replica OSD给Primary OSD发送了2个消息，其中第二个消息，对应的是第二阶段任务的完成。当Journal中的数据向OSD的data partition写入成功后，第二阶段任务就算完成了，Replica OSD就会给Primary OSD发送第二个消息。当Primary OSD搜集齐了所有OSD都完成了的消息之后，就会确认整体第二阶段的任务完成，就会给Client消息，通知数据可读。==