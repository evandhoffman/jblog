---
title: The VMware datastore LUN 2TB (well, 1.99999 TB) size limit
author: Evan Hoffman
layout: post
permalink: /2010/09/24/the-vmware-datastore-lun-2tb-well-1-99999-tb-size-limit/
dsq_thread_id:
  - 2949527921
categories:
  - Uncategorized
tags:
  - 1tb
  - 2tb
  - compellent
  - disk
  - esx
  - esxi
  - größe
  - iscsi
  - linux
  - reservation errors
  - scsi
  - vmware
  - work
---
I started migrating our physical machines to VMs using VMware a few years ago and the first problem I ran into is still the most annoying one: the size limit for LUNs is, <a href="http://kb.vmware.com/kb/3371739" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://kb.vmware.com/kb/3371739', 'per VMware&#8217;s docs']);" >per VMware&#8217;s docs</a>, (2TB &#8211; 512B). That&#8217;s 512 bytes shy of 2TB, so basically 1.99999 TB, or 2047.999 GB. So when I create a new LUN for a datastore in the SAN the max size is 2047 GB. Now, as the VMware KB article states, **this is a limitation of SCSI, not VMware per se**, but that doesn&#8217;t make it any less annoying. When I first setup ESX, I created a 5 TB LUN for the datastore. It showed up in vCenter as 1 TB. After some Googling I learned of the 2 TB limit &#8212; the usable space is basically <tt>usable space = (size of lun) % 2TB</tt>, where % is <a href="http://en.wikipedia.org/wiki/Modulo_operation" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://en.wikipedia.org/wiki/Modulo_operation', 'the modulo operator']);" >the modulo operator</a> &#8212; and found something suggesting using extents to expand the datastore across luns. I did that, but I later learned that there seems to be a consensus that extents should be avoided.

There are other things I learned along the way &#8211; that you want to limit the number of vmdks per datastore anyway, for example, due to the risk of SCSI reservation errors and IO contention, but these are all things that it feels like we shouldn&#8217;t have to worry about. I can see having separate LUNs/datastores for different logical groupings of disks, allowing you to have different snapshot schedules for each datastore, or allowing you to put an entire datastore in Tier 1 or Tier 3 (to use Compellent parlance) based on its value to you. But having to segregate stuff for technical reasons seems like a problem that should already be solved.

And maybe it is&#8230; I&#8217;ve never tried NFS datastores, but if I created an 8TB LUN, mapped it to a physical box (skirting the 2TB limit imposed by VMware), export the volume from the host over NFS and use that as the datastore, I guess I&#8217;d be able to do all the things I want. Hmm. I&#8217;ll have to think about that. I guess I&#8217;d still keep the ESX host local luns on iSCSI so they could boot from SAN, though I suppose when we move to ESXi that won&#8217;t be much of an issue anyway.

Hmm&#8230; Well, I started writing this as a rant but I think I just morphed it into a new research project.