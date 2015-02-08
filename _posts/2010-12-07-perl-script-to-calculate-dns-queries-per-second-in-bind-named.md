---
title: Perl script to calculate DNS queries per second in BIND (named)
author: Evan Hoffman
layout: post
permalink: /2010/12/07/perl-script-to-calculate-dns-queries-per-second-in-bind-named/
dsq_thread_id:
  - 2949526367
categories:
  - Uncategorized
tags:
  - analyzer
  - bind
  - code
  - dns
  - linux
  - log
  - name server
  - named
  - perl
  - queries per second
  - query
  - rndc
  - work
---
I&#8217;m pricing out DNS providers and was asked what our current queries-per-second currently are. Sadly I had no idea. After lots of Googling I decided there was really no good way to get this information so I decided to parse the logfile myself.  
<!--more-->

  
First, I turned on logging with timestamp in named.conf:

<pre>logging
{
...
        # Query logging 2010-12-07
        channel query-log {
                file "data/queries.log" versions 3 size 10m;
                print-time yes;
        };
        category queries { query-log; };
};
options
{
...
        # Query logging 2010-12-07
        querylog yes;

};
</pre>

Then I restarted named and had to figure out how to parse the file. My first guess was Perl and it didn&#8217;t disappoint. Here&#8217;s the hideous mess of code:

parsebind.pl

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
</pre>
      </td>
      
      <td class="code">
        <pre class="perl" style="font-family:monospace;"><span style="color: #666666; font-style: italic;">#!/usr/bin/perl</span>
&nbsp;
<span style="color: #666666; font-style: italic;">#use Date::Parse;</span>
<span style="color: #000000; font-weight: bold;">use</span> Time<span style="color: #339933;">::</span><span style="color: #006600;">ParseDate</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">$line_num</span> <span style="color: #339933;">=</span> <span style="color: #cc66cc;"></span><span style="color: #339933;">;</span>
<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">$first_line</span> <span style="color: #339933;">=</span> <span style="color: #cc66cc;"></span><span style="color: #339933;">;</span>
<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">$oldest_record</span> <span style="color: #339933;">=</span> <span style="color: #cc66cc;"></span><span style="color: #339933;">;</span>
<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">$line</span> <span style="color: #339933;">=</span> <span style="color: #ff0000;">''</span><span style="color: #339933;">;</span>
<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">$date</span> <span style="color: #339933;">=</span> <span style="color: #cc66cc;"></span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">%query_counter</span> <span style="color: #339933;">=</span> <span style="color: #009900;">&#123;</span><span style="color: #009900;">&#125;</span><span style="color: #339933;">;</span>
<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">@abbr</span> <span style="color: #339933;">=</span> <span style="color: #000066;">qw</span><span style="color: #009900;">&#40;</span> Jan Feb Mar Apr May Jun Jul Aug Sep Oct Nov Dec <span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #b1b100;">while</span> <span style="color: #009900;">&#40;</span><span style="color: #339933;">&lt;&gt;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
	<span style="color: #000066;">chomp</span><span style="color: #339933;">;</span>
	<span style="color: #0000ff;">$line</span> <span style="color: #339933;">=</span> <span style="color: #0000ff;">$_</span><span style="color: #339933;">;</span>
&nbsp;
	<span style="color: #666666; font-style: italic;">#my ($date, $time, $ip, $hostname, $in, $type, $crap) = $line  =~ m/^([\d]{2}-[\w]{3}-[\d]{4}) ([\d]{2}:[\d]{2}:[\d]{2}\.[\d]{3}) client ([\d\.]+)#[\d]*: [\w]+: ([\w\d\.]+) ([\w]+) ([\w]+) ([.]*)/;</span>
	<span style="color: #b1b100;">if</span> <span style="color: #009900;">&#40;</span><span style="color: #0000ff;">$line</span> <span style="color: #339933;">=~</span> <span style="color: #009966; font-style: italic;">m/^([\d]{2}-[\w]{3}-[\d]{4}) ([\d]{2}:[\d]{2}:[\d]{2}\.[\d]{3}) client ([\d\.]+)#[\d]*: [\w]+: ([\w\d\.]+) ([\w]+) ([\w]+) ([.]*)/</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
		<span style="color: #0000ff;">$date</span> <span style="color: #339933;">=</span> parsedate<span style="color: #009900;">&#40;</span><span style="color: #ff0000;">"$1 $2"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
