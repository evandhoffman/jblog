---
title: Reorganizing photos in 1 line with exiftool
author: Evan Hoffman
layout: post
permalink: /2012/03/02/reorganizing-photos-in-1-line-with-exiftool/
image:
  - 
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
dsq_thread_id:
  - 2949528413
categories:
  - Uncategorized
tags:
  - code
  - devops
  - exif
  - exiftool
  - jpeg
  - linux
  - perl
---
A few years ago I wrote <a href="http://www.evanhoffman.com/evan/?p=34" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=34', 'a utility']);" >a utility</a> in Java to find all JPG files in a directory and move them into a date-based directory structure like /YYYY/MM/DD/<file> based on the date the photo was taken, extracted from the exif metadata in the file. Well, apparently that was a huge waste of time, as I just discovered that `exiftool`, an awesome perl utility I&#8217;ve used for years to edit/extract the metadata on the command line, can also do this natively. So my entire program can be replaced with this simple command:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #666666;">$ </span>exiftool <span style="color: #660033;">-r</span> <span style="color: #ff0000;">'-FileName&lt;CreateDate'</span> <span style="color: #660033;">-d</span> <span style="color: #000000; font-weight: bold;">/</span>targetDir<span style="color: #000000; font-weight: bold;">/%</span>Y<span style="color: #000000; font-weight: bold;">/%</span>Y-<span style="color: #000000; font-weight: bold;">%</span>m<span style="color: #000000; font-weight: bold;">/%</span>Y-<span style="color: #000000; font-weight: bold;">%</span>m-<span style="color: #000000; font-weight: bold;">%</span>d<span style="color: #000000; font-weight: bold;">/%</span>Y-<span style="color: #000000; font-weight: bold;">%</span>m-<span style="color: #000000; font-weight: bold;">%</span>d.<span style="color: #000000; font-weight: bold;">%%</span>f.<span style="color: #000000; font-weight: bold;">%%</span>e <span style="color: #000000; font-weight: bold;">/</span>media<span style="color: #000000; font-weight: bold;">/</span>EOS_DIGITAL<span style="color: #000000; font-weight: bold;">/</span></pre>
      </td>
    </tr>
  </table>
</div>

This will copy the files directly off the SD card mounted at /media/EOS_DIGITAL/ into the proper structure in /targetDir/.