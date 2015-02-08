---
title: 'Benchmarking disk IO on ext3 vs ext4 vs xfs with <code>fio</code>'
author: Evan Hoffman
excerpt: 3 filesystems, 1 server.
layout: post
permalink: /2011/07/22/benchmarking-disk-io-on-ext3-vs-ext4-with-fio/
image:
  - 
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
wp-ui-load:
  - 'a:1:{i:0;s:7:"spoiler";}'
dsq_thread_id:
  - 2949521972
categories:
  - Uncategorized
tags:
  - benchmark
  - database
  - devops
  - disk
  - fio
  - io
  - iops
  - linux
  - msa 70
  - msa70
  - p800
  - performance
  - work
  - xfs
---
With the old database phased out, I figured this was a good time to benchmark IO on it before either repurposing it or mothballing it. In <a href="http://www.evanhoffman.com/evan/?p=188" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=188', 'the past']);" >the past</a> I&#8217;ve used `dd` for elementary sequential read/write testing, but I recently found <a href="http://freshmeat.net/projects/fio/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://freshmeat.net/projects/fio/', 'fio']);" >fio</a> which is much more versatile. Apparently developed by FusionIO, fio lets you control concurrency, blocksize and many other parameters and reports lots more data, most notably <a href="http://en.wikipedia.org/wiki/IOPS" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://en.wikipedia.org/wiki/IOPS', 'IOPS']);" >IOPS</a>.  
<!--more-->

  
I created 4 fio config files, basically the same as the one below, for direct / buffered disk access and sequential / random writes. All have 8k blocksize since that&#8217;s what Postgres uses by default.

<pre>[root@link ~]# cat random-write-test.fio
[random-write]
rw=randwrite
size=5G
direct=1
directory=/msa70/fio/data/
numjobs=5
group_reporting
name=random-write-direct
bs=8k
runtime=10
</pre>

<pre>[root@link ~]# cat sequential-write-test-buf.fio
[sequential-write]
rw=write
size=5G
direct=0
directory=/msa70/fio/data/
numjobs=5
group_reporting
name=sequential-write-buffered
bs=8k
runtime=10

</pre>

First I ran fio against a freshly created ext3 volume on our HP MSA70 with 24x 10kRPM SAS disks in RAID 10. The mkfs command used was `mke2fs -j -m0 /dev/cciss/c0d0`. The controller card is a <a href="http://h18000.www1.hp.com/products/servers/proliantstorage/arraycontrollers/smartarrayp800/index.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://h18000.www1.hp.com/products/servers/proliantstorage/arraycontrollers/smartarrayp800/index.html', 'HP P800']);" >HP P800</a>. Here&#8217;s the output:

