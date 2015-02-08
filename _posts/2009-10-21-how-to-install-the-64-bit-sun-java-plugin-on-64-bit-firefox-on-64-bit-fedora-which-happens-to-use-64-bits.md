---
title: How to install the 64-bit Sun Java plugin on 64-bit firefox on 64-bit Fedora Core 11 Linux (which happens to use 64 bits)
author: Evan Hoffman
layout: post
permalink: /2009/10/21/how-to-install-the-64-bit-sun-java-plugin-on-64-bit-firefox-on-64-bit-fedora-which-happens-to-use-64-bits/
categories:
  - Uncategorized
tags:
  - 64
  - fc11
  - fc12
  - fc13
  - fc14
  - fc15
  - fedora
  - firefox
  - howto
  - java
  - jdk
  - jre
  - linux
  - plugin
  - sun java
---
I&#8217;m giddy! I found <a href="http://plugindoc.mozdev.org/linux-amd64.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://plugindoc.mozdev.org/linux-amd64.html', 'this post']);" >this post</a> on mozdev.org which was magical.

> <pre>[evan@ehoffman ~]$ java -version
java version "1.6.0_17"
Java(TM) SE Runtime Environment (build 1.6.0_17-b04)
Java HotSpot(TM) 64-Bit Server VM (build 14.3-b01, mixed mode)
</pre>

> <pre>[root@ehoffman plugins]# uname -a
Linux ehoffman 2.6.30.8-64.fc11.x86_64 #1 SMP Fri Sep 25 04:43:32 EDT 2009 x86_64 x86_64 x86_64 GNU/Linux
[root@ehoffman plugins]# pwd
/usr/lib64/mozilla/plugins
[root@ehoffman plugins]# ln -s /usr/java/jdk1.6.0_16/jre/lib/amd64/libnpjp2.so
</pre>

The main thing I was missing was that the plugin isn&#8217;t libpluginjava_oji.so, or whatever I thought it was, but libnpjp2.so. Once I created the symlink into /usr/lib64/mozilla/plugins it worked (as verified on <a href="http://www.java.com/en/download/help/testvm.xml" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.java.com/en/download/help/testvm.xml', 'http://www.java.com/en/download/help/testvm.xml']);" >http://www.java.com/en/download/help/testvm.xml</a> and <a href="http://www.java.com/en/download/installed.jsp" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.java.com/en/download/installed.jsp', 'http://www.java.com/en/download/installed.jsp']);" >http://www.java.com/en/download/installed.jsp</a>).

That&#8217;s all it takes to get the Sun Java plugin working in Firefox on Linux.