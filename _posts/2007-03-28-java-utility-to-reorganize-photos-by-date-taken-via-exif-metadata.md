---
title: Java utility to reorganize photos by date taken (via EXIF metadata)
author: Evan Hoffman
layout: post
permalink: /2007/03/28/java-utility-to-reorganize-photos-by-date-taken-via-exif-metadata/
image:
  - 
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
categories:
  - Uncategorized
tags:
  - code
  - date
  - download
  - exif
  - git
  - java
  - jpeg
  - metadata
  - old
  - reorganize
---
<ins datetime="2011-05-26T14:44:01+00:00">Edit:</ins> I&#8217;ve moved this code to github (<a href="https://github.com/evandhoffman/JPEG-Organizer" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://github.com/evandhoffman/JPEG-Organizer', 'https://github.com/evandhoffman/JPEG-Organizer']);" >https://github.com/evandhoffman/JPEG-Organizer</a>). I recently modified it so it won&#8217;t move duplicate files &#8211; if you have a file named IMG\_0012.JPG in the target directory already, it will calculate the SHA-1 sum of the source &#038; target files. If the files have the same hash (i.e., they&#8217;re identical files) it won&#8217;t copy the duplicate over. If they hash differently, it will still copy the new file as IMG\_0012.JPG.001 (or whatever).

<ins datetime="2011-05-26T14:52:10+00:00">Edit 2:</ins> I just realized that the old links to the code didn&#8217;t work (apparently since I moved to JustHost) so I&#8217;ve deleted them.

<ins datetime="2012-02-28T04:33:10+00:00">Edit 3, Feb 27th, 2012</ins>: I just discovered that this entire project can be replaced with a couple of args to exiftool:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;">exiftool <span style="color: #660033;">-r</span> -FileName<span style="color: #000000; font-weight: bold;">&lt;</span>CreateDate <span style="color: #660033;">-d</span> <span style="color: #000000; font-weight: bold;">%</span>Y<span style="color: #000000; font-weight: bold;">/%</span>Y-<span style="color: #000000; font-weight: bold;">%</span>m<span style="color: #000000; font-weight: bold;">/%</span>Y-<span style="color: #000000; font-weight: bold;">%</span>m-<span style="color: #000000; font-weight: bold;">%</span>d<span style="color: #000000; font-weight: bold;">/%</span>Y-<span style="color: #000000; font-weight: bold;">%</span>m-<span style="color: #000000; font-weight: bold;">%</span>d-<span style="color: #000000; font-weight: bold;">%%</span>f.<span style="color: #000000; font-weight: bold;">%%</span>e <span style="color: #000000; font-weight: bold;">&lt;</span><span style="color: #c20cb9; font-weight: bold;">dir</span><span style="color: #000000; font-weight: bold;">&gt;</span></pre>
      </td>
    </tr>
  </table>
</div>

<del datetime="2011-05-26T14:44:01+00:00"><br /> I wrote a little utility to reorganize my digital camera pics based on the EXIF date-taken data stored in the pic. It&#8217;s pretty simple, I hacked it together in about an hour,<br /> but I thought it might be worth sharing for anyone else looking to do something similar. My camera organizes stuff as it sees fit sometimes, I am particular about my directory structure. This is probably a job best left to perl, but I don&#8217;t feel like getting Perl running on my Windows box where all the pics are. Now I can finally burn all my images to DVD.</p> 


  <p>
    </del>
  </p>