---
title: 'Migrating a PostgreSQL DB to a new machine without doing a dump &#038; restore'
author: Evan Hoffman
excerpt: rsync over NFS can be so fast.
layout: post
permalink: /2011/07/11/migrating-a-postgresql-db-to-a-new-machine-without-doing-a-dump-restore/
dsq_thread_id:
  - 2949520998
categories:
  - Uncategorized
tags:
  - compellent
  - database
  - dba
  - devops
  - dl360
  - dump
  - howto
  - hp
  - linkedin
  - linux
  - migration
  - msa
  - msa70
  - nfs
  - postgres
  - postgresql
  - restore
  - rsync
  - work
---
Long ago, before I stepped into my role as de facto DBA in my current job, dump &#038; restore of our Postgres database (dumping the entire DB to a text file, formatting the data disk, and restoring the data) was a pretty regular event. This was needed because we didn&#8217;t regularly vacuum the DB, which in turn was due to vacuums taking forever (which in the end ended up being due to crappy underlying hardware). We started doing monthly global vacuums (they took so long that monthly was about all we could handle) and we discovered we no longer needed to do dump &#038; restores. When we finally upgraded to Postgres 8.2, which introduced autovacuum, things got even better, since we didn&#8217;t have to manage vacuuming via cron jobs.  
<!--more-->

  
In the years between then and now our Postgres DB has ballooned to such a size that a D&#038;R isn&#8217;t something we could feasibly do in a scheduled maintenance window. The last time I did one was when we bade farewell to the decrepit database in September, 2007. At that point our database consumed 730 GB on disk. Looking through my email archives, we began the dump at 6:30 PM on a Friday night and it completed at 3:48 AM Saturday. The restore started around 9 AM and ran until around 1 PM (I assume it went so much faster than the dump due to the new DB being significantly better hardware-wise). Building indices took until 9:27 PM Saturday. We then ran a global &#8220;ANALYZE&#8221; to generate DB stats; the analyze ran from 10 PM until 1 AM Sunday. We then had most of Sunday to process the backlog of data that accumulated since Friday afternoon when we took the database offline.

So, with a 730 GB DB, the entire procedure took 9 hours (dump) + 4 hours (restore) + 8 hours (index rebuild) + 3 hours (analyze), so about 24 hours.

However, as I said, in the years since then our database has grown as we house more and more data, currently at about 1220 GB. It might have been possible to do the migration via dump &#038; restore in the scheduled window, but I wasn&#8217;t looking forward to it. Instead, I decided to try a different option: copying the data files directly from the old server to the new one. If this worked it would eliminate almost all the overhead and the move would be complete in however long it took to copy the data from one host to the other.

### Reasons

We had a couple reasons for doing this upgrade. Performance wasn&#8217;t really one of them though; we were pretty confident that the performance of the DB was as good as we were likely to get with platter disks, and our SAN doesn&#8217;t currently have SSDs. The old DB has dual Xeon 5160 2-core CPUs @3.0ghz, 32 GB memory, and a RAID 5 OS volume. The database resided on an <a href="http://h10010.www1.hp.com/wwpc/us/en/sm/WF05a/12169-304616-3930445-3930445-3930445-3355734.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://h10010.www1.hp.com/wwpc/us/en/sm/WF05a/12169-304616-3930445-3930445-3930445-3355734.html', 'HP MSA70']);" >HP MSA70</a>, with 24x 10krpm 146 GB SAS drives (+1 hot spare) in RAID 10 for a 1.6 TB logical volume. At the time I debated RAID 6 vs RAID 10 but in the end I opted for the performance of RAID 10 vs the capacity of RAID 6 and it worked out well. 

But one of the reasons we decided to upgrade was that the drives in the DB were starting to die and the warranty had expired on them, and each disk cost about $300 to replace. That was a pretty big liability and I expected the disks to begin dying more frequently.

