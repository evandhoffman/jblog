---
title: 'Using rrdtool to generate server load &#038; bandwidth graphs'
author: Evan Hoffman
layout: post
permalink: /2012/02/17/using-rrdtool-to-generate-server-load-bandwidth-graphs/
image:
  - 
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
dsq_thread_id:
  - 2949525994
categories:
  - Uncategorized
tags:
  - devops
  - graphs
  - linux
  - load
  - mrtg
  - rrdtool
  - server
  - snmp
---
I&#8217;ve been using MRTG and routers2.cgi for years to graph the various aspects of a server that warrant monitoring. I&#8217;ve long known that they used something called rrdtool to do&#8230; well, something, but never had a need or desire to figure out exactly what that was.

But, having just moved my site to a new server, I was curious how the server would handle the load. Rather than setting up some behemoth like Nagios or Zabbix, which are full monitoring/alerting suites, I just wanted graphing. As I said, in the past I&#8217;ve used MRTG or routers2.cgi for this but both of them were overkill for me in this case. Since both of them used rrdtool, I figured that was a good place to look.

The two metrics I want to record are server load and in/out bandwidth. The first step is to create the RRDs (round robin databases). This was done via these commands:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #666666; font-style: italic;"># rrdtool create /mrtg/load.rrd --start N DS:load1:GAUGE:600:0:100 DS:load5:GAUGE:600:0:100 DS:load15:GAUGE:600:0:100 RRA:AVERAGE:0.5:2:800</span>
&nbsp;
<span style="color: #666666; font-style: italic;"># rrdtool create /mrtg/eth1.rrd --start N DS:in:COUNTER:600:0:10000000000 DS:out:COUNTER:600:0:10000000000 RRA:AVERAGE:0.5:2:800</span></pre>
      </td>
    </tr>
  </table>
</div>

A good explanation of what these various fields mean is <a href="http://silverwraith.com/papers/freebsd-snmp.php" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://silverwraith.com/papers/freebsd-snmp.php', 'here']);" >here</a>. In short, each &#8220;<tt>DS:</tt>&#8221; section defines a &#8220;column&#8221; (for fellow RDBMS users) in the database. The first one has 3 &#8220;columns,&#8221; named load1, load5, load15, each of which will contain GAUGE data. The second one contains two COUNTER fields, representing the bytes in/out for interface eth1.

To actually get the data I poll snmpd via this bash script:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #666666; font-style: italic;">#!/bin/bash</span>
&nbsp;
rrdupdate <span style="color: #000000; font-weight: bold;">/</span>mrtg<span style="color: #000000; font-weight: bold;">/</span>load.rrd N:\
<span style="color: #000000; font-weight: bold;">`/</span>usr<span style="color: #000000; font-weight: bold;">/</span>bin<span style="color: #000000; font-weight: bold;">/</span>snmpget <span style="color: #660033;">-v</span> 2c <span style="color: #660033;">-c</span> public <span style="color: #660033;">-Oqv</span> localhost laLoad.1<span style="color: #000000; font-weight: bold;">`</span>:\
<span style="color: #000000; font-weight: bold;">`/</span>usr<span style="color: #000000; font-weight: bold;">/</span>bin<span style="color: #000000; font-weight: bold;">/</span>snmpget <span style="color: #660033;">-v</span> 2c <span style="color: #660033;">-c</span> public <span style="color: #660033;">-Oqv</span> localhost laLoad.2<span style="color: #000000; font-weight: bold;">`</span>:\
<span style="color: #000000; font-weight: bold;">`/</span>usr<span style="color: #000000; font-weight: bold;">/</span>bin<span style="color: #000000; font-weight: bold;">/</span>snmpget <span style="color: #660033;">-v</span> 2c <span style="color: #660033;">-c</span> public <span style="color: #660033;">-Oqv</span> localhost laLoad.3<span style="color: #000000; font-weight: bold;">`</span>
&nbsp;
rrdupdate <span style="color: #000000; font-weight: bold;">/</span>mrtg<span style="color: #000000; font-weight: bold;">/</span>eth1.rrd N:\
<span style="color: #000000; font-weight: bold;">`/</span>usr<span style="color: #000000; font-weight: bold;">/</span>bin<span style="color: #000000; font-weight: bold;">/</span>snmpget <span style="color: #660033;">-v</span> 2c <span style="color: #660033;">-c</span> public <span style="color: #660033;">-Oqv</span> localhost ifInOctets.3<span style="color: #000000; font-weight: bold;">`</span>:\
<span style="color: #000000; font-weight: bold;">`/</span>usr<span style="color: #000000; font-weight: bold;">/</span>bin<span style="color: #000000; font-weight: bold;">/</span>snmpget <span style="color: #660033;">-v</span> 2c <span style="color: #660033;">-c</span> public <span style="color: #660033;">-Oqv</span> localhost ifOutOctets.3<span style="color: #000000; font-weight: bold;">`</span></pre>
      </td>
    </tr>
  </table>
