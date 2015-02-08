---
title: Upgraded to Fedora Core 12
author: Evan Hoffman
layout: post
permalink: /2010/04/20/upgraded-to-fedora-core-12/
categories:
  - Uncategorized
tags:
  - computers
  - fc12
  - fedora
  - linux
  - radeon
  - work
---
I upgraded my work laptop from FC11 to FC12 yesterday using the &#8220;preupgrade&#8221; tool. It was pretty simple, though it took a lot longer than I expected. There was some funkiness with my screen going crazy after the upgrade &#8211; my external monitor and the laptop&#8217;s LCD both did this crazy wavy-line thing. I tried changing the refresh rate, running system-config-display, nothing worked. I found a post that suggested passing &#8220;nomodeset&#8221; to the kernel boot options &#8211; that solved it. Yay!

The other problem I had was reinstalling VMware Workstation &#8211; I couldn&#8217;t. I got this error: **/tmp/vmware-root/modules/vmnet-only/vnetUserListener.c:240: error: ‘TASK_INTERRUPTIBLE’ **&#8230; etc. I ended up having to edit the vmplayer source files directly (!!!) to get them to compile &#8211; instructions found <a href="http://osvn.pastebin.com/hPpCZEXe" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://osvn.pastebin.com/hPpCZEXe', 'here']);" >here</a>

So far FC12 seems exactly like FC11. But that&#8217;s fine &#8211; I only upgraded because I didn&#8217;t want to be on a dead-end version once FC13 is released.