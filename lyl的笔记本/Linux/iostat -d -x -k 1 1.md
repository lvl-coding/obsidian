查看设备使用率（%util）、响应时间（await）
 ![Exported image](Exported%20image%2020250319155848-0.png)

   
说明：  
**tps**:每秒钟发送到的I/O请求数。  
**Blk_read/s**:每秒读取的block数。  
**Blk_wrtn/s**:每秒写入的block数。  
**Blk_read**:读入的block总数。  
**Blk_wrtn**:写入的block总数。  
**rrqm/s:** 每秒进行 merge 的读操作数目。即 rmerge/s  
**wrqm/s:** 每秒进行 merge 的写操作数目。即 wmerge/s  
**r/s:** 每秒完成的读 I/O 设备次数。即 rio/s  
**w/s:** 每秒完成的写 I/O 设备次数。即 wio/s  
**rkB/s:** 每秒读K字节数。是 rsect/s 的一半，因为每扇区大小为512字节。  
**wkB/s:** 每秒写K字节数。是 wsect/s 的一半。  
**avgrq-sz:** 平均每次设备I/O操作的数据大小 (扇区)。  
**avgqu-sz:** 平均I/O队列长度。  
**rsec/s:** 每秒读扇区数。即 rsect/s  
**wsec/s:** 每秒写扇区数。即 wsect/s  
**r_await:**每个读操作平均所需的时间  
不仅包括硬盘设备读操作的时间，还包括了在kernel队列中等待的时间。  
**w_await:**每个写操作平均所需的时间  
不仅包括硬盘设备写操作的时间，还包括了在kernel队列中等待的时间。  
**await:** 平均每次设备I/O操作的等待时间 (毫秒)。  
**svctm:** 平均每次设备I/O操作的服务时间 (毫秒)。  
**%util:** 一秒中有百分之多少的时间用于 I/O 操作，即被io消耗的cpu百分比  
==备注：如果 %util 接近 100%，说明产生的I/O请求太多，I/O系统已经满负荷，该磁盘可能存在瓶颈。如果 svctm 比较接近 await，说明 I/O 几乎没有等待时间；如果 await 远大于 svctm，说明I/O 队列太长，io响应太慢，则需要进行必要优化。如果avgqu-sz比较大，也表示有当量io在等待。==
 > 来自 <[https://blog.csdn.net/weixin_38428439/article/details/121525172](https://blog.csdn.net/weixin_38428439/article/details/121525172)>