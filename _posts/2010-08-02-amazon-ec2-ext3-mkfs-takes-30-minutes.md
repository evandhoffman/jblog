---
title: 'Amazon EC2 &#8211; ext3 mkfs takes 30+ minutes?'
author: Evan Hoffman
layout: post
permalink: /2010/08/02/amazon-ec2-ext3-mkfs-takes-30-minutes/
dsq_thread_id:
  - 2951677954
categories:
  - Uncategorized
tags:
  - amazon
  - aws
  - ebs
  - ec2
  - error
  - ext3
  - hangs
  - linux
  - mke2fs
  - mkfs
  - slow
  - superblock
  - work
---
I&#8217;ve been playing around with Amazon EC2 for a new project I&#8217;m working on and so far I&#8217;m really impressed. One thing I&#8217;ve noticed, however, is that it takes forever to create an ext3 filesystem on a new volume. For example, the below command took over 30 minutes to create the filesystem on a 300 GB volume:

> \# mke2fs -j -m0 /dev/sdf1  
> mke2fs 1.40.4 (31-Dec-2007)  
> Filesystem label=  
> OS type: Linux  
> Block size=4096 (log=2)  
> Fragment size=4096 (log=2)  
> 39321600 inodes, 78642183 blocks  
> 0 blocks (0.00%) reserved for the super user  
> First data block=0  
> Maximum filesystem blocks=4294967296  
> 2400 block groups  
> 32768 blocks per group, 32768 fragments per group  
> 16384 inodes per group  
> Superblock backups stored on blocks:  
> 32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,  
> 4096000, 7962624, 11239424, 20480000, 23887872, 71663616
> 
> Writing inode tables: done  
> Creating journal (32768 blocks): done  
> Writing superblocks and filesystem accounting information: done
> 
> This filesystem will be automatically checked every 38 mounts or  
> 180 days, whichever comes first. Use tune2fs -c or -i to override. 

It took about 30 seconds do do everything up to the writing of the superblocks. Not sure why this takes so long, but it&#8217;s happened for every EBS volume I&#8217;ve formatted ext3. Annoying. Initially I thought it was hanging, and ended up terminating an instance that wouldn&#8217;t shutdown or let me cancel the operation. The terminated instance is still being displayed in the UI with a status of &#8220;terminated&#8221; and I can&#8217;t find any way to remove it from the list.

<ins datetime="2011-04-21T17:49:52+00:00">Update 4/21/2011</ins>: I just did a mke2fs on a 300 GB EBS volume in us-west-1b and it finished in under 4 minutes:

<pre>[root@database-west ~]# fdisk /dev/sdf
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x1878699d.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.


The number of cylinders for this disk is set to 39162.
There is nothing wrong with that, but this is larger than 1024,
and could in certain setups cause problems with:
1) software that runs at boot time (e.g., old versions of LILO)
2) booting and partitioning software from other OSs
   (e.g., DOS FDISK, OS/2 FDISK)
Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): p

Disk /dev/sdf: 322.1 GB, 322122547200 bytes
255 heads, 63 sectors/track, 39162 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Disk identifier: 0x1878699d

   Device Boot      Start         End      Blocks   Id  System

Command (m for help): n
Command action
   e   extended
   p   primary partition (1-4)
p
Partition number (1-4): 1
First cylinder (1-39162, default 1): 
Using default value 1
Last cylinder or +size or +sizeM or +sizeK (1-39162, default 39162): 
Using default value 39162

Command (m for help): p

Disk /dev/sdf: 322.1 GB, 322122547200 bytes
255 heads, 63 sectors/track, 39162 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Disk identifier: 0x1878699d

   Device Boot      Start         End      Blocks   Id  System
/dev/sdf1               1       39162   314568733+  83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
[root@database-west ~]# time mke2fs -j -m0 /dev/sdf1 
mke2fs 1.40.4 (31-Dec-2007)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
39321600 inodes, 78642183 blocks
0 blocks (0.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=4294967296
2400 block groups
32768 blocks per group, 32768 fragments per group
16384 inodes per group
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
	4096000, 7962624, 11239424, 20480000, 23887872, 71663616

Writing inode tables: done                            
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done

This filesystem will be automatically checked every 24 mounts or
180 days, whichever comes first.  Use tune2fs -c or -i to override.

real	3m39.197s
user	0m0.114s
sys	0m5.109s
[root@database-west ~]# 

</pre>