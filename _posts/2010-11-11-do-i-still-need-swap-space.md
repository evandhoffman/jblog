---
title: Do I still need swap space?
author: Evan Hoffman
layout: post
permalink: /2010/11/11/do-i-still-need-swap-space/
dsq_thread_id:
  - 2949526239
categories:
  - Uncategorized
tags:
  - compellent
  - database
  - iscsi
  - linux
  - postgresql
  - swap
  - work
---
About three years ago I replaced our primary database. For years we&#8217;d been plagued by awful performance in the database and we were never able to diagnose the problem. The original server was a real beast at the time: 8 Opterons (single core), 32 gigs ram, and a fibre channel RAID connected via a QLogic HBA. This was back in 2005, so those specs don&#8217;t probably sound that impressive today, but this was a crazy configuration (with a crazy price tag to match). On paper it looked like this server should be basically invincible but the performance was awful, slowing down every process within the company. We contacted a few different companies (including <a href="http://commandprompt.com/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://commandprompt.com/', 'CommandPrompt']);" >CommandPrompt</a>, which employs several of the core Pg devs) to see if they could assist us in diagnosing the problems but tuning only helped to a point. There was just something wrong with the box, maybe having to do with the FC HBA itself (which nobody knew much about).  
<!--more-->

When the database became my responsibility I decided it was time to bite the bullet and just replace the bad machine rather than trying to debug it while it was still in service (which was always a fun exercise). After some research I ended up going with a dual-Xeon 5160 (dual-core Woodcrest) @ 3.0 GHz with 32 GB memory. For storage I decided to get away from fibre channel, since that wasn&#8217;t working out too well for us. For the database volume, I went with a <a href="http://h10010.www1.hp.com/wwpc/us/en/sm/WF05a/12169-304616-3930445-3930445-3930445-3355734.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://h10010.www1.hp.com/wwpc/us/en/sm/WF05a/12169-304616-3930445-3930445-3930445-3355734.html', 'HP MSA70']);" >HP MSA70</a> SAS array with 25x 146GB 10K RPM drives. I played around with RAID 5 and RAID 6 but ended up going with RAID 10 since space wasn&#8217;t as much of a concern as raw speed. So we have 24 spindles in RAID 10 + 1 hot spare.

When we put the new DB into service in September, 2007 it was like someone hit the turbo button on all of our processes. Despite halving the number of CPU cores and keeping the same amount of RAM, base performance more or less improved by 5x, with several bottlenecks apparently eliminated completely. Some bad queries went from minutes to seconds. The improvement was so drastic that we were finally able to nightly pg\_dump backups of the database (performance on the 8-cpu DB had been so bad that a pg\_dump would take 4-5 days, so we could only do them weekly at best). We were also able to do weekly VACUUMs and REINDEXES, which helped keep the performance well above what we were used to while keeping disk bloat down (a REINDEX once a month seems to shave 20-30 GB off our consumed disk space).

So now it&#8217;s 2010. The database is still working fine; we&#8217;ve had some disk failures over the years but the hot spare took over automatically and we returned the bad disk to HP for a free replacement under warranty (the array holds 25 disks but I purchased 26 so we could always drop a new one in and never be down a disk for more than a few hours). But now the entire thing is out of warranty, and technology has improved. We&#8217;ve moved almost all of our systems to our Compellent SAN but this core system is still running on its own RAID.

Moving the DB onto the SAN has several advantages:

  1. Leveraging the faster storage within the SAN &#8211; our tier 1 in the SAN currently has 30x 15kRPM drives (plus two hot spares), as opposed to 24x 10kRPM. If we add SSD to the SAN, the DB would be able to take advantage of this &#8220;for free.&#8221;
  2. Snapshots/Replays. Currently our only real method of recovery is from the previous night&#8217;s full backup, and restoring takes > 24 hours. We haven&#8217;t needed to do this in my memory, but it would be nice to have the ability if needed.
  3. Disaster Recovery. When we ultimately implement SAN-SAN replication, the DB data on the SAN will be replicated &#8220;for free&#8221; to the remote site.

The main risk I&#8217;ve been thinking about has been the possible bottlenecking of iSCSI for getting data in and out of the SAN. We have gigabit Ethernet, and the SAN traffic is segregated from regular traffic, but gig-E is still gig-E. After reviewing some iostat numbers though, it doesn&#8217;t appear that will be an issue.

The new hardware for the database itself is pretty incredible, even compared to what we&#8217;ve been purchasing over the past couple of years for our ESX hosts: HP DL360 G7 with dual Xeon X5660 (6-core (!) Westmere) CPUs @ 2.8GHz, **96 GB** PC3-10600 SDRAM, and a QLogic QLE-4062 iSCSI HBA. This is actually about 1/3 the cost of the previous database (since there&#8217;s no RAID) despite having triple the memory and triple the CPU cores. I was originally planning to get the machine with 144GB, but my sales rep reminded me about how <a href="http://www.delltechcenter.com/page/04-08-2009+-+Nehalem+and+Memory+Configurations" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.delltechcenter.com/page/04-08-2009+-+Nehalem+and+Memory+Configurations', 'if you use over 6 DIMMs per CPU, the memory gets downclocked']);" >if you use over 6 DIMMs per CPU, the memory gets downclocked</a>. I opted for 96 GB of faster memory rather than 144 GB of slower mem.

So that&#8217;s a short history (and future) of our primary database. But what prompted me to start writing this post is the question posed in the title: do I still need to configure swap space? With the storage on the SAN, swapping will incur latency which I imagine will be well above local disk. The server will have a local disk (15k SAS) which I intended to use for swap, but now I&#8217;m wondering if there&#8217;s even a point to having swap at all with 96 GB of physical memory.