</div>

I have that run every 5 minutes via cron. Then to generate the actual graph, I run this script via cron:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #666666; font-style: italic;">#!/bin/bash</span>
&nbsp;
rrdtool graph <span style="color: #000000; font-weight: bold;">/</span>var<span style="color: #000000; font-weight: bold;">/</span>www<span style="color: #000000; font-weight: bold;">/</span>html<span style="color: #000000; font-weight: bold;">/</span>graphs<span style="color: #000000; font-weight: bold;">/</span>load.png \
        <span style="color: #660033;">-N</span> \
        <span style="color: #660033;">-E</span> \
        <span style="color: #660033;">--start</span> now-30hours \
        <span style="color: #660033;">--title</span> <span style="color: #ff0000;">"Load Averages"</span> \
        <span style="color: #660033;">--width</span> <span style="color: #000000;">300</span>\
         <span style="color: #660033;">--x-grid</span> MINUTE:<span style="color: #000000;">60</span>:HOUR:<span style="color: #000000;">2</span>:HOUR:<span style="color: #000000;">4</span>:<span style="color: #000000;"></span>:<span style="color: #000000; font-weight: bold;">%</span>H\
        <span style="color: #660033;">--height</span> <span style="color: #000000;">200</span> \
        <span style="color: #660033;">-u</span> <span style="color: #000000;">1.0</span> \
        <span style="color: #660033;">--lower-limit</span> <span style="color: #000000;"></span>\
        <span style="color: #660033;">--vertical-label</span> <span style="color: #ff0000;">"Load Avg"</span> \
        <span style="color: #660033;">--full-size-mode</span> \
