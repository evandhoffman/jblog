---
title: Running MRTG cfgmaker across your entire subnet?
author: Evan Hoffman
layout: post
permalink: /2010/01/27/running-mrtg-cfgmaker-across-your-entire-subnet/
dsq_thread_id:
  - 2949524193
categories:
  - Uncategorized
tags:
  - bash
  - linux
  - mrtg
  - nmap
  - perl
  - routers2.cgi
  - work
---
I realized recently that I had a bunch of newly-provisioned VMs that weren&#8217;t being monitored by MRTG (one of the tools we use to monitor network usage and other fun stats). Rather than manually run cfgmaker against all the new machines, I decided to script my way out of this.

<!--more-->

**Step 1: Build a list of hosts to monitor**

For this I used an nmap ping test to generate a list of &#8220;up&#8221; hosts:

`nmap -sP 10.0.0.0/24 -oG 10.0.0.0-255.255.255.0`

This creates a list of IPs that are up in a format like this:

`<br />
Host: 10.0.0.60 ()      Status: Up<br />
Host: 10.0.0.61 ()      Status: Up<br />
Host: 10.0.0.63 ()      Status: Up<br />
Host: 10.0.0.64 ()      Status: Up<br />
Host: 10.0.0.65 ()      Status: Up<br />
`

**Step 2: Parse out the IP address and run cfgmaker**

Here&#8217;s the command I ran:

``$ for i in `perl -ne 'chomp; next if $_ =~ /^#/; my @a = split(/ /); print "$a[1]\n";' 10.0.0.0-255.255.255.0`; do /usr/bin/cfgmaker --global 'LogFormat: rrdtool' --global 'WorkDir: /mrtg/data/mrtg' --global 'Options[_]: growright,bits' --global 'XSize[_]: 600' --global 'YSize[_]: 400' public@$i > /etc/mrtg/mrtg-$i ; done``

This parses out the IP address from the input file and for each address, runs cfgmaker and puts the output in /etc/mrtg/mrtg-[ipaddress] .

Taking this a step further, I used gethostbyaddr to resolve the IPs to hostnames, so the MRTG files have the proper hostnames in them:

``$ for i in `perl -MSocket -ne 'chomp; next if $_ =~ /^#/; my @a = split(/ /); my $ip = inet_aton($a[1]); my $host = gethostbyaddr($ip, AF_INET); print "$host\n";' 10.0.0.0-255.255.255.0`; do /usr/bin/cfgmaker --global 'LogFormat: rrdtool' --global 'WorkDir: /mrtg/data/mrtg' --global 'Options[_]: growright,bits' --global 'XSize[_]: 600' --global 'YSize[_]: 400' public@$i  > /etc/mrtg/mrtg-$i ; done``

Then all of the mrtg .cfg config files are in /etc/mrtg/, but who wants to put in all those cron entries to run each one? Not me. Simple fix?

` cat /etc/mrtg/*.cfg > /etc/mrtg-all.cfg`

Then you only have one cron entry.