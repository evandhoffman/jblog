---
title: 'Windows 7 RC &#8211; day 1'
author: Evan Hoffman
layout: post
permalink: /2009/07/12/windows-7-rc-day-1/
categories:
  - Uncategorized
tags:
  - computers
  - windows 7
---
So I&#8217;ve been having problems with my computer since I built it. In particular the screen will freeze up from time to time with this checkerboard of black and normal. I can&#8217;t really describe it, and since the computer&#8217;s frozen when it happens, I can&#8217;t screenshot it. Anyway, I reinstalled XP a while ago and that didn&#8217;t do anything to solve it.

<!--more-->

On Thursday my computer started really pissing me off because when I plugged in my camera, the Canon CameraWindow app that I use to transfer stuff off wasn&#8217;t autostarting. When I then tried starting it manually it still wouldn&#8217;t start. When I opened &#8220;My Computer&#8221; what greeted me was a flashlight, apparently searching for the innards of my system. It searched for about 60 seconds before showing me the correct contents of the computer, but when I closed My Computer and reopened it, it did the same thing. This persisted through a reboot. So I had a bad feeling that maybe the computer was about to do something unpleasant.

This probably doesn&#8217;t sound like enough of a reason to ditch the whole OS and install a beta as a primary OS, but I was already kind of sick of running a 32-bit OS on 64-bit hardware; my computer has 4 GB of memory and XP was only able to see 3.25. So I resized my 1 TB partition down to 500 GB and installed build 7100 of Windows 7 in the free space. I assume this will allow me to boot back to XP when I want to, but I haven&#8217;t tried it.

Anyway, the install went fine &#8212; my computer&#8217;s performance score is only 5.9 out of 7.9, I guess due to disk performance &#8212; though it did spend a lot of time doing absolutely nothing, which made me think it was hanging. I&#8217;m not crazy about this taskbar, and I miss the Quick Launch bar from XP. Also not a fan of the Program selector in the Start menu. I&#8217;ve never liked &#8220;personalized&#8221; menus; I like to know where something is always going to be rather than having the computer populate a list for convenience. There are some other minor annoyances but overall it seems pretty fast &#8211; Firefox started up very anyway, and that&#8217;s probably the biggest thing I&#8217;ve installed so far.

One problem I ran into was trying to connect to my old Linux box via Samba. It wasn&#8217;t accepting my password no matter what I did; I reset it on the server and it still wasn&#8217;t working. A quick Google search turned up a solution: <a href="http://www.builderau.com.au/blogs/codemonkeybusiness/viewblogpost.htm?p=339270746" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.builderau.com.au/blogs/codemonkeybusiness/viewblogpost.htm?p=339270746', 'Get Vista and Samba to work']);" >Get Vista and Samba to work</a>. This post is from 2006 and it&#8217;s now 2009 and it&#8217;s still a problem, so I guess Microsoft doesn&#8217;t consider it a problem. My Samba installation is way older than that, though, so maybe it&#8217;s more of a Samba issue than a Vista one.

The other thing, which wasn&#8217;t as much of a problem as something that just sucked, is I had to reauthorize my computer in iTunes. I copied over all my settings &#8211; <a href="http://www.somelifeblog.com/2007/07/vista-transfer-itunes-library-and.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.somelifeblog.com/2007/07/vista-transfer-itunes-library-and.html', 'this page']);" >this page</a> explained what goes where &#8211; but I still had to reauthorize the computer. I guess I could have dug further, but I just assumed that going from Windows 5.1 to 6.1 and 32-bit to 64-bit, there&#8217;s no way to &#8220;trick&#8221; it. This is only my 2nd authorized computer though so it&#8217;s not a big deal&#8230; just annoying, since it&#8217;s not a second computer.

Anyway, back to my original problem: the camera now works as expected. That reminds me of another problem I had &#8211; I couldn&#8217;t run my Java program to reorganize the pics for some reason. It turned out that even though I had a 64-bit JVM installed, and Java is supposedly platform independent, I need to download <a href="http://www.eclipse.org/downloads/download.php?file=/eclipse/downloads/drops/R-3.5-200906111540/eclipse-SDK-3.5-win32-x86_64.zip" onclick="_gaq.push(['_trackEvent','download','http://www.eclipse.org/downloads/download.php?file=/eclipse/downloads/drops/R-3.5-200906111540/eclipse-SDK-3.5-win32-x86_64.zip']);" >64-bit Eclipse</a>. Then I just had to hunt down the old jars and put them back in the classpath of my project and that worked well.

So overall I&#8217;m pleased with the speed and responsiveness, and the Aero stuff is pretty, but I don&#8217;t *get* the taskbar changes.