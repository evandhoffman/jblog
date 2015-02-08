---
title: Macbook Pro locks up with SSD installed.
author: Evan Hoffman
layout: post
permalink: /2011/08/23/macbook-pro-locks-up-with-ssd-installed/
dsq_thread_id:
  - 2949517143
categories:
  - Uncategorized
tags:
  - beach ball
  - computers
  - disk
  - mac
  - macbook
  - MC118LL/A
  - pro
  - slow
  - speed
  - spinning
  - ssd
  - work
---
A few weeks ago I switched from my trusty old <a href="http://www.newegg.com/Product/Product.aspx?Item=N82E16834147346" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.newegg.com/Product/Product.aspx?Item=N82E16834147346', 'HP nc8430']);" >HP nc8430</a> to a <a href="http://www.amazon.com/gp/product/B002C744K6/ref=as_li_ss_tl?ie=UTF8&#038;tag=evanhoffmasho-20&#038;linkCode=as2&#038;camp=217145&#038;creative=399369&#038;creativeASIN=B002C744K6" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.amazon.com/gp/product/B002C744K6/ref=as_li_ss_tl?ie=UTF8&tag=evanhoffmasho-20&linkCode=as2&camp=217145&creative=399369&creativeASIN=B002C744K6', 'Macbook Pro']);" >Macbook Pro</a> (MC118LL/A) that was left spare when another employee left. I mostly enjoyed using Linux but I was tired of dealing with weird quirks like having X lock up, essentially forcing me to do a hard reboot. 

To transition, I copied my documents from Linux to Mac, then turned off the Linux laptop. Surprisingly I found I didn&#8217;t need to turn Linux back on at all.  
<!--more-->

  
Last week, I decided to put the final nail in Linux&#8217;s coffin by taking the SSD (<a href="http://www.amazon.com/gp/product/B002CI41US/ref=as_li_ss_tl?ie=UTF8&#038;tag=evanhoffmasho-20&#038;linkCode=as2&#038;camp=217145&#038;creative=399369&#038;creativeASIN=B002CI41US" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.amazon.com/gp/product/B002CI41US/ref=as_li_ss_tl?ie=UTF8&tag=evanhoffmasho-20&linkCode=as2&camp=217145&creative=399369&creativeASIN=B002CI41US', 'Corsair CMFSSD-128GBG2D']);" >Corsair CMFSSD-128GBG2D</a>)out of it and putting it in my Macbook. The Macbook was pretty fast (Core 2 Duo @ 2.5 GHz) but some things were noticeably slower on its 7200RPM disk than on Linux with an SSD, especially running Windows VMs.

I booted Linux to Knoppix and zeroed out the disk, then removed it. I backed my Mac up with Time Machine, shut it down, then undid the ~12 tiny screws, removed the bottom plate of the Macbook and popped the SSD in. I booted from the Mac DVD, restored from Time Machine and went home (it took ~4 hours to restore).

When I got in the next day, the restore was complete, though I had to click &#8220;Restart&#8221; to finish, which was annoying. Everything worked fine, and I was pretty impressed. The machine was kind of sluggish due to Spotlight indexing but once that was done I was pretty amazed at the transformation. Every app opened in under 1 second. Windows VMs were super snappy. Things were going well. 

But then I started noticing periods of extended hanging. In the middle of some task, I&#8217;d get the **beachball** and the whole computer would become unresponsive (cursor would spin &#038; move around but I couldn&#8217;t click anything). This would last about 30-60 seconds. I assumed it was some behind-the-scenes optimization, or some residual spotlight indexing. 

Unfortunately, it hasn&#8217;t gone away. Earlier today I copied a 3 GB zip file from our file server to my laptop and it beachballed me on and off (about 60-70% of the time) for about 15 minutes as it copied. What&#8217;s odd is that the transfer speeds were pretty good, it appeared to be my computer itself that was bottlenecking it. After the download completed, I attempted to unzip it and was beachballed again. I checked Activity Monitor and it was peaking at 30 MB/s, but had extended periods of zeroes. I ran iostat and got basically the same information:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="line_numbers">
        <pre>1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
