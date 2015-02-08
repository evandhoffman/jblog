---
title: 'Using SSH tunnel &amp; Squid to create a private encrypted proxy for true private browsing (mostly)'
author: Evan Hoffman
layout: post
permalink: /2010/09/17/using-ssh-tunnel-squid-to-create-a-private-encrypted-proxy-for-true-private-browsing-mostly/
dsq_thread_id:
  - 2949518886
categories:
  - Uncategorized
tags:
  - browser
  - chrome
  - encryption
  - firefox
  - linux
  - privacy
  - private browsing
  - proxy
  - squid
  - ssh
  - sshd
  - tunnel
---
I once worked at this place where I got a stern talking-to for viewing non-work-related pages. It was around Christmas and I was doing my shopping online (since I left the house at 7 AM and got home at 8 PM). It&#8217;s not like I was farting around all the time. Anyway, the idea that I was being proactively watched by someone with an axe to grind pissed me off, so I decided I wouldn&#8217;t give him anything to read.

I don&#8217;t have that problem anymore, but I do frequently connect to open wifi points where my traffic can be viewed. I use SSL for things like email, but why even let them see that I&#8217;ve gone to nytimes.com?

My solution to both problems was the same: on my Linux box at home, run a proxy server, and pipe all my traffic to it via an SSH tunnel.

## Step 1: Install Squid

Since I use CentOS, to do this I just did a `yum install squid`

## Step 2: Configure Squid

Well, the default squid config (/etc/squid/squid.conf) was pretty much fine, although I needed to add an ACL clause so I could actually use the proxy. The LAN in my house is 192.168.1.0/24, so I put these lines in my squid.conf:  
`<br />
acl subnet_192 src 192.168.1.0/255.255.255.0<br />
http_access allow subnet_192<br />
`  
Then start Squid.

## Step 3:Create the SSH tunnel

I run Linux, so that&#8217;s the syntax I can provide (You can use putty to do this from a Windows machine):  
`ssh -f evan@public-hostname-of-proxy-server -L 3128:private-ip-of-proxy-server.com:3128 -N`  
This opens an SSH connection from your local machine (port 3128) to the remote server&#8217;s private IP on port 3128 (3128 being the default port on which squid listens). So connections to localhost:3128 will be forwarded over the SSH tunnel to port 3128 on the other machine&#8217;s private IP.

## Step 4: Set your browser to point to localhost:3128 as proxy server

Well, that&#8217;s pretty self-explanatory. In the browser&#8217;s options (lots of other apps support HTTP proxies as well &#8211; AIM, etc), find the section about proxy settings and set the HTTP and HTTPS proxies to &#8220;localhost&#8221; and port 3128.

That&#8217;s it. To test if it&#8217;s working, try going to <a href="http://www.geoiptool.com/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.geoiptool.com/', 'geoiptool.com']);" >geoiptool.com</a> and confirm that it shows you as coming from the home machine&#8217;s IP.

If you have a strict network admin who&#8217;s locked down outbound SSH, you can just have sshd listen on port 80 or 443, which almost everyone allows. A really nosy admin may notice encrypted traffic going to the server and kill it, but&#8230; well, I never said it was foolproof. <img src="http://www.evanhoffman.com/evan/wp-includes/images/smilies/icon_smile.gif" alt=":)" class="wp-smiley" />