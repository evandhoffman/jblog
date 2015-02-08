---
title: More fun with ovftool
author: Evan Hoffman
layout: post
permalink: /2009/09/20/more-fun-with-ovftool/
dsq_thread_id:
  - 2966426469
categories:
  - Uncategorized
tags:
  - computers
  - ovftool
  - vmware
---
I tried restoring my .ova into vCenter and it failed. It said something like &#8220;Device &#8216;virtual disk&#8217; uses a controller that is not supported. this is a general limitation of the virtual hardware version.&#8221; This kind of pissed me off, especially when I couldn&#8217;t really find anything in Google about what the error meant, but it turned out to be pretty easy to fix &#8211; I just clicked &#8220;upgrade virtual hardware&#8221; in VMWare Server to upgrade the hardware version from 4 to 7. I then created another .ova, uploaded it to the datacenter and successfully deployed it in vCenter in its own resource pool. Yay!

I think the real problem was that some of our VMs were created with the BusLogic driver rather than the LSI Logic driver (which is recommended for Linux guests).

Ovftool also turned out to be a pretty sweet way to backup VMs. Once in .ova format you can use ovftool to convert back to .vmx just by specifying the .ova file as the source and something .vmx as the target. Because it so nicely shrinks the .vmdk (my 20 gig VM became a 2.5 gig .ova) and is pretty quick to run (seemed like < 10 minutes per 20-gig VM) it seems like a decent choice for backing up VMs.