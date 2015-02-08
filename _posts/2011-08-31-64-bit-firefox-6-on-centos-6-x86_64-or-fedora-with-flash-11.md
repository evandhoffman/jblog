---
title: 64-bit Firefox 6 on CentOS 6 x86_64 (or Fedora) with Flash 11
author: Evan Hoffman
layout: post
permalink: /2011/08/31/64-bit-firefox-6-on-centos-6-x86_64-or-fedora-with-flash-11/
dsq_thread_id:
  - 2949517236
categories:
  - Uncategorized
tags:
  - 64
  - 64-bit
  - centos
  - devops
  - fedora
  - firefox
  - flash
  - linux
  - redhat
  - rpm
  - work
  - x86_64
  - yum
---
So I&#8217;ve gone back to Linux from Mac, due to the SSD issues I had with my Macbook Pro basically making VMware unusable. A Win7 VM would grind the guest and host to a halt on the 7200 RPM SATA OEM drive, and the SSD wouldn&#8217;t work, so I put the SSD back in my HP and installed CentOS 6 x86_64. Not really ideal, but at least it works.

CentOS 6 ships with Firefox 3.6.9, which is really old by now. Fine for a server, but I wanted FF6. I grabbed the Firefox 6.0.1 bz2 from <a href="http://www.mozilla.org/community/mirrors.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.mozilla.org/community/mirrors.html', 'VoxCast']);" >VoxCast</a>, unzipped it and copied it to <tt>/usr/lib64/firefox-6</tt>. Then I ran <tt>yum remove firefox</tt> to remove 3.6.9 and avoid any issues. I tried the instructions from <a href="http://www.evanhoffman.com/evan/?p=407" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=407', 'my older post']);" >my older post</a> on this subject but for whatever reason it didn&#8217;t work &#8211; I&#8217;m guessing because I&#8217;m not using the distro&#8217;s Firefox RPM.

I followed <a href="http://fedoraproject.org/wiki/Multimedia/Flash#64-bit_Preview_Release" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://fedoraproject.org/wiki/Multimedia/Flash#64-bit_Preview_Release', 'these instructions']);" >these instructions</a> to get the Flash 11 64-bit plugin installed. It still wasn&#8217;t working though.

I ended up creating a symlink:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #c20cb9; font-weight: bold;">ln</span> <span style="color: #660033;">-s</span> <span style="color: #000000; font-weight: bold;">/</span>usr<span style="color: #000000; font-weight: bold;">/</span>lib64<span style="color: #000000; font-weight: bold;">/</span>mozilla<span style="color: #000000; font-weight: bold;">/</span>plugins<span style="color: #000000; font-weight: bold;">/</span>libflashplayer.so <span style="color: #000000; font-weight: bold;">/</span>home<span style="color: #000000; font-weight: bold;">/</span>evan<span style="color: #000000; font-weight: bold;">/</span>.mozilla<span style="color: #000000; font-weight: bold;">/</span>plugins<span style="color: #000000; font-weight: bold;">/</span>libflashplayer.so</pre>
      </td>
    </tr>
  </table>
</div>

When I started up Firefox after creating the symlink, Flash worked.

Additionally, to set Firefox 6 as the default browser, run <tt>gnome-default-applications-properties</tt>, select Custom, and paste <tt>/usr/lib64/firefox-6/firefox %s</tt> in the Command: field.

<ins datetime="2011-09-17T21:54:13+00:00">Update:</ins> I upgraded my home desktop PC from FC12 to FC14 last night and used the above procedure to install Firefox 6.0.2 on it with Flash 11. So, in case anyone was wondering, the above works for Fedora as well.