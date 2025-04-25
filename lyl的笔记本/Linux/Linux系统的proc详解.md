Linux系统上的/proc目录是一种文件系统，即proc文件系统。与其它常见的文件系统不同的是，/proc是一种伪文件系统（也即虚拟文件系统），存储的是当前内核运行状态的一系列特殊文件，用户可以通过这些文件查看有关系统硬件及当前正在运行进程的信息，甚至可以通过更改其中某些文件来改变内核的运行状态。
 
基于/proc文件系统如上所述的特殊性，其内的文件也常被称作虚拟文件，并具有一些独特的特点。例如，其中有些文件虽然使用查看命令查看时会返回大量信息，但文件本身的大小却会显示为0字节。此外，这些特殊文件中大多数文件的时间及日期属性通常为当前系统时间和日期，这跟它们随时会被刷新（存储于RAM中）有关。另外，在/proc下还有三个很重要的目录：net，scsi和sys。 Sys目录是可写的，可以通过它来访问或修改内核的参数，而net和scsi则依赖于内核配置。例如，如果系统不支持scsi，则scsi 目录不存在。
 
为了查看及使用上的方便，这些文件通常会按照相关性进行分类存储于不同的目录甚至子目录中，如/proc/scsi目录中存储的就是当前系统上所有SCSI设备的相关信息，/proc/N中存储的则是系统当前正在运行的进程的相关信息，其中N为正在运行的进程（可以想象得到，在某进程结束后其相关目录则会消失）。
 
大多数虚拟文件可以使用文件查看命令如cat、more或者less进行查看，有些文件信息表述的内容可以一目了然，但也有文件的信息却不怎么具有可读性。不过，这些可读性较差的文件在使用一些命令如apm、free、lspci或top查看时却可以有着不错的表现
 
1.1 cmdline  
启动当前进程的完整命令，但僵尸进程目录中的此文件不包含任何信息；
 
[root@test7 1120]# cat cmdline  
/usr/sbin/sshd-D
 
1.2 cwd  
指向当前进程运行目录的一个符号链接
 
lrwxrwxrwx 1 root root 0 Jun 30 19:06 cwd -> /
 
1.3 environ  
当前进程的环境变量列表，彼此之间用空字符NULL隔开，变量用大写字母表示，其值用小写字母表示
 
[root@test7 1120]# more /proc/1120/environ  
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/binLANG=en_US.UTF-8SSH_USE_STRONG_RNG=0
 
1.4 exe  
指向启动当前进程的可执行文件（完整路径）的符号链接，通过/proc/N/exe 可以启动当前进程的一个拷贝, 这个文件是二进制文件。
 
1.5 fd  
这是个目录，包含当前进程打开的每一个文件的文件描述符（file descriptor），这些文件描述符是指向实际文件的一个符号链接
 
[root@test7 fd]# ll /proc/1120/fd  
total 0  
lr-x------ 1 root root 64 Jun 30 19:12 0 -> /dev/null  
lrwx------ 1 root root 64 Jun 30 19:12 1 -> socket:[17589]  
lrwx------ 1 root root 64 Jun 30 19:12 2 -> socket:[17589]  
lrwx------ 1 root root 64 Jun 30 19:12 3 -> socket:[17632]  
lrwx------ 1 root root 64 Jun 30 19:12 4 -> socket:[17634]  
￼1.6 limits  
当前进程所使用的每一个受限资源的软限制、硬限制和管理单元；此文件仅可由实际启动当前进程的UID用户读取；（2.6.24以后的内核版本支持此功能）；
 
[root@test7 1120]# cat limits

|   |   |   |   |
|---|---|---|---|
|Limit|Soft Limit|Hard Limit|Units|
|Max cpu time|unlimited|unlimited|seconds|
|Max file size|unlimited|unlimited|bytes|
|Max data size|unlimited|unlimited|bytes|
|Max stack size|8388608|unlimited|bytes|
|Max core file size|0|unlimited|bytes|
|Max resident set|unlimited|unlimited|bytes|
|Max processes|3707|3707|processes|
|Max open files|1024|4096|files|
|Max locked memory|65536|65536|bytes|
|Max address space|unlimited|unlimited|bytes|
|Max file locks|unlimited|unlimited|locks|
|Max pending signals|3707|3707|signals|
|Max msgqueue size|819200|819200|bytes|
|Max nice priority|0|0||
|Max realtime priority|0|0||
|Max realtime timeout|unlimited|unlimited|us|
 
1.7 maps  
当前进程关联到的每个可执行文件和库文件在内存中的映射区域及其访问权限所组成的列表
 
[root@test7 1120]# cat maps  
7fcacee51000-7fcacee5c000 r-xp 00000000 fd:01 201770159 /usr/lib64/libnss_files-2.17.so (deleted)  
7fcacee5c000-7fcacf05b000 ---p 0000b000 fd:01 201770159 /usr/lib64/libnss_files-2.17.so (deleted)  
7fcacf05b000-7fcacf05c000 r--p 0000a000 fd:01 201770159 /usr/lib64/libnss_files-2.17.so (deleted)  
7fcacf05c000-7fcacf05d000 rw-p 0000b000 fd:01 201770159 /usr/lib64/libnss_files-2.17.so (deleted)  
7fcacf05d000-7fcacf064000 r-xp 00000000 fd:01 201770171 /usr/lib64/librt-2.17.so (deleted)  
7fcacf064000-7fcacf263000 ---p 00007000 fd:01 201770171 /usr/lib64/librt-2.17.so (deleted)  
7fcacf263000-7fcacf264000 r--p 00006000 fd:01 201770171 /usr/lib64/librt-2.17.so (deleted)  
7fcacf264000-7fcacf265000 rw-p 00007000 fd:01 201770171 /usr/lib64/librt-2.17.so (deleted)  
7fcacf265000-7fcacf268000 r-xp 00000000 fd:01 201449283 /usr/lib64/libkeyutils.so.1.5  
7fcacf268000-7fcacf467000 ---p 00003000 fd:01 201449283 /usr/lib64/libkeyutils.so.1.5  
......  
7fcad5bcc000-7fcad5bed000 rw-p 00000000 00:00 0 [heap]  
7fff792fb000-7fff7931c000 rw-p 00000000 00:00 0 [stack]  
7fff793ad000-7fff793af000 r-xp 00000000 00:00 0 [vdso]  
ffffffffff600000-ffffffffff601000 r-xp 00000000 00:00 0 [vsyscall]  
￼1.8 mem  
当前进程所占用的内存空间，由open，read和seek等系统调用使用，不能被用户读取
 
1.9 root  
指向当前进程运行根目录的符号链接；在Unix和Linux系统上，通常采用chroot命令使每个进程运行于独立的根目录；
 
lrwxrwxrwx 1 root root 0 Jun 30 19:06 root -> /