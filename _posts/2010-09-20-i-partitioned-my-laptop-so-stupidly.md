---
title: I partitioned my laptop so stupidly.
author: Evan Hoffman
layout: post
permalink: /2010/09/20/i-partitioned-my-laptop-so-stupidly/
dsq_thread_id:
  - 3012901178
categories:
  - Uncategorized
tags:
  - gparted
  - linux
  - parted
  - partition
  - ssd
  - swap
---
When I first installed Linux on my laptop (on my old hard drive) I did it as dual-boot, so I resized my Windows XP partition down to 50 GB, created a 2nd partition for Linux and installed it there. I think I played around with Fedora and Ubuntu and one other distro (maybe FreeBSD?) so I had a bunch of stupid partitions. I eventually went to Linux exclusively and repurposed my XP partition to be my home directory (/docs) and moved all my documents there.

Then I moved to an SSD, which in addition to being incalculably faster than the Seagate Momentus 5400rpm drive, was also bigger &#8211; 128 GB instead of 100 GB. This was good, except that my method of moving the data from old drive to new drive was &#8220;dd if=/dev/sda of=/dev/sdb&#8221;, doing a bit copy from the old drive to the new one. This worked, but it left the old partition table in place on the new drive, basically leaving the extra 28 GB invisible. I didn&#8217;t realize this until a couple of weeks ago when I extended the &#8220;rear&#8221; partition to consume the rest of the space on the disk.

Anyway, now I have this totally stupid partition scheme on the disk:<figure id="attachment_628" style="width: 600px;" class="wp-caption aligncenter">

<a href="http://evanhoffman.com/evan/wp-content/uploads/2010/09/Screenshot-dev-sda-GParted.png" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://evanhoffman.com/evan/wp-content/uploads/2010/09/Screenshot-dev-sda-GParted.png', '']);" ><img src="http://evanhoffman.com/evan/wp-content/uploads/2010/09/Screenshot-dev-sda-GParted.png" alt="Retarded partitioning scheme" title="Screenshot--dev-sda - GParted" width="590" class="size-full wp-image-628" /></a><figcaption class="wp-caption-text">Retarded partitioning scheme</figcaption></figure> 

I could fix it but it seems like a big pain in the ass. Maybe I can clear out /dev/sd8 and copy my docs to it, then copy the root partition to /dev/sda1&#8230; Oh, I don&#8217;t know. This is why my rule of thumb about partitioning is: don&#8217;t.