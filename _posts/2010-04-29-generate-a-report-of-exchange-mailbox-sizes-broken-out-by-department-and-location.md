---
title: Generate a report of Exchange mailbox sizes broken out by department and location
author: Evan Hoffman
layout: post
permalink: /2010/04/29/generate-a-report-of-exchange-mailbox-sizes-broken-out-by-department-and-location/
dsq_thread_id:
  - 2949516871
categories:
  - Uncategorized
tags:
  - active directory
  - computers
  - exchange
  - ldap
  - microsoft
  - vbscript
  - work
---
I found a script a few months ago that generated a CSV report of mailbox size, which included the Mailbox Name (usually the user&#8217;s name), size in Kbytes, number of items, which server it&#8217;s on, etc. This was very helpful, but I wanted to see which department within the company used the most space on the mail server, and the department wasn&#8217;t one of the pieces of data included in the report. It took a while but I figured out how to do LDAP lookups in vbscript and was able to add that info, so the report now has the user&#8217;s department, office location, and quota limit in it as well as the other fields. This makes it very easy to do a PivotChart in Excel to generate a pie chart of the size by department. The script is attached &#8211; change the extension to .vbs to run it. You&#8217;ll need to plug in your Exchange server and domain controller where the placeholders currently are.

<a href="http://evanhoffman.com/evan/wp-content/uploads/2010/04/EmailSizeByDepartment.vbs_.txt" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://evanhoffman.com/evan/wp-content/uploads/2010/04/EmailSizeByDepartment.vbs_.txt', 'EmailSizeByDepartment.vbs']);" >EmailSizeByDepartment.vbs</a>