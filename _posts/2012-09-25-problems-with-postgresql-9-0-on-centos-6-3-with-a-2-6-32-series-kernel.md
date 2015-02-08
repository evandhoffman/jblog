---
title: Problems with PostgreSQL 9.0 on CentOS 6.3 with a 2.6.32-series kernel
author: Evan Hoffman
layout: post
permalink: /2012/09/25/problems-with-postgresql-9-0-on-centos-6-3-with-a-2-6-32-series-kernel/
amazon-product-excerpt-hook-override:
  - 3
amazon-product-content-hook-override:
  - 2
amazon-product-newwindow:
  - 3
categories:
  - Uncategorized
tags:
  - 2.6.32
  - 9.0
  - blocking
  - cache
  - centos
  - database
  - hardware
  - io
  - linkedin
  - memory
  - numa
  - postgres
  - postgresql
  - proc
  - purestorage
  - reclaim
  - redhat
  - server
  - sql
  - vmstat
  - zone_reclaim
  - zone_reclaim_mode
---
*Note: this is cross-posted from the <a href="http://www.yodletechblog.com/post/problems-with-postgresql-9.0-on-centos-6.3-with-a-2.6.32-series-kernel/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.yodletechblog.com/post/problems-with-postgresql-9.0-on-centos-6.3-with-a-2.6.32-series-kernel/', 'Yodle Tech Blog']);" >Yodle Tech Blog</a>*.

We recently migrated our Postgres database to a significantly more powerful machine. While our existing server was doing okay load-wise, we were approaching 90% full on its Postgres volume. We&#8217;d also recently acquired an awesome new PureStorage SSD-based SAN and were excited to move our database onto it. For comparison, here are the the specs for the two systems:

<table style='background-color: #fff; border-spacing: 2;' border='0' cellspacing='2' cellpadding='3'>
  <tr>
    <td style='background-color: #fff;'>
    </td>
    
    <td style='background-color: #ccc; font-weight: bold;'>
      Old server
    </td>
    
    <td style='background-color: #ccc; font-weight: bold;'>
      New server
    </td>
  </tr>
  
  <tr>
    <td style='background-color: #ccc; font-weight: bold;'>
      Nickname
    </td>
    
    <td style='background-color: #eee;'>
      DB1
    </td>
    
    <td style='background-color: #eee;'>
      DB2
    </td>
  </tr>
  
  <tr>
    <td style='background-color: #ccc; font-weight: bold;'>
      CPU
    </td>
    
    <td style='background-color: #eee;'>
      2x 6-core <a href="http://ark.intel.com/products/52576/Intel-Xeon-Processor-X5690-(12M-Cache-3_46-GHz-6_40-GTs-Intel-QPI)" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://ark.intel.com/products/52576/Intel-Xeon-Processor-X5690-(12M-Cache-3_46-GHz-6_40-GTs-Intel-QPI)', 'Xeon X5690']);" >Xeon X5690</a> CPUs at 3.47GHz
    </td>
    
    <td style='background-color: #eee;'>
      2x 8-core <a href="http://ark.intel.com/products/64596/Intel-Xeon-Processor-E5-2690-%2820M-Cache-2_90-GHz-8_00-GTs-Intel-QPI%29" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://ark.intel.com/products/64596/Intel-Xeon-Processor-E5-2690-%2820M-Cache-2_90-GHz-8_00-GTs-Intel-QPI%29', 'E5-2690']);" >E5-2690</a> CPUs at 2.90 GHz
    </td>
  </tr>
  
  <tr>
    <td style='background-color: #ccc; font-weight: bold;'>
      Memory
    </td>
    
    <td style='background-color: #eee; border-spacing: 2;'>
      96 GB 1333 MHz
    </td>
    
    <td style='background-color: #eee;'>
      128 GB 1600 MHz
    </td>
  </tr>
  
  <tr>
    <td style='background-color: #ccc; font-weight: bold;'>
      Storage
    </td>
    
    <td style='background-color: #eee;'>
      <ul>
        <li>
          Dell PERC H800 SAS HBA
        </li>
        <li>
          Dell MD1220 drive enclosure
        </li>
        <li>
          24x 15k RPM SAS drives configured in RAID 10
        </li>
      </ul>
    </td>
    
    <td style='background-color: #eee;'>
      <ul>
        <li>
          QLogic QLE2562 dual-port 8 Gbit FC HBA
        </li>
        <li>
          PureStorage SSD-based SAN
        </li>
      </ul>
    </td>
  </tr>
  
  <tr>
    <td style='background-color: #ccc; font-weight: bold;'>
      OS
    </td>
    
    <td style='background-color: #eee;'>
      CentOS 5.6 x86_64
    </td>
    
    <td style='background-color: #eee;'>
      CentOS 6.3 x86_64
    </td>
  </tr>
  
  <tr>
    <td style='background-color: #ccc; font-weight: bold;'>
      Kernel
    </td>
    
    <td style='background-color: #eee;'>
      2.6.18-238.9.1.el5
    </td>
    
    <td style='background-color: #eee;'>
      2.6.32-279.2.1.el6.x86_64
    </td>
  </tr>
