---
title: Slow HTTP downloads through Cisco ASA 5500
author: Evan Hoffman
layout: post
permalink: /2012/02/28/slow-http-downloads-through-cisco-asa-5500/
image:
  - 
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
dsq_thread_id:
  - 2949526092
categories:
  - Uncategorized
tags:
  - 5500
  - asa
  - cisco
  - curl
  - devops
  - disable
  - download
  - http
  - inspect
  - linux
  - network
  - router
  - slow
  - speed
---
Recently we noticed weird behavior downloading files from certain sites. The transfer would start out fast (around 10 MB/s), then after a couple of seconds it would plummet to around 9 KB/s. It didn&#8217;t happen for every file or every site: downloads from S3 buckets were still particularly fast. But some files that I remember being particularly fast were now showing this weird fast/slow/fast/slow behavior, for example the <a href="http://download.oracle.com/otn-pub/java/jdk/6u25-b06/jdk-6u25-linux-i586-rpm.bin" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://download.oracle.com/otn-pub/java/jdk/6u25-b06/jdk-6u25-linux-i586-rpm.bin', 'Sun JDK']);" >Sun JDK</a> and ISOs from <a href="http://mirrors.rit.edu/centos/6/isos/x86_64/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://mirrors.rit.edu/centos/6/isos/x86_64/', 'rit.edu']);" >rit.edu</a> that used to saturate our pipe were now getting all cRAzY.

After some poking around I decided to test HTTP versus FTP to see if it could be an application/protocol-level issue. The easiest way to do this was to find a file available via both FTP and HTTP and download it via both protocols. This is where mirrors.rit.edu came in handy. I used cURL to download it and noticed that via HTTP it was much slower than over FTP:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #7a0874; font-weight: bold;">&#91;</span>evan<span style="color: #000000; font-weight: bold;">@</span>boba <span style="color: #000000;">16</span>:07:03 ~<span style="color: #7a0874; font-weight: bold;">&#93;</span>$ curl <span style="color: #660033;">-O</span> ftp:<span style="color: #000000; font-weight: bold;">//</span>mirrors.rit.edu<span style="color: #000000; font-weight: bold;">/</span>pub<span style="color: #000000; font-weight: bold;">/</span>centos<span style="color: #000000; font-weight: bold;">/</span><span style="color: #000000;">6</span><span style="color: #000000; font-weight: bold;">/</span>isos<span style="color: #000000; font-weight: bold;">/</span>x86_64<span style="color: #000000; font-weight: bold;">/</span>CentOS-<span style="color: #000000;">6.2</span>-x86_64-netinstall.iso
  <span style="color: #000000; font-weight: bold;">%</span> Total    <span style="color: #000000; font-weight: bold;">%</span> Received <span style="color: #000000; font-weight: bold;">%</span> Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
<span style="color: #000000;">100</span>  227M  <span style="color: #000000;">100</span>  227M    <span style="color: #000000;"></span>     <span style="color: #000000;"></span>   9.8M      <span style="color: #000000;"></span>  <span style="color: #000000;"></span>:00:<span style="color: #000000;">22</span>  <span style="color: #000000;"></span>:00:<span style="color: #000000;">22</span> --:--:-- 7816k
<span style="color: #7a0874; font-weight: bold;">&#91;</span>evan<span style="color: #000000; font-weight: bold;">@</span>boba <span style="color: #000000;">16</span>:07:<span style="color: #000000;">33</span> ~<span style="color: #7a0874; font-weight: bold;">&#93;</span>$ <span style="color: #c20cb9; font-weight: bold;">rm</span> CentOS-<span style="color: #000000;">6.2</span>-x86_64-netinstall.iso 
<span style="color: #7a0874; font-weight: bold;">&#91;</span>evan<span style="color: #000000; font-weight: bold;">@</span>boba <span style="color: #000000;">16</span>:07:<span style="color: #000000;">39</span> ~<span style="color: #7a0874; font-weight: bold;">&#93;</span>$ curl <span style="color: #660033;">-O</span> http:<span style="color: #000000; font-weight: bold;">//</span>mirrors.rit.edu<span style="color: #000000; font-weight: bold;">/</span>centos<span style="color: #000000; font-weight: bold;">/</span><span style="color: #000000;">6</span><span style="color: #000000; font-weight: bold;">/</span>isos<span style="color: #000000; font-weight: bold;">/</span>x86_64<span style="color: #000000; font-weight: bold;">/</span>CentOS-<span style="color: #000000;">6.2</span>-x86_64-netinstall.iso
  <span style="color: #000000; font-weight: bold;">%</span> Total    <span style="color: #000000; font-weight: bold;">%</span> Received <span style="color: #000000; font-weight: bold;">%</span> Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
<span style="color: #000000;">100</span>  227M  <span style="color: #000000;">100</span>  227M    <span style="color: #000000;"></span>     <span style="color: #000000;"></span>  5686k      <span style="color: #000000;"></span>  <span style="color: #000000;"></span>:00:<span style="color: #000000;">40</span>  <span style="color: #000000;"></span>:00:<span style="color: #000000;">40</span> --:--:-- 6269k</pre>
      </td>
    </tr>
  </table>
</div>

22 seconds via FTP at 9.8MB/s average, 40 seconds over HTTP at 5.6 MB/s average (which was one of the better HTTP runs).

This was affecting all machines on our network, and had nothing to do with the per-machine iptables rules (verified by flushing all rules). The only thing I could think of that might affect all machines, but only HTTP and not FTP would be something like packet inspection. Well, turns out that <a href="http://www.cisco.com/en/US/docs/security/asa/asa82/configuration/guide/inspect_basic.html#wp1514315" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.cisco.com/en/US/docs/security/asa/asa82/configuration/guide/inspect_basic.html#wp1514315', 'http packet inspection']);" >http packet inspection</a> is on by default on the ASA. So I disabled it as described <a href="http://www.cisco.com/en/US/products/hw/vpndevc/ps2030/products_tech_note09186a008085283d.shtml#disab" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.cisco.com/en/US/products/hw/vpndevc/ps2030/products_tech_note09186a008085283d.shtml#disab', 'here']);" >here</a>:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;">Zeus<span style="color: #7a0874; font-weight: bold;">&#40;</span>config<span style="color: #7a0874; font-weight: bold;">&#41;</span><span style="color: #666666; font-style: italic;"># conf t</span>
Zeus<span style="color: #7a0874; font-weight: bold;">&#40;</span>config<span style="color: #7a0874; font-weight: bold;">&#41;</span><span style="color: #666666; font-style: italic;"># policy-map global_policy</span>
Zeus<span style="color: #7a0874; font-weight: bold;">&#40;</span>config-pmap<span style="color: #7a0874; font-weight: bold;">&#41;</span><span style="color: #666666; font-style: italic;"># class inspection_default</span>
Zeus<span style="color: #7a0874; font-weight: bold;">&#40;</span>config-pmap-c<span style="color: #7a0874; font-weight: bold;">&#41;</span><span style="color: #666666; font-style: italic;"># no inspect http</span>
Zeus<span style="color: #7a0874; font-weight: bold;">&#40;</span>config-pmap-c<span style="color: #7a0874; font-weight: bold;">&#41;</span><span style="color: #666666; font-style: italic;"># write mem</span>
Building configuration...</pre>
      </td>
    </tr>
  </table>
</div>

Since then HTTP transfers have been consistently fast.