<span style="color: #666666; font-style: italic;">#		print "$1 $2 =&gt; $date\n";</span>
		<span style="color: #b1b100;">if</span> <span style="color: #009900;">&#40;</span><span style="color: #0000ff;">$oldest_record</span> <span style="color: #339933;">==</span> <span style="color: #cc66cc;"></span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
			<span style="color: #0000ff;">$oldest_record</span> <span style="color: #339933;">=</span> <span style="color: #0000ff;">$date</span><span style="color: #339933;">;</span>
			<span style="color: #0000ff;">$first_line</span> <span style="color: #339933;">=</span> <span style="color: #0000ff;">$line_num</span><span style="color: #339933;">;</span>
			<span style="color: #000066;">print</span> <span style="color: #ff0000;">"First line:<span style="color: #000099; font-weight: bold;">\t</span>$line_num<span style="color: #000099; font-weight: bold;">\t</span>date<span style="color: #000099; font-weight: bold;">\t</span>"</span><span style="color: #339933;">.</span><span style="color: #000066;">localtime</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">$date</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">.</span><span style="color: #ff0000;">"<span style="color: #000099; font-weight: bold;">\n</span>"</span><span style="color: #339933;">;</span>
		<span style="color: #009900;">&#125;</span>
		<span style="color: #b1b100;">my</span> <span style="color: #009900;">&#40;</span><span style="color: #0000ff;">$sec</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$min</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$hour</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$mday</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$mon</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$year</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$wday</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$yday</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$isdst</span><span style="color: #009900;">&#41;</span> <span style="color: #339933;">=</span> <span style="color: #000066;">localtime</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">$date</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
		<span style="color: #0000ff;">$year</span> <span style="color: #339933;">+=</span> <span style="color: #cc66cc;">1900</span><span style="color: #339933;">;</span>
		<span style="color: #0000ff;">$mon</span> <span style="color: #339933;">+=</span> <span style="color: #cc66cc;">1</span><span style="color: #339933;">;</span>
		<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">$key</span> <span style="color: #339933;">=</span> <span style="color: #0000ff;">$year</span> <span style="color: #339933;">.</span> <span style="color: #ff0000;">'-'</span> <span style="color: #339933;">.</span> <span style="color: #000066;">sprintf</span><span style="color: #009900;">&#40;</span><span style="color: #ff0000;">'%02d'</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$mon</span><span style="color: #009900;">&#41;</span> <span style="color: #339933;">.</span> <span style="color: #ff0000;">'-'</span> <span style="color: #339933;">.</span> <span style="color: #000066;">sprintf</span><span style="color: #009900;">&#40;</span><span style="color: #ff0000;">'%02d'</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$mday</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">.</span> <span style="color: #ff0000;">' '</span><span style="color: #339933;">.</span><span style="color: #000066;">sprintf</span><span style="color: #009900;">&#40;</span><span style="color: #ff0000;">'%02d'</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$hour</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">.</span><span style="color: #ff0000;">':00 to '</span><span style="color: #339933;">.</span><span style="color: #000066;">sprintf</span><span style="color: #009900;">&#40;</span><span style="color: #ff0000;">'%02d'</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$hour</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">.</span><span style="color: #ff0000;">':59'</span> <span style="color: #339933;">;</span>
&nbsp;
		<span style="color: #b1b100;">if</span> <span style="color: #009900;">&#40;</span><span style="color: #0000ff;">$query_counter</span><span style="color: #009900;">&#123;</span><span style="color: #0000ff;">$key</span><span style="color: #009900;">&#125;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
			<span style="color: #0000ff;">$query_counter</span><span style="color: #009900;">&#123;</span><span style="color: #0000ff;">$key</span><span style="color: #009900;">&#125;</span><span style="color: #339933;">++;</span>
		<span style="color: #009900;">&#125;</span> <span style="color: #b1b100;">else</span> <span style="color: #009900;">&#123;</span>
			<span style="color: #0000ff;">$query_counter</span><span style="color: #009900;">&#123;</span><span style="color: #0000ff;">$key</span><span style="color: #009900;">&#125;</span> <span style="color: #339933;">=</span> <span style="color: #cc66cc;">1</span><span style="color: #339933;">;</span>
		<span style="color: #009900;">&#125;</span>
	<span style="color: #009900;">&#125;</span>
	<span style="color: #0000ff;">$line_num</span><span style="color: #339933;">++;</span>
