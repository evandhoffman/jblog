---
title: 'vCenter: Error parsing the server &quot;(server IP)&quot; &quot;clients.xml&quot; file'
author: Evan Hoffman
layout: post
permalink: /2010/06/23/vcenter-error-parsing-the-server-clients-xml-file/
dsq_thread_id:
  - 2949525441
categories:
  - Uncategorized
tags:
  - clients.xml
  - error
  - error parsing the server
  - vcenter
  - vmware
  - vsphere
  - windows
  - work
---
I got the above error today after running Windows Update on my XP VM a few days ago. A quick search showed that the error is caused by a Microsoft update to the .NET framework. To resolve it, remove update KB980773 (Add/Remove programs, make sure &#8220;Show Updates&#8221; is checked; KB980773 is under &#8220;Microsoft .NET Framework 2.0 Service Pack 2&#8243;). I removed it and was able to log in without problems.

References: 

  * <a href="http://communities.vmware.com/message/1553296#1553296" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://communities.vmware.com/message/1553296#1553296', 'http://communities.vmware.com/message/1553296#1553296']);" >http://communities.vmware.com/message/1553296#1553296</a></p> 
      * <a href="http://www.experts-exchange.com/Software/VMWare/Q_26251559.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.experts-exchange.com/Software/VMWare/Q_26251559.html', 'http://www.experts-exchange.com/Software/VMWare/Q_26251559.html']);" >http://www.experts-exchange.com/Software/VMWare/Q_26251559.html</a> </ul> 
        **Edit 10/22/2010:** You can also resolve this by upgrading your vCenter client to 4.1, which I recently did. 4.1 is available on vmware.com.