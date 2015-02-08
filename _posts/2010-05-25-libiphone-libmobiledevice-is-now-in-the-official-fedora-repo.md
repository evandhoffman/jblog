---
title: libiphone / libmobiledevice is now in the official Fedora repo!
author: Evan Hoffman
layout: post
permalink: /2010/05/25/libiphone-libmobiledevice-is-now-in-the-official-fedora-repo/
dsq_thread_id:
  - 2950664740
categories:
  - Uncategorized
tags:
  - iphone
  - libiphone
  - linux
---
Hooray! This was strange though:

<!--more-->

<pre>[root@ehoffman yum.repos.d]# yum install libmobiledevice
Loaded plugins: refresh-packagekit
Setting up Install Process
<b>No package libmobiledevice available.</b>
Nothing to do
</pre>

<pre>[root@ehoffman yum.repos.d]# yum install libiphone libiphone-devel ifuse
Loaded plugins: refresh-packagekit
Setting up Install Process
Package libiphone is obsoleted by libimobiledevice, trying to install
libimobiledevice-1.0.1-1.fc12.i686 instead

...

================================================================================
 Package                     Arch        Version             Repository    Size
================================================================================
Installing:
 ifuse                       x86_64      1.0.0-1.fc12        updates       20 k
<b> libimobiledevice            i686        1.0.1-1.fc12        updates       73 k</b>
 libimobiledevice-devel      x86_64      1.0.1-1.fc12        updates       59 k
</pre>

&#8220;There&#8217;s no such thing as libmobiledevice&#8230; no wait&#8230; there it is.&#8221; <a href="http://libimobiledevice.org/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://libimobiledevice.org/', 'http://libimobiledevice.org/']);" >http://libimobiledevice.org/</a>

Edit: I see it now &#8211; it&#8217;s lib**i**mobiledevice.