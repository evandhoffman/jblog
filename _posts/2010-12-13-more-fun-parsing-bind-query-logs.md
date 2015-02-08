---
title: More fun parsing BIND query logs
author: Evan Hoffman
layout: post
permalink: /2010/12/13/more-fun-parsing-bind-query-logs/
dsq_thread_id:
  - 2949519002
categories:
  - Uncategorized
tags:
  - bind
  - dns
  - linux
  - log
  - perl
  - queries per second
  - sysadmin
  - work
---
&#8220;But wouldn&#8217;t it be cool if it also&#8230;&#8221; That phrase usually triggers a lot of wasted cycles in my brain, though it sometimes comes up with something neat. I added a super lame graph to the DNS QPS parser. Makes it really easy to see peaks &#038; troughs in usage:  
<!--more-->

<pre>2010-12-12 00:00 to 00:59 => 3237, rate 0.899 queries/sec	***********
2010-12-12 01:00 to 01:59 => 2709, rate 0.752 queries/sec	**********
2010-12-12 02:00 to 02:59 => 2473, rate 0.687 queries/sec	*********
2010-12-12 03:00 to 03:59 => 2171, rate 0.603 queries/sec	********
2010-12-12 04:00 to 04:59 => 2048, rate 0.569 queries/sec	*******
2010-12-12 05:00 to 05:59 => 1918, rate 0.533 queries/sec	*******
2010-12-12 06:00 to 06:59 => 2120, rate 0.589 queries/sec	********
2010-12-12 07:00 to 07:59 => 2477, rate 0.688 queries/sec	*********
2010-12-12 08:00 to 08:59 => 2994, rate 0.832 queries/sec	**********
2010-12-12 09:00 to 09:59 => 3704, rate 1.029 queries/sec	*************
2010-12-12 10:00 to 10:59 => 4297, rate 1.194 queries/sec	***************
2010-12-12 11:00 to 11:59 => 4744, rate 1.318 queries/sec	****************
2010-12-12 12:00 to 12:59 => 5106, rate 1.418 queries/sec	******************
2010-12-12 13:00 to 13:59 => 5311, rate 1.475 queries/sec	******************
2010-12-12 14:00 to 14:59 => 5083, rate 1.412 queries/sec	*****************
2010-12-12 15:00 to 15:59 => 4855, rate 1.349 queries/sec	*****************
2010-12-12 16:00 to 16:59 => 5179, rate 1.439 queries/sec	******************
2010-12-12 17:00 to 17:59 => 4959, rate 1.377 queries/sec	*****************
2010-12-12 18:00 to 18:59 => 4693, rate 1.304 queries/sec	****************
2010-12-12 19:00 to 19:59 => 4792, rate 1.331 queries/sec	****************
2010-12-12 20:00 to 20:59 => 4799, rate 1.333 queries/sec	****************
2010-12-12 21:00 to 21:59 => 5068, rate 1.408 queries/sec	*****************
2010-12-12 22:00 to 22:59 => 4672, rate 1.298 queries/sec	****************
2010-12-12 23:00 to 23:59 => 4514, rate 1.254 queries/sec	****************
2010-12-13 00:00 to 00:59 => 3917, rate 1.088 queries/sec	**************
2010-12-13 01:00 to 01:59 => 3458, rate 0.961 queries/sec	************
2010-12-13 02:00 to 02:59 => 2874, rate 0.798 queries/sec	**********
2010-12-13 03:00 to 03:59 => 2715, rate 0.754 queries/sec	**********
2010-12-13 04:00 to 04:59 => 2690, rate 0.747 queries/sec	*********
2010-12-13 05:00 to 05:59 => 2719, rate 0.755 queries/sec	**********
2010-12-13 06:00 to 06:59 => 2831, rate 0.786 queries/sec	**********
2010-12-13 07:00 to 07:59 => 3416, rate 0.949 queries/sec	************
2010-12-13 08:00 to 08:59 => 4962, rate 1.378 queries/sec	*****************
2010-12-13 09:00 to 09:59 => 6943, rate 1.929 queries/sec	************************
2010-12-13 10:00 to 10:59 => 8296, rate 2.304 queries/sec	****************************
2010-12-13 11:00 to 11:59 => 8938, rate 2.483 queries/sec	******************************
2010-12-13 12:00 to 12:59 => 8926, rate 2.479 queries/sec	******************************
2010-12-13 13:00 to 13:59 => 8950, rate 2.486 queries/sec	******************************
2010-12-13 14:00 to 14:59 => 2173, rate 0.604 queries/sec	********
</pre>

You can see pretty quickly that 4-5 AM (Eastern) is the period of lowest activity, as you&#8217;d probably expect for a US-based US-centric site. Modified perl script below.