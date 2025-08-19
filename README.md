# obsidian
my obsidian note

```
C:\Users\Administrator>fio -bs=4M -ioengine=windowsaio -iodepth=16 -numjobs=1 -direct=1 -rw=write -thread -time_based -runtime=300 -refill_buffers -norandommap -randrepeat=0 -group_reporting -name=fio-write-throughput -size=40G -filename="E\:targetfile"
fio-write-throughput: (g=0): rw=write, bs=(R) 4096KiB-4096KiB, (W) 4096KiB-4096KiB, (T) 4096KiB-4096KiB, ioengine=windowsaio, iodepth=16
fio-3.38
Starting 1 thread
Jobs: 1 (f=1): [W(1)][50.3%][w=39.8MiB/s][w=9 IOPS][eta 04m:58s]
fio-write-throughput: (groupid=0, jobs=1): err= 0: pid=7848: Wed Aug 13 19:46:32 2025
  write: IOPS=17, BW=68.6MiB/s (71.9MB/s)(20.1GiB/300645msec); 0 zone resets
    slat (usec): min=78, max=4325, avg=396.16, stdev=191.50
    clat (msec): min=45, max=1559, avg=931.22, stdev=112.91
     lat (msec): min=45, max=1559, avg=931.62, stdev=112.90
    clat percentiles (msec):
     |  1.00th=[  693],  5.00th=[  768], 10.00th=[  802], 20.00th=[  844],
     | 30.00th=[  877], 40.00th=[  902], 50.00th=[  927], 60.00th=[  953],
     | 70.00th=[  978], 80.00th=[ 1020], 90.00th=[ 1070], 95.00th=[ 1116],
     | 99.00th=[ 1217], 99.50th=[ 1250], 99.90th=[ 1334], 99.95th=[ 1519],
     | 99.99th=[ 1552]
   bw (  KiB/s): min=32833, max=122147, per=100.00%, avg=70315.34, stdev=12666.39, samples=596
   iops        : min=    8, max=   29, avg=16.79, stdev= 3.12, samples=596
  lat (msec)   : 50=0.08%, 250=0.08%, 500=0.12%, 750=2.50%, 1000=72.85%
  lat (msec)   : 2000=24.37%
  cpu          : usr=1.33%, sys=0.33%, ctx=0, majf=0, minf=0
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=6.0%, 16=93.9%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.1%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwts: total=0,5157,0,0 short=0,0,0,0 dropped=0,0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=16

Run status group 0 (all jobs):
  WRITE: bw=68.6MiB/s (71.9MB/s), 68.6MiB/s-68.6MiB/s (71.9MB/s-71.9MB/s), io=20.1GiB (21.6GB), run=300645-300645msec

C:\Users\Administrator>
C:\Users\Administrator>
C:\Users\Administrator>
C:\Users\Administrator>fio -bs=4k -ioengine=windowsaio -iodepth=32 -numjobs=1 -direct=1 -rw=randread -thread -time_based -runtime=300 -refill_buffers -norandommap -randrepeat=0 -group_reporting -name=fio-randread-iops -size=10G -filename="E\:targetfile"
fio-randread-iops: (g=0): rw=randread, bs=(R) 4096B-4096B, (W) 4096B-4096B, (T) 4096B-4096B, ioengine=windowsaio, iodepth=32
fio-3.38
Starting 1 thread
Jobs: 1 (f=1): [r(1)][5.8%][r=1865KiB/s][r=466 IOPS][eta 01h:20m:55s]
fio-randread-iops: (groupid=0, jobs=1): err= 0: pid=3684: Wed Aug 13 19:56:27 2025
  read: IOPS=509, BW=2040KiB/s (2089kB/s)(598MiB/300133msec)
    slat (usec): min=6, max=10351, avg=58.73, stdev=89.31
    clat (usec): min=2, max=568572, avg=62683.52, stdev=76345.72
     lat (usec): min=332, max=568662, avg=62742.25, stdev=76345.75
    clat percentiles (usec):
     |  1.00th=[   570],  5.00th=[  4080], 10.00th=[  5932], 20.00th=[  9241],
     | 30.00th=[ 13435], 40.00th=[ 19530], 50.00th=[ 29230], 60.00th=[ 43779],
     | 70.00th=[ 66323], 80.00th=[106431], 90.00th=[181404], 95.00th=[238027],
     | 99.00th=[325059], 99.50th=[350225], 99.90th=[392168], 99.95th=[413139],
     | 99.99th=[455082]
   bw (  KiB/s): min=  986, max= 2504, per=100.00%, avg=2040.25, stdev=159.71, samples=594
   iops        : min=  246, max=  626, avg=509.78, stdev=40.03, samples=594
  lat (usec)   : 4=0.01%, 20=0.01%, 100=0.01%, 250=0.01%, 500=0.82%
  lat (usec)   : 750=0.51%, 1000=0.09%
  lat (msec)   : 2=0.13%, 4=3.23%, 10=17.58%, 20=18.08%, 50=22.84%
  lat (msec)   : 100=15.47%, 250=17.03%, 500=4.21%, 750=0.01%
  cpu          : usr=0.33%, sys=2.33%, ctx=0, majf=0, minf=0
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=3.4%, 32=96.6%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.1%, 16=0.1%, 32=0.1%, 64=0.0%, >=64=0.0%
     issued rwts: total=153053,0,0,0 short=0,0,0,0 dropped=0,0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=32

Run status group 0 (all jobs):
   READ: bw=2040KiB/s (2089kB/s), 2040KiB/s-2040KiB/s (2089kB/s-2089kB/s), io=598MiB (627MB), run=300133-300133msec
```