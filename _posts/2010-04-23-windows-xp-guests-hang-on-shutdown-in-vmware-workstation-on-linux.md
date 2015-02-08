---
title: Windows XP guests hang on shutdown in VMware Workstation on Linux
author: Evan Hoffman
layout: post
permalink: /2010/04/23/windows-xp-guests-hang-on-shutdown-in-vmware-workstation-on-linux/
dsq_thread_id:
  - 2949518185
categories:
  - Uncategorized
tags:
  - guest
  - hangs
  - host
  - kill
  - linux
  - rpm
  - vmware
  - vmware-vmx
  - windows xp
  - yum
---
I had this problem with FC11, where I couldn&#8217;t properly shut down or suspend the Windows XP VM I run (mostly for Outlook). When I&#8217;d shutdown inside the guest, it would mostly do what you&#8217;d expect, but then the screen would go blue, then black, and then the only way I could get the thing to exit was to kill the pid of vmware-vmx (or a &#8216;killall vmware-vmx&#8217;). I solved it somehow by removing some RPM, but when I went to FC12 the other day it came back, and I couldn&#8217;t remember how I&#8217;d fixed it initially. I Googled for nearly a full day before I found the original blog post that told me which RPMs to remove &#8211; for some reason I thought it was fprintd, but it was something else completely. <a href="http://marksmanuk.blogspot.com/2009/08/vmware-hang-on-shutdown-fedora-11.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://marksmanuk.blogspot.com/2009/08/vmware-hang-on-shutdown-fedora-11.html', 'Here&#8217;s the original post']);" >Here&#8217;s the original post</a>, and when I just read it I was going to comment on it to say thanks for posting it, but apparently I did that already. Anyway, I removed pcsc-lite again and everything appears to be good now.