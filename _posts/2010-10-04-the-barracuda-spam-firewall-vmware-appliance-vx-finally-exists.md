---
title: The Barracuda Spam Firewall VMware Appliance (Vx) finally exists!
author: Evan Hoffman
layout: post
permalink: /2010/10/04/the-barracuda-spam-firewall-vmware-appliance-vx-finally-exists/
dsq_thread_id:
  - 2949524854
categories:
  - Uncategorized
tags:
  - barracuda
  - email
  - linux
  - mail
  - money
  - ovf
  - security
  - spam
  - spam filter
  - virtual
  - virtual appliance
  - vmware
  - vx
  - work
---
When I started at my current company, spam was handled with a separate server running SpamAssassin and a few other services. This sort of got the job done but required babysitting. I wasn&#8217;t part of the Sysadmin team at that point but I know they had to restart SpamAssassin relatively frequently, manually clear out the email queue when people noticed they weren&#8217;t receiving email, etc.

<!--more-->

  
After a few months, someone wised up and purchased a Barracuda Spam Firewall. I assumed this was basically a pretty GUI wrapper around the same tools we had already been using, but it was certainly worth the money. In addition to the basic filtering of spam it filtered for viruses, performed recipient verification against AD via LDAP, had nice graphing and reporting.

We bought the unit in October, 2005 and it&#8217;s mostly been great. There&#8217;ve been some hiccups over the years but all things considered it&#8217;s performed very well. However, when we recently updated the firmware from the 3.x series to the 4.x series we started experiencing problems: intermittent outages (connection refused for ports 25 and 443), CPU load pegging at 100%. We called support and, as I suspected, the problem is most likely due to the age of the hardware. It&#8217;s been nearly 5 years so I figured it was time to replace the unit. The original unit was a model 300, so I was considering a <a href="http://www.barracudanetworks.com/ns/products/spam_specs.php" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.barracudanetworks.com/ns/products/spam_specs.php', 'Model 400']);" >Model 400</a>, which, in addition to the updated hardware, offered SNMP monitoring (of which I am a big fan).

It was at that point that I saw they now offer a <a href="http://www.barracudanetworks.com/ns/products/vm_overview.php" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.barracudanetworks.com/ns/products/vm_overview.php', 'VMware Virtual Appliance']);" >VMware Virtual Appliance</a>. I requested a demo license and downloaded the appliance, which was just a zipped .ovf. A demo key was emailed to me. I imported the OVF, gave it 6GB memory (we have plenty to spare in our ESX cluster &#8211; our original 300 unit only had 512 MB) and powered it on. It booted to a standard Linux console prompting me for a username and password. I didn&#8217;t know what the default was (and I needed this to configure the network info) but Google turned up <a href="http://barracudanetworks.com/ns/downloads/Setup_Guides/Barracuda_Spam_&#038;_Virus_Firewall_Vx_Setup_US.pdf" onclick="_gaq.push(['_trackEvent','download','http://barracudanetworks.com/ns/downloads/Setup_Guides/Barracuda_Spam_&_Virus_Firewall_Vx_Setup_US.pdf']);" >PDF instructions</a> that revealed the default username/password to be **admin/admin** (which I probably should have just guessed). After that I was able to configure networking and access the web UI. I exported the config from our 300 unit and imported it into the Vx appliance which restored most of the settings (including our massive whitelist).

I haven&#8217;t tried routing any actual mail through it yet but so far it seems great &#8211; the same UI as the 300 but much faster &#8211; it takes just 1-2 seconds to login versus 60-90 on our old box, and the menus are much more responsive.

I was somewhat apprehensive about moving our inbound MX from a physical server to a VM in case of some outage affecting VMware, but that&#8217;s only happened once in the two years we&#8217;ve been running VMware, and was due to a network misconfiguration. In the end I decided to risk it, though, because the virtual appliance costs $1,000 plus $180/year for each 100 users versus $3,999 for the Model 400 + $1,099 for the first year of updates (with no limit on the number of users). Plus, the appliance has all the software features of the highest-level model.

I don&#8217;t expect any problems, but this is my first virtual appliance purchase so I&#8217;m mildly apprehensive. Here&#8217;s hoping it works out. <img src="http://www.evanhoffman.com/evan/wp-includes/images/smilies/icon_smile.gif" alt=":)" class="wp-smiley" />