[wpspoiler name=&#8217;fio &#8211; ext3 full results&#8217;]

<pre>[root@link ~]# time fio random-write-test.fio random-write-test-buf.fio sequential-write-test.fio sequential-write-test-buf.fio
random-write-direct: (g=0): rw=randwrite, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
...
random-write-direct: (g=0): rw=randwrite, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
random-write-buf: (g=1): rw=randwrite, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
...
random-write-buf: (g=1): rw=randwrite, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
sequential-write-direct: (g=2): rw=write, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
...
sequential-write-direct: (g=2): rw=write, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
sequential-write-buffered: (g=3): rw=write, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
...
sequential-write-buffered: (g=3): rw=write, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
fio 1.57
Starting 20 processes
random-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-buf: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-buf: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-buf: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-buf: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-buf: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-buffered: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-buffered: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-buffered: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-buffered: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-buffered: Laying out IO file(s) (1 file(s) / 5120MB)
Jobs: 1 (f=1): [________________W___] [19.8% done] [0K/0K /s] [0 /0  iops] [eta 03m:14s]
random-write-direct: (groupid=0, jobs=5): err= 0: pid=2447
  write: io=596856KB, bw=56946KB/s, iops=7118 , runt= 10481msec
    clat (usec): min=67 , max=944937 , avg=645.32, stdev=4647.69
     lat (usec): min=67 , max=944937 , avg=645.46, stdev=4647.69
    bw (KB/s) : min=  144, max=47152, per=22.71%, avg=12933.03, stdev=4985.93
  cpu          : usr=0.26%, sys=1.48%, ctx=74614, majf=0, minf=134
  IO depths    : 1=100.0%, 2=0.0%, 4=0.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued r/w/d: total=0/74607/0, short=0/0/0
     lat (usec): 100=12.22%, 250=80.24%, 500=6.59%, 750=0.20%, 1000=0.16%
     lat (msec): 2=0.07%, 4=0.01%, 10=0.10%, 20=0.05%, 50=0.08%
     lat (msec): 100=0.10%, 250=0.14%, 500=0.04%, 750=0.01%, 1000=0.01%
random-write-buf: (groupid=1, jobs=5): err= 0: pid=2452
  write: io=6377.3MB, bw=472902KB/s, iops=59112 , runt= 13809msec
    clat (usec): min=8 , max=4490.8K, avg=77.54, stdev=3751.21
     lat (usec): min=8 , max=4490.8K, avg=77.66, stdev=3751.21
    bw (KB/s) : min=    7, max=438688, per=34.13%, avg=161414.56, stdev=63682.89
  cpu          : usr=2.27%, sys=24.01%, ctx=1596, majf=0, minf=134
  IO depths    : 1=100.0%, 2=0.0%, 4=0.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued r/w/d: total=0/816288/0, short=0/0/0
     lat (usec): 10=0.06%, 20=83.59%, 50=16.19%, 100=0.04%, 250=0.04%
     lat (usec): 500=0.03%, 750=0.01%, 1000=0.01%
     lat (msec): 2=0.01%, 4=0.01%, 10=0.01%, 20=0.03%, 50=0.01%
     lat (msec): 100=0.01%, 250=0.01%, 500=0.01%, 750=0.01%, 1000=0.01%
     lat (msec): 2000=0.01%, >=2000=0.01%
sequential-write-direct: (groupid=2, jobs=5): err= 0: pid=2457
  write: io=2832.0KB, bw=286105 B/s, iops=34 , runt= 10136msec
    clat (usec): min=75 , max=1506.6K, avg=157500.69, stdev=96237.55
     lat (usec): min=75 , max=1506.6K, avg=157500.81, stdev=96237.54
    bw (KB/s) : min=    6, max=  444, per=19.58%, avg=54.61, stdev=13.65
  cpu          : usr=0.00%, sys=0.00%, ctx=572, majf=0, minf=140
  IO depths    : 1=100.0%, 2=0.0%, 4=0.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued r/w/d: total=0/354/0, short=0/0/0
     lat (usec): 100=4.80%, 250=33.05%, 500=0.56%
     lat (msec): 50=1.13%, 100=18.36%, 250=25.99%, 500=9.04%, 750=4.24%
     lat (msec): 1000=1.41%, 2000=1.41%
sequential-write-buffered: (groupid=3, jobs=5): err= 0: pid=2462
  write: io=255624KB, bw=19933KB/s, iops=2491 , runt= 12824msec
    clat (usec): min=7 , max=9411.7K, avg=1443.28, stdev=33091.95
     lat (usec): min=7 , max=9411.7K, avg=1443.38, stdev=33091.95
    bw (KB/s) : min=   31, max=43687, per=29.58%, avg=5896.30, stdev=2696.81
  cpu          : usr=0.05%, sys=0.89%, ctx=600, majf=0, minf=136
  IO depths    : 1=100.0%, 2=0.0%, 4=0.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued r/w/d: total=0/31953/0, short=0/0/0
     lat (usec): 10=13.51%, 20=77.52%, 50=8.68%, 100=0.07%, 250=0.08%
     lat (usec): 500=0.03%, 750=0.01%
     lat (msec): 4=0.01%, 10=0.01%, 20=0.02%, 50=0.01%, 250=0.01%
     lat (msec): 750=0.02%, 2000=0.01%, >=2000=0.03%

Run status group 0 (all jobs):
  WRITE: io=596856KB, aggrb=56946KB/s, minb=58313KB/s, maxb=58313KB/s, mint=10481msec, maxt=10481msec

Run status group 1 (all jobs):
  WRITE: io=6377.3MB, aggrb=472902KB/s, minb=484251KB/s, maxb=484251KB/s, mint=13809msec, maxt=13809msec

Run status group 2 (all jobs):
  WRITE: io=2832KB, aggrb=279KB/s, minb=286KB/s, maxb=286KB/s, mint=10136msec, maxt=10136msec

Run status group 3 (all jobs):
  WRITE: io=255624KB, aggrb=19933KB/s, minb=20411KB/s, maxb=20411KB/s, mint=12824msec, maxt=12824msec

Disk stats (read/write):
  cciss!c0d0: ios=798/24542244, merge=0/937067, ticks=15866/140262853, in_queue=140448875, util=89.71%

real    18m35.082s
user    0m4.328s
sys     5m16.479s
[root@link ~]#

</pre>

[/wpspoiler]  
That&#8217;s a lot of data; the most interesting numbers are these:

<pre>random-write-direct: (groupid=0, jobs=5): err= 0: pid=2447
  write: io=596856KB, bw=56946KB/s, iops=7118 , runt= 10481msec
random-write-buf: (groupid=1, jobs=5): err= 0: pid=2452
  write: io=6377.3MB, bw=472902KB/s, iops=59112 , runt= 13809msec
sequential-write-direct: (groupid=2, jobs=5): err= 0: pid=2457
  write: io=2832.0KB, bw=286105 B/s, iops=34 , runt= 10136msec
sequential-write-buffered: (groupid=3, jobs=5): err= 0: pid=2462
  write: io=255624KB, bw=19933KB/s, iops=2491 , runt= 12824msec
</pre>

I then reformatted the RAIDed LUN with ext4 with `mkfs.ext4 -m0 /dev/cciss/c0d0` and ran the same tests again:  
[wpspoiler name=&#8217;fio &#8211; ext4 full results&#8217;]

<pre>[root@link ~]# time fio random-write-test.fio random-write-test-buf.fio sequential-write-test.fio sequential-write-test-buf.fio
random-write-direct: (g=0): rw=randwrite, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
...
random-write-direct: (g=0): rw=randwrite, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
random-write-buf: (g=1): rw=randwrite, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
...
random-write-buf: (g=1): rw=randwrite, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
sequential-write-direct: (g=2): rw=write, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
...
sequential-write-direct: (g=2): rw=write, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
sequential-write-buffered: (g=3): rw=write, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
...
sequential-write-buffered: (g=3): rw=write, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
fio 1.57
Starting 20 processes
random-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-buf: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-buf: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-buf: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-buf: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-buf: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-buffered: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-buffered: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-buffered: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-buffered: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-buffered: Laying out IO file(s) (1 file(s) / 5120MB)
Jobs: 5 (f=5): [_______________WWWWW] [21.5% done] [0K/126.1M /s] [0 /15.9K iops] [eta 02m:30s]
random-write-direct: (groupid=0, jobs=5): err= 0: pid=2379
  write: io=589392KB, bw=58933KB/s, iops=7366 , runt= 10001msec
    clat (usec): min=77 , max=2522.4K, avg=781.82, stdev=9768.99
     lat (usec): min=77 , max=2522.4K, avg=781.95, stdev=9768.99
    bw (KB/s) : min=  515, max=44176, per=22.05%, avg=12996.62, stdev=5332.75
  cpu          : usr=0.26%, sys=2.86%, ctx=73715, majf=0, minf=134
  IO depths    : 1=100.0%, 2=0.0%, 4=0.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued r/w/d: total=0/73674/0, short=0/0/0
     lat (usec): 100=2.54%, 250=92.13%, 500=4.76%, 750=0.14%, 1000=0.01%
     lat (msec): 2=0.01%, 4=0.01%, 10=0.07%, 20=0.04%, 50=0.07%
     lat (msec): 100=0.09%, 250=0.11%, 500=0.03%, 750=0.01%, 1000=0.01%
     lat (msec): 2000=0.01%, >=2000=0.01%
random-write-buf: (groupid=1, jobs=5): err= 0: pid=2384
  write: io=6707.1MB, bw=670727KB/s, iops=83840 , runt= 10241msec
    clat (usec): min=7 , max=1582.5K, avg=57.04, stdev=1910.33
     lat (usec): min=7 , max=1582.5K, avg=57.15, stdev=1910.33
    bw (KB/s) : min=   30, max=518784, per=25.08%, avg=168240.26, stdev=84472.16
  cpu          : usr=2.51%, sys=25.56%, ctx=35766, majf=0, minf=134
  IO depths    : 1=100.0%, 2=0.0%, 4=0.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued r/w/d: total=0/858614/0, short=0/0/0
     lat (usec): 10=2.14%, 20=91.72%, 50=5.11%, 100=0.31%, 250=0.15%
     lat (usec): 500=0.19%, 750=0.15%, 1000=0.09%
     lat (msec): 2=0.08%, 4=0.03%, 10=0.01%, 20=0.01%, 50=0.01%
     lat (msec): 100=0.01%, 250=0.01%, 500=0.01%, 750=0.01%, 1000=0.01%
     lat (msec): 2000=0.01%
sequential-write-direct: (groupid=2, jobs=5): err= 0: pid=2389
  write: io=839760KB, bw=83959KB/s, iops=10494 , runt= 10002msec
    clat (usec): min=70 , max=300244 , avg=473.09, stdev=3815.84
     lat (usec): min=71 , max=300244 , avg=473.22, stdev=3815.84
    bw (KB/s) : min= 8198, max=30528, per=20.27%, avg=17014.88, stdev=1677.78
  cpu          : usr=0.32%, sys=3.15%, ctx=104986, majf=0, minf=140
  IO depths    : 1=100.0%, 2=0.0%, 4=0.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued r/w/d: total=0/104970/0, short=0/0/0
     lat (usec): 100=73.60%, 250=26.18%, 500=0.01%, 750=0.01%
     lat (msec): 50=0.01%, 100=0.01%, 250=0.18%, 500=0.02%
sequential-write-buffered: (groupid=3, jobs=5): err= 0: pid=2394
  write: io=1730.7MB, bw=163683KB/s, iops=20460 , runt= 10827msec
    clat (usec): min=7 , max=7649.9K, avg=220.41, stdev=12535.19
     lat (usec): min=7 , max=7649.9K, avg=220.52, stdev=12535.19
    bw (KB/s) : min=   27, max=331055, per=35.92%, avg=58788.88, stdev=27863.44
  cpu          : usr=0.42%, sys=5.54%, ctx=4119, majf=0, minf=136
  IO depths    : 1=100.0%, 2=0.0%, 4=0.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued r/w/d: total=0/221525/0, short=0/0/0
     lat (usec): 10=50.94%, 20=47.34%, 50=1.45%, 100=0.18%, 250=0.05%
     lat (usec): 500=0.01%, 750=0.01%, 1000=0.01%
     lat (msec): 2=0.01%, 4=0.01%, 10=0.01%, 20=0.01%, 50=0.01%
     lat (msec): 100=0.01%, 250=0.01%, 500=0.01%, 750=0.01%, 1000=0.01%
     lat (msec): 2000=0.01%, >=2000=0.01%

Run status group 0 (all jobs):
  WRITE: io=589392KB, aggrb=58933KB/s, minb=60347KB/s, maxb=60347KB/s, mint=10001msec, maxt=10001msec

Run status group 1 (all jobs):
  WRITE: io=6707.1MB, aggrb=670726KB/s, minb=686824KB/s, maxb=686824KB/s, mint=10241msec, maxt=10241msec

Run status group 2 (all jobs):
  WRITE: io=839760KB, aggrb=83959KB/s, minb=85974KB/s, maxb=85974KB/s, mint=10002msec, maxt=10002msec

Run status group 3 (all jobs):
  WRITE: io=1730.7MB, aggrb=163683KB/s, minb=167611KB/s, maxb=167611KB/s, mint=10827msec, maxt=10827msec

Disk stats (read/write):
  cciss!c0d0: ios=52/276341, merge=0/553601, ticks=224/1731709, in_queue=1735311, util=94.47%

real    0m42.218s
user    0m1.812s
sys     0m19.028s
[root@link ~]#
</pre>

[/wpspoiler]  
Summary numbers:

<pre>random-write-direct: (groupid=0, jobs=5): err= 0: pid=2379
  write: io=589392KB, bw=58933KB/s, iops=7366 , runt= 10001msec
random-write-buf: (groupid=1, jobs=5): err= 0: pid=2384
  write: io=6707.1MB, bw=670727KB/s, iops=83840 , runt= 10241msec
sequential-write-direct: (groupid=2, jobs=5): err= 0: pid=2389
  write: io=839760KB, bw=83959KB/s, iops=10494 , runt= 10002msec
sequential-write-buffered: (groupid=3, jobs=5): err= 0: pid=2394
  write: io=1730.7MB, bw=163683KB/s, iops=20460 , runt= 10827msec
</pre>

This is just one run of each test, so not very scientific, but it does look pretty clear that ext4 outperforms ext3 (I actually did run the test several times but didn&#8217;t collect the results, they were pretty consistent though). What the test doesn&#8217;t show, but you can see from the `time` output is that each ext3 test took over 4 minutes to run while the ext4 tests each took about 10-12 seconds. In the ext3 tests, each &#8220;file1: Laying out IO file(s) (1 file(s) / 5120MB)&#8221; line took nearly a minute; in the ext4 tests they were essentially instant. 

This is nothing groundbreaking, I was just playing around with fio and figured I&#8217;d post the results in case anyone else was curious. I did notice some oddities though, like the fact that random buffered writes appear to be faster than even sequential buffered writes, on both ext3 and ext4 &#8211; on ext4, showing 670 MB/s random buffered writes and 83k IOPS. That doesn&#8217;t seem to make much sense, but I&#8217;ll leave the tea-leaf reading to someone else.

<ins datetime="2011-07-22T04:17:06+00:00">Addendum</ins>: I ran the test with XFS, just for fun. Overall, it was significantly faster than ext3, but not as fast as ext4, though sequential buffered writes were much faster than either ext3 or ext4.

[wpspoiler name=&#8217;fio &#8211; xfs full results&#8217;]

<pre>[root@link ~]# mkfs.xfs -f -b size=4k /dev/cciss/c0d0
meta-data=/dev/cciss/c0d0        isize=256    agcount=4, agsize=107506378 blks
         =                       sectsz=512   attr=2
data     =                       bsize=4096   blocks=430025510, imaxpct=5
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0
log      =internal log           bsize=4096   blocks=209973, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
[root@link ~]# mount -v -t xfs /dev/cciss/c0d0 /msa70/
/dev/cciss/c0d0 on /msa70 type xfs (rw)
[root@link ~]# mkdir -p /msa70/fio/data
(reverse-i-search)`f': mkdir -p /msa70/^Co/data
[root@link ~]# time fio random-write-test.fio random-write-test-buf.fio sequential-write-test.fio sequential-write-test-buf.fio
random-write-direct: (g=0): rw=randwrite, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
...
random-write-direct: (g=0): rw=randwrite, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
random-write-buf: (g=1): rw=randwrite, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
...
random-write-buf: (g=1): rw=randwrite, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
sequential-write-direct: (g=2): rw=write, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
...
sequential-write-direct: (g=2): rw=write, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
sequential-write-buffered: (g=3): rw=write, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
...
sequential-write-buffered: (g=3): rw=write, bs=8K-8K/8K-8K, ioengine=sync, iodepth=1
fio 1.57
Starting 20 processes
random-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-buf: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-buf: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-buf: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-buf: Laying out IO file(s) (1 file(s) / 5120MB)
random-write-buf: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-direct: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-buffered: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-buffered: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-buffered: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-buffered: Laying out IO file(s) (1 file(s) / 5120MB)
sequential-write-buffered: Laying out IO file(s) (1 file(s) / 5120MB)
Jobs: 5 (f=5): [_______________WWWWW] [91.3% done] [0K/143.4M /s] [0 /17.1K iops] [eta 00m:26s]
random-write-direct: (groupid=0, jobs=5): err= 0: pid=2662
  write: io=416920KB, bw=41688KB/s, iops=5210 , runt= 10001msec
    clat (usec): min=83 , max=1592.6K, avg=1013.62, stdev=10137.19
     lat (usec): min=83 , max=1592.6K, avg=1013.77, stdev=10137.19
    bw (KB/s) : min=    7, max=23184, per=22.46%, avg=9363.32, stdev=2852.49
  cpu          : usr=0.31%, sys=1.61%, ctx=106802, majf=0, minf=134
  IO depths    : 1=100.0%, 2=0.0%, 4=0.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued r/w/d: total=0/52115/0, short=0/0/0
     lat (usec): 100=0.06%, 250=4.55%, 500=87.85%, 750=4.23%, 1000=0.70%
     lat (msec): 2=2.01%, 4=0.13%, 10=0.24%, 20=0.07%, 50=0.01%
     lat (msec): 100=0.03%, 250=0.06%, 500=0.03%, 750=0.02%, 2000=0.02%
random-write-buf: (groupid=1, jobs=5): err= 0: pid=2667
  write: io=6557.9MB, bw=27876KB/s, iops=3484 , runt=240892msec
    clat (usec): min=7 , max=237984K, avg=1631.10, stdev=257906.92
     lat (usec): min=7 , max=237984K, avg=1631.22, stdev=257906.92
    bw (KB/s) : min=    0, max=628832, per=1222.74%, avg=340851.04, stdev=70996.06
  cpu          : usr=0.13%, sys=1.33%, ctx=24945, majf=0, minf=134
  IO depths    : 1=100.0%, 2=0.0%, 4=0.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued r/w/d: total=0/839397/0, short=0/0/0
     lat (usec): 10=18.02%, 20=79.39%, 50=2.19%, 100=0.18%, 250=0.07%
     lat (usec): 500=0.03%, 750=0.01%, 1000=0.04%
     lat (msec): 2=0.02%, 4=0.01%, 10=0.02%, 20=0.01%, 50=0.01%
     lat (msec): 100=0.01%, 250=0.01%, 500=0.01%, 750=0.01%, 2000=0.01%
     lat (msec): >=2000=0.01%
sequential-write-direct: (groupid=2, jobs=5): err= 0: pid=2677
  write: io=519056KB, bw=51895KB/s, iops=6486 , runt= 10002msec
    clat (usec): min=81 , max=242925 , avg=825.67, stdev=3980.13
     lat (usec): min=81 , max=242925 , avg=825.81, stdev=3980.13
    bw (KB/s) : min=   59, max=20029, per=19.03%, avg=9873.41, stdev=2128.50
  cpu          : usr=0.23%, sys=1.43%, ctx=129921, majf=0, minf=140
  IO depths    : 1=100.0%, 2=0.0%, 4=0.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued r/w/d: total=0/64882/0, short=0/0/0
     lat (usec): 100=60.60%, 250=38.63%, 500=0.01%
     lat (msec): 10=0.10%, 50=0.11%, 100=0.23%, 250=0.32%
sequential-write-buffered: (groupid=3, jobs=5): err= 0: pid=2682
  write: io=7688.1MB, bw=770776KB/s, iops=96347 , runt= 10215msec
    clat (usec): min=6 , max=1727.2K, avg=53.67, stdev=1633.96
     lat (usec): min=6 , max=1727.2K, avg=53.78, stdev=1633.96
    bw (KB/s) : min= 5340, max=704608, per=21.28%, avg=164049.10, stdev=87065.00
  cpu          : usr=2.15%, sys=19.54%, ctx=16284, majf=0, minf=136
  IO depths    : 1=100.0%, 2=0.0%, 4=0.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued r/w/d: total=0/984185/0, short=0/0/0
     lat (usec): 10=41.97%, 20=56.42%, 50=1.10%, 100=0.23%, 250=0.10%
     lat (usec): 500=0.05%, 750=0.02%, 1000=0.03%
     lat (msec): 2=0.01%, 4=0.01%, 10=0.02%, 20=0.01%, 50=0.01%
     lat (msec): 100=0.01%, 250=0.01%, 500=0.01%, 750=0.01%, 2000=0.01%

Run status group 0 (all jobs):
  WRITE: io=416920KB, aggrb=41687KB/s, minb=42688KB/s, maxb=42688KB/s, mint=10001msec, maxt=10001msec

Run status group 1 (all jobs):
  WRITE: io=6557.9MB, aggrb=27876KB/s, minb=28545KB/s, maxb=28545KB/s, mint=240892msec, maxt=240892msec

Run status group 2 (all jobs):
  WRITE: io=519056KB, aggrb=51895KB/s, minb=53140KB/s, maxb=53140KB/s, mint=10002msec, maxt=10002msec

Run status group 3 (all jobs):
  WRITE: io=7688.1MB, aggrb=770776KB/s, minb=789274KB/s, maxb=789274KB/s, mint=10215msec, maxt=10215msec

Disk stats (read/write):
  cciss!c0d0: ios=8/836060, merge=0/549215, ticks=11/36458428, in_queue=36468678, util=98.88%

real    4m31.948s
user    0m2.692s
sys     0m24.351s
[root@link ~]#
</pre>

[/wpspoiler]

XFS abridged:

<pre>random-write-direct: (groupid=0, jobs=5): err= 0: pid=2662
  write: io=416920KB, bw=41688KB/s, iops=5210 , runt= 10001msec
random-write-buf: (groupid=1, jobs=5): err= 0: pid=2667
  write: io=6557.9MB, bw=27876KB/s, iops=3484 , runt=240892msec
sequential-write-direct: (groupid=2, jobs=5): err= 0: pid=2677
  write: io=519056KB, bw=51895KB/s, iops=6486 , runt= 10002msec
sequential-write-buffered: (groupid=3, jobs=5): err= 0: pid=2682
  write: io=7688.1MB, bw=770776KB/s, iops=96347 , runt= 10215msec


</pre>

IOPS graphed:

<ins datetime="2011-07-28T20:08:02+00:00"><img src="https://spreadsheets.google.com/spreadsheet/oimg?key=0Arz9JIGL5KjodGplTjZIZGdnV1o3NHAwTER3ZmJEYnc&#038;oid=2&#038;zx=3w96k79xtmxm" /></p> 


  <p>
    Bandwidth:
  </p>



  <p>
    <img src="https://spreadsheets.google.com/spreadsheet/oimg?key=0Arz9JIGL5KjodGplTjZIZGdnV1o3NHAwTER3ZmJEYnc&#038;oid=4&#038;zx=fz9y6uzgniox" /><br /> </ins>
  </p>