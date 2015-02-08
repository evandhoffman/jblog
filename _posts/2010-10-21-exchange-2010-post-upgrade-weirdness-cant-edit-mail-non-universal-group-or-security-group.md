---
title: 'Exchange 2010 Post-Upgrade weirdness: can&#039;t edit Mail Non-Universal Group or Security Group'
author: Evan Hoffman
layout: post
permalink: /2010/10/21/exchange-2010-post-upgrade-weirdness-cant-edit-mail-non-universal-group-or-security-group/
dsq_thread_id:
  - 2949525783
categories:
  - Uncategorized
tags:
  - active directory
  - ad
  - exchange
  - exchange 2010
  - migration
  - powershell
  - windows
  - work
---
Now that everyone&#8217;s been moved to Exchange 2010 we&#8217;ve started using the 2010 Exchange Managment Console/Shell exclusively which has revealed some weirdness. First, we created a new group in AD using an old script (which used LDAP) and created a Mail-enabled Global Security group. We put people in the group, and everything seemed to be working fine until it was discovered that users in the group couldn&#8217;t see the group in the Global Address List. Users not in the group had no problem seeing the group. Additionally, users in the group couldn&#8217;t see *users* added directly in 2010. This only appeared to affect the GAL; the users were able to send/receive email fine with the full SMTP addresses.

<!--more-->

  
My first guess was that I was being punished for having forgotten to upgrade the LDAP address lists to OPATH. I don&#8217;t really know what that even means, but when I attempted to edit the address lists in EMC I&#8217;d get an error that they needed to be upgraded. Fortunately, <a href="http://technet.microsoft.com/en-us/library/cc164366%28EXCHG.80%29.aspx" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://technet.microsoft.com/en-us/library/cc164366%28EXCHG.80%29.aspx', 'this Technet article']);" >this Technet article</a> lists the commands needed to upgrade the lists. I did it but this didn&#8217;t appear to resolve all the issues.

At this point, after some Googling, I came across this tidbit:

> If you&#8217;re moving from Exchange 2000/2003 to Exchange 2007 or Exchange 2010, you&#8217;re going to want to convert all your domain local and global distribution and mail-enabled security groups to universal groups so they can be managed using the Exchange management tools.

This explains a few things we&#8217;ve noticed &#8211; inability to add Global (Non-Universal) groups to a newly created (Universal) group, for one. So it appears what we should do is upgrade all the Global groups to Universal. First, how do we get a list of all the Global groups? EMS/PowerShell to the rescue:

<pre>[PS] C:\Windows\system32>Get-Group  | Where {$_.GroupType -Like "Global*"  -AND $_.RecipientType -eq "MailNonUniversalGroup"} |
Export-Csv -encoding "utf8" -Path \\fileserver\Tech\groups1.csv</pre>

You can refine the filter further, and when it looks correct you can just pipe the output to Set-Group:

<pre>Get-Group  | Where {$_.GroupType -Like "Global*"  -AND $_.RecipientType -eq "MailNonUniversalGroup"} | Set-Group -Universal</pre>

But now for the most important question: **will this break anything?** I have no idea. We only have a single domain in our AD forest so we&#8217;ve never had need to use Universal, and I don&#8217;t *think* there should be a problem, but I don&#8217;t really have any idea.

I ran the Get-Group/Set-Group commands and they seemed to work as intended for all but about 60 of the target groups. The groups that didn&#8217;t get converted all had weird issues &#8211; aliases that contained illegal characters (which I fixed), or some of them complained that a particular user (I think the Owner of the group in AD) was not found (even though it was in the exact location it was saying it wasn&#8217;t, though the user was disabled). I &#8220;manually&#8221; converted these groups to Universal via the radio button in the properties dialog in Active Dir Users &#038; Groups. Not the most elegant solution but it worked. So all the groups in question are now Universal Security groups. Will this solve the problem? Well, I&#8217;ll have to wait until tomorrow to find out.

Reference links:

  * <a href="http://blogs.technet.com/b/sfodel/archive/2008/08/04/need-to-convert-global-groups-to-universal-groups.aspx" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://blogs.technet.com/b/sfodel/archive/2008/08/04/need-to-convert-global-groups-to-universal-groups.aspx', 'Need to convert Global groups to Universal groups? Do you have messages to global groups disappearing?']);" >Need to convert Global groups to Universal groups? Do you have messages to global groups disappearing?</a> 
      * <a href="http://social.technet.microsoft.com/Forums/en-US/exchangesvradmin/thread/c7d25d85-b246-45d2-b52b-a6539bc0edb2" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://social.technet.microsoft.com/Forums/en-US/exchangesvradmin/thread/c7d25d85-b246-45d2-b52b-a6539bc0edb2', '  Changing Group Type via PowerShell']);" > Changing Group Type via PowerShell</a> 
          * <a href="http://www.expta.com/2009/10/how-to-convert-local-and-global-groups.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.expta.com/2009/10/how-to-convert-local-and-global-groups.html', 'How to Convert Local and Global Groups to Universal Groups']);" >How to Convert Local and Global Groups to Universal Groups</a> 
              * <a href="http://social.technet.microsoft.com/Forums/en-US/exchange2010/thread/a587a97d-0d22-4540-aff0-5728b06e287c" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://social.technet.microsoft.com/Forums/en-US/exchange2010/thread/a587a97d-0d22-4540-aff0-5728b06e287c', 'Forum post about the &#8220;BypassSecurityGroupManagerCheck&#8221; security error']);" >Forum post about the &#8220;BypassSecurityGroupManagerCheck&#8221; security error</a> 
                  * <a href="http://theessentialexchange.com/blogs/michael/archive/2008/02/28/exchange-server-2007-and-universal-groups.aspx" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://theessentialexchange.com/blogs/michael/archive/2008/02/28/exchange-server-2007-and-universal-groups.aspx', 'Exchange Server 2007 and Universal Groups']);" >Exchange Server 2007 and Universal Groups</a> 
                      * <a href="http://support.microsoft.com/kb/231273" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://support.microsoft.com/kb/231273', 'KB 231273: Group Type and Scope Usage in Windows']);" >KB 231273: Group Type and Scope Usage in Windows</a> 
                          * <a href="http://www.experts-exchange.com/Software/Server_Software/Email_Servers/Exchange/Q_24694362.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.experts-exchange.com/Software/Server_Software/Email_Servers/Exchange/Q_24694362.html', 'Mail Universal Groups vs Mail Non-Universal Groups']);" >Mail Universal Groups vs Mail Non-Universal Groups</a> on Experts Exchange 
                              * <a href="http://blogs.technet.com/b/msukucc/archive/2009/02/23/recipients-list.aspx" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://blogs.technet.com/b/msukucc/archive/2009/02/23/recipients-list.aspx', 'Technet blog: Recipients List']);" >Technet blog: Recipients List</a> 
                                  * <a href="http://social.technet.microsoft.com/forums/en-US/exchangesvrdeploy/thread/a6ed9be8-bae9-4c4d-b540-224e4911be1a" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://social.technet.microsoft.com/forums/en-US/exchangesvrdeploy/thread/a6ed9be8-bae9-4c4d-b540-224e4911be1a', 'Technet forum: Mail enabled distribution groups in AD']);" >Technet forum: Mail enabled distribution groups in AD</a> </ul>