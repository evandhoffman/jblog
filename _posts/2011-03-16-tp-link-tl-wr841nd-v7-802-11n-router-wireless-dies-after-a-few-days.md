---
title: TP-Link TL-WR841ND v7 802.11n router, wireless dies after a few days
author: Evan Hoffman
layout: post
permalink: /2011/03/16/tp-link-tl-wr841nd-v7-802-11n-router-wireless-dies-after-a-few-days/
dsq_thread_id:
  - 2949528320
categories:
  - Uncategorized
tags:
  - 802.11n
  - dd-wrt
  - internet
  - router
  - snmp
  - speed
  - TL-WR841ND
  - tp-link
  - wifi
---
I mentioned in <a href="http://www.evanhoffman.com/evan/?p=1085" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=1085', 'a previous post']);" >a previous post</a> that I got the <a href="http://www.amazon.com/gp/product/B0034CN0AS/ref=as_li_ss_tl?ie=UTF8&#038;tag=evanhoffmasho-20&#038;linkCode=as2&#038;camp=217145&#038;creative=399349&#038;creativeASIN=B0034CN0AS" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.amazon.com/gp/product/B0034CN0AS/ref=as_li_ss_tl?ie=UTF8&tag=evanhoffmasho-20&linkCode=as2&camp=217145&creative=399349&creativeASIN=B0034CN0AS', 'TP-Link TL-WR841ND 802.11n wifi router']);" >TP-Link TL-WR841ND 802.11n wifi router</a> and it solved the speed problems I was noticing with wifi connections since going from FiOS to Cablevision. This seems to be the case still, however I&#8217;ve now had another problem with the TP router. Basically, wireless becomes unusable and the web UI becomes inaccessible. The SSID still shows up but I can&#8217;t get an IP address. When accessing it from the wired LAN via a browser, the connection times out &#8211; apparently whatever&#8217;s going on inside the router is also crashing its internal webserver.

Power-cycling the router resolved the issue both times it occurred (most recently tonight), but twice is two times too many. Tonight I downloaded and installed <a href="http://www.dd-wrt.com/wiki/index.php/Supported_Devices#TP-Link" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.dd-wrt.com/wiki/index.php/Supported_Devices#TP-Link', 'DD-WRT v24-sp2']);" >DD-WRT v24-sp2</a> and configured it. It only took a few minutes &#8211; I was pretty impressed with dd-wrt &#8211; though I was surprised not to see SNMP monitoring included. Not sure if I missed it in the UI but I assumed it would be under &#8220;Services,&#8221; and I didn&#8217;t see it there. I tried snmpwalk against the router and it returned nothing, so it&#8217;s not on by default. 

Anyway, hopefully dd-wrt will give me better luck than the native TP-Link firmware. It seems to be a good router hardware wise, but crashing every few days negates that.

**Update: April 19, 2011**: I&#8217;ve had DD-WRT running for a few weeks now on the TP-Link router and it&#8217;s been great. No reboots required. For some reason DD-WRT doesn&#8217;t seem to have SNMP available, at least not through the web UI, but other than that it&#8217;s far better than the default TP-Link software.