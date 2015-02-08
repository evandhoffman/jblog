---
title: Why is my laptop so freaking hot?
author: Evan Hoffman
layout: post
permalink: /2010/08/26/why-is-my-laptop-so-freaking-hot/
dsq_thread_id:
  - 3196495992
categories:
  - Uncategorized
tags:
  - fc12
  - fedora
  - kernel
  - laptop
  - linux
  - nc8430
  - overheat
  - 'RB554UT#ABA'
  - update
  - yum
---
HP Compaq nc8430, Core 2 Duo T7200 2.0GHz. Fedora Core 12 x86\_64, kernel 2.6.32.19-163.fc12.x86\_64 (just updated via yum).

At idle, with nothing running in the foreground, the CPU is around 70-72° Celsius, and the other sensors are 80°+. Why is this? It seems like every time I run yum update the machine gets hotter. I wonder if there&#8217;s any way to figure out what&#8217;s causing this. In any case, it&#8217;s pretty annoying. It&#8217;s uncomfortable putting the thing on my lap for any length of time.

<a href="http://evanhoffman.com/evan/wp-content/uploads/2010/08/why-so-hot.png" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://evanhoffman.com/evan/wp-content/uploads/2010/08/why-so-hot.png', '']);" ><img src="http://www.evanhoffman.com/evan/wp-content/uploads/2010/08/why-so-hot-286x300.png" alt="" title="why so hot" width="286" height="300" class="aligncenter size-medium wp-image-570" /></a>

Notice the temperatures at the top. The first two are Core0 and Core1, the other 2 are other sensors. The CPU is running at 1.0 GHz due to SpeedStep and the temp is still 70°C.

Edit: I forgot I&#8217;ve been logging temperature via sensord. I just turned the computer on after having it off all night and within 10 minutes the CPU is back up to > 60°C.

<a href="http://evanhoffman.com/evan/wp-content/uploads/2010/08/hot.png" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://evanhoffman.com/evan/wp-content/uploads/2010/08/hot.png', '']);" ><img src="http://evanhoffman.com/evan/wp-content/uploads/2010/08/hot.png" alt="" title="hot" width="640" height="400" class="aligncenter size-full wp-image-575" /></a>

