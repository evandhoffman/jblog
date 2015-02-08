---
title: 'Fedora Core 15 &#038; Gnome 3'
author: Evan Hoffman
excerpt: Shrinking partitions, installing/upgrading to FC15, and systemd/chkconfig.
layout: post
permalink: /2011/09/23/fedora-core-15-gnome-3/
image:
  - 
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
dsq_thread_id:
  - 2949520339
categories:
  - Uncategorized
tags:
  - autostart
  - chkconfig
  - devops
  - fc12
  - fc15
  - fedora
  - linux
  - personal
  - preupgrade
  - samba
  - service
  - smb
  - systemctl
  - systemd
---
Over a year ago, the hard drive in my primary desktop at home bricked itself and rather than going through the hassle of reinstalling Win7 on the new disk, I decided to go with FC12. I&#8217;ve been pretty happy with it in general, since I&#8217;ve always been partial to Red Hat and use CentOS primarily at work.  
<!--more-->

  
Last week I got the great idea to upgrade to FC14. In hindsight I can&#8217;t even recall what led me to try this, but it didn&#8217;t end well. I tried the &#8220;preupgrade&#8221; procedure, which appeared to do the entire upgrade from FC12 to FC14 in place. I left it overnight, and when I looked at it the next day it looked like it was done. I was in FC14 and everything looked ok. But then I tried syncing my photos over NFS and discovered nfs wasn&#8217;t running on my desktop. When I tried starting it, it failed. After some trial and error, I used the Google and found that <a href="http://forums.fedoraforum.org/showthread.php?t=264182" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://forums.fedoraforum.org/showthread.php?t=264182', 'this is just what happens when upgrading to FC14']);" >this is just what happens when upgrading to FC14</a> due to changes between FC12 and FC14, namely the introduction of <a href="http://fedoraproject.org/wiki/Systemd" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://fedoraproject.org/wiki/Systemd', 'systemd']);" >systemd</a>.

In all the threads I read, the &#8220;solution&#8221; was a clean install of Fedora. I tried doing this without formatting my / (root) partition, since that had 500 gigs of my stuff on it, but it kept failing. What I ended up doing was downloading <a href="http://partedmagic.com/doku.php?id=start" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://partedmagic.com/doku.php?id=start', 'partedmagic']);" >partedmagic</a>, which is a totally awesome partitioning tool. If you&#8217;re familiar with Partition Magic, this is similar but Linux-based and free. I burned the iso to disc, booted to it, and shrunk my / partition from 900 GB to 850 GB, and created a new 50 GB partition at the end of my disk without losing any of my data:<figure id="attachment_1619" style="width: 300px;" class="wp-caption aligncenter">

<a href="http://www.evanhoffman.com/evan/2011/09/23/fedora-core-15-gnome-3/parted-20110923/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/2011/09/23/fedora-core-15-gnome-3/parted-20110923/', '']);"  rel="attachment wp-att-1619"><img class="size-medium wp-image-1619" title="FC15 Partitions" src="http://www.evanhoffman.com/evan/wp-content/uploads/2011/09/parted-20110923-300x195.png" alt="FC15 Partitions" width="300" height="195" /></a><figcaption class="wp-caption-text">FC15 Partitions</figcaption></figure> 

Once this was done, which took surprisingly little time, I did a net install of FC15. I opted for a net install rather than downloading the ISO because I feel that with FiOS it&#8217;s actually faster than reading a DVD, and avoids having to run &#8220;yum update&#8221; afterwards.

So, I ended up with FC15 clean-installed to the new &#8220;/&#8221; partition. I moved everything around so the old partition is mounted at /docs and has all my stuff in it. I&#8217;d heard that FC15 was causing an uproar but until I logged into Gnome 3 myself I didn&#8217;t really understand the fuss. It&#8217;s going to take some getting used to, but after adding the <a href="http://code.google.com/p/tint2/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://code.google.com/p/tint2/', 'tint2']);" >tint2</a> taskbar it&#8217;s not too awful.

But back to systemd. In FC15 I wanted to ensure Samba started at boot, since that&#8217;s how I share docs between my VMs and host. Chkconfig is still there, but based on my problems with NFS and systemd on FC14 I decided to look into it a bit and see if there&#8217;s a &#8220;new&#8221; way to enable stuff at startup. <a href="http://fedoraproject.org/wiki/Systemd#How_do_I_start.2Fstop_or_enable.2Fdisable_services.3F" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://fedoraproject.org/wiki/Systemd#How_do_I_start.2Fstop_or_enable.2Fdisable_services.3F', 'There is!']);" >There is!</a>

Instead of:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #666666;"># </span>chkconfig smb on</pre>
      </td>
    </tr>
  </table>
</div>

The command is:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #666666;"># </span>systemctl <span style="color: #7a0874; font-weight: bold;">enable</span> smb.service</pre>
      </td>
    </tr>
  </table>
</div>

Of course, when I did this it apparently fell back to using chkconfig for smb:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #7a0874; font-weight: bold;">&#91;</span>root<span style="color: #000000; font-weight: bold;">@</span>evan-fedora ~<span style="color: #7a0874; font-weight: bold;">&#93;</span><span style="color: #666666; font-style: italic;"># systemctl enable smb.service</span>
smb.service is not a native service, redirecting to <span style="color: #000000; font-weight: bold;">/</span>sbin<span style="color: #000000; font-weight: bold;">/</span>chkconfig.
Executing <span style="color: #000000; font-weight: bold;">/</span>sbin<span style="color: #000000; font-weight: bold;">/</span>chkconfig smb on</pre>
      </td>
    </tr>
  </table>
</div>

It does say in the <a href="http://fedoraproject.org/wiki/Systemd#Does_chkconfig_command_work_with_systemd.3F" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://fedoraproject.org/wiki/Systemd#Does_chkconfig_command_work_with_systemd.3F', 'Fedora wiki']);" >Fedora wiki</a> that systemd respects chkconfig and vice versa, so I guess this post is kind of pointless and I should have just linked to the wiki. But, whatever.