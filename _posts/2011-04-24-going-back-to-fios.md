---
title: Going back to FiOS
author: Evan Hoffman
layout: post
permalink: /2011/04/24/going-back-to-fios/
dsq_thread_id:
  - 2949520057
categories:
  - Uncategorized
tags:
  - cable
  - cablevision
  - cablevision vs fios
  - dig
  - dns
  - dvr
  - error
  - fios
  - internet
  - linux
  - long island
  - money
  - movies.netflix.com
  - netflix
  - ool
  - optimum
  - tv
---
I&#8217;m not sure why these guys operate this way &#8211; they&#8217;re more than happy to lose me as a customer and then throw huge discounts at me to get me back. If they&#8217;d just give me a good price I&#8217;d love not to have to go through this rigmarole. But after being with Cablevision for 2 months I checked Verizon&#8217;s pricing and it beat my current deal with Cablevision.

FiOS digital voice with number ported for free; 25/25 Mbps internet; HMDVR free &#8220;forever&#8221; plus a second HD STB, Showtime, Movie Channel and Flix. Since I already had the battery thing installed last time I had FiOS they gave me a fair discount. Basically the whole package for $87/month + tax, price locked for 2 years, no contract. Not as great of a deal as I&#8217;d had with FiOS originally, but it&#8217;s pretty good, and FiOS&#8217;s service is definitely better than Cablevision&#8217;s. I&#8217;ve heard Cablevision was rolling out their &#8220;DVR plus&#8221; service with all programs recorded &#8220;in the cloud&#8221; rather than on the actual box, but it&#8217;s been two months and I haven&#8217;t heard of it coming to Long Island. So basically 2 years later Cablevision&#8217;s service is exactly the same while Verizon has iPhone apps to control the DVR and use the phone as a remote, plus DVR that&#8217;s much faster and just generally better service.

On a side note, I noticed tonight I was having problems trying to stream Netflix to my Wii. I tried loading netflix.com on my laptop and that also didn&#8217;t work, it said &#8220;couldn&#8217;t find server movies.netflix.com.&#8221; I tested this via dig on my linux box and sure enough, movies.netflix.com isn&#8217;t resolving against the default Cablevision nameserver (167.206.3.206) &#8211; getting a SERVFAIL:

<pre>[evan@lunix ~]$ dig movies.netflix.com

; &lt;&lt;>> DiG 9.3.6-P1-RedHat-9.3.6-4.P1.el5_5.3 &lt;&lt;>> movies.netflix.com
;; global options:  printcmd
;; Got answer:
;; ->>HEADER&lt;&lt;- opcode: QUERY, status: SERVFAIL, id: 17569
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;movies.netflix.com.            IN      A

;; ANSWER SECTION:
movies.netflix.com.     232     IN      CNAME   merchweb-frontend-1502974957.us-east-1.elb.amazonaws.com.

;; Query time: 2129 msec
;; SERVER: 167.206.3.206#53(167.206.3.206)
;; WHEN: Sun Apr 24 01:23:58 2011
;; MSG SIZE  rcvd: 103

</pre>

I tried the same query against Google's nameserver (8.8.8.8) and it resolves correctly:

<pre>[evan@lunix ~]$ dig movies.netflix.com @8.8.8.8

; &lt;&lt;>> DiG 9.3.6-P1-RedHat-9.3.6-4.P1.el5_5.3 &lt;&lt;>> movies.netflix.com @8.8.8.8
;; global options:  printcmd
;; Got answer:
;; ->>HEADER&lt;&lt;- opcode: QUERY, status: NOERROR, id: 43718
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;movies.netflix.com.            IN      A

;; ANSWER SECTION:
movies.netflix.com.     300     IN      CNAME   merchweb-frontend-1502974957.us-east-1.elb.amazonaws.com.
merchweb-frontend-1502974957.us-east-1.elb.amazonaws.com. 39 IN A 174.129.220.6

;; Query time: 34 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Sun Apr 24 01:37:26 2011
;; MSG SIZE  rcvd: 119
</pre>

I set my router to resolve against 8.8.8.8 rather than whatever Cablevision provides and now it works. I'm not sure if this is related to <a href="http://news.google.com/news/more?pz=1&#038;cf=all&#038;cf=all&#038;ncl=dXGaFnLxwIuhH4M6TS8iFF_fwl7SM" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://news.google.com/news/more?pz=1&cf=all&cf=all&ncl=dXGaFnLxwIuhH4M6TS8iFF_fwl7SM', 'the big EC2 disaster']);" >the big EC2 disaster</a> of the past few days but it looks more like Cablevision's fault than Amazon's or Netflix's.