---
title: MongoDB logrotate script
author: Evan Hoffman
excerpt: 'Daily mongodb log rotation via <code>logrotate</code>.'
layout: post
permalink: /2011/07/21/mongodb-logrotate-script/
categories:
  - Uncategorized
tags:
  - centos
  - code
  - devops
  - linux
  - logrotate
  - logrotate.conf
  - logrotate.d
  - mongo
  - mongod
  - mongodb
  - script
---
MongoDB has log rotation functionality <a href="http://www.mongodb.org/display/DOCS/Logging#Logging-Fromthemongoshell" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.mongodb.org/display/DOCS/Logging#Logging-Fromthemongoshell', 'built in']);" >built in</a>, but since I run CentOS I like to have everything managed through `logrotate`. The 10gen RPM I installed doesn&#8217;t have a logrotate script, so I wrote one. Create file `/etc/logrotate.d/mongod`:

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
</pre>
      </td>
      
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #000000; font-weight: bold;">/</span>var<span style="color: #000000; font-weight: bold;">/</span>log<span style="color: #000000; font-weight: bold;">/</span>mongo<span style="color: #000000; font-weight: bold;">/</span>mongod.log <span style="color: #7a0874; font-weight: bold;">&#123;</span>
        daily
        rotate <span style="color: #000000;">30</span>
        compress
        dateext
&nbsp;
    missingok
    notifempty
    sharedscripts
    postrotate
        <span style="color: #000000; font-weight: bold;">/</span>bin<span style="color: #000000; font-weight: bold;">/</span><span style="color: #c20cb9; font-weight: bold;">kill</span> <span style="color: #660033;">-SIGUSR1</span> <span style="color: #000000; font-weight: bold;">`</span><span style="color: #c20cb9; font-weight: bold;">cat</span> <span style="color: #000000; font-weight: bold;">/</span>var<span style="color: #000000; font-weight: bold;">/</span>lib<span style="color: #000000; font-weight: bold;">/</span>mongo<span style="color: #000000; font-weight: bold;">/</span>mongod.lock <span style="color: #000000;">2</span><span style="color: #000000; font-weight: bold;">&gt;</span> <span style="color: #000000; font-weight: bold;">/</span>dev<span style="color: #000000; font-weight: bold;">/</span>null<span style="color: #000000; font-weight: bold;">`</span> <span style="color: #000000;">2</span><span style="color: #000000; font-weight: bold;">&gt;</span> <span style="color: #000000; font-weight: bold;">/</span>dev<span style="color: #000000; font-weight: bold;">/</span>null <span style="color: #000000; font-weight: bold;">||</span> <span style="color: #c20cb9; font-weight: bold;">true</span>
    endscript
<span style="color: #7a0874; font-weight: bold;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>

`logrotate` runs at 4 AM daily by default (via the script in `/etc/cron.daily/logrotate`). The file above rotates the mongod.log file daily, retaining 30 days of files, appending the date to each one after rotation (rather than the &#8220;.1&#8243; or &#8220;.2&#8243; suffix) and then gzipping it.