</table>

As you can probably guess, moving from spinning disk onto SSD was the centerpiece of the upgrade. We&#8217;d tested the PureStorage system pretty extensively and used it in some secondary databases and seen some amazing improvements. We were excited to see how it would handle our production workload.

## Basic Configuration

DB1 was built in early 2011 and was running CentOS 5.6. We&#8217;re generally pretty big fans of CentOS, so when building DB2 we went with CentOS 6.3, which was the latest version. We followed our standard installation procedure, which essentially means doing a minimal installation and then installing just what we needed, and of course updating all packages to the latest versions. 

We provisioned a big LUN for the Postgres data and 500 GB each for Postgres &#8220;temp&#8221; and logging. While a separate partition/volume for pg\_log/pg\_xlog is a common practice, we also created a separate volume for temp as a safeguard against runaway queries that generate lots of temp files due to hash-joins or sorting gigantic result sets. In the past we&#8217;ve run into situations where the temporary files consume all available space, and if the sort space is on the same partition as your data you run the risk of crashing the database if Postgres attempts to allocate space and there&#8217;s none to be had. Since all the SAN volumes are <a href="http://en.wikipedia.org/wiki/Thin_provisioning" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://en.wikipedia.org/wiki/Thin_provisioning', 'thin provisioned']);" >thin provisioned</a>, there&#8217;s no penalty for creating separate volumes, and we get the additional benefit of being able to specify different mount options for each volume. (It&#8217;s worth noting that PostgreSQL 9.2 allows you to <a href="http://www.postgresql.org/docs/9.2/static/runtime-config-resource.html#GUC-TEMP-FILE-LIMIT" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.postgresql.org/docs/9.2/static/runtime-config-resource.html#GUC-TEMP-FILE-LIMIT', 'specify a maximum size for temp space']);" >specify a maximum size for temp space</a>, but since we&#8217;re still on 9.0 that option isn&#8217;t available to us.) We then created XFS filesystems on each volume. We didn&#8217;t bother partitioning the volumes, just created the filesystems on the raw devices. This way, in the event we need to expand a volume, we don&#8217;t have to worry about growing the partition, just the LUN (in the SAN) and the filesystem.

## The initial cutover

We configured <a href="http://wiki.postgresql.org/wiki/Streaming_Replication" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://wiki.postgresql.org/wiki/Streaming_Replication', 'streaming replication']);" >streaming replication</a> between DB1 and DB2 to get DB2 in sync with DB1. On the night of Sunday, August 19th, we cut over from DB1 to DB2 as the primary. All things considered, everything went well and our database was only inaccessible to our servers for a few minutes. (Incidentally, while the database was &#8220;down&#8221; all our client sites were fine since they are completely decoupled from the database.) After verifying everything was up we ran some test queries and confirmed the performance was awesome, gave ourselves some virtual high-fives and went to bed. 

The next day, Monday, started out well. Everything that touched the DB was much faster. Pages that took 3-5 seconds to load were nearly instant. Jobs that took hours to run finished in under 15 minutes. It was as if someone had hit the <a href="http://en.wikipedia.org/wiki/Turbo_button" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://en.wikipedia.org/wiki/Turbo_button', 'turbo button']);" >turbo button</a> on our whole system. Everything was wonderful. Or so we thought. 

## The problems begin

Around midday Monday we started seeing lots of errors being thrown in our applications with the cause of &#8220;too many clients&#8221; to the database. Our connection limit has been set at 250 forever, so it seemed strange to encounter problems with the connection limit out of the blue. We had recently done a code release so our first line of thought was that there was a bug in the code. We use Hibernate, and it&#8217;s pretty easy to blame Hibernate. The problem was intermittent &#8212; every 20-30 minutes we&#8217;d see a bunch of SQLExceptions due to &#8220;too many connections&#8221;, which would last 1-2 minutes, and then it would clear up and everything was fine again. We made it through the rest of the day, and in the evening the problem seemed to have cleared up. We thought maybe it was a transient problem that had resolved itself but we saw a bunch of the same exceptions in some jobs that ran overnight. Our system is pretty resilient though, and most connection attempts automatically retried until they succeeded. The problem was a high priority but since it wasn&#8217;t affecting end users we were able to limp along. Tuesday was much the same as Monday and we continued investigating. Wednesday the real trouble started. 