<span style="color: #009900;">&#125;</span>
&nbsp;
<span style="color: #000066;">print</span> <span style="color: #ff0000;">"Last line:<span style="color: #000099; font-weight: bold;">\t</span>$line_num<span style="color: #000099; font-weight: bold;">\t</span>date<span style="color: #000099; font-weight: bold;">\t</span>"</span><span style="color: #339933;">.</span><span style="color: #000066;">localtime</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">$date</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">.</span><span style="color: #ff0000;">"<span style="color: #000099; font-weight: bold;">\n</span>"</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">$elapsed</span> <span style="color: #339933;">=</span> <span style="color: #0000ff;">$date</span> <span style="color: #339933;">-</span> <span style="color: #0000ff;">$oldest_record</span><span style="color: #339933;">;</span>
<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">$net_records</span> <span style="color: #339933;">=</span> <span style="color: #0000ff;">$line_num</span> <span style="color: #339933;">-</span> <span style="color: #0000ff;">$first_line</span><span style="color: #339933;">;</span>
<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">$rate</span> <span style="color: #339933;">=</span> <span style="color: #009900;">&#40;</span><span style="color: #0000ff;">$net_records</span> <span style="color: #339933;">*</span> <span style="color: #cc66cc;">1.0</span><span style="color: #009900;">&#41;</span> <span style="color: #339933;">/</span> <span style="color: #0000ff;">$elapsed</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #000066;">print</span> <span style="color: #ff0000;">"Rate for $elapsed seconds: "</span><span style="color: #339933;">.</span><span style="color: #000066;">sprintf</span><span style="color: #009900;">&#40;</span><span style="color: #ff0000;">'%0.2f'</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$rate</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">.</span><span style="color: #ff0000;">" per second<span style="color: #000099; font-weight: bold;">\n</span>"</span><span style="color: #339933;">;</span>
<span style="color: #000066;">print</span> <span style="color: #ff0000;">"<span style="color: #000099; font-weight: bold;">\n</span><span style="color: #000099; font-weight: bold;">\n</span>"</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #b1b100;">foreach</span> <span style="color: #b1b100;">my</span> <span style="color: #0000ff;">$key</span> <span style="color: #009900;">&#40;</span><span style="color: #000066;">sort</span> <span style="color: #000066;">keys</span> <span style="color: #0000ff;">%query_counter</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
	<span style="color: #0000ff;">$rate</span> <span style="color: #339933;">=</span> <span style="color: #009900;">&#40;</span><span style="color: #0000ff;">$query_counter</span><span style="color: #009900;">&#123;</span><span style="color: #0000ff;">$key</span><span style="color: #009900;">&#125;</span> <span style="color: #339933;">*</span> <span style="color: #cc66cc;">1.0</span><span style="color: #009900;">&#41;</span> <span style="color: #339933;">/</span> <span style="color: #cc66cc;">3600</span><span style="color: #339933;">;</span>
	<span style="color: #000066;">print</span> <span style="color: #ff0000;">"$key =&gt; $query_counter{$key}, rate "</span><span style="color: #339933;">.</span><span style="color: #000066;">sprintf</span><span style="color: #009900;">&#40;</span><span style="color: #ff0000;">'%0.3f'</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$rate</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">.</span><span style="color: #ff0000;">" queries/sec<span style="color: #000099; font-weight: bold;">\n</span>"</span><span style="color: #339933;">;</span>
<span style="color: #009900;">&#125;</span>
&nbsp;
<span style="color: #000066;">exit</span> <span style="color: #cc66cc;"></span><span style="color: #339933;">;</span></pre>
      </td>
    </tr>
  </table>
</div>

And the beautiful output:

<pre>[root@ns3 ~]# perl ~evan/parsebind.pl /var/named/chroot/var/named/data/queries.log
First line:	308	date	Tue Dec  7 17:56:02 2010
Last line:	41289	date	Tue Dec  7 22:44:01 2010
Rate for 17279 seconds: 2.37 per second


