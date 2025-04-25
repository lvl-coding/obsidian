一、用GBD 调试多进程程序  
如果一个进程通过fork系统调用创建了子进程，gdb会继续调试原来的进程，子进程则正常运行。  
那么如何调试子进程呢
 
1、单独调试子进程  
子进程本质也是一个进程，因此也可通过gdb来调试，首先找到目标子进程的PID，再将其附加（attach）到  
gdb调试器上，具体操作如下：  
$ps -ef|grep 进程名 //找到待调试进程的PID  
$gdb  
(gdb) attach "PID"
 
2、使用调试器选项follow-fork-mode  
gdb调试器的选项follow-fork-mode允许我们  
选择程序在执行fork系统调用后是继续调试父进程还是调试子进程。  
用法：  
(gdb)set follow-fork-mode parent/child 可以选parent 或 child, 分别表示调试父进程和子进程
 
二、用gdb调试多线程程序  
gdb有一组命令可辅助多线程程序的调试。  
info threads  
显示当前可调试的所有线程。gdb会为每个线程分配一个ID，我们可以使用这个ID来操作对应的线程。  
ID前面有“*”的线程是当前被调试的线程。
 
thread ID  
调试目标ID指定的线程。
 
set scheduler-locking[off|on|step]  
调试多线程程序时，默认除了被调试的线程在执行外，其他线程也在继续执行，  
但有的时候我们希望只让被调试的线程运行。这可以通过这个命令来实现。  
该命令设置sceduler-locking的值：  
off表示不锁定任何线程，即所有线程都可以继续执行，这是默认值。  
on表示只有当前被调试的线程会继续执行。  
step表示在单步执行的时候，只有当前线程会执行。
 
三 用gdb工具分析core文件  
在Unix系统下，应用程序崩溃，一般会产生core文件，如何根据core文件查找问题的所在，  
并做相应的分析和调试，是非常重要的。   通常情况下，Core 文件会包含程序运行时的内存、寄存器状态、堆栈指针、  
内存管理信息还有各种函数调用堆栈信息等。  
我们可以理解为是程序工作当前状态存储生成第一个文件，  
许多程序出错时都会产生一个Core 文件，通过工具分析这个文件，  
我们可以定位到程序异常退出时对应的堆栈调用等信息，找出问题所在并进行及时解决。
 
段错误，就是大名鼎鼎的Segmentation Fault，这通常是由指针错误引起的。  
简而言之，产生段错误就是访问了错误的内存段，一般是你没有权限，  
或者根本就不存在对应的物理内存，尤其常见的是访问0 地址。  
在编程中有以下几种做法容易导致段错误，基本都是错误地使用指针引起的：
 
访问系统数据区，尤其是往系统保护的内存地址写数据，最常见的就是给指针以0地址  
内存越界(数据越界、变量类型不一致等)访问到不属于你的内存区域  
程序在运行过程中如果出现段错误，那么就会收到SIGSEGV 信号，  
SIGSEGV 默认handler 的动作是打印“段错误”的出错信息，并产生Core 文件。
 
1、core文件开关  
ulimit -c 查看core开关，如果为0表示关闭，不会生成core文件；  
使用 ulimit -c [filesize] 设置core文件大小，当最小设置为4之后才会生成core文件；  
使用 ulimit -c unlimited 设置core文件大小为不限制，这是常用的做法；  
如果需要开机就执行，则需要将这句命令写到 /etc/profile 等文件。  
core文件路径一般在可执行文件同一目录
 
2、core文件命名和保存路径  
core文件有默认的名称和路径，但为了方便，我们通常会自己命名和指定保存路径；  
可以通过 /proc/sys/kernel/core_pattern 设置 core 文件名和保存路径，方法如下：  
echo "/corefile/core-%e-%p-%t" > /proc/sys/kernel/core_pattern
 
命名的参数列表：  
%p - insert pid into filename 添加pid  
%u - insert current uid into filename 添加当前uid  
%g - insert current gid into filename 添加当前gid  
%s - insert signal that caused the coredump into the filename 添加导致产生core的信号  
%t - insert UNIX time that the coredump occurred into filename 添加core文件生成时的unix时间  
%h - insert hostname where the coredump happened into filename 添加主机名  
%e - insert coredumping executable name into filename 添加命令名。
 
3、分析Core 文件  
设置Core文件大小，运行程序生成Core文件  
执行ulimit -c unlimited表示不限制生成的Core 文件的大小，
 
4、运行这个有bug 的程序，可以在当前目录下生成core文件  
执行 gdb [exec file] [core file]启动gdb后，  
调用gdb的where或bt命令可以查看当时的调用栈信息，  
进入某个栈：f N，f是frame的缩写，N是栈号，如0、1、2、3...  
进入到某个栈后，才能通过p命令查看这个栈的临时变量，否则只能查看全局变量。
 
四、调试一个正在运行的程序使用gdb -p PID命令，PID即程序的pid。  
需要注意的是，gdb调试正在运行的程序会导致程序挂起，因此请记住不要gdb调试正在运行的在线服务。
 
五、设置断点的方式有很多种，最常见的有两种：一是设置程序运行到源代码的某一行，二是设置程序运行到某个函数。  
设置程序运行到某一行，通过“文件名:行号”的形式：  
b test.cpp:100  
设置程序运行到某个函数，通过“名字空间::函数名”的形式：  
gdb namespace_a::func
 
六、查看断点：info b  
删除断点：d N，d是delete的缩写，N是断点的编号，可以通过info b查看。  
无论哪种方式设置断点，都要执行c命令（continue），让程序继续运行。
 
七、在调试程序时，最常用的gdb命令是：n、s、p  
n即next，单步执行，执行下一步的意思，遇到函数会调用函数。  
s即step，也是单步执行，但是会进入函数内部，然后结合n命令来调试函数。  
p即print，打印变量，最常用的命令。p可以打印普通变量、std::string字符串、指针、数组等。