---
title: 'Error returning browse list: NT_STATUS_NOT_SUPPORTED'
author: Evan Hoffman
layout: post
permalink: /2009/10/24/error-returning-browse-list-nt_status_not_supported/
dsq_thread_id:
  - 2949515870
categories:
  - Uncategorized
tags:
  - linux
  - samba
  - windows 7
---
This is annoying. I&#8217;m attempting to get to my photos on my Win 7 desktop from my Linux laptop. It works in Win XP clients, but smbclient is failing:

> <pre>[evan@ehoffman ~]$ smbclient -L //192.168.10.105/
Enter evan's password:
Domain=[EVAN-WOLFDALE7] OS=[Windows 7 Ultimate 7100] Server=[Windows 7 Ultimate 6.1]

	Sharename       Type      Comment
	---------       ----      -------
Error returning browse list: NT_STATUS_NOT_SUPPORTED
session request to 192.168.10.105 failed (Called name not present)
session request to 192 failed (Called name not present)
session request to *SMBSERVER failed (Called name not present)
NetBIOS over TCP disabled -- no workgroup available
[evan@ehoffman ~]$
</pre>

**Update**: This appears to be resolved in <a href="http://news.samba.org/releases/3.4.3/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://news.samba.org/releases/3.4.3/', 'Samba 3.4.3']);" >Samba 3.4.3</a> (<a href="http://samba.org/samba/history/samba-3.4.3.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://samba.org/samba/history/samba-3.4.3.html', 'release notes']);" >release notes</a>). Since there&#8217;s no RPM for 3.4.2 for FC11 I downloaded the source and built it and tried the smbclient against my Win7 box and it worked fine.