2010-12-07 17:00 to 17:59 => 533, rate 0.148 queries/sec
2010-12-07 18:00 to 18:59 => 9185, rate 2.551 queries/sec
2010-12-07 19:00 to 19:59 => 8618, rate 2.394 queries/sec
2010-12-07 20:00 to 20:59 => 8075, rate 2.243 queries/sec
2010-12-07 21:00 to 21:59 => 8762, rate 2.434 queries/sec
2010-12-07 22:00 to 22:59 => 5808, rate 1.613 queries/sec
HASH(0x112382a0) => , rate 0.000 queries/sec
[root@ns3 ~]#
</pre>

Not pretty but good enough to get a sense of QPS. Hope it helps someone.

**Edit**: It occurred to me that you may have more than one domain on a single bind server, and you may want the stats for a single one (rather than all aggregated together, which the above script does). The below version includes the second-level domain in the hash key for the final report:

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
</pre>
      </td>
      
      <td class="code">
        <pre class="perl" style="font-family:monospace;"><span style="color: #666666; font-style: italic;">#!/usr/bin/perl</span>
&nbsp;
<span style="color: #666666; font-style: italic;">#use Date::Parse;</span>
<span style="color: #000000; font-weight: bold;">use</span> Time<span style="color: #339933;">::</span><span style="color: #006600;">ParseDate</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">$line_num</span> <span style="color: #339933;">=</span> <span style="color: #cc66cc;"></span><span style="color: #339933;">;</span>
<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">$first_line</span> <span style="color: #339933;">=</span> <span style="color: #cc66cc;"></span><span style="color: #339933;">;</span>
<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">$oldest_record</span> <span style="color: #339933;">=</span> <span style="color: #cc66cc;"></span><span style="color: #339933;">;</span>
<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">$line</span> <span style="color: #339933;">=</span> <span style="color: #ff0000;">''</span><span style="color: #339933;">;</span>
<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">$date</span> <span style="color: #339933;">=</span> <span style="color: #cc66cc;"></span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">%query_counter</span> <span style="color: #339933;">=</span> <span style="color: #009900;">&#123;</span><span style="color: #009900;">&#125;</span><span style="color: #339933;">;</span>
<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">@abbr</span> <span style="color: #339933;">=</span> <span style="color: #000066;">qw</span><span style="color: #009900;">&#40;</span> Jan Feb Mar Apr May Jun Jul Aug Sep Oct Nov Dec <span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #b1b100;">while</span> <span style="color: #009900;">&#40;</span><span style="color: #339933;">&lt;&gt;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
	<span style="color: #000066;">chomp</span><span style="color: #339933;">;</span>
	<span style="color: #0000ff;">$line</span> <span style="color: #339933;">=</span> <span style="color: #0000ff;">$_</span><span style="color: #339933;">;</span>
&nbsp;
	<span style="color: #666666; font-style: italic;">#my ($date, $time, $ip, $hostname, $in, $type, $crap) = $line  =~ m/^([\d]{2}-[\w]{3}-[\d]{4}) ([\d]{2}:[\d]{2}:[\d]{2}\.[\d]{3}) client ([\d\.]+)#[\d]*: [\w]+: ([\w\d\.]+) ([\w]+) ([\w]+) ([.]*)/;</span>
	<span style="color: #b1b100;">if</span> <span style="color: #009900;">&#40;</span><span style="color: #0000ff;">$line</span> <span style="color: #339933;">=~</span> <span style="color: #009966; font-style: italic;">m/^([\d]{2}-[\w]{3}-[\d]{4}) ([\d]{2}:[\d]{2}:[\d]{2}\.[\d]{3}) client ([\d\.]+)#[\d]*: [\w]+: ([\w\d\.]+) ([\w]+) ([\w]+) ([.]*)/</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
		<span style="color: #0000ff;">$date</span> <span style="color: #339933;">=</span> parsedate<span style="color: #009900;">&#40;</span><span style="color: #ff0000;">"$1 $2"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
		<span style="color: #b1b100;">my</span> <span style="color: #009900;">&#40;</span><span style="color: #0000ff;">$domain</span><span style="color: #009900;">&#41;</span> <span style="color: #339933;">=</span> <span style="color: #009900;">&#40;</span><span style="color: #0000ff;">$4</span> <span style="color: #339933;">=~</span> <span style="color: #009966; font-style: italic;">m/([\w\d\-]+\.[\w]+)$/</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
		<span style="color: #0000ff;">$domain</span> <span style="color: #339933;">=</span> <span style="color: #000066;">lc</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">$domain</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