</pre>
      </td>
      
      <td class="code">
        <pre class="log" style="font-family:monospace;">EvanMBP:~ root# iostat -Kw 3
          disk0           disk1       cpu     load average
    KB/t tps  MB/s     KB/t tps  MB/s  us sy id   1m   5m   15m
    9.33   3  0.03     0.00   0  0.00   9  4 87  0.25 0.28 0.32
    0.00   0  0.00     0.00   0  0.00  13  5 82  0.23 0.27 0.32
   20.00   0  0.01     0.00   0  0.00  15  5 80  0.23 0.27 0.32
   36.00   1  0.02     0.00   0  0.00  16  6 77  0.21 0.27 0.32
    0.00   0  0.00     0.00   0  0.00  12  5 83  0.21 0.27 0.32
   20.02  60  1.18     0.00   0  0.00  17 10 74  0.19 0.26 0.32
   24.60 363  8.72     0.00   0  0.00  18 13 69  0.17 0.26 0.31
   25.40 307  7.60     0.00   0  0.00  14 11 75  0.17 0.26 0.31
   21.95 426  9.14     0.00   0  0.00  15 12 73  0.32 0.29 0.32
   82.50 352 28.35     0.00   0  0.00  17 11 73  0.32 0.29 0.32
  809.70  84 66.41     0.00   0  0.00  22  9 69  0.29 0.28 0.32
    0.00   0  0.00     0.00   0  0.00   9  5 86  0.27 0.28 0.32
    0.00   0  0.00     8.89  11  0.09   9  5 86  0.27 0.28 0.32
    0.00   0  0.00     0.00   0  0.00   9  5 87  0.33 0.29 0.32
    0.00   0  0.00     0.00   0  0.00  12  7 81  0.33 0.29 0.32
    0.00   0  0.00     0.00   0  0.00  14  8 78  0.38 0.30 0.33
    0.00   0  0.00     0.00   0  0.00  11  6 83  0.35 0.29 0.33
    0.00   0  0.00     0.00   0  0.00  10  6 84  0.35 0.29 0.33
    0.00   0  0.00     0.00   0  0.00  10  5 84  0.32 0.29 0.32
    0.00   0  0.00     0.00   0  0.00  11  6 84  0.32 0.29 0.32
          disk0           disk1       cpu     load average
    KB/t tps  MB/s     KB/t tps  MB/s  us sy id   1m   5m   15m
    0.00   0  0.00     0.00   0  0.00  10  5 84  0.30 0.28 0.32
    0.00   0  0.00     0.00   0  0.00  11  6 84  0.27 0.28 0.32
    0.00   0  0.00     0.00   0  0.00  11  5 84  0.27 0.28 0.32
    0.00   0  0.00     0.00   0  0.00   9  5 85  0.25 0.27 0.32
    0.00   0  0.00     0.00   0  0.00  10  5 85  0.25 0.27 0.32
    0.00   0  0.00     0.00   0  0.00  10  5 85  0.31 0.29 0.32
    0.00   0  0.00     0.00   0  0.00  10  5 85  0.45 0.31 0.33
    0.00   0  0.00     0.00   0  0.00  10  5 85  0.45 0.31 0.33
    0.00   0  0.00     0.00   0  0.00  10  5 85  0.41 0.31 0.33
    0.00   0  0.00     0.00   0  0.00  10  5 85  0.41 0.31 0.33
  384.49  10  3.85    10.00   0  0.00  12  6 82  0.32 0.29 0.32
  291.73 126 35.78     0.00   0  0.00  23 13 64  0.38 0.31 0.33
  236.65 338 78.20     0.00   0  0.00  30 17 53  0.34 0.30 0.33
  397.61  21  8.02     0.00   0  0.00  15  8 77  0.34 0.30 0.33
    0.00   0  0.00     0.00   0  0.00  11  6 83  0.32 0.30 0.32
    0.00   0  0.00     0.00   0  0.00  12  7 81  0.32 0.30 0.32
    0.00   0  0.00     0.00   0  0.00  12  6 82  0.29 0.29 0.32
    0.00   0  0.00     0.00   0  0.00  11  6 83  0.35 0.30 0.33
    0.00   0  0.00     0.00   0  0.00  13  6 80  0.35 0.30 0.33
    0.00   0  0.00     0.00   0  0.00  11  6 83  0.32 0.30 0.32
          disk0           disk1       cpu     load average
    KB/t tps  MB/s     KB/t tps  MB/s  us sy id   1m   5m   15m
    0.00   0  0.00     0.00   0  0.00  13  8 78  0.32 0.30 0.32
    0.00   0  0.00     0.00   0  0.00  11  7 82  0.29 0.29 0.32
    0.00   0  0.00     0.00   0  0.00  10  5 86  0.35 0.30 0.33
  148.94 124 18.03     0.00   0  0.00  18  8 73  0.35 0.30 0.33
  267.73 121 31.72     0.00   0  0.00  17  8 76  0.32 0.30 0.32
  355.54 162 56.35     0.00   0  0.00  22  8 69  0.32 0.30 0.32
  738.07  38 27.38     0.00   0  0.00  16  6 78  0.30 0.29 0.32
  512.42  67 33.52     0.00   0  0.00  20  7 73  0.27 0.29 0.32
  835.74  61 50.05     0.00   0  0.00  19  7 73  0.27 0.29 0.32
  536.83  69 36.17     0.00   0  0.00  17  6 76  0.49 0.33 0.34
  543.89  83 43.90     0.00   0  0.00  20  8 72  0.49 0.33 0.34
  720.70  59 41.74     0.00   0  0.00  18  7 76  0.45 0.33 0.33
  541.23 124 65.70     0.00   0  0.00  22  9 70  0.41 0.32 0.33
  260.54 210 53.37     0.00   0  0.00  22  9 70  0.41 0.32 0.33
  806.93  73 57.78     0.00   0  0.00  20  8 72  0.46 0.33 0.34
  874.98  43 37.02     0.00   0  0.00  13  7 80  0.46 0.33 0.34
    0.00   0  0.00     0.00   0  0.00  11  4 85  0.42 0.33 0.33
    0.00   0  0.00     0.00   0  0.00   8  4 88  0.39 0.32 0.33
    0.00   0  0.00     0.00   0  0.00   9  4 87  0.39 0.32 0.33
    0.00   0  0.00     0.00   0  0.00   8  4 88  0.44 0.33 0.33
          disk0           disk1       cpu     load average
    KB/t tps  MB/s     KB/t tps  MB/s  us sy id   1m   5m   15m
    0.00   0  0.00     0.00   0  0.00  11  5 84  0.44 0.33 0.33
    0.00   0  0.00     0.00   0  0.00  13  6 81  0.48 0.34 0.34
    0.00   0  0.00     0.00   0  0.00  10  4 86  0.44 0.34 0.34
    0.00   0  0.00     0.00   0  0.00   8  4 88  0.44 0.34 0.34
    0.00   0  0.00     0.00   0  0.00   8  4 88  0.41 0.33 0.33
    0.00   0  0.00     0.00   0  0.00   8  4 88  0.41 0.33 0.33
    0.00   0  0.00     0.00   0  0.00   8  4 87  0.38 0.32 0.33
    0.00   0  0.00     0.00   0  0.00   7  4 89  0.35 0.32 0.33
    0.00   0  0.00     0.00   0  0.00   8  4 87  0.35 0.32 0.33
    0.00   0  0.00     0.00   0  0.00   7  3 89  0.32 0.31 0.33
    0.00   0  0.00     0.00   0  0.00   8  4 88  0.32 0.31 0.33
    0.00   0  0.00     0.00   0  0.00   7  3 90  0.29 0.31 0.33
  347.11  92 31.29     0.00   0  0.00  20  8 72  0.43 0.34 0.33
   49.98 656 32.03     0.00   0  0.00  32 10 59  0.43 0.34 0.33
  113.45 351 38.90     0.00   0  0.00  40 15 45  0.47 0.35 0.34
  819.41  34 27.20     0.00   0  0.00  26 12 63  0.47 0.35 0.34
  686.99  50 33.76     0.00   0  0.00  18  7 76  0.52 0.36 0.34
  878.17  23 20.01     0.00   0  0.00  20  8 72  0.47 0.35 0.34
    0.00   0  0.00     0.00   0  0.00   8  4 87  0.47 0.35 0.34
    0.00   0  0.00     0.00   0  0.00   8  4 88  0.44 0.34 0.34</pre>
      </td>
    </tr>
  </table>