## The real trouble starts

Our nightly pg_dump, which would normally finish by 8:30 AM, was only halfway complete by 8 AM on Wednesday. We had the same too-many-clients exceptions throughout the night. But around midday we started getting reports from users that our UI was really slow: some pages that had been loading nearly instantly were taking 30-60 seconds to load, or more. This quickly turned the problem into a crisis. We saw the same &#8220;too many connections&#8221; errors in the frontend logs as we had in the backend. The basic behavior was the same &#8211; we&#8217;d get a wave of &#8220;too many connections&#8221; for a couple minutes, then it would clear up for a while, and then the cycle would repeat. 

We finally had to admit that maybe the problem wasn&#8217;t in the code, but in the database. To mitigate the error, we lowered the active connection limits in our connection pools, which improved the &#8220;too many connections&#8221; error for users, but led page loads to hang while the connection pool waited for a slot to open. This wasn&#8217;t much of an improvement for end users though &#8211; rather than failing quickly, their page loads were hanging indefinitely.

## A minor breakthrough &#8211; and a workaround

Thursday morning we were able to get much closer to the actual problem by running vmstat on the database itself. <a href="http://linuxcommand.org/man_pages/vmstat8.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://linuxcommand.org/man_pages/vmstat8.html', 'vmstat']);" >vmstat</a> shows the amount of memory cached vs free along with IO in/out. As people who&#8217;ve run Postgres databases before are probably aware, it&#8217;s common to see almost 100% of available memory &#8220;used&#8221; in top, even when the database doesn&#8217;t seem to be doing very much. This is due to <a href="http://www.westnet.com/~gsmith/content/linux-pdflush.htm" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.westnet.com/~gsmith/content/linux-pdflush.htm', 'caching']);" >caching</a>. 

What we saw in vmstat, however, was that during the &#8220;problem&#8221; periods the &#8220;cached&#8221; was decreasing while the &#8220;free&#8221; was increasing while the io in/out dropped to nearly zero. After some thought, we realized that the reason for the &#8220;too many connections&#8221; errors was that queries that normally finished in a few milliseconds were instead taking 30+ seconds due to the IO blocking we were seeing in vmstat. With queries running much longer than usual, they quickly ran over each other and we hit the limit of 250. 