<span style="color: #660033;">-a</span> PNG <span style="color: #660033;">--title</span>=<span style="color: #ff0000;">"Load Avg"</span> \
<span style="color: #ff0000;">'DEF:load1=/mrtg/load.rrd:load1:AVERAGE'</span> \
<span style="color: #ff0000;">'VDEF:load1last=load1,LAST'</span> \
<span style="color: #ff0000;">'DEF:load5=/mrtg/load.rrd:load5:AVERAGE'</span> \
<span style="color: #ff0000;">'DEF:load15=/mrtg/load.rrd:load15:AVERAGE'</span> \
<span style="color: #ff0000;">'AREA:load15#33CC33:15 Min Load Avg '</span> \
<span style="color: #ff0000;">'LINE1:load1#0000ff:1 Min Load Avg '</span> \
<span style="color: #ff0000;">'GPRINT:load1:AVERAGE:"Load1 Avg\:%3.2lf"'</span> \
<span style="color: #ff0000;">'GPRINT:load1last:Drawn at %Y-%m-%d, %H\:%M:strftime'</span> 
<span style="color: #666666; font-style: italic;">#'LINE1:load5#ff00ff:5 Min Load Avg ' \</span>
&nbsp;
&nbsp;
rrdtool graph <span style="color: #000000; font-weight: bold;">/</span>var<span style="color: #000000; font-weight: bold;">/</span>www<span style="color: #000000; font-weight: bold;">/</span>html<span style="color: #000000; font-weight: bold;">/</span>graphs<span style="color: #000000; font-weight: bold;">/</span>eth1.png \
        <span style="color: #660033;">-N</span> \
        <span style="color: #660033;">-E</span> \
        <span style="color: #660033;">--start</span> now-30hours \
        <span style="color: #660033;">--title</span> <span style="color: #ff0000;">"eth1 traffic"</span> \
        <span style="color: #660033;">--width</span> <span style="color: #000000;">300</span>\
         <span style="color: #660033;">--x-grid</span> MINUTE:<span style="color: #000000;">60</span>:HOUR:<span style="color: #000000;">2</span>:HOUR:<span style="color: #000000;">4</span>:<span style="color: #000000;"></span>:<span style="color: #000000; font-weight: bold;">%</span>H\
        <span style="color: #660033;">--height</span> <span style="color: #000000;">200</span> \
        <span style="color: #660033;">-u</span> <span style="color: #000000;">1000000</span> \
        <span style="color: #660033;">--lower-limit</span> <span style="color: #000000;"></span>\
        <span style="color: #660033;">--vertical-label</span> <span style="color: #ff0000;">"bps"</span> \
        <span style="color: #660033;">--full-size-mode</span> \
<span style="color: #660033;">-a</span> PNG <span style="color: #660033;">--title</span>=<span style="color: #ff0000;">"eth1 traffic"</span> \
<span style="color: #ff0000;">'DEF:eth1in=/mrtg/eth1.rrd:in:AVERAGE'</span> \
<span style="color: #ff0000;">'CDEF:eth1inbits=eth1in,8,*'</span> \
<span style="color: #ff0000;">'VDEF:eth1last=eth1in,LAST'</span> \
<span style="color: #ff0000;">'DEF:eth1out=/mrtg/eth1.rrd:out:AVERAGE'</span> \
<span style="color: #ff0000;">'CDEF:eth1outbits=eth1out,8,*'</span> \
<span style="color: #ff0000;">'AREA:eth1inbits#33CC33:eth1 in '</span> \
<span style="color: #ff0000;">'LINE1:eth1outbits#0000ff:eth1 out'</span> \
<span style="color: #ff0000;">'GPRINT:eth1last:Drawn at %Y-%m-%d, %H\:%M:strftime'</span></pre>
      </td>
    </tr>
  </table>
</div>

The final graphs look decent, though not very fancy, but I&#8217;ll play around with it a bit more:<figure id="attachment_1850" style="width: 300px;" class="wp-caption aligncenter">

<a href="http://www.evanhoffman.com/evan/2012/02/17/using-rrdtool-to-generate-server-load-bandwidth-graphs/eth1-2/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/2012/02/17/using-rrdtool-to-generate-server-load-bandwidth-graphs/eth1-2/', '']);"  rel="attachment wp-att-1850"><img src="http://www.evanhoffman.com/evan/wp-content/uploads/2012/02/eth1.png" alt="eth1 graph" title="eth1 graph" width="300" height="200" class="size-full wp-image-1850" /></a><figcaption class="wp-caption-text">eth1 graph</figcaption></figure> <figure id="attachment_1851" style="width: 300px;" class="wp-caption aligncenter"><a href="http://www.evanhoffman.com/evan/2012/02/17/using-rrdtool-to-generate-server-load-bandwidth-graphs/load-2/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/2012/02/17/using-rrdtool-to-generate-server-load-bandwidth-graphs/load-2/', '']);"  rel="attachment wp-att-1851"><img src="http://www.evanhoffman.com/evan/wp-content/uploads/2012/02/load.png" alt="load graph" title="load graph" width="300" height="200" class="size-full wp-image-1851" /></a><figcaption class="wp-caption-text">load graph</figcaption></figure>