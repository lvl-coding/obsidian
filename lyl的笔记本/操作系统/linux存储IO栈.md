![Exported image](Exported%20image%2020250319160231-0.png)

注意：图中的PageCache位置其实不大准确，应该是在具体文件系统下层，其中会根据open的选项（是否指定O_DIRECT）判断是否需经过PageCache。 #question  
由图可见，从系统调用的接口再往下，Linux下的存储IO栈大致有三个层次：

- 文件系统层，以 write(2) 为例，内核拷贝了write(2)参数指定的用户态数据到文件系统Cache中，并适时向下层同步
- block层，管理块设备的IO队列，对IO请求进行合并、排序
- 设备层，通过DMA与内存直接交互，完成数据和具体设备之间的交互

在Linux下，文件的缓存习惯性的称之为Page Cache，用于缓存文件的内容；而更低一级的设备的缓存称之为Buffer Cache，用于缓存存储设备块（比如磁盘扇区）的数据。
 ![Exported image](Exported%20image%2020250319160235-1.png)

传统的Buffered IO使用read(2)读取文件的过程什么样的？假设要去读一个冷文件（Cache中不存在），open(2)打开文件内核后建立了一系列的数据结构，接下来调用read(2)，到达文件系统这一层，发现Page Cache中不存在该位置的磁盘映射，然后创建相应的Page Cache并和相关的扇区关联。然后请求继续到达块设备层，在IO队列里排队，接受一系列的调度后到达设备驱动层，此时一般使用DMA方式读取相应的磁盘扇区到Cache中，然后read(2)拷贝数据到用户提供的用户态buffer中去（read(2)的参数指出的）。  
整个过程有几次拷贝？从磁盘到Page Cache算第一次的话，从Page Cache到用户态buffer就是第二次了。而mmap(2)做了什么？mmap(2)直接把Page Cache映射到了用户态的地址空间里了，所以mmap(2)的方式读文件是没有第二次拷贝过程的。那Direct IO做了什么？这个机制更狠，直接让用户态和块IO层对接，直接放弃Page Cache，从磁盘直接和用户态拷贝数据。好处是什么？写操作直接映射进程的buffer到磁盘扇区，以DMA的方式传输数据，减少了原本需要到Page Cache层的一次拷贝，提升了写的效率。对于读而言，第一次肯定也是快于传统的方式的，但是之后的读就不如传统方式了（当然也可以在用户态自己做Cache，有些商用数据库就是这么做的）。  
除了传统的Buffered IO可以比较自由的用偏移+长度的方式读写文件之外，mmap(2)和Direct IO均有数据按页对齐的要求，Direct IO还限制读写必须是底层存储设备块大小的整数倍（甚至Linux 2.4还要求是文件系统逻辑块的整数倍）。所以接口越来越底层，换来表面上的效率提升的背后，需要在应用程序这一层做更多的事情。所以想用好这些高级特性，除了深刻理解其背后的机制之外，也要在系统设计上下一番功夫。

# Page Cache的同步机制

广义上Cache的同步方式有两种，即Write Through（写穿）和Write back（写回）。  
从名字上就能看出这两种方式都是从写操作的不同处理方式引出的概念（纯读的话就不存在Cache一致性了）。对应到Linux的Page Cache上：

- 所谓Write Through就是指write(2)操作将数据拷贝到Page Cache后立即和下层进行同步的写操作，完成下层的更新后才返回。
- 而Write back正好相反，指的是写完Page Cache就可以返回了。Page Cache到下层的更新操作是异步进行的。

Linux下Buffered IO默认使用的是Write back机制，即文件操作的写只写到Page Cache就返回，之后Page Cache到磁盘的更新操作是异步进行的。  
Page Cache中被修改的内存页称之为脏页（Dirty Page），脏页在特定的时候被一个叫做pdflush(Page Dirty Flush)的内核线程写入磁盘。写入的时机和条件如下：

- 当空闲内存低于一个特定的阈值时，内核必须将脏页写回磁盘，以便释放内存。 (vm.dirty_background_ratio)
- 当脏页在内存中驻留时间超过一个特定的阈值时，内核必须将超时的脏页写回磁盘。 (vm.dirty_expire_centisecs)
- 用户进程调用sync(2)、fsync(2)、fdatasync(2)系统调用时，内核会执行相应的写回操作。

默认是写回方式，如果想指定某个文件是写穿方式呢？除了fsync(2)之类的系统调用外，在open(2)打开文件时，传入O_SYNC这个flag即可实现。

![Exported image](Exported%20image%2020250319160238-2.png)