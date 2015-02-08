---
title: Renaming a single-label domain to a FQDN
author: Evan Hoffman
layout: post
permalink: /2011/04/07/renaming-a-single-label-domain-to-a-fqdn/
dsq_thread_id:
  - 2949520016
categories:
  - Uncategorized
tags:
  - 2003
  - 2008
  - active directory
  - ad
  - dfs
  - domain
  - exchange
  - exchange 2010
  - rename
  - rendom
  - single-label
  - windows
  - work
---
Long ago &#8212; eons, perhaps &#8212; before I had anything to do with the Windows environment here, someone created the AD domain in my company as a single-label domain (e.g. instead of &#8220;example.com&#8221; our domain is just &#8220;example&#8221;). Over the years this has led to lots of &#8220;fun&#8221; on the part of Windows admins who&#8217;ve worked here as the implications of this choice became more apparent.

Since I inherited this system about a year ago, I haven&#8217;t really bumped up against any problems stemming from the single-label domain issue&#8230; until now. I recently attempted to add a new Windows 2008r2 file server to our DFS replication group/namespace. This totally failed for some mysterious reason. Well, I shouldn&#8217;t say &#8220;totally&#8221; failed, as I was able to add it to the DFS replication group, but unable to add it to the DFS namespace. In my attempt to debug the namespace issue, I deleted the namespace and attempted to recreate it, but just kept getting this error: **The namespace cannot be queried. The specified domain either does not exist or could not be contacted.**. I couldn&#8217;t do anything with the namespace &#8211; even clicking on it in the DFS Management console brought up an error. After some searching I found that this was likely due to having a single-label domain. I wasn&#8217;t sure why the error was happening even on Windows 2003 machines though, maybe joining a 2008r2 box to the domain made some schema changes? I tried a few suggestions like editing the hosts file but nothing seemed to resolve this.

Fortunately, we didn&#8217;t really need DFS namespaces and were able to just direct everybody to the fileserver via its DNS name, though as you can imagine this was clumsy. However, since this has been a problem since time immemorial, I figured it was time to see if it was fixable. After some quick searching, I found <a href="http://technet.microsoft.com/en-us/library/cc732097%28WS.10%29.aspx" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://technet.microsoft.com/en-us/library/cc732097%28WS.10%29.aspx', 'RENDOM']);" >RENDOM</a>. However, after even more searching I discovered <a href="http://technet.microsoft.com/en-us/library/cc816848%28WS.10%29.aspx" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://technet.microsoft.com/en-us/library/cc816848%28WS.10%29.aspx', 'this TechNet article']);" >this TechNet article</a> which says: 

> The domain rename operation is not supported in Microsoft Exchange Server 2007 or Exchange Server 2010. DNS domain rename is supported in Exchange Server 2003. However, renaming of the NetBIOS domain name is not supported in any version of Exchange Server. Other non-Microsoft applications might also not support domain rename. 

Well. We&#8217;re running Exchange 2010. So now what? I guess we&#8217;re going to have to create a second domain and migrate over to it. We&#8217;d already discussed this as a likely way of implementing the rename anyway, since it didn&#8217;t seem like &#8220;RENDOM&#8221; had any rollback procedure &#8211; it either just works (hahaha) or semi-works and semi-fails, leaving a wake of destruction throughout AD. Building a second domain seems like a lot of work, but at least we can move users over one at a time, and we get the side benefit of starting fresh, outgrowing the 5+ years of crud that&#8217;s accumulated in our AD.

Guess we&#8217;ll see what happens. Neither option seems like much fun. I guess the alternative is do nothing, but Microsoft clearly doesn&#8217;t think very highly of single-label domains, and anyone who asks about them gets looked at funny. At least it gives us something to do!