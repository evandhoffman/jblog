---
title: Migrating RT from mysql to postgresql.
author: Evan Hoffman
layout: post
permalink: /2009/08/06/migrating-rt-from-mysql-to-postgresql/
dsq_thread_id:
  - 2955939845
categories:
  - Uncategorized
tags:
  - howto
  - migrate
  - mysql
  - pgsql
  - postgresql
  - rt
---
So I wanted to move our old RT 3.6 system to a new VM running a clean CentOS 5.3 x86 install and RT 3.8. I got RT installed but the 3.6 system was running on MySQL, which sucks (still), and I wanted to move it to Postgres, which rules (more and more each day). So Googling for &#8220;migrate rt from mysql to postgresql&#8221; brought me <a href="http://himdel.blogspot.com/2009/04/migrating-request-tracker-from-rt-345.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://himdel.blogspot.com/2009/04/migrating-request-tracker-from-rt-345.html', 'here']);" >here</a>. I tried the procedure listed there but it wasn&#8217;t quite working for me. my2pg.pl and mysql2postgresql.perl both caused segfaults when I tried to run them. I realized I was on my own, and it wasn&#8217;t really that bad.

<!--more-->

I got the DB mostly imported by doing this:

`mysqldump -p --complete-insert rt3 --extended-insert=false --compatible=postgresql --compact -t  --default-character-set=UTF8 --user=root > helpdesk-mysql.dump.sql`

Gzip the .sql file and upload to the new server. Once there I run this to convert the escaped quotes to Postgres format and strip the quotes from the table name (e.g. INSERT INTO &#8220;ACL&#8221; &#8230;):

`perl -i -npe 's/^INSERT INTO "(\w+)"/INSERT INTO \L$1/; '"s/,'/,E'/g;" helpdesk-mysql.dump.sql`

Then the problem I had was that all of the column names in the insert were wrapped in quotes. I wrote this Perl script to strip them (maybe it can be done in a one-liner but my Perl-fu isn&#8217;t that good <img src="http://www.evanhoffman.com/evan/wp-includes/images/smilies/icon_sad.gif" alt=":(" class="wp-smiley" /> )

<pre>#!/usr/bin/perl

use strict;
use warnings;

while (&lt;>) {
#       INSERT INTO acl ("id", "PrincipalType", "PrincipalId", "RightName", "ObjectType", "ObjectId", "DelegatedBy", "DelegatedFrom") VALUES (6,E'Group',41,E'AdminGroup',E'RT::System',1,0,0);
        if ($_  =~ m/^INSERT INTO (\w+) \(([ \w\"\,]+)\) VALUES (.+)/) {

                my $table = $1;
                my $cols = $2;
                my $data = $3;

                $cols =~ s/\"//g;       # remove double-quotes from column names

                print "INSERT INTO $table ($cols) VALUES $data\n";
        }
        else {
                print $_;
        }
}
</pre>

I then piped that right into psql:

`psql -Upostgres rt_db < helpdesk-mysql.dump.sql.ins 2>out.txt`

That did generate some errors about duplicate key constraints being violated, so I still have some work to do on it, but it seems ok overall. Tickets showed up in RT and I was able to make a new one, though I did have to set the currval() for each sequence via the queries listed in the blog post from himdel.

**Edit:** I should add that the destination DB should be created by running the &#8220;make initialize-database&#8221; command in the RT directory. If there are any errors in the restore process, run &#8220;make dropdb&#8221; and then just &#8220;make initialize-database&#8221; again to recreate it pristine.