<pre>Aug 26 00:01:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 00:06:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 00:11:34 ehoffman sensord:   Core 0: 66.0 C
Aug 26 00:16:34 ehoffman sensord:   Core 0: 66.0 C
Aug 26 00:21:34 ehoffman sensord:   Core 0: 66.0 C
Aug 26 00:26:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 00:31:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 00:36:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 00:41:34 ehoffman sensord:   Core 0: 66.0 C
Aug 26 00:46:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 00:51:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 00:56:34 ehoffman sensord:   Core 0: 66.0 C
Aug 26 01:01:34 ehoffman sensord:   Core 0: 66.0 C
Aug 26 01:06:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 01:11:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 01:16:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 01:21:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 01:26:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 01:31:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 01:36:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 01:41:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 01:46:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 01:51:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 01:56:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 02:01:34 ehoffman sensord:   Core 0: 64.0 C
Aug 26 02:06:34 ehoffman sensord:   Core 0: 64.0 C
Aug 26 02:11:34 ehoffman sensord:   Core 0: 64.0 C
Aug 26 02:16:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 02:21:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 02:26:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 02:31:34 ehoffman sensord:   Core 0: 65.0 C
Aug 26 02:36:35 ehoffman sensord:   Core 0: 64.0 C
Aug 26 02:41:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 02:46:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 02:51:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 02:56:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 03:01:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 03:06:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 03:11:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 03:16:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 03:21:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 03:26:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 03:31:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 03:36:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 03:41:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 03:46:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 03:51:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 03:56:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 04:01:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 04:06:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 04:11:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 04:16:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 04:21:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 04:26:35 ehoffman sensord:   Core 0: 66.0 C
Aug 26 04:31:35 ehoffman sensord:   Core 0: 66.0 C
Aug 26 04:36:35 ehoffman sensord:   Core 0: 66.0 C
Aug 26 04:41:35 ehoffman sensord:   Core 0: 66.0 C
Aug 26 04:46:35 ehoffman sensord:   Core 0: 66.0 C
Aug 26 04:51:35 ehoffman sensord:   Core 0: 66.0 C
Aug 26 04:56:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 05:01:35 ehoffman sensord:   Core 0: 66.0 C
Aug 26 05:06:35 ehoffman sensord:   Core 0: 66.0 C
Aug 26 05:11:35 ehoffman sensord:   Core 0: 66.0 C
Aug 26 05:16:35 ehoffman sensord:   Core 0: 65.0 C
Aug 26 05:21:36 ehoffman sensord:   Core 0: 65.0 C
Aug 26 05:26:36 ehoffman sensord:   Core 0: 65.0 C
Aug 26 05:31:36 ehoffman sensord:   Core 0: 65.0 C
Aug 26 05:36:36 ehoffman sensord:   Core 0: 65.0 C
Aug 26 05:41:36 ehoffman sensord:   Core 0: 65.0 C
Aug 26 05:46:36 ehoffman sensord:   Core 0: 66.0 C
Aug 26 05:51:36 ehoffman sensord:   Core 0: 65.0 C
Aug 26 05:56:36 ehoffman sensord:   Core 0: 65.0 C
Aug 26 06:01:36 ehoffman sensord:   Core 0: 65.0 C
Aug 26 06:06:36 ehoffman sensord:   Core 0: 65.0 C
Aug 26 06:11:36 ehoffman sensord:   Core 0: 65.0 C
Aug 26 06:16:36 ehoffman sensord:   Core 0: 66.0 C
Aug 26 06:21:36 ehoffman sensord:   Core 0: 66.0 C
Aug 26 06:26:36 ehoffman sensord:   Core 0: 65.0 C
Aug 26 06:31:36 ehoffman sensord:   Core 0: 65.0 C
Aug 26 06:36:36 ehoffman sensord:   Core 0: 65.0 C
Aug 26 06:41:36 ehoffman sensord:   Core 0: 65.0 C
Aug 26 06:46:36 ehoffman sensord:   Core 0: 65.0 C
Aug 26 06:51:36 ehoffman sensord:   Core 0: 65.0 C
Aug 26 06:56:36 ehoffman sensord:   Core 0: 65.0 C
Aug 26 07:01:36 ehoffman sensord:   Core 0: 65.0 C
Aug 26 07:06:36 ehoffman sensord:   Core 0: 65.0 C
Aug 26 07:11:36 ehoffman sensord:   Core 0: 65.0 C
Aug 26 07:16:36 ehoffman sensord:   Core 0: 64.0 C
Aug 26 07:21:36 ehoffman sensord:   Core 0: 64.0 C
Aug 26 07:26:36 ehoffman sensord:   Core 0: 64.0 C
Aug 26 07:31:36 ehoffman sensord:   Core 0: 63.0 C
Aug 26 07:36:36 ehoffman sensord:   Core 0: 63.0 C
Aug 26 07:41:36 ehoffman sensord:   Core 0: 63.0 C
Aug 26 07:46:36 ehoffman sensord:   Core 0: 64.0 C
Aug 26 07:51:36 ehoffman sensord:   Core 0: 64.0 C
Aug 26 07:56:36 ehoffman sensord:   Core 0: 64.0 C
Aug 26 08:01:36 ehoffman sensord:   Core 0: 64.0 C
Aug 26 08:06:37 ehoffman sensord:   Core 0: 64.0 C
Aug 26 08:11:37 ehoffman sensord:   Core 0: 64.0 C
Aug 26 08:16:37 ehoffman sensord:   Core 0: 64.0 C
Aug 26 08:21:37 ehoffman sensord:   Core 0: 65.0 C
Aug 26 08:26:37 ehoffman sensord:   Core 0: 65.0 C
Aug 26 08:31:37 ehoffman sensord:   Core 0: 65.0 C
Aug 26 08:36:37 ehoffman sensord:   Core 0: 65.0 C
Aug 26 08:41:37 ehoffman sensord:   Core 0: 65.0 C
Aug 26 08:46:37 ehoffman sensord:   Core 0: 65.0 C
Aug 26 08:51:37 ehoffman sensord:   Core 0: 65.0 C
Aug 26 08:56:37 ehoffman sensord:   Core 0: 65.0 C
Aug 26 09:01:37 ehoffman sensord:   Core 0: 65.0 C
Aug 26 09:06:37 ehoffman sensord:   Core 0: 66.0 C
Aug 26 09:11:37 ehoffman sensord:   Core 0: 66.0 C
Aug 26 09:16:37 ehoffman sensord:   Core 0: 78.0 C
Aug 26 09:21:37 ehoffman sensord:   Core 0: 83.0 C
Aug 26 09:26:37 ehoffman sensord:   Core 0: 70.0 C
Aug 26 09:31:37 ehoffman sensord:   Core 0: 72.0 C
Aug 26 09:36:37 ehoffman sensord:   Core 0: 74.0 C
Aug 26 09:41:37 ehoffman sensord:   Core 0: 72.0 C
Aug 26 09:46:38 ehoffman sensord:   Core 0: 71.0 C
Aug 26 09:51:38 ehoffman sensord:   Core 0: 71.0 C
Aug 26 09:56:38 ehoffman sensord:   Core 0: 72.0 C
Aug 26 10:01:38 ehoffman sensord:   Core 0: 73.0 C
Aug 26 10:06:38 ehoffman sensord:   Core 0: 71.0 C
Aug 26 10:11:38 ehoffman sensord:   Core 0: 78.0 C
Aug 26 10:16:38 ehoffman sensord:   Core 0: 71.0 C
Aug 26 10:21:38 ehoffman sensord:   Core 0: 72.0 C
Aug 26 10:26:38 ehoffman sensord:   Core 0: 72.0 C
Aug 26 10:31:38 ehoffman sensord:   Core 0: 73.0 C
Aug 26 10:36:38 ehoffman sensord:   Core 0: 74.0 C
Aug 26 10:41:38 ehoffman sensord:   Core 0: 76.0 C
Aug 26 10:46:38 ehoffman sensord:   Core 0: 73.0 C
Aug 26 10:51:38 ehoffman sensord:   Core 0: 73.0 C
Aug 26 10:56:38 ehoffman sensord:   Core 0: 74.0 C
Aug 26 11:01:38 ehoffman sensord:   Core 0: 74.0 C
Aug 26 11:06:38 ehoffman sensord:   Core 0: 74.0 C
Aug 26 11:11:38 ehoffman sensord:   Core 0: 75.0 C
Aug 26 11:16:38 ehoffman sensord:   Core 0: 74.0 C
Aug 26 11:21:38 ehoffman sensord:   Core 0: 75.0 C
Aug 26 11:26:38 ehoffman sensord:   Core 0: 75.0 C
Aug 26 11:31:38 ehoffman sensord:   Core 0: 74.0 C
Aug 26 11:36:38 ehoffman sensord:   Core 0: 75.0 C
Aug 26 11:41:38 ehoffman sensord:   Core 0: 73.0 C
Aug 26 11:46:38 ehoffman sensord:   Core 0: 74.0 C
Aug 26 11:51:38 ehoffman sensord:   Core 0: 74.0 C
Aug 26 11:56:38 ehoffman sensord:   Core 0: 73.0 C
Aug 26 12:01:38 ehoffman sensord:   Core 0: 74.0 C
Aug 26 12:06:38 ehoffman sensord:   Core 0: 74.0 C
Aug 26 12:11:38 ehoffman sensord:   Core 0: 73.0 C
Aug 26 12:16:38 ehoffman sensord:   Core 0: 86.0 C
Aug 26 12:21:38 ehoffman sensord:   Core 0: 77.0 C
Aug 26 12:26:39 ehoffman sensord:   Core 0: 74.0 C
Aug 26 12:31:39 ehoffman sensord:   Core 0: 73.0 C
Aug 26 12:36:39 ehoffman sensord:   Core 0: 77.0 C
Aug 26 12:41:39 ehoffman sensord:   Core 0: 79.0 C
Aug 26 12:46:39 ehoffman sensord:   Core 0: 96.0 C
Aug 26 12:51:39 ehoffman sensord:   Core 0: 95.0 C
Aug 26 12:56:39 ehoffman sensord:   Core 0: 75.0 C
Aug 26 13:01:39 ehoffman sensord:   Core 0: 94.0 C
Aug 26 13:06:39 ehoffman sensord:   Core 0: 73.0 C
Aug 26 13:11:39 ehoffman sensord:   Core 0: 94.0 C
Aug 26 13:16:39 ehoffman sensord:   Core 0: 94.0 C
Aug 26 13:21:39 ehoffman sensord:   Core 0: 77.0 C
Aug 26 13:26:39 ehoffman sensord:   Core 0: 94.0 C
Aug 26 13:31:39 ehoffman sensord:   Core 0: 94.0 C
Aug 26 13:36:39 ehoffman sensord:   Core 0: 95.0 C
Aug 26 13:42:18 ehoffman sensord:   Core 0: 80.0 C
Aug 26 13:47:18 ehoffman sensord:   Core 0: 72.0 C
Aug 26 13:52:18 ehoffman sensord:   Core 0: 70.0 C
Aug 26 13:57:18 ehoffman sensord:   Core 0: 71.0 C
Aug 26 14:02:18 ehoffman sensord:   Core 0: 69.0 C
Aug 26 14:07:18 ehoffman sensord:   Core 0: 77.0 C
Aug 26 14:12:18 ehoffman sensord:   Core 0: 78.0 C
Aug 26 14:17:18 ehoffman sensord:   Core 0: 72.0 C
Aug 26 14:22:18 ehoffman sensord:   Core 0: 70.0 C
Aug 26 14:27:18 ehoffman sensord:   Core 0: 70.0 C
Aug 26 14:32:18 ehoffman sensord:   Core 0: 72.0 C
Aug 26 14:37:18 ehoffman sensord:   Core 0: 71.0 C
Aug 26 14:42:18 ehoffman sensord:   Core 0: 70.0 C
Aug 26 14:47:18 ehoffman sensord:   Core 0: 71.0 C
Aug 26 14:52:18 ehoffman sensord:   Core 0: 70.0 C
Aug 26 14:57:18 ehoffman sensord:   Core 0: 69.0 C
Aug 26 15:02:18 ehoffman sensord:   Core 0: 69.0 C
Aug 26 15:07:18 ehoffman sensord:   Core 0: 69.0 C
Aug 26 15:12:18 ehoffman sensord:   Core 0: 70.0 C
Aug 26 15:17:18 ehoffman sensord:   Core 0: 70.0 C
Aug 26 15:22:18 ehoffman sensord:   Core 0: 70.0 C
Aug 26 15:27:18 ehoffman sensord:   Core 0: 69.0 C
Aug 26 15:32:19 ehoffman sensord:   Core 0: 69.0 C
Aug 26 15:37:19 ehoffman sensord:   Core 0: 68.0 C
Aug 26 15:42:19 ehoffman sensord:   Core 0: 70.0 C
Aug 26 15:47:19 ehoffman sensord:   Core 0: 69.0 C
Aug 26 15:52:19 ehoffman sensord:   Core 0: 70.0 C
Aug 26 15:57:19 ehoffman sensord:   Core 0: 70.0 C
Aug 26 16:02:19 ehoffman sensord:   Core 0: 70.0 C
Aug 26 16:07:19 ehoffman sensord:   Core 0: 70.0 C
Aug 26 16:12:19 ehoffman sensord:   Core 0: 71.0 C
Aug 26 16:17:19 ehoffman sensord:   Core 0: 70.0 C
Aug 26 16:22:19 ehoffman sensord:   Core 0: 70.0 C
Aug 26 16:27:19 ehoffman sensord:   Core 0: 71.0 C
Aug 26 16:32:19 ehoffman sensord:   Core 0: 72.0 C
Aug 26 16:37:19 ehoffman sensord:   Core 0: 71.0 C
Aug 26 16:42:19 ehoffman sensord:   Core 0: 73.0 C
Aug 26 16:47:19 ehoffman sensord:   Core 0: 73.0 C
Aug 26 16:52:19 ehoffman sensord:   Core 0: 72.0 C
Aug 26 16:57:19 ehoffman sensord:   Core 0: 75.0 C
Aug 26 17:02:19 ehoffman sensord:   Core 0: 71.0 C
Aug 26 17:07:19 ehoffman sensord:   Core 0: 71.0 C
Aug 27 08:23:10 ehoffman sensord:   Core 0: 43.0 C
Aug 27 08:28:10 ehoffman sensord:   Core 0: 55.0 C
Aug 27 08:33:09 ehoffman sensord:   Core 0: 62.0 C
</pre>