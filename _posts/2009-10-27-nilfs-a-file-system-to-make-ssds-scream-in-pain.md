---
title: 'NILFS &#8211; A File system to make SSDs scream&#8230; in pain?'
author: Evan Hoffman
layout: post
permalink: /2009/10/27/nilfs-a-file-system-to-make-ssds-scream-in-pain/
dsq_thread_id:
  - 2949515979
categories:
  - Uncategorized
tags:
  - fedora
  - filesystems
  - linux
  - nilfs
  - ssd
---
So I got this 128 gig Corsair SSD and put it in my laptop at work. After some fiddling I copied my old disk over to my new disk by booting to Knoppix and doing `dd if=/dev/sda of=/dev/sdb bs=4k conv=notrunc,noerror`. It&#8217;s a lot faster, but what&#8217;s really fast now is my Windows XP VM. Anyway, I was looking into other filesystems to try out on SSD to improve speed and I found <a href="http://www.linux-mag.com/cache/7345/1.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.linux-mag.com/cache/7345/1.html', 'this article']);" >this article</a> claiming that NILFS is the best choice. So I decided to test it using the same ghetto test I always use for filesystem performance: dd!

<!--more-->

Drive info:

<pre>Model: ATA CORSAIR CMFSSD-1 (scsi)
Disk /dev/sda: 128GB
</pre>

The nilfs version in use is whatever&#8217;s in yum:

<pre>[root@ehoffman ~]# rpm -qai nilfs-utils
Name        : nilfs-utils                  Relocations: (not relocatable)
Version     : 2.0.14                            Vendor: Fedora Project
Release     : 2.fc11                        Build Date: Thu 30 Jul 2009 07:16:08 PM EDT
Install Date: Tue 27 Oct 2009 04:18:28 PM EDT      Build Host: xenbuilder4.fedora.phx.redhat.com
Group       : System Environment/Base       Source RPM: nilfs-utils-2.0.14-2.fc11.src.rpm
Size        : 211949                           License: GPLv2+
Signature   : RSA/8, Thu 30 Jul 2009 09:25:18 PM EDT, Key ID 1dc5c758d22e77f2
Packager    : Fedora Project
URL         : http://www.nilfs.org
Summary     : Utilities for managing NILFS v2 filesystems
Description :
Userspace utilities for creating and mounting NILFS v2 filesystems.
</pre>

NILFS volume on /dev/sda9, ext3 on /dev/sda8

<pre>/dev/sda8 on /docs type ext3 (rw)
/dev/sda9 on /nilfs type nilfs2 (rw,gcpid=3711)
</pre>

I found this pretty unsettling when I mounted the nilfs volume:

<pre>[root@ehoffman ~]# mount -t nilfs2 /dev/sda9 /nilfs/
mount.nilfs2: WARNING! - The NILFS on-disk format may change at any time.
mount.nilfs2: WARNING! - Do not place critical data on a NILFS filesystem.
</pre>

Write a 100 MB file and a 1.0 GB file to the nilfs volume and the ext3 volume:

<pre>[root@ehoffman ~]# dd if=/dev/zero of=/docs/zeros.dat bs=4k count=25600
25600+0 records in
25600+0 records out
104857600 bytes (105 MB) copied, 0.434741 s, 241 MB/s
[root@ehoffman ~]# rm -f /docs/zeros.dat
[root@ehoffman ~]# dd if=/dev/zero of=/docs/zeros.dat bs=4k count=256000
256000+0 records in
256000+0 records out
1048576000 bytes (1.0 GB) copied, 19.6931 s, 53.2 MB/s
[root@ehoffman ~]# rm -f /docs/zeros.dat
[root@ehoffman ~]# dd if=/dev/zero of=/docs/zeros.dat bs=4k count=256000
256000+0 records in
256000+0 records out
1048576000 bytes (1.0 GB) copied, 12.7625 s, 82.2 MB/s
[root@ehoffman ~]# rm -f /docs/zeros.dat
[root@ehoffman ~]# dd if=/dev/zero of=/nilfs/zeros.dat bs=4k count=25600
25600+0 records in
25600+0 records out
104857600 bytes (105 MB) copied, 5.42617 s, 19.3 MB/s
[root@ehoffman ~]# dd if=/dev/zero of=/nilfs/zeros.dat bs=4k count=256000
256000+0 records in
256000+0 records out
1048576000 bytes (1.0 GB) copied, 47.4966 s, 22.1 MB/s
[root@ehoffman ~]# rm -f /nilfs/zeros.dat
</pre>

With 100 MB, ext3 writes at 200+MB/s and with 1 gig it writes around 50+ MB/s. I think the SATA controller on my laptop is SATA I (not SATA II). On nilfs it seems to hover around 20-25 MB regardless of file size. Anyway, based on this I guess I&#8217;ll be staying with ext3/4 for the foreseeable future.