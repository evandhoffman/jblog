---
title: 'Digital Ocean &#8211; First Impressions'
author: Evan Hoffman
layout: post
permalink: /2013/07/24/digital-ocean-first-impressions/
dsq_thread_id:
  - 2949522088
categories:
  - Uncategorized
tags:
  - apache
  - digitalocean
  - hosting
  - linux
  - meta
  - mysql
  - nginx
  - wordpress
---
For the past few years I&#8217;ve been hosting this site on an old desktop in my basement on my FiOS connection. This was one of the things I really liked when I switched from Cablevision to Verizon &#8211; they don&#8217;t block port 80 inbound, so I didn&#8217;t have to pay for separate hosting. My &#8220;server&#8221; was an old AMD desktop with 1 gig ram and a sata drive. It was ok; my site was slow but I was ok with that. I configured Nginx to cache the static assets which sped most things up to &#8220;ok&#8221; levels but it was never fast. 

This setup had a bunch of problems though, and the biggest one was power. Namely, it goes out in my house all the time. I probably have 4 or 5 brief outages each month, and my old box doesn&#8217;t come back up properly on reboot (some bios conflict with an eSATA disk I have hooked up to it). Plus, since my basement became a huge bathtub during Sandy, my site was down for about a month, but that wasn&#8217;t really a big concern at the time.

Anyway, via a &#8220;Promoted Tweet&#8221; on Twitter I found Digital Ocean, a VPS provider with rates starting at <a href="https://www.digitalocean.com/?refcode=6046a8ff1ac9" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://www.digitalocean.com/?refcode=6046a8ff1ac9', '$5/month for an SSD-backed VM']);" >$5/month for an SSD-backed VM</a>. They also had a promo at the time for a $10 credit, so I figured I&#8217;d give it a try. 

Account creation was simple and I didn&#8217;t need to enter my CC until I actually created a server (&#8220;droplet&#8221; in their parlance). Server creation was pretty trivial: select the OS image (I chose CentOS 6.4 but they offer Ubuntu, Arch, Debian and Fedora as well), the size (512 MB ram through 16 GB), the region (San Francisco, New York, or Amsterdam), enter a hostname and your SSH pubkey. In about 60 seconds your server is ready to go, with a public IP and everything. My VM has a 20 GB disk and the base OS install was about 900 MB. I installed Apache, Nginx, MySQL and some other stuff, then dumped my WordPress DB and uploaded it to the new VM and copied the entire Apache docroot over as well. Within about 30 minutes of spinning up the VM I had everything up on the new box, and I made the DNS changes shortly after that. Pretty straightforward.

It&#8217;s only been a couple of days but so far I&#8217;m really liking the performance. My site doesn&#8217;t get a lot of traffic to begin with, but since I cache most stuff to disk, and the disk is SSD, it&#8217;s really quick. I&#8217;ll keep an eye on it but so far this is looking like a great choice for small website hosting. The only thing is I&#8217;ll need to setup some sort of offsite backups, but I can just cron an rsync to my home machine for now.

<ins datetime="2013-07-24T18:12:24+00:00"><img src='http://share.pingdom.com/banners/bed0a3d6' /></ins>

<ins datetime="2013-08-08T12:00:18+00:00"><a href="http://www.evanhoffman.com/evan/2013/07/24/digital-ocean-first-impressions/evanhoffman_digitalocean/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/2013/07/24/digital-ocean-first-impressions/evanhoffman_digitalocean/', '']);"  rel="attachment wp-att-2212"><img src="http://www.evanhoffman.com/evan/wp-content/uploads/2013/07/evanhoffman_digitalocean.png" alt="evanhoffman_digitalocean" width="976" height="852" class="aligncenter size-full wp-image-2212" /></a></ins>