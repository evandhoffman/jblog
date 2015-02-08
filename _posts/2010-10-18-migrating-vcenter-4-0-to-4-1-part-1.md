---
title: Migrating vCenter 4.0 to 4.1 (part 1)
author: Evan Hoffman
layout: post
permalink: /2010/10/18/migrating-vcenter-4-0-to-4-1-part-1/
dsq_thread_id:
  - 2949523811
categories:
  - Uncategorized
tags:
  - 4.0
  - 4.1
  - migration
  - vcenter
  - vmware
  - vsphere
---
When vSphere 4.1 was released earlier this year, I was mildly excited. Our ESX servers are still running 4.0.0 and I had been planning to upgrade to 4.0u1 for a while. When 4.1 came out it seemed to have vindicated my procrastination: now I could just upgrade to 4.1 and take advantage of whatever new features there were (I read something about memory compression&#8230; whatever).  
<!--more-->

  
I assumed the upgrade procedure for vCenter itself would be pretty simple: uninstall 4.0 and install 4.1. Well, it turns out that&#8217;s not the case! I sat down one day to create my game plan for the upgrade and right away I learned this was going to be a lot more work than I&#8217;d guessed. From <a href="http://kb.vmware.com/kb/1021635" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://kb.vmware.com/kb/1021635', 'VMware&#8217;s KB']);" >VMware&#8217;s KB</a>:

> vCenter Server 4.1 is supported only on 64bit operating systems. There is no in-place upgrade path for vCenter Server installation on 32bit operating systems. If your vCenter Server is installed on a 32bit operating system, you need to migrate to a 64bit operating system where vCenter Server 4.1 can be installed.

Boom. I&#8217;d either have to setup a machine in parallel running 64-bit Windows, or back everything up, format the existing machine, install 64-bit Windows on it, and restore it. Either way it looked like it was the same procedure.

I currently run vCenter in a VM, but not within the ESX cluster itself. I know it&#8217;s considered acceptable to do so, but somehow it just never sat well with me to have the VM managing the cluster reside inside the cluster it&#8217;s managing. So, the way I have it setup currently is there&#8217;s one machine running CentOS running VMware Server (the free one), and inside that is the Windows VM running vCenter. This is kind of a weird setup, but it works and if we ever want to move vCenter into the ESX cluster, it&#8217;s already in a VMDK, so it should be relatively easy to move it in.

Since vCenter is already running in a VM, I opted to go for a second Windows VM running in parallel with the original. I am pretty averse to procedures that require you to back a machine up, wipe it, and restore to it, since once you format the old computer you don&#8217;t have much of a rollback procedure. When you set things up in parallel you can usually just bring the &#8220;old&#8221; service back up if the migration fails. If you&#8217;re racing across a bridge, do you want to blow it up behind you? I don&#8217;t, if I can avoid it.

I created a new VM running Windows 2008r2 x86_64 and started installing vCenter server 4.1, but then I read <a href="http://kb.vmware.com/kb/1022137" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://kb.vmware.com/kb/1022137', 'KB 1022137']);" >KB 1022137</a> which includes this great YouTube video <a href="http://www.youtube.com/watch?v=s-zDTfwV-Dk" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.youtube.com/watch?v=s-zDTfwV-Dk', 'demonstrating use of the migration tool']);" >demonstrating use of the migration tool</a>. I cancelled my installation of 4.1 and began the backup procedure on the 4.0 (source) vCenter server.

The first bump I hit in this procedure was an error thrown when attempting to dump the SQL Server 2005 Express database. This is addressed in <a href="http://kb.vmware.com/kb/1024490" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://kb.vmware.com/kb/1024490', 'VMware KB 1024490']);" >VMware KB 1024490</a>. Step 5.c. resolved it for me:

> On the Protocol tab, notice the value of the Pipe Name item. If the value contains an entry similar to \\.\pipe\MSSQL$SQLEXPRESS\sql\query , rename the entry to \\.\pipe\sql\query .

After changing that and restarting the SQL service, the backup ran to completion.

I then attempted to install vCenter server on the new VM, which looked like it was going ok, until I got &#8220;Error 25060: Setup failed to execute sqlcmd.exe.&#8221; Google took me to <a href="http://communities.vmware.com/thread/150188" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://communities.vmware.com/thread/150188', 'this post on the VMware forums']);" >this post on the VMware forums</a> which said I needed to download &#038; install some stuff from Microsoft:

> I&#8217;m working through this now &#8230; and the answer you are actually looking for
> 
> http://www.microsoft.com/downloads/details.aspx?FamilyId=50b97994-8453-4998-8226-fa42ec403d17&#038;DisplayLang=en
> 
> You need to download:  
> Microsoft SQL Server Native Client  
> Microsoft SQL Server 2005 Command Line Query Utility 

I downloaded sqlncli\_x64.msi and SQLServer2005\_SQLCMD_x64.msi. After installing both, I was able to complete the installation of vCenter 4.1. I then restored the database from the 4.0 vCenter installation as per the video (run install.bat) and that appeared to work without incident.

I installed the vSphere 4.1 client on my desktop, connected to the new vCenter 4.1 server and&#8230; it was blank. It had appeared to restore successfully, but vCenter doesn&#8217;t show any of the imported data. I installed <a href="http://www.microsoft.com/downloads/en/details.aspx?FamilyID=5d76230d-580d-4874-8c7d-93491a29db15" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.microsoft.com/downloads/en/details.aspx?FamilyID=5d76230d-580d-4874-8c7d-93491a29db15', 'SQL Server Management Studio Express']);" >SQL Server Management Studio Express</a>, browsed the database, and&#8230; it was empty. I tried deleting the databases that appeared to be related to vCenter and running install.bat again, but that failed with the same error:

<pre>C:\datamigration>install.bat
[INFO] Starting vSphere 4.1 migration installer script...
[INFO] Checking prerequisites...
[INFO]   Migration data directory: C:\datamigration\data
[INFO] Checking vCenter Server migration data...
[INFO] vCenter Server is already installed on this system
[WARNING]   vCenter Server migration  data or system does not meet prerequisite

[INFO] Checking VMware vCenter Update Manager migration data...
[INFO] VMware vCenter Update Manager is already installed on this system.
[WARNING]   VMware Update Manager backup data or system does not meet prerequisi
te

[INFO] Checking vCenter Orchestrator prerequisites...
[WARNING]  vCO already installed on this machine
[WARNING]   vCenter Orchestrator backup data or system does not meet prerequisit
e

[WARNING]   vCenter Server will not be installed
[INFO]   VMware Update Manager will not be installed
[INFO]   vCenter Orchestrator configuration will not been imported

C:\datamigration>
</pre>

Well, I&#8217;ve been writing this post as I go, and right now I&#8217;m somewhat stumped. I&#8217;m going to uninstall vCenter and Update Manager and see if I can start over. For now, I&#8217;ll post this while I ruminate.

**Edit:** My &#8220;Part 2&#8243; post about the migration to vCenter 4.1 (which I completed successfully) is available here: <a href="http://www.evanhoffman.com/evan/?p=840" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=840', 'http://www.evanhoffman.com/evan/?p=840']);" >http://www.evanhoffman.com/evan/?p=840</a>