---
title: The Joy of Migrating from Exchange 2003 to 2010
author: Evan Hoffman
layout: post
permalink: /2010/07/20/migrating-from-exchange-2003-to-2010-and-other-fun-bits/
dsq_thread_id:
  - 2956253071
categories:
  - Uncategorized
tags:
  - coexistence
  - exchange
  - exchange 2003
  - exchange 2010
  - legacy
  - migration
  - outlook
  - owa
  - virtualization
  - vmware
  - windows
  - work
---
I&#8217;ve been working on migrating from Exchange 2003 to Exchange 2010 for several weeks. Actually, at this point it feels like several months. Now that I think about it, I guess that&#8217;s because it&#8217;s actually *been* several months.

Back in January or February, I got fed up with the Exchange setup I inherited: our Exchange 2003 server was running on a server in the basement of our office, on non-UPS power, with a power company that likes to pull shenanigans (like 3-4 hour outages every few months). In addition, the physical machine itself has some weird bug where it would hang at the POST screen complaining about some USB device, even though there are no USB devices plugged in, and USB is disabled in the BIOS. Meanwhile, in the datacenter, I had recently finished migrating most of our ancient physical servers to virtual machines on beautiful new hardware. It didn&#8217;t take long to see the solution that seemed to be obvious: move Exchange to the datacenter, in a VM.

There was a major wrinkle in this plan, however: there were no quota limits enforced in Exchange, and the average mailbox was 6-7 gigabytes, with 4 users over 10 gigs. At the time, we only had a 5 mbit upload connection to the datacenter, and the total size of the mailboxes was around 400 gigs. I didn&#8217;t want to spend weeks and weeks moving tons of mail over a slow pipe &#8211; and with mailboxes being so big, I wasn&#8217;t sure I could even complete some of them overnight.

At this point I brought up the idea of migrating the company to <a href="http://www.google.com/apps/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.google.com/apps/', 'Google Apps']);" >Google Apps</a>. I&#8217;m a big fan of Gmail and moving off of Exchange would have certainly simplified some aspects of my job, and nobody would need Outlook (especially not me). I knew it would be a tough sell internally, but the pricing certainly didn&#8217;t help; it came out to $83/user/year for Google Apps + document retention. The price came out to about the same as upgrading to Exchange 2010. If it had been half or a third the cost I may have pushed harder, but to make the story (a little) shorter, we ended up sticking with Exchange, and instituting quotas.

We phased in the quotas over the course of a month to give users time to archive and clean up their mailboxes. Once that was done, I setup a new Exchange 2003 frontend server (in a VM) in the datacenter and pointed our webmail (OWA &#038; ActiveSync) there. So we had the frontend in the datacenter and the backend &#8220;mailbox&#8221; server still in the office. I then setup another VM running Exchange 2003 in the datacenter. This enabled me to move mailboxes over one at a time with almost no interruption in service, except for the user whose mail was in transit. Since we instituted quotas, the mailboxes were all under 2 GB, and I was able to do 6-10 mailboxes each night.

I can&#8217;t tell you how happy I was when we lost power yet everyone retained full connectivity to email via their phones (except BlackBerry users, since BES was still in the basement &#8212; note to RIM: ActiveSync!).

So phase 1 &#038; 2 (instituting quotas and moving email out of the basement) were complete. Phase 3 was the bigger unknown &#8211; moving to Exchange 2010. After lots of reading and planning, installing, configuring and testing, about two weeks ago I setup a Client Access Server to serve as the new webmail &#8220;frontend.&#8221; Microsoft has some pretty great <a href="http://technet.microsoft.com/en-us/library/ee332348.aspx" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://technet.microsoft.com/en-us/library/ee332348.aspx', 'instructions for setting up 2003 and 2010 in coexistence']);" >instructions for setting up 2003 and 2010 in coexistence</a>, but basically you point your &#8220;real&#8221; webmail URL to the 2010 CAS and move your &#8220;old&#8221; Exchange 2003 webmail to another url (they suggest legacy.company.com). Then people log in to the 2010 interface, and if their mailbox is housed on the 2003 server, it seamlessly redirects them to https://legacy.company.com/, and they **don&#8217;t** have to log in again. Pretty slick, and I didn&#8217;t believe it would work until I saw it for myself (which, btw, it does). So ActiveSync and Outlook Anywhere were working through the 2010 CAS even for the users housed on the 2003 server (which was all of them).

This week I started moving users over to Exchange 2010. So far it&#8217;s been mostly positive. We have several Mac users, so the ability for them to have native mail &#038; calendaring is pretty epic. The Outlook Web App in Exchange 2010 is phenomenal. I mean, it almost brings a tear to my eye, it&#8217;s so beautiful &#8211; especially when compared with 2003. And being able to do server-side searching in OWA &#038; on my iPhone is fabulous.

All is not perfect, though. I keep getting stupid certificate errors for Autodiscover when I open Outlook 2007. I guess I&#8217;ll need to buy another SSL certificate and dedicate another IP to this service&#8230; ugh. And now that I moved my mailbox to Exchange 2010, Outlook Anywhere appears not to work. Oh well, almost there&#8230;