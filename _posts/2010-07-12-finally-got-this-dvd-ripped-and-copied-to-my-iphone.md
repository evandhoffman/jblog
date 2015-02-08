---
title: Finally got this DVD ripped and copied to my iPhone
author: Evan Hoffman
layout: post
permalink: /2010/07/12/finally-got-this-dvd-ripped-and-copied-to-my-iphone/
categories:
  - Uncategorized
tags:
  - aac
  - dvd
  - dvdrip
  - encode
  - ffmpeg
  - iphone
  - itunes
  - libfaac
  - linux
  - movie
  - streamdvd
  - winff
---
This was a lot more work than I expected. Then again, I am a newb. And lazy.  
<!--more-->

  * <tt>yum install streamdvd</tt> &#8211; This is what I used to rip the DVD to a VOB file. I played around with a few other utils, including vobcopy and dvd::rip, but this was the only one I tried that ripped the DVD to a single VOB file AND started the movie at the right place. I&#8217;m sure there&#8217;s other ways to do it, but <tt>streamdvd > whatever.vob</tt> worked well for me.</p> 
      * You need to recompile ffmpeg with AAC support. I followed <a href="http://fozzolog.fozzilinymoo.org/tech/2009/11/recompiling-ffmpeg-for-fedora-12-to-add-faac-support.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://fozzolog.fozzilinymoo.org/tech/2009/11/recompiling-ffmpeg-for-fedora-12-to-add-faac-support.html', 'this great walkthrough']);" >this great walkthrough</a>; you need to install a bunch of libs but when I was done I ended up ffmpeg supporting everything I needed: 
        `[evan@evanfc12 ~]$ ffmpeg -version<br />
FFmpeg version SVN-r22963, Copyright (c) 2000-2010 the FFmpeg developers<br />
  built on Jul 11 2010 11:48:47 with gcc 4.4.3 20100127 (Red Hat 4.4.3-4)<br />
  configuration: --prefix=/usr --bindir=/usr/bin --datadir=/usr/share/ffmpeg --incdir=/usr/include/ffmpeg --libdir=/usr/lib64 --mandir=/usr/share/man --arch=x86_64 --extra-cflags='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector --param=ssp-buffer-size=4 -m64 -mtune=generic' --extra-version=rpmfusion --enable-bzlib --enable-libdc1394 --enable-libdirac --enable-libfaac --enable-nonfree --enable-libfaad --enable-libgsm --enable-libmp3lame --enable-libopenjpeg --enable-libschroedinger --enable-libspeex --enable-libtheora --enable-libvorbis --enable-libx264 --enable-libxvid --enable-x11grab --enable-avfilter --enable-avfilter-lavf --enable-postproc --enable-pthreads --disable-static --enable-shared --enable-gpl --disable-debug --disable-stripping --shlibdir=/usr/lib64 --enable-runtime-cpudetect<br />
  libavutil     50.14. 0 / 50.14. 0<br />
  libavcodec    52.66. 0 / 52.66. 0<br />
  libavformat   52.61. 0 / 52.61. 0<br />
  libavdevice   52. 2. 0 / 52. 2. 0<br />
  libavfilter    1.19. 0 /  1.19. 0<br />
  libswscale     0.10. 0 /  0.10. 0<br />
  libpostproc   51. 2. 0 / 51. 2. 0<br />
FFmpeg SVN-r22963<br />
libavutil     50.14. 0 / 50.14. 0<br />
libavcodec    52.66. 0 / 52.66. 0<br />
libavformat   52.61. 0 / 52.61. 0<br />
libavdevice   52. 2. 0 / 52. 2. 0<br />
libavfilter    1.19. 0 /  1.19. 0<br />
libswscale     0.10. 0 /  0.10. 0<br />
libpostproc   51. 2. 0 / 51. 2. 0<br />
[evan@evanfc12 ~]$ `
        
          * Used WinFF to encode the VOB file to .m4v, with these preset settings (conveniently included in the Windows version of WinFF):  
            > -r 29.97 -vcodec libx264 -flags +loop -cmp +chroma -deblockalpha 0 -deblockbeta 0 -crf 21 -bt 256k -refs 1 -coder 0 -me\_method full -me\_range 16 -subq 5 -partitions +parti4x4+parti8x8+partp8x8 -g 250 -keyint\_min 25 -level 30 -trellis 2 -sc\_threshold 40 -i_qfactor 0.71 -s 320&#215;240 -aspect 4:3 -acodec libfaac -ab 112kb -ar 48000 -ac 2 -vpre default</ul> 
            
            That created the .m4v, I loaded it into iTunes and it synced to the phone and plays perfectly. Hooray!