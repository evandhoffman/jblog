---
title: Changing all Exchange mailboxes to use database defaults for quota limits with PowerShell
author: Evan Hoffman
layout: post
permalink: /2010/10/12/changing-all-exchange-mailboxes-to-use-database-defaults-for-quota-limits-with-powershell/
dsq_thread_id:
  - 2949525562
categories:
  - Uncategorized
tags:
  - exchange
  - exchange 2010
  - get-mailbox
  - limit
  - powershell
  - quota
  - recipienttypedetails
  - reset
  - sbs
  - set-mailbox
  - usedatabasequotadefaults
  - usermailbox
---
Here&#8217;s an example of why I&#8217;ve come to love PowerShell. Under Exchange 2003, we set users&#8217; mailbox quota limits per-user, since we initially set them ridiculously high (around 20 GB). The reason we did that was that prior to this year we actually didn&#8217;t have **any** quotas on user mailboxes and the average mailbox was about 9 GB. In preparation for moving Exchange to the datacenter (over our measly 5Mbps upload line) we introduced quotas and over the course of about 2 months gradually lowered the limits as people whittled away their old email until they were under the quota we had established (about 2.25 GB for the no-send limit &#8212; I forgot the rationale for that number &#8212; and 1.75 GB for the warning limit [the no-receive limit was set to about 40GB since I never wanted users to hit that limit]).

I think the quota limits Exchange 2003 would allow you to set through the UI were capped at around 2.0 GB so to set quota limits above this we used ADModify to update the mDBQuota* settings for each user directly in their AD entries.

However, now all those users have been moved to Exchange 2010 which let me easily set the quota limits on the Exchange DB to 2000 MB (warning) and 3000 MB (no-send). The problem with this was that all the users were set to override the database setting. How could I quickly set them all to use the database default once again? With PowerShell, this was simple:

First, get the list of all users not using the database defaults:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="powershell" style="font-family:monospace;"><span style="color: #000000;">&#91;</span><span style="color: #008080; font-weight: bold;">PS</span><span style="color: #000000;">&#93;</span> C:\Windows\system32<span style="color: pink;">&gt;</span>get<span style="color: pink;">-</span>mailbox <span style="color: pink;">-</span><span style="color: #0000FF;">filter</span> <span style="color: #000000;">&#123;</span> usedatabasequotadefaults <span style="color: #FF0000;">-eq</span> <span style="color: #800080;">$false</span> <span style="color: #FF0000;">-AND</span> recipientTypeDetails <span style="color: #FF0000;">-eq</span> <span style="color: #800000;">'usermailbox'</span>  <span style="color: #000000;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>

When doing any bulk operation, always verify the list of objects you&#8217;re going to be modifying before actually doing the modification. <img src="http://www.evanhoffman.com/evan/wp-includes/images/smilies/icon_smile.gif" alt=":)" class="wp-smiley" /> The first filter I created didn&#8217;t have the RecipientTypeDetails in it and included a DiscoveryMailbox, which I didn&#8217;t want to touch. The above command affects only UserMailboxes. If the list looks ok, set them all back to defaults:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="powershell" style="font-family:monospace;"><span style="color: #000000;">&#91;</span><span style="color: #008080; font-weight: bold;">PS</span><span style="color: #000000;">&#93;</span> C:\Windows\system32<span style="color: pink;">&gt;</span>get<span style="color: pink;">-</span>mailbox <span style="color: pink;">-</span><span style="color: #0000FF;">filter</span> <span style="color: #000000;">&#123;</span> usedatabasequotadefaults <span style="color: #FF0000;">-eq</span> <span style="color: #800080;">$false</span> <span style="color: #FF0000;">-AND</span> recipientTypeDetails <span style="color: #FF0000;">-eq</span> <span style="color: #800000;">'usermailbox'</span>  <span style="color: #000000;">&#125;</span> <span style="color: pink;">|</span> set<span style="color: pink;">-</span>mailbox <span style="color: pink;">-</span>UseDatabaseQuotaDefaults $true</pre>
      </td>
    </tr>
  </table>
</div>

And *voilà*, everyone&#8217;s back to using DB defaults (which I increased to 3000MB to celebrate not having to do any more mailbox moves any time soon).