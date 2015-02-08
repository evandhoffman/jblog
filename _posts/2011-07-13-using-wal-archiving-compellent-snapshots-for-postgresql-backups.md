---
title: 'Using WAL archiving &#038; Compellent snapshots for PostgreSQL backups'
author: Evan Hoffman
excerpt: 'Changing my Postgres DB backup strategy from nightly <code>pg_dump</code>s to WAL archiving with SAN snapshots.'
layout: post
permalink: /2011/07/13/using-wal-archiving-compellent-snapshots-for-postgresql-backups/
categories:
  - Uncategorized
tags:
  - archive_command
  - archiving
  - backup
  - CompCU.jar
  - compellent
  - database
  - devops
  - linux
  - pg_dump
  - pg_start_backup
  - pitr
  - postgres
  - postgresql
  - replay
  - san
  - snapshot
  - wal
---
I seem to have what may be an irrational dislike for differential backups in general. No matter what system I&#8217;m backing up, I feel far more confident in doing complete backups than differential ones. The significant exception to this is rsync, but even rsync does &#8220;full&#8221; backups of the files that have changed. Even so, I usually add the `-W` flag to rsync so it moves the entire file if it&#8217;s been modified. I guess I just like knowing there&#8217;s a single file that contains the entire database rather than having to restore a huge file and then replay a bunch of differential files in sequence.  
<!--more-->

  
This has worked for a long time, even with our PostgreSQL DB, which I&#8217;ve been backing up with good ol&#8217; `pg_dump`, but it&#8217;s gotten to the point that the DB backup takes over 12 hours now, during which performance is seriously degraded. With the <a href="http://www.evanhoffman.com/evan/?p=1390" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=1390', 'recent migration']);" >recent migration</a> to the new server, overall performance seems significantly better overall, but performance during the nightly pg_dump is much worse. Rather than trying to troubleshoot it, I think it&#8217;s time I bit the bullet and move to <a href="http://www.postgresql.org/docs/8.2/interactive/continuous-archiving.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.postgresql.org/docs/8.2/interactive/continuous-archiving.html', 'WAL archiving']);" >WAL archiving</a> to enable differential backups, and stop doing a full backup every night.

The PostgreSQL docs are pretty great at explaining how to do this. Basically:

  1. Configure WAL archiving to copy the WAL files to another server as soon as they&#8217;re complete.
  2. Issue the `pg_start_backup()` command.
  3. Perform a filesystem-level backup of the DB (usually `/var/lib/pgsql` on RedHat/CentOS distros).
  4. Issue the `pg_stop_backup()` command.

That&#8217;s basically it. The backup from step 2 plus the archived WAL files will allow you to recover to any point in time after issuing the pg\_stop\_backup() command. So if you complete your backup at 2 AM on Sunday and your DB crashes on Wednesday at 6 PM, you can restore to any point between 2 AM Sunday and whatever your most recently archived WAL file is (presumably within a few minutes of the crash at 6 PM Wednesday). If you decided you wanted to restore to 12:01 AM on Wednesday, you can do that using the <a href="http://www.postgresql.org/docs/8.2/interactive/continuous-archiving.html#RECOVERY-TARGET-TIME" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.postgresql.org/docs/8.2/interactive/continuous-archiving.html#RECOVERY-TARGET-TIME', 'recovery_target_time setting']);" ><code>recovery_target_time</code> setting</a>. My aversion to differential backups aside, this is pretty awesome. 

As I said, the Postgres docs do a good job of covering this procedure completely. The only thing I have to add is how I did this using a SAN-level snapshot as my &#8220;filesystem backup.&#8221; I&#8217;d never done any scripting with Compellent before and it turned out to be pretty easy. From the <a href="https://kc.compellent.com/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://kc.compellent.com/', 'Knowledge Center']);" >Knowledge Center</a>, under **Software** download the latest version of **Command Utility** (I downloaded 5.4.1). The utility itself is just a JAR, you&#8217;ll need Java installed to run it. 

I considered doing a real filesystem backup of the DB (love that rsync) but the problem was that the DB is currently 1.2 TB and gobbling that much space on the NAS wasn&#8217;t very appealing. Compellent replays (snapshots) are just deltas, and I can easily store a week worth of backups for much less than (7 * 1.2 = ) 8.4 TB.

I wrote a crappy bash script to do everything, included below:



I retain the WAL files for 8 days and the snapshots for 7 days, but I may adjust this since the WAL files themselves consume a lot of space &#8211; about 30-40 GB per day. Though this is still less than the gzipped pg_dump I had been doing, which was about 85 GB per day.

I&#8217;ve cronned the script to run at 2 AM and so far it appears to work. Compellent replays are created almost instantly, so the backup script completed in about 10 seconds, which includes 6 seconds of `sleep`, which probably aren&#8217;t necessary. Considering that the pg_dump method took 12+ hours to complete, 10 seconds is immeasurably better. Well, I guess it *is* measurable, you just need to divide 12 hours by 10 seconds. 

I&#8217;m pretty happy with this so far. The improved performance far outweighs any irrational philosophical objection I may have had to differential backups. Buuuut I&#8217;m still going to do pg_dumps on Saturdays.