---
title: Flac to MP3 (flac2mp3.pl)
author: Evan Hoffman
layout: post
permalink: /2005/11/07/flac-to-mp3-flac2mp3-pl/
image:
  - 
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
categories:
  - Uncategorized
tags:
  - convert
  - flac
  - flac2mp3
  - flac2mp3.pl
  - lame
  - linux
  - mp3
  - old
  - perl
---
I wrote a crappy perl script to read all the .flac files in a directory and convert them to MP3 using LAME, extracting the meta-data and inserting it as ID3v2 tags. I figured this might be somewhat useful to others, so here you go: <strike><flac2mp3.pl></strike>