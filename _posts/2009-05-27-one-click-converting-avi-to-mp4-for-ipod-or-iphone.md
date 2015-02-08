---
title: One-click converting .avi to .mp4 for iPod or iPhone
author: Evan Hoffman
excerpt: "I found a way to convert my camera's .avi files to iPod and iPhone-compatible .mp4 files with minimal effort.  The .mp4 files are also significantly smaller."
layout: post
permalink: /2009/05/27/one-click-converting-avi-to-mp4-for-ipod-or-iphone/
image:
  - 
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
dsq_thread_id:
  - 3181798203
categories:
  - Uncategorized
tags:
  - avi
  - conversion
  - convert
  - encode
  - ffmpeg
  - ffmpeg.exe
  - howto
  - iphone
  - ipod
  - mp4
  - mpeg
  - rip
  - video
  - vob
---
After a lot of searching for an easy way to do this, I stumbled onto <a href="http://winff.org/html/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://winff.org/html/', 'WinFF']);" >WinFF</a>, which is a gui wrapper around <a href="http://www.ffmpeg.org/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.ffmpeg.org/', 'ffmpeg']);" >ffmpeg</a>, a GPL&#8217;d MPEG encoder. It worked pretty well out of the box, but for all of the AVIs I&#8217;d recorded with my old camera (Canon Powershot A540) it would fail immediately with this error:

<pre>Audio resampler only works with 16 bits per sample. patch welcome.</pre>

After some searching it seems this is related to a <a href="https://roundup.mplayerhq.hu/roundup/ffmpeg/issue582" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://roundup.mplayerhq.hu/roundup/ffmpeg/issue582', 'known bug']);" >known bug</a> which was apparently fixed in February 2009. I set out to find a newer ffmpeg.exe (which is what WinFF calls to do the actual conversion). I found some Win32 builds of ffmpeg <a href="http://ffmpeg.arrozcru.org/builds/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://ffmpeg.arrozcru.org/builds/', 'here']);" >here</a> but found that they all claimed to not know the &#8220;libfaac&#8221; codec. After some more digging I learned that libfaac (the audio codec for MP4 for iPod/iPhone) is no longer considered free software and was dropped from the repository in mid-April. Fortunately there were <a href="http://ffmpeg.arrozcru.org/autobuilds/ffmpeg/mingw32/static/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://ffmpeg.arrozcru.org/autobuilds/ffmpeg/mingw32/static/', 'older builds available']);" >older builds available</a>, and I grabbed <a href="http://ffmpeg.arrozcru.org/autobuilds/ffmpeg/mingw32/static/ffmpeg-r18306-swscale-r29124-mingw32-static.tar.bz2" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://ffmpeg.arrozcru.org/autobuilds/ffmpeg/mingw32/static/ffmpeg-r18306-swscale-r29124-mingw32-static.tar.bz2', 'the one dated 2009-04-01']);" >the one dated 2009-04-01</a>, extracted the ffmpeg.exe into my WinFF directory and voila, it worked.

Old ffmpeg.exe:

<pre>C:\Documents and Settings\Evan>"C:\Program Files\WinFF\ffmpeg.exe" -version
FFmpeg version SVN-r15986, Copyright (c) 2000-2008 Fabrice Bellard, et al.
  configuration: --extra-cflags=-fno-common --enable-memalign-hack --enable-pthr
eads --enable-libmp3lame --enable-libxvid --enable-libvorbis --enable-libtheora
--enable-libspeex --enable-libfaac --enable-libgsm --enable-libx264 --enable-lib
schroedinger --enable-avisynth --enable-swscale --enable-gpl
  libavutil     49.12. 0 / 49.12. 0
  libavcodec    52. 6. 0 / 52. 6. 0
  libavformat   52.23. 1 / 52.23. 1
  libavdevice   52. 1. 0 / 52. 1. 0
  libswscale     0. 6. 1 /  0. 6. 1
  built on Dec  3 2008 01:59:37, gcc: 4.2.4
FFmpeg SVN-r15986
libavutil     49.12. 0 / 49.12. 0
libavcodec    52. 6. 0 / 52. 6. 0
libavformat   52.23. 1 / 52.23. 1
libavdevice   52. 1. 0 / 52. 1. 0
libswscale     0. 6. 1 /  0. 6. 1</pre>

New ffmpeg.exe:

<pre>C:\Documents and Settings\Evan>"C:\Program Files\WinFF\ffmpeg.exe" -version
FFmpeg version SVN-r18306, Copyright (c) 2000-2009 Fabrice Bellard, et al.
  configuration: --enable-memalign-hack --prefix=/mingw --cross-prefix=i686-ming
w32- --cc=ccache-i686-mingw32-gcc --target-os=mingw32 --arch=i686 --cpu=i686 --e
xtra-cflags=-fno-common --enable-avisynth --enable-gpl --enable-zlib --enable-bz
lib --enable-libgsm --enable-libfaac --enable-pthreads --enable-libvorbis --enab
le-libmp3lame --enable-libopenjpeg --enable-libtheora --enable-libspeex --enable
-libxvid --enable-libfaad --enable-libschroedinger --enable-libx264
  libavutil     50. 2. 0 / 50. 2. 0
  libavcodec    52.22. 3 / 52.22. 3
  libavformat   52.32. 0 / 52.32. 0
  libavdevice   52. 1. 0 / 52. 1. 0
  libswscale     0. 7. 1 /  0. 7. 1
  built on Apr  2 2009 03:25:40, gcc: 4.2.4
FFmpeg SVN-r18306
libavutil     50. 2. 0 / 50. 2. 0
libavcodec    52.22. 3 / 52.22. 3
libavformat   52.32. 0 / 52.32. 0
libavdevice   52. 1. 0 / 52. 1. 0
libswscale     0. 7. 1 /  0. 7. 1</pre>

This also works great for encoding videos to XviD, which shrinks them to about 10-20% of their original size, making it much faster (in some cases, possible) to upload them to sharing sites.