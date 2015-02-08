---
title: Back on FiOS again (finally)
author: Evan Hoffman
excerpt: Well, that was quite an ordeal.
layout: post
permalink: /2011/05/20/back-on-fios-again-finally/
categories:
  - Uncategorized
tags:
  - cablevision vs fios
  - dns
  - fios
  - long island
  - money
  - speed test
  - verizon
---
Well, that was quite an ordeal. But Verizon came today and finally installed FiOS. All&#8217;s well that ends well, I suppose. My phone number was finally ported over and the internet is insanely fast. This is 25/25 internet with my desktop Fedora box plugged into the TP-Link router which is then plugged into the FiOS ActionTec router. I didn&#8217;t want to have to reconnect all my computers to a new SSID so I&#8217;ll just continue using the TP-Link until I have a reason not to.<figure id="attachment_1288" style="width: 300px;" class="wp-caption aligncenter">

<a href="http://www.evanhoffman.com/evan/2011/05/20/back-on-fios-again-finally/attachment/1304853152/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/2011/05/20/back-on-fios-again-finally/attachment/1304853152/', '']);"  rel="attachment wp-att-1288"><img src="http://www.evanhoffman.com/evan/wp-content/uploads/2011/05/1304853152.png" alt="FiOS 25/25 Speed Test - May 20th, 2011" title="FiOS 25/25 Speed Test - May 20th, 2011" width="300" height="135" class="size-full wp-image-1288" /></a><figcaption class="wp-caption-text">FiOS 25/25 Speed Test - May 20th, 2011</figcaption></figure> 

One thing I did right away was change my DNS servers. The default DNS servers with Verizon were 68.237.161.12 and 71.243.0.12. By default, Verizon uses &#8220;DNS assistance,&#8221; meaning that DNS queries against these servers will return IP addresses when they should return NXDOMAIN, so if you mistype the hostname in a URL it can direct you to a page full of ads. You can disable this by replacing the last octet of the default DNS IP with 14. So for the two IPs above, it would be 68.237.161.14 and 71.243.0.14. I figured I&#8217;d compare the response times of these servers with <a href="http://code.google.com/speed/public-dns/docs/using.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://code.google.com/speed/public-dns/docs/using.html', 'Google&#8217;s 8.8.8.8 and 8.8.4.4']);" >Google&#8217;s 8.8.8.8 and 8.8.4.4</a>. I used dig to time DNS requests and also used ping to measure latency. 68.237.161.14 was the fastest for me, followed by 8.8.4.4 and then 71.243.0.14, so those are my primary, secondary, and tertiary DNS servers.