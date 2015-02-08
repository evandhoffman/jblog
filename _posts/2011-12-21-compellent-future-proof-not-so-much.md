---
title: 'Compellent &#8220;future proof?&#8221; Not so much.'
author: Evan Hoffman
layout: post
permalink: /2011/12/21/compellent-future-proof-not-so-much/
image:
  - 
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
dsq_thread_id:
  - 2949526751
categories:
  - Uncategorized
tags:
  - compellent
  - dell
  - iscsi
  - money
  - pci
  - pci-e
  - pci-x
  - rant
  - sas
  - stopwhining
---
So, I&#8217;ve written about Compellent a few times from a price perspective, mostly on the disk side. I was recently contacted by our vendor with quotes for two new Compellent controllers. &#8220;What&#8217;s this all about?&#8221; I asked. &#8220;Why don&#8217;t we have a call with Compellent to discuss?&#8221; he replied. I rolled my eyes a little but figured it was worth hearing them out, since our Compellent SAN is at the heart of our infrastructure.

We currently have two controllers setup in failover mode. The first was bought in 2008 and the other in 2010 to add redundancy. Earlier this year we upgraded to the latest software version in preparation for moving our production DB onto the SAN, to allow us a nice window before we had to perform another upgrade (which would now risk DB downtime&#8230; I like failover but I don&#8217;t trust it enough to have a DB up during a failover), so I was kind of skeptical about any sort of upgrade to begin with. 

On the call, the Compellent reps explained that they&#8217;ve dropped Fibre Channel connectivity between the controller and the disk enclosure, and the purpose of the upgrade is to give us SAS. In addition, they no longer sell SATA (!). I asked why we couldn&#8217;t simply add SAS cards to our existing controllers and was told that our current controllers are PCI-X, so can only support up to 3Gb/s SAS, while the new controllers have PCI-e and support 6Gb/s. And they want to ensure that we have the best possible performance. Pretty sure someone said the new controllers &#8220;have the future built in&#8221; to them.

One of the features we really liked about Compellent from the beginning was the fact that it was basically a software solution on top of commodity hardware. They stressed this point repeatedly. &#8220;When new technology comes out, we can just add a new card into your existing controller.&#8221; I think the example at the time was 10-gig Ethernet, but it seems like the same logic would apply to SAS. I understand that PCI-X doesn&#8217;t support 6Gb/s SAS, but it&#8217;s a tough pill to swallow that if we want to expand our SAN at all now, on top of whatever the actual expansion costs, we&#8217;re going to need to plunk down some serious money to upgrade the controllers, which really seems like a net-zero for us. We&#8217;re not going to ditch our existing FC enclosures so we&#8217;re going to be limited to 4Gb/s anyway. If they&#8217;re only selling SAS, well, that sucks for us, but ok. But why can&#8217;t we just throw a $500 PCI-X 3Gb/s card in to expand? So we&#8217;re not running at peak performance. I doubt that would be our performance bottleneck anyway. Plus, swapping out controllers is a huge operation for us.

I know at some point we&#8217;re going to have to bite the bullet and do this upgrade, but it just irks me. On the bright side, I guess, we don&#8217;t have to do a &#8220;forklift upgrade,&#8221; and the disks/enclosures will all still work. But we have a long way to grow before we need to expand, so fortunately I can put this off for a while.