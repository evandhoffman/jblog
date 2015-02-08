---
title: 'VMWare 5&#8217;s new licensing model.'
author: Evan Hoffman
layout: post
permalink: /2011/07/17/vmware-5s-new-licensing-model/
dsq_thread_id:
  - 3006344507
categories:
  - Uncategorized
tags:
  - 5
  - devops
  - licensing
  - linux
  - money
  - pricing
  - vmware
  - vmware 5
  - vsphere
  - work
---
After reading up on the new VMware licensing&#038;pricing model I understand the uproar. Limiting vRAM is a reasonable constraint, but 32GB per socket for Enterprise? 48 GB for &#8220;Enterprise Plus&#8221;? If you have a dual CPU server with 144 GB (easily configurable last year), with 4.1 you&#8217;d only need 2 enterprise licenses to use all 144 GB, since in 4.1 an &#8220;Enterprise&#8221; license covered 1 CPU (up to 6 cores) and up to 256 GB memory on the host.

But with 5.0 you&#8217;ll either have to buy 5 Enterprise licenses (160 GB) or 3 Enterprise Plus licenses (144 GB) just to use the full 144 GB. I guess VMware has done away with memory overcommit as a selling point? They used to tell us it was recommended to go up to 2:1 so we could safely put ~140 GB of VMs on a 72 GB machine &#8211; with the new model that&#8217;s completely gone.

To put it in monetary terms, on the machine with 144 GB ram from above, the cost for 4.1 would be $2875 \* 2 = $5750. To stick with Enterprise it would be $2875 \* 5 = $14,375, or $3495 * 3 = $10,485. A gigantic price jump. I haven&#8217;t read up on any features of vSphere 5, but I don&#8217;t think any feature can make up for at minimum nearly doubling the cost, with loss of a major selling point (memory overcommit). I mean, you can still overcommit as long as you&#8217;re willing to pay for the overcommitted memory. Also, the above numbers are per-host, so if you&#8217;ve got a 5-host cluster you&#8217;re looking at a $25,000 price hike.

VMWare has long been one of my favorite products, but this is making me consider alternatives. Almost 100% of the feedback I&#8217;ve read about this change has been negative. Seems like a huge mistake on VMware&#8217;s part.

  * <a href="http://www.vmware.com/files/pdf/vsphere_pricing.pdf" onclick="_gaq.push(['_trackEvent','download','http://www.vmware.com/files/pdf/vsphere_pricing.pdf']);" >VMware 5.0 pricing</a> 
      * <a href="http://www.vmware.com/products/vsphere/pricing.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.vmware.com/products/vsphere/pricing.html', 'VMware 4.1 pricing (as of 7/17/2011)']);" >VMware 4.1 pricing (as of 7/17/2011)</a> 
          * <a href="http://thinkcloud.nl/2011/07/13/vmware-licensing-vram-entitlement-explained/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://thinkcloud.nl/2011/07/13/vmware-licensing-vram-entitlement-explained/', 'ThinkCloud: vRAM entitlement Explained']);" >ThinkCloud: vRAM entitlement Explained</a> </ul>