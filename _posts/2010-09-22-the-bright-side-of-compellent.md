---
title: The bright side of Compellent
author: Evan Hoffman
layout: post
permalink: /2010/09/22/the-bright-side-of-compellent/
dsq_thread_id:
  - 2949524756
categories:
  - Uncategorized
tags:
  - compellent
  - computers
  - disk
  - easy
  - expand
  - install
  - iscsi
  - linux
  - managment
  - money
  - price
  - pricing
  - san
  - sata
  - upgrade
  - work
---
Since I was bemoaning Compellent&#8217;s pricing recently I figured it would be unfair of me not to highlight the upside. Their tagline is (or was when we purchased it) &#8220;The only SAN so sophisticated it&#8217;s simple.&#8221; While I can&#8217;t say whether they&#8217;re the ONLY one, the idea is definitely true. This is the first SAN I&#8217;ve ever used, and aside from the learning curve for iSCSI itself (targets, spinup delay, etc.) it&#8217;s totally simple and intuitive. Create LUNs, map them to servers. Don&#8217;t worry about things like RAID levels or hot disks. We&#8217;re into our second year with Compellent and it&#8217;s definitely lived up to its promise of simplicity.

I don&#8217;t know how much management the average SAN requires but our sales rep recently asked me how much time per week we spend managing the SAN. I crinkled my brow, because I don&#8217;t really spend *any* time managing the SAN. I&#8217;ve logged in to the web interface a lot more over the past few weeks than I normally do because the SAN filled up quickly due to our experimentation with Hadoop, and I wanted to make sure we didn&#8217;t get to 100% before I was able to order more disks. But aside from that incident I think the only times I&#8217;ve logged in to the management console have been to add a LUN or map a datastore to a new ESX host.

I was reminded about this simplicity when we finally added the disks last week. We went to the datacenter Wednesday to move some servers around in the racks to ensure there would be enough power in the SAN rack for the new enclosure (16x 2TB disks). We also updated a firmware update for the SAN (required so it would recognize the new 2TB drives). We have redundant controllers, so we were told there shouldn&#8217;t be any downtime. I don&#8217;t tend to trust those types of statements &#8211; if someone says something will be down for an hour I budget for 4. If it&#8217;s 8 hours I budget for a day. If it&#8217;s zero I just think they&#8217;re lying and it&#8217;s going to explode and kill people.

So all things considered I was rather impressed. We have dual controllers, so the update was installed on one controller first, and that controller rebooted. When it rebooted, the iSCSI traffic did actually fail over properly to the secondary controller. This wasn&#8217;t completely flawless &#8211; the console on some of the machines showed some iSCSI errors, but the machines seemed to be working fine (I rebooted them just to be safe). A couple of the VMs (whose data/swap drives are all on the SAN) barfed and had to be rebooted &#8211; I think our Jabber server was the only casualty, but that was back up in under a minute. When the second controller updated itself, its traffic failed back over to the first one. When it was all done (took about 30 mins total) there was a warning about the ports being unbalanced, which was rectified by clicking the &#8220;rebalance ports&#8221; button. So all in all, I&#8217;d say there was &#8220;pretty much&#8221; no downtime. After the update, we racked the new enclosure and called it a day.

This week a tech from Compellent came onsite to do the actual install for the enclosure (hooking up the fibre loops and installing the new license). This was really zero downtime. I got some alerts that one of the loops was down, but it didn&#8217;t affect anything. Pop the disks in, wire it up, install license, and we&#8217;ve got another 32 TB usable space. It&#8217;s been over a day and the data is in the process of moving from our tier 1 (32x 15krpm FC disks) down to tier 3 (SATA). All in all it was a pretty painless procedure. Sure, it would have been easier had we not had to do the firmware update, but I guess when a new type of drive is introduced that&#8217;s to be expected.

So in conclusion, I guess this just reinforces my theory that the only bad thing about Compellent is the price. And if that&#8217;s the worst thing someone can say about your product, that&#8217;s probably a pretty good place to be.