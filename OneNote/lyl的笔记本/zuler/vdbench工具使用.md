[https://www.cnblogs.com/luxf0/p/13321077.html](https://www.cnblogs.com/luxf0/p/13321077.html)
 
**1、常用操作命令****#**  
 ==检查vdbench环境==  

```
./vdbench -t
￼



./
vdbench 
-
f
 {filename} 
-
o
 {exportpath}
￼



./vdbench rsh
￼







hd
=default,vdbench=/root/vdbench50406,user=root,shell=ssh
￼
hd
=hd1,system=node1
￼
hd
=hd2,system=node2
￼
hd
=hd3,system=node3
￼
fsd
=fsd1,anchor=/client/,depth=
2
,width=
100
,files=
100
,size=
4
k,shared=
yes
￼




fsd=fsd1,anchor=/dir1,depth=
2
,
width
=
3
,files=
2
,size=
128
k
￼
￼
=
3
^
2
=
9
 
=
9
*
2
=
18
￼
/dir1/
￼
├── no_dismount.txt
￼
├── vdb.
1_1
.dir
￼
│   ├── vdb.
2_1
.dir
￼
│   │   ├── vdb_f0001.
file
￼
│   │   └── vdb_f0002.
file
￼
│   ├── vdb.
2_2
.dir
￼
│   │   ├── vdb_f0001.
file
￼
│   │   └── vdb_f0002.
file
￼
│   └── vdb.
2_3
.dir
￼
│       ├── vdb_f0001.
file
￼
│       └── vdb_f0002.
file
￼
├── vdb.
1_2
.dir
￼
│   ├── vdb.
2_1
.dir
￼
│   │   ├── vdb_f0001.
file
￼
│   │   └── vdb_f0002.
file
￼
│   ├── vdb.
2_2
.dir
￼
│   │   ├── vdb_f0001.
file
￼
│   │   └── vdb_f0002.
file
￼
│   └── vdb.
2_3
.dir
￼
│       ├── vdb_f0001.
file
￼
│       └── vdb_f0002.
file
￼
├── vdb.
1_3
.dir
￼
│   ├── vdb.
2_1
.dir
￼
│   │   ├── vdb_f0001.
file
￼
│   │   └── vdb_f0002.
file
￼
│   ├── vdb.
2_2
.dir
￼
│   │   ├── vdb_f0001.
file
￼
│   │   └── vdb_f0002.
file
￼
│   └── vdb.
2_3
.dir
￼
│       ├── vdb_f0001.
file
￼
│       └── vdb_f0002.
file
￼
└── vdb_control.
file
￼

12
 directories, 
20
 files
￼







E:\vdbench50406>more "Single FileSystem.txt"
￼
fsd=fsd1,anchor=E:\Sigle-FileSystem,depth=2,width=3,files=10,size=200M
￼
fwd=fwd1,fsd=fsd1,operation=write,xfersize=1M,fileio=sequential,fileselect=rando
￼
m,threads=2
￼
rd=rd1,fwd=fwd1,fwdrate=max,format=yes,elapsed=600,interval=5
￼
E:\vdbench50406>
￼
E:\vdbench50406>vdbench -f "Single FileSystem.txt"
￼
E:\vdbench50406>vdbench rsh
￼



E:\vdbench50406>more "Multi FileSystem.txt"
￼
hd=default,vdbench=E:\vdbench50406,user=Micah,shell=vdbench
￼
hd=hd1,system=66.66.66.250
￼
hd=hd2,system=66.66.66.252
￼
fsd=fsd1,anchor=Z:\Sigle-FileSystem-01,depth=2,width=3,files=10000,size=200M
￼
fsd=fsd2,anchor=Z:\Sigle-FileSystem-02,depth=2,width=3,files=10000,size=200M
￼
fwd=default,operation=write,xfersize=1M,fileio=sequential,fileselect=random,threads=16
￼
fwd=fwd1,fsd=fsd1,host=hd1
￼
fwd=fwd2,fsd=fsd2,host=hd2
￼
rd=rd1,fwd=fwd*,fwdrate=max,format=yes,elapsed=600,interval=1
￼
E:\vdbench50406>
￼
E:\vdbench50406>vdbench -f "Multi FileSystem.txt"
￼

 ./vdbench
 
parseflat
 
-i
 
<flatfile.html>
 
-o
 
output.csv
 [
-c
 
col1
 
col2
 
..
] [
-a
] [
-f
 
col1
 
value1
 
col2
 
value2..
]
￼
    
-i
 
input
 
flatfile,
 
e.g.
 
output/flatfile.html
￼
    
-o
 
output
 
CSV
 
file
 
name
 
(default
 
stdout)
￼
    
-c
 
which
 
column
 
to
 
write
 
to
 
CSV.
 
Columns
 
are
 
written
 
in
 
the
 
order
 
specified
￼
    
-f
 
filters:
 
'if (colX == valueX) ... ...'
 
(Alphabetic
 
compare)
￼
    
-a
 
include
 
only
 
the
 
'avg'
 
data. Default:
 
include
 
only
 
non-avg
 
data.
￼
    
-i
vdbench
flatfile.html
￼
    
-o
CSV
-c
-c
￼
    
-a
csv
avg
￼


￼
hd=default,vdbench=E:\vdbench50406,user=Micah,shell=vdbench
￼
hd=hd1,system=
66.66.66.250
￼
hd=hd2,system=
66.66.66.252
￼
fsd=fsd1,anchor=Z:\Sigle-FileSystem
-01
,depth=
2
,width=
3
,files=
10
,size=
4
M
￼
fsd=fsd2,anchor=Z:\Sigle-FileSystem
-02
,depth=
2
,width=
3
,files=
10
,size=
4
M
￼
fwd=default,operation=
write
,xfersize=
1
M,fileio=sequential,fileselect=
random
,threads=
2
￼
fwd=fwd1,fsd=fsd1,host=hd1
￼
fwd=fwd2,fsd=fsd2,host=hd2
￼
rd=rd1,fwd=fwd*,fwdrate=
max
,
format
=yes,elapsed=
600
,interval=
5
￼

￼
18
:
47
:
03.001
 Starting RD=format_for_rd1
￼

 
04
, 
2020
 .Interval. .ReqstdOps... ...cpu%...  
read
 ....
read
..... ....
write
.... ..mb/sec... mb/sec .xfer.. ...mkdir.... ...rmdir.... ...
create
... ....
open
.... ...
close
.... ...delete...
￼
                          rate   resp total  sys   pct   rate   resp   rate   resp  
read
 
write
  total    size  rate   resp  rate   resp  rate   resp  rate   resp  rate   resp  rate   resp
￼
18
:
48
:
40.218
   avg_2
-20
   
57.6 6.244 13.4
 
2.99
   
0.0
    
0.0 0.000
   
57.6 6.244 0.00 7.20
   
7.20 131072
   
0.2
 
104.49
   
0.2
 
41.526
   
1.8
 
7527.0
   
1.8
 
192.01
   
1.8
 
7134.3
   
1.8
 
21.984
￼

18
:
48
:
42.000
 Starting RD=rd1; elapsed=
600
; fwdrate=
max
. For loops: None
￼

18
:
58
:
42.205
  avg_2
-120
    
6.2 1.063 13.0
 
2.80
   
0.0
    
0.0 0.000
    
6.2 1.063 0.00 6.24
   
6.24
 
1048576
   
0.0 0.000
   
0.0 0.000
   
0.0 0.000
   
1.6
 
47.864
   
1.6
 
2401.1
   
0.0 0.000
￼

￼
messagescan=
no
￼
hd=default,vdbench=
/root/
vdbench50406,user=root,shell=ssh
￼
hd=hd1,
system
=node241
￼
hd=hd2,
system
=node242
￼
hd=hd3,
system
=node243
￼
sd=sd1,hd=hd1,lun=
/dev/sd
b,openflag=o_direct
￼
sd=sd2,hd=hd2,lun=
/dev/sd
b,openflag=o_direct
￼
sd=sd3,hd=hd3,lun=
/dev/sd
b,openflag=o_direct
￼
wd=wd1,sd=sd*,seekpct=
0
,rdpct=
0
,xfersize=
1
M
￼
rd=rd1,wd=wd1,iorate=max,elapsed=
600
,warmup=
30
,interval=
5
￼

￼
<a name=
"_1143839598"
><
/a><i><b>19:02:15.001 Starting RD=rd1; I/
O rate: Uncontrolled MAX; elapsed=
600
 warmup=
30
; For loops: None<
/b></i
>
￼

hd
=default,vdbench=/root/vdbench50406,user=root,shell=ssh
￼
hd
=hd1,system=node21
￼
hd
=hd2,system=node22
￼
hd
=hd3,system=node23
￼
hd
=hd4,system=node24
￼
hd
=hd5,system=node25
￼
hd
=hd6,system=node26
￼
fsd
=fsd1,anchor=/client/test03,depth=
2
,width=
100
,files=
100
,size=
8
M,shared=
yes
￼
fwd
=format,threads=
24
,xfersize=
1
m
￼
fwd
=default,xfersize=
1
m,fileio=random,fileselect=random,rdpct=
60
,threads=
24
￼
fwd
=fwd1,fsd=fsd1,host=hd1
￼
fwd
=fwd2,fsd=fsd1,host=hd2
￼
fwd
=fwd3,fsd=fsd1,host=hd3
￼
fwd
=fwd4,fsd=fsd1,host=hd4
￼
fwd
=fwd5,fsd=fsd1,host=hd5
￼
fwd
=fwd6,fsd=fsd1,host=hd6
￼
rd
=rd1,fwd=fwd*,fwdrate=max,format=restart,elapsed=
600
,interval=
1
￼





19:37:41.155 19:37:44.813 error: 20
￼
19:37:41.155 19:37:44.813 file_open(), open /trash/64M/vdb.1_47.dir/vdb.2_24.dir/vdb_f0002.file failed
￼
19:37:41.155 19:37:44.814 error: 20
￼
19:37:41.155 19:37:44.814 file_open(), open /trash/64M/vdb.1_47.dir/vdb.2_24.dir/vdb_f0003.file failed
￼
19:37:41.156 19:37:44.814 Maximum native memory allocation:    1,048,576; Current allocation:    1,048,576
￼
19:37:41.156 19:37:44.814 Maximum native memory allocation:    1,048,576; Current allocation:    1,048,576
￼
19:37:41.156 19:37:44.814 error: 20
￼
19:37:41.156 19:37:44.814 
￼
19:37:41.156 19:37:44.814 open failed 
for
 /trash/64M/vdb.1_47.dir/vdb.2_24.dir/vdb_f0002.file
￼
19:37:41.156 19:37:44.814 
￼
19:37:41.158 java.lang.RuntimeException: open failed 
for
 /trash/64M/vdb.1_47.dir/vdb.2_24.dir/vdb_f0002.file
￼





[
root
@
node40
 ~]
# cat vdbench50406/vdbench
￼
if
 [ 
"
$1
"
 == 
"SlaveJvm"
 ]; then
￼ $java
 
-client
 
-Xmx10240m
 
-Xms128m
 
-cp
 
$cp
 Vdb.SlaveJvm 
$
*
￼ exit
 
$
?
￼
else
￼ $java
 
-client
 
-Xmx10240m -Xms64m -cp
 
$cp
 Vdb.Vdbmain 
$
*
￼ exit
 
$
?
￼
fi
￼






[root@node163 vdbench50407]
# ./vdbench -t
￼

14:07:47.486 Created output directory '/root/vdbench50407/output'
￼
14:07:47.737 input argument scanned: '-f/tmp/parmfile'
￼
14:07:47.866 Starting slave: /root/vdbench50407/vdbench SlaveJvm -m localhost -n localhost-10-220209-14.07.47.444 -l localhost-0 -p 5570   
￼
14:07:47.890 
￼
14:07:47.890 File /root/vdbench50407/linux/aarch64.so does not exist.
￼
14:07:47.891 This may be an OS that a shared library currently 
￼
14:07:47.891 is not available for. You may have to do your own compile.
￼
14:07:47.891 t: java.lang.UnsatisfiedLinkError: Can't load library: /root/vdbench50407/linux/aarch64.so
￼
14:07:47.892 
￼
14:07:47.892 Loading of shared library /root/vdbench50407/linux/aarch64.so failed.
￼
14:07:47.892 There may be issues related to a cpu type not being 
￼
14:07:47.892 acceptable to Vdbench, e.g. MAC PPC vs. X86
￼
14:07:47.893 Contact me at the Oracle Vdbench Forum for support.
￼
14:07:47.893 
￼
14:07:48.395 
￼
14:07:48.396 Failure loading shared library
￼
14:07:48.396 
￼
java.lang.RuntimeException: Failure loading shared library
￼
    at Vdb.common.failure(common.java:350)
￼
    at Vdb.common.get_shared_lib(common.java:1103)
￼
    at Vdb.Native.<clinit>(Native.java:31)
￼
    at Vdb.common.signal_caller(common.java:737)
￼
    at Vdb.ConnectSlaves.connectToSlaves(ConnectSlaves.java:98)
￼
    at Vdb.Vdbmain.masterRun(Vdbmain.java:814)
￼
    at Vdb.Vdbmain.main(Vdbmain.java:628)
￼



[
root@node163 vdbench50407
]
# ls linux/
￼
config.sh  linux32.so  linux64.so  sparc64.so
￼
[
root@node163 vdbench50407
]
# file linux/linux64.so 
￼
linux/linux64.so: ELF 
64
-bit LSB shared 
object
, x86
-64
, version 
1
 (SYSV), statically linked, BuildID[sha1]=
34
a31f32956f21153c372a95e73c02e84ddd29f8, 
not
 stripped
￼
[root@node163 Jni]
# ./make.linux
￼
target directory: 
/root/
vdbench50407-src/src
￼
Compiling 
32
 bit
￼
Linking 
32
 bit
￼

Compiling 
64
 bit
￼
Linking 
64
 bit
￼

[root@node163 Jni]
# ll ../linux/
￼
total 
160
￼
-rwxrwxrwx 
1
 root root 
78656
 Feb  
9
 
14
:
54
 linux32.so
￼
-rwxrwxrwx 
1
 root root 
78656
 Feb  
9
 
14
:
54
 linux64.so
￼

[root@node163 Jni]
# cp ../linux/linux64.so /root/vdbench50407/linux/aarch64.so
￼




D:\vdbench50406>more raw-test.txt
￼
sd=sd1,lun=\\.\G:
￼
wd=wd1,sd=sd1,seekpct=
0
,rdpct=
0
,xfersize=
1
M
￼
rd=rd1,wd=wd1,iorate=max,maxdata=
100
M,elapsed=
64800
,warmup=
30
,interval=
5
￼

D:\vdbench50406>vdbench.
bat 
-f raw-test.txt
￼
Copyright (c) 
2000
, 
2016
, 
Oracle and/or 
its affiliates. All rights reserved.
￼
Vdbench 
distribution: 
vdbench50406 Wed 
July 
20
 
15
:
49
:
52
 MDT 
2016
￼
For documentation, see 
'vdbench.pdf'
.
￼

17:
35
:
24
.
328
 input argument 
scanned: 
'-fraw-test.txt'
￼
17
:
35
:
24
.
375
 Starting slave: D:\vdbench50406\vdbench SlaveJvm -m localhost -n localhost
-10-220119-17
.
35
.
24
.
293
 -l localhost
-0
 -p 
5570
￼
17
:
35
:
24
.
834
 All slaves are now connected
￼
17
:
35
:
25
.
263
 Raw device 
'sd=sd1,lun=\\.\G:'
 does not exist, 
or 
no permissions.
￼
17
:
35
:
25
.
764
￼
17
:
35
:
25
.
764
 Please check above failures
￼
17
:
35
:
25
.
765
￼
java.lang.RuntimeException: 
Please check above failures
￼
        
at
 Vdb.common.failure(common.
java:335)
￼
        
at
 Vdb.InfoFromHost.matchDataWithSds(InfoFromHost.
java:674)
￼
        
at
 Vdb.InfoFromHost.receiveInfoFromHost(InfoFromHost.
java:485)
￼
        
at
 Vdb.SlaveOnMaster.processSlave(SlaveOnMaster.
java:151)
￼
        
at
 Vdb.SlaveOnMaster.run(SlaveOnMaster.
java:42)
￼







root@node65:/home/vdbench50406# ./vdbench -t
￼

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.
￼
Vdbench distribution: vdbench50406 Wed July 20 15:49:52 MDT 2016
￼
For documentation, see 'vdbench.pdf'.
￼

*
￼
*
￼
*
￼
*
 Minimum required Java version for Vdbench is 1.7.0; 
￼
*
 You are currently running 11.0.9.1
￼
*
 Vdbench terminated.
￼
*
￼
*
￼
*
￼
CTRL-C requested. vdbench terminating
￼



   
// Removed as per 50407 because of java 1.10.x
￼
    
//checkJavaVersion();
￼

    
//....
￼

     
private
 
static
 void checkJavaVersion()
￼
  {
￼
    
if
 (common.get_debug(common.
USE_ANY_JAVA
))
￼
      
return
;
￼
    
if
 (
!
JVMCheck
.isJREValid(
System
.getProperty(
"java.version"
), 
1
, 
7
, 
0
))
￼
    {
￼
      
System
.out.print(
"*
\n
*
\n
*
\n
"
);
￼
      
System
.out.println(
"* Minimum required Java version for Vdbench is 1.7.0; 
\n
"
 
+
￼
                         
"* You are currently running "
 
+
 
System
.getProperty(
"java.version"
) 
+
￼
                         
"
\n
* Vdbench terminated."
);
￼
      
System
.out.println(
"*
\n
*
\n
*
\n
"
);
￼

      
System
.exit(
-
99
);
￼
    }
￼
  } 
￼


50407
rc29
￼
*
   The 
check
 
to
 make sure you 
are
 
running
 java 
1.7
 
or
 higher has been removed.


hd
=default,vdbench=/root/vdbench50406,user=root,shell=ssh
￼
hd
=hd1,system=node241
￼
hd
=hd2,system=node242
￼
hd
=hd3,system=node243
￼

fsd
=default,openflags=directio,depth=
2
,width=
3
,files=
2
,size=
128
k
￼
fsd
=fsd1,anchor=/mnt/client1
￼
fsd
=fsd2,anchor=/mnt/client2
￼
fsd
=fsd3,anchor=/mnt/client3
￼

fwd
=default,operation=read,xfersize=
4
k,fileio=sequential,fileselect=random,threads=
2
￼
fwd
=fwd1,fsd=fsd1,host=hd1
￼
fwd
=fwd2,fsd=fsd2,host=hd2
￼
fwd
=fwd3,fsd=fsd3,host=hd3
￼

rd
=rd1,fwd=(fwd1-fwd3),fwdrate=max,format=restart,elapsed=
604800
,interval=
10
￼

hd
=default,vdbench=/root/vdbench50406,user=root,shell=ssh
￼
hd
=hd1,system=node241
￼
hd
=hd2,system=node242
￼
hd
=hd3,system=node243
￼

sd
=sd1,hd=hd1,lun=/dev/sdb,openflags=o_direct,threads=
6
￼
sd
=sd3,hd=hd2,lun=/dev/sdb,openflags=o_direct,threads=
6
￼
sd
=sd6,hd=hd3,lun=/dev/sdb,openflags=o_direct,threads=
6
￼

wd
=wd1,sd=sd*,seekpct=
100
,rdpct=
100
,xfersize=
8
k,skew=
40
￼
wd
=wd2,sd=sd*,seekpct=
100
,rdpct=
0
,xfersize=
8
k,skew=
10
￼
wd
=wd3,sd=sd*,seekpct=
100
,rdpct=
100
,xfersize=
1024
k,skew=
40
￼
wd
=wd4,sd=sd*,seekpct=
100
,rdpct=
0
,xfersize=
1024
k,skew=
10
￼

rd
=rd1,wd=wd*,iorate=max,maxdata=
400
GB,warmup=
30
,elapse=
604800
,interval=
5
￼


[root@node241 vdbench50406]
# cat Single-RawDisk.html 
￼
sd
=sd1,lun=/dev/sdb,openflag=o_direct
￼
wd
=wd1,sd=sd1,seekpct=
0
,rdpct=
0
,xfersize=
1
M
￼
rd
=rd1,wd=wd1,iorate=max,warmup=
60
,elapsed=
600
,interval=
2
￼
[root@node241 vdbench50406]
# 
￼
[root@node241 vdbench50406]
# ./vdbench -f Single-RawDisk.html
￼


[root@node241 vdbench50406]
# cat Multi-RawDisk
￼
hd
=default,vdbench=/root/vdbench50406,user=root,shell=ssh
￼
hd
=hd1,system=node241
￼
hd
=hd2,system=node242
￼
hd
=hd3,system=node243
￼
sd
=sd1,hd=hd1,lun=/dev/sdb,openflag=o_direct
￼
sd
=sd2,hd=hd2,lun=/dev/sdb,openflag=o_direct
￼
sd
=sd3,hd=hd3,lun=/dev/sdb,openflag=o_direct
￼
wd
=wd1,sd=sd*,seekpct=
0
,rdpct=
0
,xfersize=
1
M
￼
rd
=rd1,wd=wd1,iorate=max,maxdata=
100
M,elapsed=
64800
,warmup=
30
,interval=
5
￼
[root@node241 vdbench50406]
# 
￼
[root@node241 vdbench50406]
# ./vdbench -f Multi-RawDisk
￼

 ￼ .\vdbench.bat
 
parseflat
 
-i
 
D:\vdbench50406\output\flatfile.html
 
-c
 
run
 
rate
 
MB/sec
 
seekpct
 
rdpct
 
bytes/io
 
threads
 
resp
 
-o
 
d:\output.csv
 
-a
￼
vdbench parseflat arguments:
￼
Argument 0:
 
-i
￼
Argument 1:
 
D:\vdbench50406\output\flatfile.html
￼
Argument 2:
 
-c
￼
Argument 3:
 
run
￼
Argument 4:
 
rate
￼
Argument 5:
 
MB/sec
￼
Argument 6:
 
seekpct
￼
Argument 7:
 
rdpct
￼
Argument 8:
 
bytes/io
￼
Argument 9:
 
threads
￼
Argument 10:
 
resp
￼
Argument 11:
 
-o
￼
Argument 12:
 
D:\output.csv
￼
Argument 13:
 
-a
￼
14
:12:49.265
 
ParseFlat
 
completed
 
successfully.
￼


Jun 
04
, 
2020
  interval        i/o   MB/sec   bytes   
read
     resp     
read
    
write
     resp     resp queue  cpu%  cpu%
￼
                             rate  
1024
**
2
     i/o    pct     
time
     resp     resp      max   stddev depth sys+u   sys
￼
19
:
12
:
46.068
 avg_7-
126
      
82.74
    
82.74
 
1048576
   
0
.
00 289.158
    
0
.
000 289.158
 
2092.803 155.103 23.9 16.3 14.2
￼


hd
=default,vdbench=/root/vdbench50406,user=root,shell=ssh
￼
hd
=hd1,system=node21
￼
hd
=hd2,system=node22
￼
hd
=hd3,system=node23
￼
hd
=hd4,system=node24
￼
hd
=hd5,system=node25
￼
hd
=hd6,system=node26
￼
fsd
=fsd1,anchor=/client/test01,depth=
2
,width=
100
,files=
100
,size=
4
M,shared=
yes
￼
fwd
=format,threads=
24
,xfersize=
1
m
￼
fwd
=default,xfersize=
1
m,fileio=sequential,fileselect=sequential,operation=write,threads=
24
￼
fwd
=fwd1,fsd=fsd1,host=hd1
￼
fwd
=fwd2,fsd=fsd1,host=hd2
￼
fwd
=fwd3,fsd=fsd1,host=hd3
￼
fwd
=fwd4,fsd=fsd1,host=hd4
￼
fwd
=fwd5,fsd=fsd1,host=hd5
￼
fwd
=fwd6,fsd=fsd1,host=hd6
￼
rd
=rd1,fwd=fwd*,fwdrate=max,format=restart,elapsed=
600
,interval=
1
￼

hd
=default,vdbench=/root/vdbench50406,user=root,shell=ssh
￼
hd
=hd1,system=node21
￼
hd
=hd2,system=node22
￼
hd
=hd3,system=node23
￼
hd
=hd4,system=node24
￼
hd
=hd5,system=node25
￼
hd
=hd6,system=node26
￼
fsd
=fsd1,anchor=/client/test02,depth=
2
,width=
100
,files=
100
,size=
4
M,shared=
yes
￼
fwd
=format,threads=
24
,xfersize=
1
m
￼
fwd
=default,xfersize=
1
m,fileio=sequential,fileselect=sequential,operation=read,threads=
24
￼
fwd
=fwd1,fsd=fsd1,host=hd1
￼
fwd
=fwd2,fsd=fsd1,host=hd2
￼
fwd
=fwd3,fsd=fsd1,host=hd3
￼
fwd
=fwd4,fsd=fsd1,host=hd4
￼
fwd
=fwd5,fsd=fsd1,host=hd5
￼
fwd
=fwd6,fsd=fsd1,host=hd6
￼
rd
=rd1,fwd=fwd*,fwdrate=max,format=restart,elapsed=
600
,interval=
1
￼


mkdir
 vdbench50407-src
￼
unzip vdbench50407.src.zip -d vdbench50407-src/
￼
cd
 vdbench50407-src/src/
￼
mkdir
 linux
￼

cd Jni/
￼
sed -i 
's#vdb=$mine/vdbench504#vdb=/root/vdbench50407-src/src#g'
 
make
.linux
￼
sed -i 
's#java=/net/sbm-240a.us.oracle.com/export/swat/swat_java/linux/jdk1.5.0_22/#java=/usr/lib/jvm/java-1.8.0-openjdk/#g'
 
make
.linux
￼
sed -i 
's/-m32//g'
 
make
.linux 
￼
sed -i 
's/-m64//g'
 
make
.linux 
￼

```

￼  
 ==运行测试模型==  
注：-f后接测试参数文件名，-o后接导出测试结果路径  
./vdbench -f {filename} -o {exportpath}￼  
 ==建立rsh通信==  
注：此命令是用于windows系统多主机联机跑vdbench时使用，因为windows操作系统不支持ssh，因此，vdbench提供了rsh的通信方式。在目标主机上执行此工具后，vdbench将会启动一个java socket用于vdbench slave与master之间通信  
./vdbench rsh￼  
**四、参数说明****#**  
vdbench可用于文件系统及块设备基准性能测试，以下主要介绍文件系统及块存储的常用测试参数  
vdbench所有测试参数都定义到一个参数文件内，在运行时按照顺序被读取执行相应操作，在参数文件定义时需要执行顺序进行定义  
**1、文件系统****#**  
 ==文件系统参数文件定义顺序为：HD、FSD、FWD、RD==  
==1.1、HD（Host Define）==**#**  
非必选项，单机运行时不需要配置HD参数，一般只有在多主机联机测试时才需要配置

- ==hd= 标识主机定义的名称，多主机运行时，可以使用hd1、hd2、hd3...区分==
- ==system= 主机IP地址或主机名==
- ==vdbench= vdbench执行文件存放路径，当多主机存放路径不同时，可在hd定义时单独指定==
- ==user= slave和master通信使用用户==
- ==shell= 可选值为====rsh====、====ssh====或====vdbench====，默认值为====rsh====，多主机联机测试时，mater和slave主机间通信方式====￼====当参数值为====rsh====时，需要配置master和slave主机rsh互信，考虑到rsh使用明文传输，安全级别不够，通常情况下不建议使用这种通信方式====￼====当参数值为====ssh====时，需要配置master和slave主机ssh互信，通常Linux主机联机时使用此通信方式====￼====当参数值为====vdbench====，需要在所有slave主机运行====vdbench rsh====启用vdbench本身的rsh守护进程，通常Window主机联机时使用此通信方式==

==1.2、FSD（File System Define）==**#**

- ==fsd= 标识文件系统定义的名称，多文件系统时（====fsd1====、====fsd2====、====fsd3====...），可以指定====default====（将相同的参数作为所有fsd的默认值）==
- ==openflags= 通过设置为====o_direct====或====directio====，以无缓冲缓存的方式进行读写操作==
- ==anchor= 文件写入目录，linux指定路径为====/dir01====；windows指定路径为====E:\dir01====；==
- ==depth= 创建目录层级数（即目录深度）==
- ==width= 每层文件夹的子文件夹数==
- ==files= 测试文件个数（vdbench测试过程中会生成多层级目录结构，实际只有最后一层目录会生成测试文件）==
- ==size= 每个测试文件大小==
- ==**distribution= ** 可选值为====bottom====或====all====，默认为====bottom====￼====--当参数值为====bottom====时，程序只在最后一层目录写入测试文件====￼====--当参数值为====all====时，程序在每一层目录都写入测试文件==
- ==shared= 可选值为====yes====或====no====，默认值为====no====，一般只有在多主机联机测试时指定====￼====vdbench不允许不同的slave之间共享同一个目录结构下的所有文件，因为这样会带来很大的开销，但是它们允许共享同一个目录结构。加入设置了shared=yes，那么不同的slave可以平分一个目录下所有的文件来进行访问，相当于每个slave有各自等分的访问区域，因此不能测试多个客户的对同一个文件的读写====￼====--当多主机联机测试时，写入的根目录====anchor====为同一个路径时，需要指定参数值为====yes==

hd=default,vdbench=/root/vdbench50406,user=root,shell=ssh￼hd=hd1,system=node1￼hd=hd2,system=node2￼hd=hd3,system=node3￼fsd=fsd1,anchor=/client/,depth=2,width=100,files=100,size=4k,shared=yes￼  
 ==计算公式如下：==  
 ==最后一层生成文件夹个数=====width====^====depth==  
 ==测试文件个数=（====width====^====depth====）*====files==  
fsd=fsd1,anchor=/dir1,depth=2,width=3,files=2,size=128k￼==以上述参数为例，生成目录结构及测试文件如下：==￼==最后一层文件夹数===3^2=9 ==最后一层文件数===9*2=18￼/dir1/￼├── no_dismount.txt￼├── vdb.1_1.dir￼│ ├── vdb.2_1.dir￼│ │ ├── vdb_f0001.file￼│ │ └── vdb_f0002.file￼│ ├── vdb.2_2.dir￼│ │ ├── vdb_f0001.file￼│ │ └── vdb_f0002.file￼│ └── vdb.2_3.dir￼│ ├── vdb_f0001.file￼│ └── vdb_f0002.file￼├── vdb.1_2.dir￼│ ├── vdb.2_1.dir￼│ │ ├── vdb_f0001.file￼│ │ └── vdb_f0002.file￼│ ├── vdb.2_2.dir￼│ │ ├── vdb_f0001.file￼│ │ └── vdb_f0002.file￼│ └── vdb.2_3.dir￼│ ├── vdb_f0001.file￼│ └── vdb_f0002.file￼├── vdb.1_3.dir￼│ ├── vdb.2_1.dir￼│ │ ├── vdb_f0001.file￼│ │ └── vdb_f0002.file￼│ ├── vdb.2_2.dir￼│ │ ├── vdb_f0001.file￼│ │ └── vdb_f0002.file￼│ └── vdb.2_3.dir￼│ ├── vdb_f0001.file￼│ └── vdb_f0002.file￼└── vdb_control.file￼  
12 directories, 20 files￼  
**1.3、**==FWD（FileSystem Workload Defile）==

- ==fwd= 标识文件系统工作负载定义的名称，多文件系统工作负载定义时，可以使用fwd1、fwd2、fwd3...区分==
- ==fsd= 标识此工作负载使用文件存储定义的名称==
- ==host= 标识此工作负载使用主机==
- ==operation= 可选值为====read====或====write====,文件操作方式==
- ==rdpct= 可选值为====0====~====100====，读操作占比百分比，一般混合读写时需要指定，当值为60时，则混合读写比为6：4==
- ==fileio=== ==可选值为====random====或====sequential====，标识文件 I/O 将执行的方式==
- ==fileselect=== ==random====或====sequential====，标识选择文件或目录的方式==
- ==xfersizes= 数据传输（读取和写入操作）处理的数据大小(即单次IO大小)==
- ==threads= 此工作负载的并发线程数量==

==注：==  
==1、默认情况下，预填数据工作负载定义为====threads=8,xfersize=128k====，即预填数据使用8线程，IO大小为128K==  
==如需更改默认预填数据工作负载定义，则需要增加参数指定线程数和IO大小（====fwd=format,threads=nn,xfersize=nn====）==  
**1.4、**==RD（Run Define）==

- ==rd= 标识文件系统运行定义的名称。==
- ==fwd= 标识文件系统工作负载定义的名称。==
- ==fwdrate= 每秒执行的文件系统操作数量。设置为max，表示不做任何限制，按照最大强度自适应==
- ==format= 可选值为====no====、====yes====、或====restart====，标识预处理目录和文件结构的方式==
- ==no：默认参数值，不执行format预处理操作，如测试目录不存在文件时，vdbench会由于无可用文件读写而异常退出==
- ==yes：表示删除测试目录已有文件结构，并且重新创建新的文件结构==
- ==restart：表示只创建未生成的目录或文件，并且增大未达到实际大小的文件==
- ==elapsed= 默认值为====30====，测试运行持续时间（单位为秒）==
- ==interval= 结果输出打印时间间隔（单位为秒）==

**2、块设备****#**  
 ==块设备参数文件定义顺序为：HD、SD、WD、RD==  
==1.1、HD（Host Define）==  
非必选项，单机运行时不需要配置HD参数，一般只有在多主机联机测试时才需要配置

- ==hd= 标识主机定义的名称，多主机运行时，可以使用hd1、hd2、hd3...区分==
- ==system= 主机IP地址或主机名==
- ==vdbench= vdbench执行文件存放路径，当多主机存放路径不同时，可在hd定义时单独指定==
- ==user= slave和master通信使用用户==
- ==shell= 可选值为====rsh====、====ssh====或====vdbench====，默认值为====rsh====，多主机联机测试时，mater和slave主机间通信方式====￼====当参数值为====rsh====时，需要配置master和slave主机rsh互信，考虑到rsh使用明文传输，安全级别不够，通常情况下不建议使用这种通信方式====￼====当参数值为====ssh====时，需要配置master和slave主机ssh互信，通常Linux主机联机时使用此通信方式====￼====当参数值为====vdbench====，需要在所有slave主机运行====vdbench rsh====启用vdbench本身的rsh守护进程，通常Window主机联机时使用此通信方式==

==1.2、SD（Storage Define）==

- ==sd= 标识存储定义的名称==
- ==hd= 标识主机定义的名称==
- ==lun= 写入块设备，linux使用sdb盘，则指定路径为====/dev/sdb====；windows使用G盘，则指定路径为====\\.\G:====；==
- ==openflags= 通过设置为====o_direct====或====directio====，以无缓冲缓存的方式进行读写操作==
- ==threads= 对SD的最大并发I/O请求数量==

==1.3、WD（Workload Define）==

- ==wd= 标识工作负载定义的名称==
- ==sd= 标识存储定义的名称==
- ==seekpct= 可选值为====0====或====100====(也可使用====sequential====或====random====表示)，默认值为====100====，随机寻道的百分比，设置为====0====时表示顺序，设置为====100====时表示随机。==
- ==rdpct= 读取请求占请求总数的百分比，设置为0时表示写，设置为100时表示读==
- ==xfersize= 要传输的数据大小。默认设置为4k==
- ==skew= 非必选项，一般在多个工作负载时需要指定，表示该工作负载占总工作量百分比（skew总和为100）==

==1.4、RD（Run Define）==

- ==rd= 标识运行定义的名称==
- ==wd= 标识工作负载定义的名称==
- ==iorate= 常用可选值为====100====、====max====，此工作负载的固定I/O速率====￼====--当参数值为====100====时，以每秒100个I/Os的速度运行工作负载，当参数值设置为一个低于最大速率的值时，可以达到限制读写速度的效果====￼====--当参数值为====max====时，以最大的I/O速率运行工作负载，一般测试读写最大性能时，该参数值均为====max==
- ==warmup= 预热时间（单位为秒），默认情况下vdbench会将第一个时间间隔输出数据排除在外,程序在预热时间内的测试不纳入最终测试结果中（即预热结束后，才开始正式测试）====￼====--当====interval====为5、====elapsed====为600时，测试性能为====2====~====elapsed/interval====（avg_2-120）时间间隔内的平均性能====￼====--当====interval====为5、====warmup====为60、====elapsed====为600时，测试性能为====1+====（====warmup/interval====）====~====（====warmup+elapsed====）====/interval====(avg_13-132)时间间隔内的平均性能==
- ==maxdata= 读写数据大小，通常情况下，当运行====elapsed====时间后测试结束；当同时指定====elapsed====和====maxdata====参数值时，以最快运行完的参数为准（即====maxdata====测试时间小于====elapsed====时，程序写完====elapsed====数据量后结束）====￼====--当参数值为====100====以下时，表示读写数据量为总存储定义大小的倍数（如maxdata=2，2个存储定义（每个存储定义数据量为100G），则实际读写数据大小为400G）====￼====--当参数值为====100====以上时，表示数据量为实际读写数据量（可以使用单位M、G、T等）==
- ==elapsed= 默认值为====30====，测试运行持续时间（单位为秒）==
- ==interval= 报告时间间隔（单位为秒）==

**五、运行使用****#**

- ==Linux====￼== ==示例如下，单节点针对裸盘测试，1M顺序写，测试时间600s，预热时间60s，报告时间间隔2s==

- ==Window====￼== ==示例如下，单节点针对文件系统测试，1M顺序写，目录深度为2，每层目录数为3，每个目录文件数为10，每个文件大小为200M，测试时间为600s，报告时间时间2s==

E:\vdbench50406>more "Single FileSystem.txt"￼fsd=fsd1,anchor=E:\Sigle-FileSystem,depth=2,width=3,files=10,size=200M￼fwd=fwd1,fsd=fsd1,operation=write,xfersize=1M,fileio=sequential,fileselect=rando￼m,threads=2￼rd=rd1,fwd=fwd1,fwdrate=max,format=yes,elapsed=600,interval=5￼E:\vdbench50406>￼E:\vdbench50406>vdbench -f "Single FileSystem.txt"￼

- ==Linux====￼====1、按照====二、安装部署====，配置多主机ssh互信====￼====2、master主机运行测试参数文件即可====￼====示例如下，三节点针对裸盘联机测试，1M顺序写，测试数据量为400G，预热时间30s，报告间隔5s==

- ==Window====￼====1、所有slave主机运行vdbench本身rsh守护进程==

E:\vdbench50406>vdbench rsh￼  
==2、master主机运行测试参数文件即可==  
==示例如下，三节点针对文件系统联机测试，1M顺序写，目录深度为2，每层目录数为3，每个目录文件数为10000，每个文件大小为200M，测试时间为600s，报告间隔1s==  
E:\vdbench50406>more "Multi FileSystem.txt"￼hd=default,vdbench=E:\vdbench50406,user=Micah,shell=vdbench￼hd=hd1,system=66.66.66.250￼hd=hd2,system=66.66.66.252￼fsd=fsd1,anchor=Z:\Sigle-FileSystem-01,depth=2,width=3,files=10000,size=200M￼fsd=fsd2,anchor=Z:\Sigle-FileSystem-02,depth=2,width=3,files=10000,size=200M￼fwd=default,operation=write,xfersize=1M,fileio=sequential,fileselect=random,threads=16￼fwd=fwd1,fsd=fsd1,host=hd1￼fwd=fwd2,fsd=fsd2,host=hd2￼rd=rd1,fwd=fwd*,fwdrate=max,format=yes,elapsed=600,interval=1￼E:\vdbench50406>￼E:\vdbench50406>vdbench -f "Multi FileSystem.txt"￼  
**六、结果分析****#**  
当vdbench运行完负载测试后，会在安装目录下生成output文件夹，里边包含测试结果文件

- ==errorlog.html====￼====当运行测试启用数据校验时，它可能会包含一些错误信息，如：==
    
    - ==无效的密钥读取==
    - ==无效的 lba 读取（一个扇区的逻辑字节地址）==
    - ==无效的 SD 或 FSD 名称读取==
    - ==数据损坏==
    - ==坏扇区==
- ==flatfile.html====￼====vdbench 生成的一种逐列的 ASCII 格式的信息，可以使用====parseflat====参数解析结果==

./vdbench parseflat -i <flatfile.html> -o output.csv [-c col1 col2 ..] [-a] [-f col1 value1 col2 value2..]￼ -i input flatfile, e.g. output/flatfile.html￼ -o output CSV file name (default stdout)￼ -c which column to write to CSV. Columns are written in the order specified￼ -f filters: 'if (colX == valueX) ... ...' (Alphabetic compare)￼ -a include only the 'avg' data. Default: include only non-avg data.￼ -i==是表示待分析的文件，这里写==vdbench==输出目录里的==flatfile.html==这个文件，写其它文件不能正常解析；==￼ -o==是解析后的输出文件，可以手动指定存放目录。文件格式为==CSV==，此文件的列由==-c==参数指定，列的顺序为==-c==参数的顺序==￼ -a==是表示==csv==文件中只记录测试过程中的==avg==值==￼

- ==histogram.html====￼====一种包含报告柱状图的响应时间、文本格式的文件==
- ==logfile.html====￼====包含 Java 代码写入控制台窗口的每行信息的副本。logfile.html 主要用于调试用途==
- ==parmfile.html====￼====包含测试运行配置参数信息==
- ==summary.html====￼====记录全部数据信息，显示每个报告间隔内总体性能情况及工作负载情况，以及除第一个间隔外的所有间隔的加权平均值==
- ==totals.html====￼====记录全部数据计算之后的平均值，一般测试结果从该文件取值，除第一个间隔外所有间隔的加权平均值==

**2、结果分析****#**  
**2.1、文件系统****#**  
==测试参数如下：==￼hd=default,vdbench=E:\vdbench50406,user=Micah,shell=vdbench￼hd=hd1,system=66.66.66.250￼hd=hd2,system=66.66.66.252￼fsd=fsd1,anchor=Z:\Sigle-FileSystem-01,depth=2,width=3,files=10,size=4M￼fsd=fsd2,anchor=Z:\Sigle-FileSystem-02,depth=2,width=3,files=10,size=4M￼fwd=default,operation=write,xfersize=1M,fileio=sequential,fileselect=random,threads=2￼fwd=fwd1,fsd=fsd1,host=hd1￼fwd=fwd2,fsd=fsd2,host=hd2￼rd=rd1,fwd=fwd*,fwdrate=max,format=yes,elapsed=600,interval=5￼  
==测试结果如下：==￼18:47:03.001 Starting RD=format_for_rd1￼  
==六月== 04, 2020 .Interval. .ReqstdOps... ...cpu%... read ....read..... ....write.... ..mb/sec... mb/sec .xfer.. ...mkdir.... ...rmdir.... ...create... ....open.... ...close.... ...delete...￼ rate resp total sys pct rate resp rate resp read write total size rate resp rate resp rate resp rate resp rate resp rate resp￼18:48:40.218 avg_2-20 57.6 6.244 13.4 2.99 0.0 0.0 0.000 57.6 6.244 0.00 7.20 7.20 131072 0.2 104.49 0.2 41.526 1.8 7527.0 1.8 192.01 1.8 7134.3 1.8 21.984￼  
18:48:42.000 Starting RD=rd1; elapsed=600; fwdrate=max. For loops: None￼  
18:58:42.205 avg_2-120 6.2 1.063 13.0 2.80 0.0 0.0 0.000 6.2 1.063 0.00 6.24 6.24 1048576 0.0 0.000 0.0 0.000 0.0 0.000 1.6 47.864 1.6 2401.1 0.0 0.000￼

- ==Interval====￼====报告间隔序号，测试结果一般为除第一个时间间隔外所有时间间隔加权平均值====￼====如elapsed=600,interval=5，则性能结果为第2个间隔到第120个间隔的平均值（avg_2-120）==
- ==ReqstdOps==
    
    - ==rate====￼====每秒读写I/O个数（读写IOPS），可以通过====rd====运行定义参数====fwdrate====控制====￼====当====fwdrate====为====max====时，以最大I/O速率运行工作负载====￼====当====fwdrate====为低于最大I/0速率的一个数值时，可以限制读写速度，以固定I/O速率运行工作负载==
    - ==resp====￼====读写请求响应时间（读写时延），单位为====ms==
- ==cpu%==
    
    - ==tatol====￼====总的cpu占用率==
    - ==sys====￼====系统cpu占用率==
- ==read pct====￼====读取请求占总请求数百分比占比，当为0时表示写，当为100时表示读==
- ==read==
    
    - ==rate====￼====每秒读I/O个数（读IOPS）==
    - ==resp====￼====读请求响应时间（读时延），单位为====ms==
- ==write==
    
    - ==rate====￼====每秒写I/O个数（写IOPS）==
    - ==resp====￼====写请求响应时间（写时延），单位为====ms==
- ==mb/sec==
    
    - ==read====￼====每秒读取速度==
    - ==write====￼====每秒写入速度==
    - ==total====￼====每秒读写速度总和==
- ==xfersize====￼====每个读写I/O传输数据量（即单个读写I/O大小），单位为字节====B==

**2.2、块设备****#**  
==测试参数如下：==￼messagescan=no￼hd=default,vdbench=/root/vdbench50406,user=root,shell=ssh￼hd=hd1,system=node241￼hd=hd2,system=node242￼hd=hd3,system=node243￼sd=sd1,hd=hd1,lun=/dev/sdb,openflag=o_direct￼sd=sd2,hd=hd2,lun=/dev/sdb,openflag=o_direct￼sd=sd3,hd=hd3,lun=/dev/sdb,openflag=o_direct￼wd=wd1,sd=sd*,seekpct=0,rdpct=0,xfersize=1M￼rd=rd1,wd=wd1,iorate=max,elapsed=600,warmup=30,interval=5￼  
==测试结果如下：==￼<a name="_1143839598"></a><i><b>19:02:15.001 Starting RD=rd1; I/O rate: Uncontrolled MAX; elapsed=600 warmup=30; For loops: None</b></i>￼

- ==interval====￼====报告间隔序号，测试结果一般为除第一个时间时间外所有时间间隔加权平均值，如有设置预热时间，则这部分测试数据也需要排除在外====￼====如elapsed=600,warmup=30，interval=5，则性能测试结果为第7个间隔到第126个间隔的平均值（avg_7-126）==
- ==i/o rate====￼====每秒读写I/O个数（读写IOPS），可以通过====rd====运行定义参数====iorate====控制====￼====当====iorate====为====max====时，以最大I/O速率运行工作负载====￼====当====iorate====为低于最大I/0速率的一个数值时，可以限制读写速度，以固定I/O速率运行工作负载==
- ==MB/sec====￼====每秒读写速度（读写带宽）====￼====注：按官方手册说明，==
- ==bytes i/0====￼====每个读写I/O传输数据量（即单个读写I/O大小），单位为字节====B====，可以通过====wd====工作负载定义参数====xfersize====控制==
- ==read pct====￼====读取请求占请求总数的百分比，可以通过====wd====工作负载定义参数====rdpct====控制====￼====当====rdpct====为====0====时，表示测试模型为写====￼====当====rdpct====为====100====时，表示测试模型为读==
- ==resp time====￼====请求响应时间（读写时延），单位为毫秒====ms==
- ==read resp====￼====读取请求响应时间，单位为毫秒====ms==
- ==write resp====￼====写入请求响应时间，单位为毫秒====ms==
- ==resp max====￼====最大请求响应时间，单位为毫秒====ms==
- ==resp stddev====￼====请求响应时间标准偏差，单位为毫秒====ms==
- ==queue depth====￼====读写I/0队列深度==
- ==cpu% sys+u====￼====内核态空间及用户态空间CPU占用率==
- ==cpu% sys====￼====内核态空间CPU占用率 2020年05月29日 16:00:16 Clear==

**七、测试说明****#**  
**1、测试总结****#**

34. ==线程数（thread）一般设置为客户端CPU线程数总大小== ==grep 'processor' /proc/cpuinfo | sort -u | wc -l==
35. ==测试总数据量需要为客户端内存大小两倍==
36. ==测试读模型时需要清理客户端缓存信息== ==sync====；====echo 3 > /proc/sys/vm/drop==
    
    - ==4M顺序写====￼====目录深度2、单级目录数100、单个目录文件数100、单文件大小4M、IO块大小1M、顺序写==
    
    - ==4M顺序读====￼====目录深度2、单级目录数100、单个目录文件数100、单文件大小4M、IO块大小1M、顺序读==
    
    - ==8M混合读写====￼====目录深度2、单级目录数100、单个目录文件数100、单文件大小8M、IO块大小1M、混合读写（读写比为6:4）==

hd=default,vdbench=/root/vdbench50406,user=root,shell=ssh￼hd=hd1,system=node21￼hd=hd2,system=node22￼hd=hd3,system=node23￼hd=hd4,system=node24￼hd=hd5,system=node25￼hd=hd6,system=node26￼fsd=fsd1,anchor=/client/test03,depth=2,width=100,files=100,size=8M,shared=yes￼fwd=format,threads=24,xfersize=1m￼fwd=default,xfersize=1m,fileio=random,fileselect=random,rdpct=60,threads=24￼fwd=fwd1,fsd=fsd1,host=hd1￼fwd=fwd2,fsd=fsd1,host=hd2￼fwd=fwd3,fsd=fsd1,host=hd3￼fwd=fwd4,fsd=fsd1,host=hd4￼fwd=fwd5,fsd=fsd1,host=hd5￼fwd=fwd6,fsd=fsd1,host=hd6￼rd=rd1,fwd=fwd*,fwdrate=max,format=restart,elapsed=600,interval=1￼  
**七、Q&A****#**  
**1、open failed for xxx****#**  
**- 问题描述：****#**  
==使用vdbench测试过程中，测试中断，报错信息如下：==  
19:37:41.155 19:37:44.813 error: 20￼19:37:41.155 19:37:44.813 file_open(), open /trash/64M/vdb.1_47.dir/vdb.2_24.dir/vdb_f0002.file failed￼19:37:41.155 19:37:44.814 error: 20￼19:37:41.155 19:37:44.814 file_open(), open /trash/64M/vdb.1_47.dir/vdb.2_24.dir/vdb_f0003.file failed￼19:37:41.156 19:37:44.814 Maximum native memory allocation: 1,048,576; Current allocation: 1,048,576￼19:37:41.156 19:37:44.814 Maximum native memory allocation: 1,048,576; Current allocation: 1,048,576￼19:37:41.156 19:37:44.814 error: 20￼19:37:41.156 19:37:44.814 ￼19:37:41.156 19:37:44.814 open failed for /trash/64M/vdb.1_47.dir/vdb.2_24.dir/vdb_f0002.file￼19:37:41.156 19:37:44.814 ￼19:37:41.158 java.lang.RuntimeException: open failed for /trash/64M/vdb.1_47.dir/vdb.2_24.dir/vdb_f0002.file￼  
**- 原因分析：****#**  
==默认vdbench限制了java内存使用量，测试期间java运行内存不足才导致测试异常中断。==  
**- 处理措施：****#**  
==增大vdbench运行脚本的java内存参数==  
[root@node40 ~]# cat vdbench50406/vdbench￼if [ "$1" == "SlaveJvm" ]; then￼ $java -client -Xmx10240m -Xms128m -cp $cp Vdb.SlaveJvm $*￼ exit $?￼else￼ $java -client -Xmx10240m -Xms64m -cp $cp Vdb.Vdbmain $*￼ exit $?￼fi￼  
==注：Xmx表示jvm最大内存分配池，Xms表示初始内存分配池==  
==进程初始以Xms内存大小启动，当空余堆内存小于40%时，jvm会增大内存至Xmx；当空余堆内存大于70%时，jvm会减少内存至Xms；为避免每次GC后JVM重新分配内存，可以将Xmx和Xms设置为相同的参数值==  
**2、linux/aarch64.so does not exist****#**  
**- 问题描述：****#**  
==arm服务器运行vdbench，出现共享库不匹配问题====linux/aarch64.so does not exist====，提示需要手动编译==  
[root@node163 vdbench50407]# ./vdbench -t￼  
14:07:47.486 Created output directory '/root/vdbench50407/output'￼14:07:47.737 input argument scanned: '-f/tmp/parmfile'￼14:07:47.866 Starting slave: /root/vdbench50407/vdbench SlaveJvm -m localhost -n localhost-10-220209-14.07.47.444 -l localhost-0 -p 5570 ￼14:07:47.890 ￼14:07:47.890 File /root/vdbench50407/linux/aarch64.so does not exist.￼14:07:47.891 This may be an OS that a shared library currently ￼14:07:47.891 is not available for. You may have to do your own compile.￼14:07:47.891 t: java.lang.UnsatisfiedLinkError: Can't load library: /root/vdbench50407/linux/aarch64.so￼14:07:47.892 ￼14:07:47.892 Loading of shared library /root/vdbench50407/linux/aarch64.so failed.￼14:07:47.892 There may be issues related to a cpu type not being ￼14:07:47.892 acceptable to Vdbench, e.g. MAC PPC vs. X86￼14:07:47.893 Contact me at the Oracle Vdbench Forum for support.￼14:07:47.893 ￼14:07:48.395 ￼14:07:48.396 Failure loading shared library￼14:07:48.396 ￼java.lang.RuntimeException: Failure loading shared library￼ at Vdb.common.failure(common.java:350)￼ at Vdb.common.get_shared_lib(common.java:1103)￼ at Vdb.Native.<clinit>(Native.java:31)￼ at Vdb.common.signal_caller(common.java:737)￼ at Vdb.ConnectSlaves.connectToSlaves(ConnectSlaves.java:98)￼ at Vdb.Vdbmain.masterRun(Vdbmain.java:814)￼ at Vdb.Vdbmain.main(Vdbmain.java:628)￼  
**- 原因分析：****#**  
==vdbench根目录动态库文件====linux/linux64.so====为基于x86编译的，需要基于aarch64重新编译====linux/linux64.so====动态库文件==  
[root@node163 vdbench50407]# ls linux/￼config.sh linux32.so linux64.so sparc64.so￼[root@node163 vdbench50407]# file linux/linux64.so ￼linux/linux64.so: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), statically linked, BuildID[sha1]=34a31f32956f21153c372a95e73c02e84ddd29f8, not stripped￼

- ==下载vdbench50407源码包，下载地址：====vdbench50407.src.zip====￼====解压缩源码包，创建linux目录==

- ==进入Jni目录下，修改make.linux文件，主要修改如下：====￼====-- 指定vdbench源码路径（vdb=xx）====￼====-- 指定jdk路径（java=xx），通常在====/usr/lib/jvm/====路径下====￼====-- 删除全文====-m32====和====-m64====字符==

- ==执行源码编译操作====./make.linux====￼====执行成功后会在====../linux====目录下生成====linux32.so====和====linux64.so====文件，将====linux64.so====文件拷贝到vdbench测试工具根目录====linux/aarch64.so====下，重新运行vdbench测试==

[root@node163 Jni]# ./make.linux￼target directory: /root/vdbench50407-src/src￼Compiling 32 bit￼Linking 32 bit￼  
Compiling 64 bit￼Linking 64 bit￼  
[root@node163 Jni]# ll ../linux/￼total 160￼-rwxrwxrwx 1 root root 78656 Feb 9 14:54 linux32.so￼-rwxrwxrwx 1 root root 78656 Feb 9 14:54 linux64.so￼  
[root@node163 Jni]# cp ../linux/linux64.so /root/vdbench50407/linux/aarch64.so￼  
**3、Raw device 'sd=sd1,lun=\.\G:' does not exist, or no permissions.****#**  
**- 问题描述：****#**  
==windows系统下使用vdbench测试裸盘性能，脚本运行失败，提示====Raw device 'sd=sd1,lun=\\.\G:' does not exist, or no permissions.==  
D:\vdbench50406>more raw-test.txt￼sd=sd1,lun=\\.\G:￼wd=wd1,sd=sd1,seekpct=0,rdpct=0,xfersize=1M￼rd=rd1,wd=wd1,iorate=max,maxdata=100M,elapsed=64800,warmup=30,interval=5￼  
D:\vdbench50406>vdbench.bat -f raw-test.txt￼Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.￼Vdbench distribution: vdbench50406 Wed July 20 15:49:52 MDT 2016￼For documentation, see 'vdbench.pdf'.￼  
17:35:24.328 input argument scanned: '-fraw-test.txt'￼17:35:24.375 Starting slave: D:\vdbench50406\vdbench SlaveJvm -m localhost -n localhost-10-220119-17.35.24.293 -l localhost-0 -p 5570￼17:35:24.834 All slaves are now connected￼17:35:25.263 Raw device 'sd=sd1,lun=\\.\G:' does not exist, or no permissions.￼17:35:25.764￼17:35:25.764 Please check above failures￼17:35:25.765￼java.lang.RuntimeException: Please check above failures￼ at Vdb.common.failure(common.java:335)￼ at Vdb.InfoFromHost.matchDataWithSds(InfoFromHost.java:674)￼ at Vdb.InfoFromHost.receiveInfoFromHost(InfoFromHost.java:485)￼ at Vdb.SlaveOnMaster.processSlave(SlaveOnMaster.java:151)￼ at Vdb.SlaveOnMaster.run(SlaveOnMaster.java:42)￼  
**- 处理措施：****#**  
==1、检查裸盘路径是否正确，如测试G盘裸盘性能，则测试参数指定为====lun=\\.\G:==  
==2、检查是否有执行权限，cmd窗口需要以管理员身份运行==  
**4、Minimum required Java version for Vdbench is 1.7.0****#**  
**- 问题描述：****#**  
==Linux下运行vdbench失败，提示====Minimum required Java version for Vdbench is 1.7.0; Vdbench terminated.==  
root@node65:/home/vdbench50406# ./vdbench -t￼  
Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.￼Vdbench distribution: vdbench50406 Wed July 20 15:49:52 MDT 2016￼For documentation, see 'vdbench.pdf'.￼  
*￼*￼*￼* Minimum required Java version for Vdbench is 1.7.0; ￼* You are currently running 11.0.9.1￼* Vdbench terminated.￼*￼*￼*￼CTRL-C requested. vdbench terminating￼  
**- 处理措施：****#**  
==查看官方相关说明，vdbench50407以前的版本，存在java检测功能（vdbench50407版本已移除），在不变更现有环境java版本的情况下，可升级版本至vdbench50407版本解决==  
// Removed as per 50407 because of java 1.10.x￼ //checkJavaVersion();￼  
//....￼  
private static void checkJavaVersion()￼ {￼ if (common.get_debug(common.USE_ANY_JAVA))￼ return;￼ if (!JVMCheck.isJREValid(System.getProperty("java.version"), 1, 7, 0))￼ {￼ System.out.print("*\n*\n*\n");￼ System.out.println("* Minimum required Java version for Vdbench is 1.7.0; \n" +￼ "* You are currently running " + System.getProperty("java.version") +￼ "\n* Vdbench terminated.");￼ System.out.println("*\n*\n*\n");￼  
System.exit(-99);￼ }￼ } ￼  
==版本发布说明：====# Vdbench 50407 is now available==  
50407rc29￼* The check to make sure you are running java 1.7 or higher has been removed.
  
hd=default,vdbench=/root/vdbench50406,user=root,shell=ssh￼hd=hd1,system=node241￼hd=hd2,system=node242￼hd=hd3,system=node243￼  
fsd=default,openflags=directio,depth=2,width=3,files=2,size=128k￼fsd=fsd1,anchor=/mnt/client1￼fsd=fsd2,anchor=/mnt/client2￼fsd=fsd3,anchor=/mnt/client3￼  
fwd=default,operation=read,xfersize=4k,fileio=sequential,fileselect=random,threads=2￼fwd=fwd1,fsd=fsd1,host=hd1￼fwd=fwd2,fsd=fsd2,host=hd2￼fwd=fwd3,fsd=fsd3,host=hd3￼  
rd=rd1,fwd=(fwd1-fwd3),fwdrate=max,format=restart,elapsed=604800,interval=10￼  
hd=default,vdbench=/root/vdbench50406,user=root,shell=ssh￼hd=hd1,system=node241￼hd=hd2,system=node242￼hd=hd3,system=node243￼  
sd=sd1,hd=hd1,lun=/dev/sdb,openflags=o_direct,threads=6￼sd=sd3,hd=hd2,lun=/dev/sdb,openflags=o_direct,threads=6￼sd=sd6,hd=hd3,lun=/dev/sdb,openflags=o_direct,threads=6￼  
wd=wd1,sd=sd*,seekpct=100,rdpct=100,xfersize=8k,skew=40￼wd=wd2,sd=sd*,seekpct=100,rdpct=0,xfersize=8k,skew=10￼wd=wd3,sd=sd*,seekpct=100,rdpct=100,xfersize=1024k,skew=40￼wd=wd4,sd=sd*,seekpct=100,rdpct=0,xfersize=1024k,skew=10￼  
rd=rd1,wd=wd*,iorate=max,maxdata=400GB,warmup=30,elapse=604800,interval=5￼  
**1、单机运行****#**  
[root@node241 vdbench50406]# cat Single-RawDisk.html ￼sd=sd1,lun=/dev/sdb,openflag=o_direct￼wd=wd1,sd=sd1,seekpct=0,rdpct=0,xfersize=1M￼rd=rd1,wd=wd1,iorate=max,warmup=60,elapsed=600,interval=2￼[root@node241 vdbench50406]# ￼[root@node241 vdbench50406]# ./vdbench -f Single-RawDisk.html￼  
**2、联机运行****#**  
[root@node241 vdbench50406]# cat Multi-RawDisk￼hd=default,vdbench=/root/vdbench50406,user=root,shell=ssh￼hd=hd1,system=node241￼hd=hd2,system=node242￼hd=hd3,system=node243￼sd=sd1,hd=hd1,lun=/dev/sdb,openflag=o_direct￼sd=sd2,hd=hd2,lun=/dev/sdb,openflag=o_direct￼sd=sd3,hd=hd3,lun=/dev/sdb,openflag=o_direct￼wd=wd1,sd=sd*,seekpct=0,rdpct=0,xfersize=1M￼rd=rd1,wd=wd1,iorate=max,maxdata=100M,elapsed=64800,warmup=30,interval=5￼[root@node241 vdbench50406]# ￼[root@node241 vdbench50406]# ./vdbench -f Multi-RawDisk￼  
**1、输出文件****#**  
==示例如下：==￼ .\vdbench.bat parseflat -i D:\vdbench50406\output\flatfile.html -c run rate MB/sec seekpct rdpct bytes/io threads resp -o d:\output.csv -a￼vdbench parseflat arguments:￼Argument 0: -i￼Argument 1: D:\vdbench50406\output\flatfile.html￼Argument 2: -c￼Argument 3: run￼Argument 4: rate￼Argument 5: MB/sec￼Argument 6: seekpct￼Argument 7: rdpct￼Argument 8: bytes/io￼Argument 9: threads￼Argument 10: resp￼Argument 11: -o￼Argument 12: D:\output.csv￼Argument 13: -a￼14:12:49.265 ParseFlat completed successfully.￼  
 ==totals.html====一般包括两个部分，第一部分为文件存储目录结构及数据填充的平均性能值，第二部分为执行测试过程中除第一个时间间隔外所有时间间隔平均性能值，主要看第二部分的内容==  
Jun 04, 2020 interval i/o MB/sec bytes read resp read write resp resp queue cpu% cpu%￼ rate 1024**2 i/o pct time resp resp max stddev depth sys+u sys￼19:12:46.068 avg_7-126 82.74 82.74 1048576 0.00 289.158 0.000 289.158 2092.803 155.103 23.9 16.3 14.2￼  
**2、常见测试参数****#**  
hd=default,vdbench=/root/vdbench50406,user=root,shell=ssh￼hd=hd1,system=node21￼hd=hd2,system=node22￼hd=hd3,system=node23￼hd=hd4,system=node24￼hd=hd5,system=node25￼hd=hd6,system=node26￼fsd=fsd1,anchor=/client/test01,depth=2,width=100,files=100,size=4M,shared=yes￼fwd=format,threads=24,xfersize=1m￼fwd=default,xfersize=1m,fileio=sequential,fileselect=sequential,operation=write,threads=24￼fwd=fwd1,fsd=fsd1,host=hd1￼fwd=fwd2,fsd=fsd1,host=hd2￼fwd=fwd3,fsd=fsd1,host=hd3￼fwd=fwd4,fsd=fsd1,host=hd4￼fwd=fwd5,fsd=fsd1,host=hd5￼fwd=fwd6,fsd=fsd1,host=hd6￼rd=rd1,fwd=fwd*,fwdrate=max,format=restart,elapsed=600,interval=1￼  
hd=default,vdbench=/root/vdbench50406,user=root,shell=ssh￼hd=hd1,system=node21￼hd=hd2,system=node22￼hd=hd3,system=node23￼hd=hd4,system=node24￼hd=hd5,system=node25￼hd=hd6,system=node26￼fsd=fsd1,anchor=/client/test02,depth=2,width=100,files=100,size=4M,shared=yes￼fwd=format,threads=24,xfersize=1m￼fwd=default,xfersize=1m,fileio=sequential,fileselect=sequential,operation=read,threads=24￼fwd=fwd1,fsd=fsd1,host=hd1￼fwd=fwd2,fsd=fsd1,host=hd2￼fwd=fwd3,fsd=fsd1,host=hd3￼fwd=fwd4,fsd=fsd1,host=hd4￼fwd=fwd5,fsd=fsd1,host=hd5￼fwd=fwd6,fsd=fsd1,host=hd6￼rd=rd1,fwd=fwd*,fwdrate=max,format=restart,elapsed=600,interval=1￼  
**- 处理措施：****#**  
mkdir vdbench50407-src￼unzip vdbench50407.src.zip -d vdbench50407-src/￼cd vdbench50407-src/src/￼mkdir linux￼  
cd Jni/￼sed -i 's#vdb=$mine/vdbench504#vdb=/root/vdbench50407-src/src#g' make.linux￼sed -i 's#java=/net/sbm-240a.us.oracle.com/export/swat/swat_java/linux/jdk1.5.0_22/#java=/usr/lib/jvm/java-1.8.0-openjdk/#g' make.linux￼sed -i 's/-m32//g' make.linux ￼sed -i 's/-m64//g' make.linux ￼