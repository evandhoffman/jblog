---
title: Passwordless SSH Everywhere
author: Evan Hoffman
layout: post
permalink: /2010/03/30/passwordless-ssh-everywhere/
dsq_thread_id:
  - 2971589719
categories:
  - Uncategorized
tags:
  - computers
  - internet
  - linux
  - security
  - ssh
  - ssh-agent
  - work
---
I&#8217;ve known about ssh keys for a long time and frequently use them, most frequently so that a script can transfer a file between two servers without having to do some mumbo-jumbo where I try to pipe a password into it or some other wacky thing. I hadn&#8217;t fully embraced ssh keys, though, because I didn&#8217;t like the idea that if I lost my laptop, I&#8217;d be losing a free key into my servers. Then I discovered ssh-agent. This isn&#8217;t new, so I&#8217;m kind of embarrassed I didn&#8217;t know about it, but I&#8217;ve been using it for a few months now and I can&#8217;t imagine going back.

<!--more-->

Using <a href="http://www.unixwiz.net/techtips/ssh-agent-forwarding.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.unixwiz.net/techtips/ssh-agent-forwarding.html', 'ssh-agent']);" >ssh-agent</a>, you can wrap your private ssh key in a password and the only time you&#8217;ll need to enter your password is when you add the key to the agent. Once that&#8217;s done ssh essentially behaves as if your key wasn&#8217;t password-protected &#8211; you don&#8217;t get prompted for it again.

If you enable agent forwarding, and put your public key on all the servers you connect to, life gets even better. If your machine is A, and you put your key on B, C, D, and E, using ssh agent forwarding you can go A -> B -> C -> D -> E without being prompted for a password. It&#8217;s sweet!