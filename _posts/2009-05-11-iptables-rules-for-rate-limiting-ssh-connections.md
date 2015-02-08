---
title: iptables rules for rate-limiting SSH connections
author: Evan Hoffman
layout: post
permalink: /2009/05/11/iptables-rules-for-rate-limiting-ssh-connections/
image:
  - 
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
dsq_thread_id:
  - 2951855848
categories:
  - Uncategorized
tags:
  - firewall
  - iptables
  - linux
  - networking
  - work
---
This is what I use on my CentOS boxes/VMs, it rate-limits the connections and also rate-limits the log messages (to prevent attacks that attempt to fill up the server&#8217;s disk).

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;">iptables <span style="color: #660033;">-F</span>
iptables <span style="color: #660033;">-X</span>
iptables <span style="color: #660033;">-N</span> LOGDROP <span style="color: #666666; font-style: italic;">#Create the LOGDROP chain</span>
iptables <span style="color: #660033;">-A</span> LOGDROP <span style="color: #660033;">-m</span> limit <span style="color: #660033;">--limit</span> <span style="color: #000000;">1</span><span style="color: #000000; font-weight: bold;">/</span>s <span style="color: #660033;">-j</span> LOG <span style="color: #660033;">--log-prefix</span> <span style="color: #ff0000;">"LOGDROP: "</span> <span style="color: #666666; font-style: italic;"># Rate-limit the logging so the logs don't fill up the server</span>
iptables <span style="color: #660033;">-A</span> LOGDROP <span style="color: #660033;">-j</span> DROP
iptables <span style="color: #660033;">-I</span> INPUT <span style="color: #660033;">-p</span> tcp <span style="color: #660033;">--dport</span> <span style="color: #000000;">22</span> <span style="color: #660033;">-s</span> 10.0.0.0<span style="color: #000000; font-weight: bold;">/</span><span style="color: #000000;">16</span> <span style="color: #660033;">-j</span> ACCEPT <span style="color: #666666; font-style: italic;"># Allow everything from the internal network</span>
iptables <span style="color: #660033;">-I</span> INPUT <span style="color: #660033;">-p</span> tcp <span style="color: #660033;">--dport</span> <span style="color: #000000;">22</span> <span style="color: #660033;">-i</span> eth0 <span style="color: #660033;">-m</span> state <span style="color: #660033;">--state</span> NEW <span style="color: #660033;">-m</span> recent <span style="color: #660033;">--set</span> <span style="color: #666666; font-style: italic;"># create the "bucket"</span>
iptables <span style="color: #660033;">-I</span> INPUT <span style="color: #660033;">-p</span> tcp <span style="color: #660033;">--dport</span> <span style="color: #000000;">22</span> <span style="color: #660033;">-i</span> eth0 <span style="color: #660033;">-m</span> state <span style="color: #660033;">--state</span> NEW <span style="color: #660033;">-m</span> recent <span style="color: #660033;">--update</span> <span style="color: #660033;">--seconds</span> <span style="color: #000000;">60</span> <span style="color: #660033;">--hitcount</span> <span style="color: #000000;">4</span> <span style="color: #660033;">-j</span> LOGDROP  <span style="color: #666666; font-style: italic;"># if there are more than 4 connection attempts in 60 seconds from a given address, log-drop it.</span></pre>
      </td>
    </tr>
  </table>
</div>

After issuing these commands I run `/etc/init.d/iptables save`, that persists the rules to &#8230; somewhere. Alternatively I sometimes put all the above commands in some bash script and just call it from /etc/rc.local.