</div>

You can see in the MB/s column, lots of &#8220;0.00&#8221; followed by some bursts of ~30 MB/s. The zeros didn&#8217;t actually print at the time, but flooded the screen in bursts when the bottleneck cleared up. It seems to me like it might be some problem with queueing or caching, or maybe the SATA controller on this Mac just isn&#8217;t up to the task of SSDs. I&#8217;m not sure, but at this point I&#8217;m afraid I might have to go back to the 7200 RPM Seagate that came with the Mac. 30-second hangups are far more annoying than having lots of things be slower. Kind of a strange amortization, if you think about it. Anyway, I&#8217;ll keep looking into it, now that I know how to reproduce the problem (unzip a huge file).

<ins datetime="2011-08-23T20:19:03+00:00">Updated</ins>: A quick Google search for &#8220;beach ball mac SSD&#8221; turned up <a href="http://crucial.lithium.com/t5/Solid-State-Drives-SSD/MacBook-Pro-Spinning-Beach-Ball-Help/td-p/42328" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://crucial.lithium.com/t5/Solid-State-Drives-SSD/MacBook-Pro-Spinning-Beach-Ball-Help/td-p/42328', 'this thread']);" >this thread</a> which seems to be about this same problem, with a different model SSD. Also referenced in <a href="https://discussions.apple.com/thread/3110516?start=0&#038;tstart=0" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://discussions.apple.com/thread/3110516?start=0&tstart=0', 'this thread']);" >this thread</a> on Apple.com. It feels like the problem may be due to an &#8220;old&#8221; SSD.

<ins datetime="2011-08-24T13:04:41+00:00">Updated again</ins>: Here&#8217;s someone having the problem with a Corsair 128 GB SSD: <a href="http://forum.corsair.com/v3/showthread.php?t=91061" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://forum.corsair.com/v3/showthread.php?t=91061', 'http://forum.corsair.com/v3/showthread.php?t=91061']);" >http://forum.corsair.com/v3/showthread.php?t=91061</a>.

<ins datetime="2011-08-24T16:48:08+00:00">Updated again</ins>: According to <a href="http://forum.crucial.com/t5/Solid-State-Drives-SSD/M4-SSD-with-8GB-DDR3-PC3-8500-on-MBP-5-5-13-2-53-GHz-Mid-2009/m-p/58702/highlight/true#M18141" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://forum.crucial.com/t5/Solid-State-Drives-SSD/M4-SSD-with-8GB-DDR3-PC3-8500-on-MBP-5-5-13-2-53-GHz-Mid-2009/m-p/58702/highlight/true#M18141', 'this post']);" >this post</a>, this appears to be a problem with the SATA controller in the 2009 Macbooks. Bah.