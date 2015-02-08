---
title: The not-so-secret secret to Postgres performance
author: Evan Hoffman
layout: post
permalink: /2013/10/07/the-not-so-secret-secret-to-postgres-performance/
dsq_thread_id:
  - 2963365464
categories:
  - Uncategorized
tags:
  - blockdev
  - centos
  - linux
  - postgresql
  - readahead
  - sql
  - sysadmin
  - work
---
I manage a bunch of Postgres DBs and one of the things I almost always forget to do when setting up a new one is set the readahead up from the default of 256. I created this script and run it out of /etc/rc.local and sometimes cron it too. The 3 commands at the top are only really relevant on systems with &#8220;huge&#8221; memory &#8211; probably over 64 GB. We <a href="http://www.evanhoffman.com/evan/?p=2027" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=2027', 'ran into some memory problems']);" >ran into some memory problems</a> with CentOS 6 on a box with 128 GB ram which we ended up working around by reinstalling CentOS 5, but the `/sys/kernel/mm/redhat_transparent_hugepage/` options below should fix them in 6.x (though we haven&#8217;t actually tried it on that DB, we haven&#8217;t seen any problems in other large DBs).

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #666666; font-style: italic;">#!/bin/bash</span>
&nbsp;
<span style="color: #7a0874; font-weight: bold;">echo</span> no <span style="color: #000000; font-weight: bold;">&gt;</span> <span style="color: #000000; font-weight: bold;">/</span>sys<span style="color: #000000; font-weight: bold;">/</span>kernel<span style="color: #000000; font-weight: bold;">/</span>mm<span style="color: #000000; font-weight: bold;">/</span>redhat_transparent_hugepage<span style="color: #000000; font-weight: bold;">/</span>khugepaged<span style="color: #000000; font-weight: bold;">/</span>defrag
<span style="color: #7a0874; font-weight: bold;">echo</span> never <span style="color: #000000; font-weight: bold;">&gt;/</span>sys<span style="color: #000000; font-weight: bold;">/</span>kernel<span style="color: #000000; font-weight: bold;">/</span>mm<span style="color: #000000; font-weight: bold;">/</span>redhat_transparent_hugepage<span style="color: #000000; font-weight: bold;">/</span>defrag
&nbsp;
<span style="color: #7a0874; font-weight: bold;">echo</span> <span style="color: #000000;">1</span> <span style="color: #000000; font-weight: bold;">&gt;</span> <span style="color: #000000; font-weight: bold;">/</span>proc<span style="color: #000000; font-weight: bold;">/</span>sys<span style="color: #000000; font-weight: bold;">/</span>vm<span style="color: #000000; font-weight: bold;">/</span>dirty_background_ratio
&nbsp;
<span style="color: #007800;">BLOCK_DEVICES</span>=<span style="color: #000000; font-weight: bold;">`</span><span style="color: #c20cb9; font-weight: bold;">perl</span> <span style="color: #660033;">-ne</span> <span style="color: #ff0000;">'chomp; my @a=split(/[\s]+/); next unless $a[4]; next if ($a[4] =~ /sd[a-z]+[\d]+/); print "$a[4]\n";'</span> <span style="color: #000000; font-weight: bold;">/</span>proc<span style="color: #000000; font-weight: bold;">/</span>partitions <span style="color: #000000; font-weight: bold;">`</span>
&nbsp;
logger <span style="color: #660033;">-t</span> tune_blockdevs <span style="color: #ff0000;">"Block devices matching: <span style="color: #007800;">$BLOCK_DEVICES</span>"</span>
&nbsp;
<span style="color: #000000; font-weight: bold;">for</span> DEV <span style="color: #000000; font-weight: bold;">in</span> <span style="color: #007800;">$BLOCK_DEVICES</span>;  <span style="color: #000000; font-weight: bold;">do</span>
        logger <span style="color: #660033;">-t</span> tune_blockdevs <span style="color: #ff0000;">"Setting IO params for <span style="color: #007800;">$DEV</span>"</span>
<span style="color: #666666; font-style: italic;">### Uncomment the below line if using SSD</span>
<span style="color: #666666; font-style: italic;">#        echo "noop" &gt; /sys/block/$DEV/queue/scheduler</span>
        <span style="color: #000000; font-weight: bold;">/</span>sbin<span style="color: #000000; font-weight: bold;">/</span>blockdev <span style="color: #660033;">--setra</span> <span style="color: #000000;">65536</span> <span style="color: #000000; font-weight: bold;">/</span>dev<span style="color: #000000; font-weight: bold;">/</span><span style="color: #007800;">$DEV</span>
<span style="color: #000000; font-weight: bold;">done</span></pre>
      </td>
    </tr>
  </table>
</div>