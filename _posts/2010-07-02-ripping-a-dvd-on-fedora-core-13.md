---
title: Ripping a DVD on Fedora Core 13
author: Evan Hoffman
layout: post
permalink: /2010/07/02/ripping-a-dvd-on-fedora-core-13/
dsq_thread_id:
  - 3074241043
categories:
  - Uncategorized
tags:
  - avi
  - fc13
  - fedora
  - iphone
  - libdvdcss
  - linux
  - mp4
  - mpeg
  - vob
---
I thought this would be a pretty straightforward task. It was, though it took several minutes to track down the CSS library needed for decryption of the DVD. All I want to do is copy <a href="http://astore.amazon.com/evanhoffmasho-20/detail/B001U7NW20" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://astore.amazon.com/evanhoffmasho-20/detail/B001U7NW20', 'Talking Words Factory']);" >Talking Words Factory</a> to my phone so my kid can watch it in the car if needed. It looked like Brasero was the tool for the job, but I got some error about needing a plugin for it to be able to decrypt it. I found <a href="http://ftp-stud.fht-esslingen.de/pub/Mirrors/rpm.livna.org/repo/13/x86_64/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://ftp-stud.fht-esslingen.de/pub/Mirrors/rpm.livna.org/repo/13/x86_64/', 'libdvdcss here']);" >libdvdcss here</a> and after installing the RPM, everything worked. Now I just need to encode it with ffmpeg.