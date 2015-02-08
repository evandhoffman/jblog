---
title: Goodbye, pg_dump
author: Evan Hoffman
layout: post
permalink: /2013/09/26/goodbye-pg_dump/
dsq_thread_id:
  - 2950049402
categories:
  - Uncategorized
tags:
  - aws
  - backup
  - database
  - devops
  - linux
  - pg_basebackup
  - pg_dump
  - postgres
  - postgresql
  - s3
  - sysadmin
  - wal-e
  - wale
---
I&#8217;ve been a Postgres user and administrator for a while. Over the years, my views on backups have evolved. 

Originally, like most people, I started out with good old pg_dump. With a reasonably small database (under 50 GB) dumping to a flat text file is a fine option. I&#8217;d generally do something like `pg_dump -Upostgres dbname  | gzip > dbname.sql.gz` to compress it on the fly and save space. For years this seemed perfect: dumping the entire database in a single transaction into a single file that can be restored anywhere.

But as my databases started growing larger and larger, the time it took to do a pg\_dump grew as well. At a previous job, the database grew to nearly 2TB and the pg\_dump took nearly 18 hours. We&#8217;d by that point already changed the pg_dump schedule from daily to weekly and then to three times a month and then finally to semi-monthly. Not only was it slow, but since it operated in a single transaction it wreaked havoc with normal database operation for queries that needed locks on tables locked by the dump.

When we moved the database from a physical RAID to a volume on our SAN, that gave us the opportunity to use LUN snapshotting rather than pg_dump (I just remembered I already wrote about that <a href="http://www.evanhoffman.com/evan/?p=1401" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=1401', 'here']);" >here</a>). This let us move to a monthly pg_dump and more frequent snapshot-level backups that took up very little space. This was ideal on Compellent since the snapshots would auto-expire after however long you specified.

When I started at Yodle we were doing nightly pg_dumps and pretty soon we ran into the same problems I&#8217;d seen at Didit with the dump itself interfering with normal DB operation &#8211; the dump would start at midnight and run until 7-8 AM when I started, and after a few months it would still be running at noon. We discussed moving to wal archiving and making a basebackup to NFS but that would require a pretty massive amount of space, and as anybody who uses &#8220;enterprise storage&#8221; knows, that&#8217;s not something you want to do. We discussed building a whitebox file server for backups but nobody was really in love with that option &#8211; we&#8217;re trying to reduce the reliance on physical machines as much as possible. We talked about pushing it all to S3 but that seemed rather difficult.

When I attended <a href="http://pgday.nycpug.org/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://pgday.nycpug.org/', 'NYC PgDay']);" >NYC PgDay</a> earlier this year, there was lots of discussion about WAL-E. I hadn&#8217;t ever head of <a href="https://github.com/wal-e/wal-e" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://github.com/wal-e/wal-e', 'WAL-E']);" >WAL-E</a> so I looked it up and was impressed. Basically, WAL-E handles archiving of wal to S3, but first compresses and pgp-encrypts it. It also handles pushing the basebackup to S3, also compressed and pgp-encrypted. This was just what we were looking for. We set it up and, amazingly, it worked perfectly. After a few weeks (and confirming we can restore from the wal-e backups) we moved our pg\_dump to weekly, on the weekend when it doesn&#8217;t interfere with any user processes. We do a wal-e basebackup every 3-4 days or so and retain 3 of them. We retain all the wal so we can restore the DB to any point within the last ~10 days if needed. The best part is it&#8217;s faster than pg\_dump, and since the basebackup doesn&#8217;t operate in a transaction (it&#8217;s a filesystem-level backup rather than an application-level backup) it doesn&#8217;t mess with user queries. There&#8217;s of course elevated IO during this time but our SAN has more than enough bandwidth.

We setup some basic monitoring of S3 (check the age of the most recent WAL and log it in Zabbix) just to ensure the backups are actually happening, and we&#8217;re at the point where we&#8217;re discussing moving pg_dump to monthly, or simply not doing it at all. Overall, wal-e has been a huge win for us, enabling better, faster backups that don&#8217;t interfere with the DB itself, and, while not free, aren&#8217;t ridiculously expensive. And since it&#8217;s in its own S3 bucket, you can tweak the bucket settings (e.g. enable RRS) to save money, and Amazon tells you exactly how much your backups cost you.