Another reason for upgrading was the benefits of having the data on the SAN, especially snapshotting. We&#8217;d been doing daily backups for a while but doing a dump of the DB while it&#8217;s in use makes it take forever, causing degraded performance while it&#8217;s running. Snapshot isn&#8217;t a perfect solution, but at least it&#8217;s an option.

Another reason for wanting to move the DB to the SAN was for DR purposes; if we setup SAN-SAN replication to another site, with the DB on the SAN, we get that backed up for free.

And probably the biggest reason, we were up to 1.25 TB used out of 1.6, over 80% full. We&#8217;d probably be good for another few months, but for me 80% is pretty full.

### Prerequisites

In order for this to work, the version of Postgres on both machines has to be of the same minor version. In my case, the source DB (server A) was running 8.2.5 (the newest at the time the box was built), so I built 8.2.18 (<a href="http://yum.pgrpms.org/srpms/8.2/redhat/rhel-5Client-i386/postgresql-8.2.18-1PGDG.rhel5.src.rpm" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://yum.pgrpms.org/srpms/8.2/redhat/rhel-5Client-i386/postgresql-8.2.18-1PGDG.rhel5.src.rpm', 'source RPM']);" >source RPM</a>) on the target (server B). Server B is pretty beefy: <a href="http://h10010.www1.hp.com/wwpc/us/en/sm/WF05a/15351-15351-3328412-241644-241475-4091408.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://h10010.www1.hp.com/wwpc/us/en/sm/WF05a/15351-15351-3328412-241644-241475-4091408.html', 'HP DL360 G7']);" >HP DL360 G7</a> with dual <a href="http://ark.intel.com/Product.aspx?id=47921" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://ark.intel.com/Product.aspx?id=47921', 'Xeon X5660 6-core CPUs']);" >Xeon X5660 6-core CPUs</a> @2.8 GHz, 96 GB PC3-10600 ECC mem, QLogic QLE-4062 iSCSI HBA connected to a 4TB volume on our Compellent SAN (tier 1, RAID 10 across 32x 15krpm FC disks). Both machines of course need to be of the same architecture, in my case x86_64. It might work across Intel/AMD, but I&#8217;m not sure about that; fortunately I didn&#8217;t have to worry about that.

### Moving the data

When we did dump &#038; restores, we dumped directly to a commonly-mounted NAS, which worked well, since we wouldn&#8217;t start the restore until the dump was complete, and we didn&#8217;t want to consume disk on the target with a gigantic dump file (in addition to spinning the disks with reading the dumpfile while attempting to write to them; the contention causes everything to go much slower). There wasn&#8217;t really any need to use a NAS as an intermediary in this case though, it would just double the amount of time needed to get the data from A to B.

I created an NFS export on B:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="line_numbers">
        <pre>1
</pre>
      </td>
      
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #000000; font-weight: bold;">/</span>data<span style="color: #000000; font-weight: bold;">/</span>pgsql             10.0.0.35<span style="color: #7a0874; font-weight: bold;">&#40;</span>rw,no_root_squash<span style="color: #7a0874; font-weight: bold;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>

And mounted it on A with these options in /etc/fstab:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="line_numbers">
        <pre>1
</pre>
      </td>
      
      <td class="code">
        <pre class="bash" style="font-family:monospace;">10.0.0.36:<span style="color: #000000; font-weight: bold;">/</span>data<span style="color: #000000; font-weight: bold;">/</span>pgsql  <span style="color: #000000; font-weight: bold;">/</span>mnt<span style="color: #000000; font-weight: bold;">/</span>gannon<span style="color: #000000; font-weight: bold;">/</span>nfs nfs     rw,<span style="color: #007800;">rsize</span>=<span style="color: #000000;">32768</span>,<span style="color: #007800;">wsize</span>=<span style="color: #000000;">32768</span>,<span style="color: #007800;">nfsvers</span>=<span style="color: #000000;">3</span>,noatime,udp        <span style="color: #000000;"></span>     <span style="color: #000000;"></span></pre>
      </td>
    </tr>
  </table>
</div>

I tried TCP vs UDP mounts and found UDP was faster.

I then copied the data over with my favorite Unix tool, rsync:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="line_numbers">
        <pre>1
</pre>
      </td>
      
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #000000; font-weight: bold;">time</span> rsync <span style="color: #660033;">-atp</span> <span style="color: #660033;">--delete</span> <span style="color: #660033;">--progress</span> <span style="color: #000000; font-weight: bold;">/</span>var<span style="color: #000000; font-weight: bold;">/</span>lib<span style="color: #000000; font-weight: bold;">/</span>pgsql<span style="color: #000000; font-weight: bold;">/</span>data <span style="color: #000000; font-weight: bold;">/</span>mnt<span style="color: #000000; font-weight: bold;">/</span>gannon<span style="color: #000000; font-weight: bold;">/</span>nfs<span style="color: #000000; font-weight: bold;">/</span> <span style="color: #000000; font-weight: bold;">&gt;</span> <span style="color: #000000; font-weight: bold;">/</span>home<span style="color: #000000; font-weight: bold;">/</span>evan<span style="color: #000000; font-weight: bold;">/</span>rsync.log</pre>
      </td>
    </tr>
  </table>
</div>

### Dry run

In January I did a dry run of the procedure. I had tried copying data over without stopping postgres on A, so as not to cause a service interruption, but it didn&#8217;t work; data was changing too rapidly. By the time the rsync completed, the files it had copied over earliest had already been modified again. I ended up scheduling some downtime for a weekend and did the copy. With the above NFS settings I was able to transfer data at around 50 MB/s over our gigabit switches, the whole thing took 3-4 hours. When it came up, everything seemed to be fine. I was pretty happy, because 3-4 hours is a whole lot better than 24+.

### Day of Reckoning

I finally did the real migration this past weekend. I started it at 8 PM and (after an rsync snafu) completed it around 4 AM. The snafu was caused by my use of the &#8220;`--delay-updates`&#8221; flag, which I later learned copies modified files to a `/.~tmp~/` directory, and when all of them are copied, moves them into place in an attempt to make it a more &#8220;atomic&#8221; operation. I didn&#8217;t realize this was what was happening, and I got a little freaked out when I saw the disk usage for the target growing 100 GB larger than the source. I cancelled the rsync and ran it again, stupidly dropping the &#8211;delay-updates flag, which with the &#8211;delete flag caused it to delete all the stuff in .~tmp~ that it had already copied over. It deleted like 300 GB of stuff before I freaked out and cancelled it again. I cursed myself a few times, then manually moved the contents of .~tmp~ up to the parent to salvage what had already been transferred, and ran the rsync once again to move the remaining data. So it probably would have been done much sooner had I not cancelled it and then deleted a bunch of my progress.

You may notice that the rsync flags above don&#8217;t include `-z`. With huge binary files being transferred over a fast LAN I don&#8217;t think there&#8217;s much reason to use -z, in fact when I added -z the throughput plummeted.

After copying the data, I moved the virtual IP of the DB to the new machine, moved the cron jobs over, started postgres on B and everything worked. I finished all of my cleanup around 6 AM Saturday, though like I said, I would have been done much sooner had I not deleted a bunch of my progress. Even still, this is a lot better than the dump &#038; restore scenario, and has the added benefit of being reversible. I&#8217;m planning to upgrade postgres on A to 8.2.18 and leave it as a standby server; if a problem arises with B, the data can be moved back relatively quickly.

### Conclusion

Well, I don&#8217;t have any great insight to put here, but so far this has worked out. My next DB project is upgrading from 8.2 to either 8.4 or the 9.x series, but that&#8217;s going to require a lot more planning since client drivers will likely need to be updated, and I&#8217;m not sure if queries might need to be altered. 

The end (I hope).