<span style="color: #666666; font-style: italic;">#		print "$1 $2 =&gt; $date\n";</span>
		<span style="color: #b1b100;">if</span> <span style="color: #009900;">&#40;</span><span style="color: #0000ff;">$oldest_record</span> <span style="color: #339933;">==</span> <span style="color: #cc66cc;"></span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
			<span style="color: #0000ff;">$oldest_record</span> <span style="color: #339933;">=</span> <span style="color: #0000ff;">$date</span><span style="color: #339933;">;</span>
			<span style="color: #0000ff;">$first_line</span> <span style="color: #339933;">=</span> <span style="color: #0000ff;">$line_num</span><span style="color: #339933;">;</span>
			<span style="color: #000066;">print</span> <span style="color: #ff0000;">"First line:<span style="color: #000099; font-weight: bold;">\t</span>$line_num<span style="color: #000099; font-weight: bold;">\t</span>date<span style="color: #000099; font-weight: bold;">\t</span>"</span><span style="color: #339933;">.</span><span style="color: #000066;">localtime</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">$date</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">.</span><span style="color: #ff0000;">"<span style="color: #000099; font-weight: bold;">\n</span>"</span><span style="color: #339933;">;</span>
		<span style="color: #009900;">&#125;</span>
		<span style="color: #b1b100;">my</span> <span style="color: #009900;">&#40;</span><span style="color: #0000ff;">$sec</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$min</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$hour</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$mday</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$mon</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$year</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$wday</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$yday</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$isdst</span><span style="color: #009900;">&#41;</span> <span style="color: #339933;">=</span> <span style="color: #000066;">localtime</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">$date</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
		<span style="color: #0000ff;">$year</span> <span style="color: #339933;">+=</span> <span style="color: #cc66cc;">1900</span><span style="color: #339933;">;</span>
		<span style="color: #0000ff;">$mon</span> <span style="color: #339933;">+=</span> <span style="color: #cc66cc;">1</span><span style="color: #339933;">;</span>
		<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">$key</span> <span style="color: #339933;">=</span> <span style="color: #0000ff;">$domain</span> <span style="color: #339933;">.</span><span style="color: #ff0000;">' '</span><span style="color: #339933;">.</span><span style="color: #0000ff;">$year</span> <span style="color: #339933;">.</span> <span style="color: #ff0000;">'-'</span> <span style="color: #339933;">.</span> <span style="color: #000066;">sprintf</span><span style="color: #009900;">&#40;</span><span style="color: #ff0000;">'%02d'</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$mon</span><span style="color: #009900;">&#41;</span> <span style="color: #339933;">.</span> <span style="color: #ff0000;">'-'</span> <span style="color: #339933;">.</span> <span style="color: #000066;">sprintf</span><span style="color: #009900;">&#40;</span><span style="color: #ff0000;">'%02d'</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$mday</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">.</span> <span style="color: #ff0000;">' '</span><span style="color: #339933;">.</span><span style="color: #000066;">sprintf</span><span style="color: #009900;">&#40;</span><span style="color: #ff0000;">'%02d'</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$hour</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">.</span><span style="color: #ff0000;">':00 to '</span><span style="color: #339933;">.</span><span style="color: #000066;">sprintf</span><span style="color: #009900;">&#40;</span><span style="color: #ff0000;">'%02d'</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$hour</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">.</span><span style="color: #ff0000;">':59'</span> <span style="color: #339933;">;</span>
&nbsp;
		<span style="color: #b1b100;">if</span> <span style="color: #009900;">&#40;</span><span style="color: #0000ff;">$query_counter</span><span style="color: #009900;">&#123;</span><span style="color: #0000ff;">$key</span><span style="color: #009900;">&#125;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
			<span style="color: #0000ff;">$query_counter</span><span style="color: #009900;">&#123;</span><span style="color: #0000ff;">$key</span><span style="color: #009900;">&#125;</span><span style="color: #339933;">++;</span>
		<span style="color: #009900;">&#125;</span> <span style="color: #b1b100;">else</span> <span style="color: #009900;">&#123;</span>
			<span style="color: #0000ff;">$query_counter</span><span style="color: #009900;">&#123;</span><span style="color: #0000ff;">$key</span><span style="color: #009900;">&#125;</span> <span style="color: #339933;">=</span> <span style="color: #cc66cc;">1</span><span style="color: #339933;">;</span>
		<span style="color: #009900;">&#125;</span>
	<span style="color: #009900;">&#125;</span>
	<span style="color: #0000ff;">$line_num</span><span style="color: #339933;">++;</span>
