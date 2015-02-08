---
title: iPad drops WiFi connection to Verizon FiOS Actiontec Router
author: Evan Hoffman
layout: post
permalink: /2012/01/01/ipad-drops-wifi-connection-to-verizon-fios-actiontec-router/
image:
  - 
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
categories:
  - Uncategorized
tags:
  - actiontec
  - apple
  - connection
  - drop
  - ipad
  - MI424WR
  - MI424WR-GEN2
  - networking
  - router
  - wifi
---
I got my wife an iPad 2 for Christmas and she soon started complaining about the Wifi dropping its connection. I suggested she try turning off the &#8220;auto join&#8221; wifi setting, but that didn&#8217;t help. She&#8217;d be doing something and get the &#8220;Sorry, there&#8217;s no internet connection&#8221; error every 5-10 minutes. We&#8217;ve had FiOS for quite a while and we have 8 or 9 other devices connected (including Macs &#038; iPhones) to the router without issue, so this seemed weird. I was starting to think it was a problem with the iPad, but we went to a friend&#8217;s house and used his wifi (with a Netgear router) and the iPad had no issues. 

Back home, I logged into the router and tried assigning her iPad a static IP through DHCP. I had her release and renew and she got the new IP but the problem continued. Since we ruled out a problem with the iPad and I knew there was nothing &#8220;wrong&#8221; with the router, I figured I&#8217;d check and see if there are any reported issues with iPads and the Verizon router. Sure enough, there are. The first thing I clicked on, <a href="http://appletoolbox.com/2010/04/fix-for-verizon-fios-vs-ipad-wi-fi-issues/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://appletoolbox.com/2010/04/fix-for-verizon-fios-vs-ipad-wi-fi-issues/', 'Fix for Verizon FIOS vs. iPad Wi-Fi Issues']);" >Fix for Verizon FIOS vs. iPad Wi-Fi Issues</a>, suggested changing the wifi channel from &#8220;Automatic&#8221; to &#8220;6&#8221; (it also suggests switching from WEP to WPA2-PSK, which I&#8217;ve always been using). I did that and it hasn&#8217;t dropped the wifi connection at all in the past 3 hours. Very odd issue. If I could get into the Actiontec (or the iPad for that matter) I&#8217;d like to check the logs and see what&#8217;s actually happening, but a win&#8217;s a win.