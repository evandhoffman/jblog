---
title: 'HOWTO: Install Flash plugin on Firefox on Fedora / CentOS / RedHat Linux'
author: Evan Hoffman
layout: post
permalink: /2010/06/09/howto-install-flash-plugin-on-firefox-on-fedora-centos-redhat-linux/
dsq_thread_id:
  - 2949525360
categories:
  - Uncategorized
tags:
  - audio
  - centos
  - fedora
  - firefox
  - flash
  - howto
  - linux
  - redhat
  - x86_64
  - yum
---
<ins datetime="2011-08-31T19:31:51+00:00">Updated 8/31/2011</ins> See my <a href="http://www.evanhoffman.com/evan/?p=1583" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=1583', 'newer post']);" >newer post</a> on this subject.

I know I&#8217;ve been through this before because I&#8217;ve setup Fedora before and it&#8217;s working. But I just installed FC12 on my desktop at home (since my Seagate drive bricked itself a few days ago and I had to reinstall anyway) and while I finally got the Adobe Flash plugin working, it didn&#8217;t have any audio, which made YouTube kind of suck. Anyway, this of course was a solved problem, I just forgot where the fix was located&#8230; but duh, it&#8217;s <a href="http://fedoraproject.org/wiki/Multimedia/Flash#On_64-bit_Fedora" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://fedoraproject.org/wiki/Multimedia/Flash#On_64-bit_Fedora', 'on the Fedora website']);" >on the Fedora website</a>. A simple

<pre>yum install flash-plugin nspluginwrapper.x86_64 \
    nspluginwrapper.i686 alsa-plugins-pulseaudio.i686 \
    libcurl.i686</pre>

and everything appears to be fine. Thank you Fedora!