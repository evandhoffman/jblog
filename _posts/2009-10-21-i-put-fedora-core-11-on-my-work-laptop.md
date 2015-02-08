---
title: I put Fedora Core 11 on my work laptop
author: Evan Hoffman
layout: post
permalink: /2009/10/21/i-put-fedora-core-11-on-my-work-laptop/
categories:
  - Uncategorized
tags:
  - centos
  - fedora
  - laptop
  - linux
  - windows 7
---
My work laptop, which I got around the end of 2006, was starting to run like crap. Pretty sure it started around the time I put antivirus on it, which reinforces my theory that antivirus is a virus itself. Anyway, being bored with Windows XP and having already played with Windows 7 at home, I decided to install Linux. The last time I tried Linux desktop was around RedHat 7.3, which was a long time ago&#8230; pre-Fedora even. I&#8217;m pretty happy with CentOS on the server side and was set to try CentOS 5.3 on my laptop when someone suggested I go with FC due to more bleeding-edge driver support. Even though the hardware is pretty old at this point I figured it was worth a shot.

Generally everything works pretty well. Everything pretty much works out of the box &#8211; I had dual screens up at native resolution, it even recognized my phone as a camera when I plugged it in to charge it. Best of all, I was able to resize the NTFS partition (rather than blowing it away) so I can still boot back to XP when the need arises.

There are 2 problems I&#8217;ve had so far though:

  * the computer now hovers between 76 and 85 degrees Celsius, whereas under Win XP it generally peaked around 68 C. I tried underclocking the CPU down to 250 MHz but the problem persisted.
  * The Java plugin for Firefox &#8230; wtf? How can this not work? I downloaded Sun&#8217;s JDK and symlinked the libjavaplugin_whatever into /usr/lib64/mozilla/whatever/ and that didn&#8217;t work. Then I tried &#8220;yum install java-plugin&#8221; or something like that and that installed some OpenJava-ish plugin which looked like it was going to work, but when I logged into the Raritan KVM and clicked &#8220;Connect&#8221; it wouldn&#8217;t work. I booted up Win XP in a VM and Firefox with Java works fine. Really annoying and dumb.

I&#8217;ll probably try Ubuntu tomorrow as several people suggested it runs cooler than Fedora. If that fails then I guess I&#8217;ll go to Win7 after all. I also want an SSD for my work laptop now&#8230; my Windows VM seems to spent eternity spinning my crappy 5400 rpm drive. <a href="http://www.newegg.com/Product/Product.aspx?Item=N82E16820233087" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.newegg.com/Product/Product.aspx?Item=N82E16820233087', 'Plox']);" >Plox</a>.