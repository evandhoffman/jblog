---
title: VMWare rules
author: Evan Hoffman
layout: post
permalink: /2009/07/05/vmware-rules/
categories:
  - Uncategorized
tags:
  - vmware
---
I love VMWare. I&#8217;ve been using it for less than a year but it&#8217;s easily one of my favorite products. I upgraded my ESX 3.5 cluster to 4.0 a little over a week ago and it was a pretty simple procedure &#8211; the vCenter server pushes out the new ISO to the ESX server, installs it, and reboots it. I haven&#8217;t really played with most of the 4.0 features yet, but the new performance overview graphs are awesome.

I did have one problem where the graphs weren&#8217;t displaying &#8211; it was giving me some kind of IE error page. After a lot of messing around with it I realized that the Tomcat server that serves those graphs wasn&#8217;t running &#8211; netstat -a showed nothing listening on 8443. My first guess was a port conflict of some sort. Nothing was listening on port 8443, but lots of other 8000+ ports were in LISTEN, so I looked in Services and realized I had VMware Converter server running. Once I stopped that I was able to start the VMWare Web Services and the graphs showed properly &#8211; at least when viewed from localhost. When I tried to view from a remote computer there was still an error. This turned out to be a dumb DNS issue; the graphs referenced the SDK via the URL &#8220;https://winxp2:8443/sdk&#8221; (btw, this is defined in a few different files named <tt>extension.xml</tt> in <tt>C:\Program Files\VMware\Infrastructure\VirtualCenter Server\extensions\*</tt>, I&#8217;m not sure which subdirectory links to which webservice so I&#8217;d make sure to change it everywhere if you change it anywhere.) The problem was that over the VPN I didn&#8217;t have the proper search domain setup (winxp2.company.com) so I ended up resolving this problem by editing the hosts file on my desktop to point to the proper IP. Kind of ghetto but it works. VMWare is very finicky about DNS stuff; it&#8217;s kind of scary how dependent on DNS everything is. But oh well. For the benefits it provides it&#8217;s a small price to pay. I could go on about it but I&#8217;m sure a VMWare sales rep could do it better. <img src="http://www.evanhoffman.com/evan/wp-includes/images/smilies/icon_smile.gif" alt=":)" class="wp-smiley" />