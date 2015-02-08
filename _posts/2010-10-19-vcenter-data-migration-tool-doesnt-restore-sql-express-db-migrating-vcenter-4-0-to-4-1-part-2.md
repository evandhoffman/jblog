---
title: 'vCenter Data Migration tool doesn&#039;t restore SQL Express DB &#8211; Migrating vCenter 4.0 to 4.1 (part 2)'
author: Evan Hoffman
layout: post
permalink: /2010/10/19/vcenter-data-migration-tool-doesnt-restore-sql-express-db-migrating-vcenter-4-0-to-4-1-part-2/
dsq_thread_id:
  - 2949525062
categories:
  - Uncategorized
tags:
  - blank database
  - empty database
  - migration
  - vcenter
  - vmware
---
I&#8217;m into my second day on this project. It appears that the backup and restore procedures run to completion but on the target machine the database is empty. This is pretty easy to check; I just went in with SQL Management Studio, looked in VIM\_VCDB -> Tables -> dbo.VPX\_DATASTORE and did &#8220;Open Table.&#8221; On the source machine I saw all the datastores in there (as expected). On the target machine, however, the table was empty.  
<!--more-->

  
I found <a href="http://kb.vmware.com/kb/1024380" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://kb.vmware.com/kb/1024380', 'KB 1024380']);" >KB 1024380</a> describing a failure of the migration tool with a symptom, &#8220;Data Migration Tool logs (restore.log) does not indicate any errors, but the database is blank.&#8221; The KB says:

> This known issue occurs when the Data Migration Tool is run against a vCenter Server with MS SQL Express that has been updated (for example, if vCenter Server 4.0 is updated to Update 2 or if vCenter Server 2.5 is updated to vCenter Server 4.0.)

Well, that&#8217;s me &#8211; we went from 3.5 to 4.0. I made the registry change and went through the procedure again (vCenter and Update Manager must be uninstalled from the target machine before attempting to restore again).

(Some time later&#8230;)

**It worked!** After changing the registry key everything appears to have been copied over. I feel dumb for having wasted so much time on this, but oh well. Moral of the story: Google Early and Google Often. vCenter appears to be working fine (with a nice performance boost since the VM it&#8217;s now on has 4GB ram). I ended up moving it into the cluster &#8211; our ESX hardware is just so much better than the machine it had been running on. I&#8217;m still not totally sold on the idea but it does make things simpler. If it ends up causing problems I can always move it back.

Next step: <a href="http://www.evanhoffman.com/evan/?p=864" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=864', 'migrating from ESX 4.0 to ESXi 4.1']);" >migrating from ESX 4.0 to ESXi 4.1</a>.