<span style="color: #009900;">&#125;</span>
&nbsp;
<span style="color: #000066;">print</span> <span style="color: #ff0000;">"Last line:<span style="color: #000099; font-weight: bold;">\t</span>$line_num<span style="color: #000099; font-weight: bold;">\t</span>date<span style="color: #000099; font-weight: bold;">\t</span>"</span><span style="color: #339933;">.</span><span style="color: #000066;">localtime</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">$date</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">.</span><span style="color: #ff0000;">"<span style="color: #000099; font-weight: bold;">\n</span>"</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">$elapsed</span> <span style="color: #339933;">=</span> <span style="color: #0000ff;">$date</span> <span style="color: #339933;">-</span> <span style="color: #0000ff;">$oldest_record</span><span style="color: #339933;">;</span>
<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">$net_records</span> <span style="color: #339933;">=</span> <span style="color: #0000ff;">$line_num</span> <span style="color: #339933;">-</span> <span style="color: #0000ff;">$first_line</span><span style="color: #339933;">;</span>
<span style="color: #b1b100;">my</span> <span style="color: #0000ff;">$rate</span> <span style="color: #339933;">=</span> <span style="color: #009900;">&#40;</span><span style="color: #0000ff;">$net_records</span> <span style="color: #339933;">*</span> <span style="color: #cc66cc;">1.0</span><span style="color: #009900;">&#41;</span> <span style="color: #339933;">/</span> <span style="color: #0000ff;">$elapsed</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #000066;">print</span> <span style="color: #ff0000;">"Rate for $elapsed seconds: "</span><span style="color: #339933;">.</span><span style="color: #000066;">sprintf</span><span style="color: #009900;">&#40;</span><span style="color: #ff0000;">'%0.2f'</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$rate</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">.</span><span style="color: #ff0000;">" per second<span style="color: #000099; font-weight: bold;">\n</span>"</span><span style="color: #339933;">;</span>
<span style="color: #000066;">print</span> <span style="color: #ff0000;">"<span style="color: #000099; font-weight: bold;">\n</span><span style="color: #000099; font-weight: bold;">\n</span>"</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #b1b100;">foreach</span> <span style="color: #b1b100;">my</span> <span style="color: #0000ff;">$key</span> <span style="color: #009900;">&#40;</span><span style="color: #000066;">sort</span> <span style="color: #000066;">keys</span> <span style="color: #0000ff;">%query_counter</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
	<span style="color: #0000ff;">$rate</span> <span style="color: #339933;">=</span> <span style="color: #009900;">&#40;</span><span style="color: #0000ff;">$query_counter</span><span style="color: #009900;">&#123;</span><span style="color: #0000ff;">$key</span><span style="color: #009900;">&#125;</span> <span style="color: #339933;">*</span> <span style="color: #cc66cc;">1.0</span><span style="color: #009900;">&#41;</span> <span style="color: #339933;">/</span> <span style="color: #cc66cc;">3600</span><span style="color: #339933;">;</span>
	<span style="color: #000066;">print</span> <span style="color: #ff0000;">"$key =&gt; $query_counter{$key}, rate "</span><span style="color: #339933;">.</span><span style="color: #000066;">sprintf</span><span style="color: #009900;">&#40;</span><span style="color: #ff0000;">'%0.3f'</span><span style="color: #339933;">,</span><span style="color: #0000ff;">$rate</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">.</span><span style="color: #ff0000;">" queries/sec<span style="color: #000099; font-weight: bold;">\n</span>"</span><span style="color: #339933;">;</span>
<span style="color: #009900;">&#125;</span>
&nbsp;
<span style="color: #000066;">exit</span> <span style="color: #cc66cc;"></span><span style="color: #339933;">;</span></pre>
      </td>
    </tr>
  </table>
</div>

I suppose this doesn&#8217;t account for PTR (reverse DNS) lookups, but oh well.

**Edit**: I added some lame ASCII graphing functionality to the script, check it out <a href="http://www.evanhoffman.com/evan/?p=978" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=978', 'in this post']);" >in this post</a>.