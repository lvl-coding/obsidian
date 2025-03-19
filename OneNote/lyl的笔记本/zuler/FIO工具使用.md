|
|
==参数名== ==说明==
==bs== ==每次请求的块大小。==
==ioengine== ==I/O 引擎。推荐Linux 用libaio，Windows用windowsaio。==
==iodepth== ==请求的I/O队列深度。==
==direct== ==指定 direct 模式。默认为 True（1）。 True（1）表示指定 O_DIRECT 标识符，忽略 I/O 缓存，数据直写。 False（0）表示不指定 O_DIRECT 标识符。==
==rw== ==读写模式。取值包括顺序读（read）、顺序写（write）、随机读（randread）、随机写（randwrite）、混合随机读写（randrw）和混合顺序读写（rw，readwrite）==
==time_based== ==指定采用时间模式。无需设置该参数值，只要 FIO 基于时间来运行。==
==runtime== ==指定测试时长，即 FIO 运行时长。==
==refill_buffers== ==FIO 将在每次提交时重新填充 I/O 缓冲区。默认设置是仅在初始时填充并重用该数据。==
==norandommap== ==在进行随机 I/O 时，FIO 将覆盖文件的每个块。若给出此参数，则将选择新的偏移量而不查看 I/O 历史记录。==
==randrepeat== ==随机序列是否可重复，默认为 True（1）。 True（1）表示随机序列可重复，False（0）表示随机序列不可重复。==
==group_reporting== ==多个 job 并发时，打印整个 group 的统计值。==
==name== ==job 的名称。==
==size== ==I/O 测试的寻址空间。==
==numjobs== ==同时测试的线程数量（这个值乘以上面的iodepth就是对磁盘产生的实际队列深度）==
==rwmixread== ==--rwmixread=0 这里读写比率是0，表示100%写，下面100%随机读测试中，这个值设置为100==
==thread== ==由于使用了多线程，这个参数必须加上==
==filename== ==测试对象，即待测试的磁盘设备名称。==
 
e.g.  
**测试硬盘的随机读时延**  
.\fio.exe -bs=4k -ioengine=windowsaio -iodepth=1 -numjobs=1 -direct=1 -rw=randread -thread -time_based -runtime=120 -refill_buffers -norandommap -randrepeat=0 -group_reporting -name=fio-randread-lat -size=10G -filename="Z\:\targetfile"  
**测试硬盘的随机写时延**  
.\fio.exe -bs=4k -ioengine=windowsaio -iodepth=1 -numjobs=1 -direct=1 -rw=randwrite -thread -time_based -runtime=120 -refill_buffers -norandommap -randrepeat=0 -group_reporting -name=fio-randwrite-lat -size=10G -filename="D\:\targetfile"  
**测试硬盘的随机混合读写时延**  
.\fio.exe -bs=4k -ioengine=windowsaio -iodepth=1 -numjobs=1 -direct=1 -rw=randrw -rwmixread=50 -thread -time_based -runtime=120 -refill_buffers -norandommap -randrepeat=0 -group_reporting -name=fio-randrw-lat -size=10G -filename="D\:\targetfile"  
**测试硬盘的顺序读吞吐性能**  
.\fio.exe -bs=128k -ioengine=windowsaio -iodepth=32 -numjobs=1 -direct=1 -rw=read -thread -time_based -runtime=120 -refill_buffers -norandommap -randrepeat=0 -group_reporting -name=fio-read-throughput -size=10G -filename="D\:\targetfile"  
**测试硬盘的顺序写吞吐性能**  
.\fio.exe -bs=128k -ioengine=windowsaio -iodepth=32 -numjobs=1 -direct=1 -rw=write -thread -time_based -runtime=120 -refill_buffers -norandommap -randrepeat=0 -group_reporting -name=fio-write-throughput -size=10G -filename="D\:\targetfile"  
**测试硬盘的顺序混合读写吞吐性能**  
.\fio.exe -bs=128k -ioengine=windowsaio -iodepth=32 -numjobs=1 -direct=1 -rw=readwrite -thread -time_based -runtime=120 -refill_buffers -norandommap -randrepeat=0 -group_reporting -name=fio-readwrite-throughput -size=10G -filename="D\:\targetfile"  
**测试硬盘的随机读IOPS**  
.\fio.exe -bs=4k -ioengine=windowsaio -iodepth=32 -numjobs=1 -direct=1 -rw=randread -thread -time_based -runtime=120 -refill_buffers -norandommap -randrepeat=0 -group_reporting -name=fio-randread-iops -size=10G -filename="D\:\targetfile"  
**测试硬盘的随机写IOPS**  
.\fio.exe -bs=4k -ioengine=windowsaio -iodepth=32 -numjobs=1 -direct=1 -rw=randwrite -thread -time_based -runtime=120 -refill_buffers -norandommap -randrepeat=0 -group_reporting -name=fio-randwrite-iops -size=10G -filename="D\:\targetfile"  
**测试硬盘的随机混合读写IOPS**  
.\fio.exe -bs=4k -ioengine=windowsaio -iodepth=32 -numjobs=1 -direct=1 -rw=randrw -thread -time_based -runtime=120 -refill_buffers -norandommap -randrepeat=0 -group_reporting -name=fio-randrw-iops -size=10G -filename="D\:\targetfile"