So the root behavior we were seeing that seemed to be causing the problem was:

  * Periodically, something was aggressively attempting to reclaim cached memory. 
      * While memory was being reclaimed, IO was almost completely blocked. 
          * While IO was blocked, database queries naturally took longer than usual, leading to rapid consumption of available database connections. </ul> 
            Now that we seemed to have a root cause (or more concrete symptom) we were able to formulate some relevant search queries. Simply Googling for <a href="http://www.google.com/search?q=linux+memory+reclaim&#038;ie=utf-8&#038;oe=utf-8" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.google.com/search?q=linux+memory+reclaim&ie=utf-8&oe=utf-8', 'linux memory reclaim']);" >linux memory reclaim</a>, one of the results on the first page was from the <a href="http://archives.postgresql.org/pgsql-performance/2012-07/msg00215.php" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://archives.postgresql.org/pgsql-performance/2012-07/msg00215.php', 'PostgreSQL performance']);" >PostgreSQL performance</a> list and seemed to describe very similar behavior on machines with a lot of cores. DB2 has 16 physical cores, and we had logical cores enabled, for a total of 32. Maybe that&#8217;s &#8220;a lot.&#8221; We found some other similar threads and even <a href="http://frosty-postgres.blogspot.com/2012/08/postgresql-numa-and-zone-reclaim-mode.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://frosty-postgres.blogspot.com/2012/08/postgresql-numa-and-zone-reclaim-mode.html', 'this blog post']);" >this blog post</a> written on the very same day we started Googling (how&#8217;s that for coincidence?). We also found references to Linux &#038; NUMA memory-reclaim problems for <a href="http://blog.jcole.us/2010/09/28/mysql-swap-insanity-and-the-numa-architecture/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://blog.jcole.us/2010/09/28/mysql-swap-insanity-and-the-numa-architecture/', 'MySQL']);" >MySQL</a> and <a href="http://www.mongodb.org/display/DOCS/NUMA" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.mongodb.org/display/DOCS/NUMA', 'MongoDB']);" >MongoDB</a>. Most posts seemed to indicate there&#8217;s a problem in the 2.6.32 kernel line with optimizations related to NUMA memory. If experiencing problems, the suggested remedy was to set `/proc/sys/vm/zone_reclaim_mode` to 0 if using NUMA. Well, `/proc/sys/vm/zone_reclaim_mode` was already zero, and we weren&#8217;t using NUMA anyway &#8211; we were using interleaved memory. But this was enough to plant the seed of doubt in our minds for the 2.6.32 kernel. DB1 had been running for well over a year without issue on a 2.6.18 kernel.
            
            On Thursday, thankfully, we discovered that &#8220;manually&#8221; clearing the cache caused the IO blockage to clear up immediately, at least until the system had time to fill it back up again and the memory-reclaim process kicked in again. The <a href="http://www.linuxinsight.com/proc_sys_vm_drop_caches.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.linuxinsight.com/proc_sys_vm_drop_caches.html', 'cache is dropped']);" >cache is dropped</a> by executing `echo 1 > /proc/sys/vm/drop_caches`. Every time queries began to block, dropping the cache immediately cleared it up. Since we now had a stopgap workaround, we just cronned dropping of the cache for every 30 minutes. This let us get through Friday and the weekend without having to babysit the database too much for failed connections, etc.
            
            On Friday we decided the best course of action was to put a QLogic fibre channel HBA in DB1 (with its presumably known-good 2.6.18-series kernel), provision some LUNs on it, set it up as a streaming replica and fail back over to it. This way we&#8217;d be on a good server but still have the IO benefits of the SSD. We ordered the card from Newegg Friday and received it Monday. 
            
            ## Tactical Retreat
            
            I installed the card Monday (8/27), configured DB1 as a replica, tested it Tuesday, and Wednesday evening we cut back over to DB1 as our primary. With fingers crossed, we went into Thursday and thankfully everything was fine. While overall performance was a little slower than it had been on DB2 (before it had gotten really bad), it was still blazingly fast compared to how it had been on spinning disk. But most importantly there was no evidence of memory-reclaim problems.
            
            We set DB2 up as a hotstandby and performed some testing on it to try and recreate the problem. Unfortunately we didn&#8217;t have a lot of success (possibly due to hostandbys being read-only replicas of the primary) but based on the grumbling about NUMA and memory-reclaim in the 2.6.32 kernel line, we felt the best course of action was to go to an older, proven kernel. Since DB1 seemed to be fine with its 2.6.18-238.9.1.el5 kernel, we opted to do a clean install of CentOS 5.8, running the latest in the CentOS 2.6.18 line: 2.6.18-308.13.1.el5. 
            
            ## DB2 reborn
            
            We installed CentOS 5.8, remounted the existing LUNs, resynced it with the primary and did some more testing. We didn&#8217;t notice any problems, but since we weren&#8217;t really able to reproduce the problem reliably in the first place that wasn&#8217;t much consolation. On Thursday, 9/6, we cut back over to DB2. As I write this, DB2 has been in service for several weeks without any sign of the memory reclamation issue, or, more importantly, the IO/query blocking.
            
            ## Conclusions
            
            While we&#8217;re really happy to have (apparently) resolved the problem, we found the resolution somewhat unsatisfying. We had tested DB2 pretty extensively internally prior to putting it into production the first time, literally running millions of real-world queries against it and never encountered anything like the blocking we saw once it took on the production workload. Prior to making it our primary DB we had used it for several days as a hot-standby read-only reporting server and still never saw an issue. After we cut back from DB2 to DB1 we did yet more testing and still weren&#8217;t able to reproduce the query-blocking behavior. While this definitely felt like a problem related to the Linux kernel we didn&#8217;t have any hard evidence, and going back to a 2.6.18-series kernel was to some degree a leap of faith. Thankfully, so far it&#8217;s worked out for us.
            
            Though we didn&#8217;t ultimately figure out what it was in the kernel that caused the problem, we did learn a lot from the experience. Most importantly, we ironed out our database cutover procedure to the point where it&#8217;s not really a major event to cutover. Since clearly at some point we&#8217;ll want to upgrade from CentOS 5.8, we know that we can always fail back to another database with minimal interruption if a problem like this arises in the future.