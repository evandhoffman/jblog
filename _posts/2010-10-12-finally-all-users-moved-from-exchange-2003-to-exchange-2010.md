---
title: Finally, all users moved from Exchange 2003 to Exchange 2010.
author: Evan Hoffman
layout: post
permalink: /2010/10/12/finally-all-users-moved-from-exchange-2003-to-exchange-2010/
dsq_thread_id:
  - 2961072020
categories:
  - Uncategorized
tags:
  - bes
  - blackberry
  - exchange
  - exchange 2003
  - exchange 2010
  - migration
  - windows
  - work
---
I&#8217;ve been working on migrating our Exchange environment from 2003 to 2010 for several months. My <a href="http://www.evanhoffman.com/evan/?p=367" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=367', 'first post about this']);" >first post about this</a> is from April 14th, when I was just trying to virtualize our existing Exchange 2003 system. Once that was complete, I started playing around with Exchange 2010 around June or July, and had most of the users moved over to 2010 by the end of August. The last holdouts were Blackberry users. I couldn&#8217;t move their mailboxes because our BES was hosted on our original Exchange 2003 server.

BES is another product that I inherited that I had no experience with. It&#8217;s BES 4.1.x and while I wasn&#8217;t a fan of the UI it seemed to do its job. However, when I started moving people to Exchange 2010 I learned that <a href="http://na.blackberry.com/eng/support/software/server_compatibility.jsp" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://na.blackberry.com/eng/support/software/server_compatibility.jsp', 'BES 4.1 doesn&#8217;t support Exchange 2010']);" >BES 4.1 doesn&#8217;t support Exchange 2010</a>. So, to cut the (absurdly long) story short, I setup BES Express on a new VM, pointed it at our Exchange 2010 server, tested it out (and it worked), and just last week was able (finally) to move the last few users over to Exchange 2010. BES users had to have their phones wiped to join them to the BES Express server, which was the major sticking point.

I can&#8217;t believe it actually took that long to complete, but we managed to move all user mailboxes twice (Ex2003 physical -> Ex2003 VM, then Ex2003 VM -> Ex2010 VM) with no noticeable interruption to users (we did the moves at night). OWA 2010 alone would make it worth the upgrade, but I&#8217;m actually loving the Exchange Management Shell too.

Anyway&#8230; nice to have it completed.