---
title: Converting Exchange 2003 conference rooms to Exchange 2010
author: Evan Hoffman
layout: post
permalink: /2010/09/01/converting-exchange-2003-conference-rooms-to-exchange-2010/
dsq_thread_id:
  - 2949518322
categories:
  - Uncategorized
tags:
  - convert
  - exchange
  - exchange 2003
  - exchange 2010
  - mailbox
  - room
  - windows
  - work
---
I&#8217;m wrapping up moving mailboxes to Exchange 2010. The last ones to be moved (except for BlackBerry users&#8230; thanks BES) are the conference rooms. So the first step was to move them using the Local Move tool, which was pretty simple. But I don&#8217;t want them in 2010 as user mailboxes if they can be designated as &#8220;rooms,&#8221; which they can. So here&#8217;s how I&#8217;m doing it:

**Identify the mailboxes to be moved**

Once you figure out the syntax for the &#8220;-Filter&#8221; flag to get-mailbox, this is easy

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="powershell" style="font-family:monospace;"><span style="color: #000000;">&#91;</span><span style="color: #008080; font-weight: bold;">PS</span><span style="color: #000000;">&#93;</span> C:\Windows\system32<span style="color: pink;">&gt;</span>get<span style="color: pink;">-</span>mailbox <span style="color: pink;">-</span><span style="color: #0000FF;">filter</span> <span style="color: #000000;">&#123;</span> <span style="color: #000000;">&#40;</span>RecipientTypeDetails <span style="color: #FF0000;">-eq</span> <span style="color: #800000;">"UserMailbox"</span><span style="color: #000000;">&#41;</span> <span style="color: #FF0000;">-and</span> <span style="color: #000000;">&#40;</span> DisplayName <span style="color: #FF0000;">-like</span> <span style="color: #800000;">"*conference*"</span><span style="color: #000000;">&#41;</span> <span style="color: #000000;">&#125;</span>
&nbsp;
Name                      Alias                ServerName       ProhibitSendQuota
<span style="color: pink;">----</span>                      <span style="color: pink;">-----</span>                <span style="color: pink;">----------</span>       <span style="color: pink;">-----------------</span>
Conference Room2          ConferenceRoom2      exch2010be1      unlimited
Production Conference ... productionconf       exch2010be1      unlimited
Conference Room <span style="color: #804000;">1</span>         conference1          exch2010be1      unlimited
L<span style="color: pink;">&</span>D Conference Room       ldconference         exch2010be1      unlimited
Tech Conference Room      techconference       exch2010be1      unlimited
Client Services Confer... csconference         exch2010be1      unlimited
Suite <span style="color: #804000;">202</span> Conference Room 202conf              exch2010be1      unlimited</pre>
      </td>
    </tr>
  </table>
</div>

**Convert them to rooms**

As Microsoft says in <a href="http://technet.microsoft.com/en-us/library/bb201749.aspx" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://technet.microsoft.com/en-us/library/bb201749.aspx', 'this story about converting mailboxes to rooms']);" >this story about converting mailboxes to rooms</a>, this can only be done via Exchange Management Shell (not EMC), so just pipe the output from the previous command to `Set-Mailbox -Type Room`:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="powershell" style="font-family:monospace;"><span style="color: #000000;">&#91;</span><span style="color: #008080; font-weight: bold;">PS</span><span style="color: #000000;">&#93;</span> C:\Windows\system32<span style="color: pink;">&gt;</span>get<span style="color: pink;">-</span>mailbox <span style="color: pink;">-</span><span style="color: #0000FF;">filter</span> <span style="color: #000000;">&#123;</span> <span style="color: #000000;">&#40;</span>RecipientTypeDetails <span style="color: #FF0000;">-eq</span> <span style="color: #800000;">"UserMailbox"</span><span style="color: #000000;">&#41;</span> <span style="color: #FF0000;">-and</span> <span style="color: #000000;">&#40;</span> DisplayName <span style="color: #FF0000;">-like</span> <span style="color: #800000;">"*conference*"</span><span style="color: #000000;">&#41;</span> <span style="color: #000000;">&#125;</span> <span style="color: pink;">|</span> set<span style="color: pink;">-</span>mailbox <span style="color: pink;">-</span><span style="color: #008080; font-weight: bold;">type</span> room
<span style="color: #000000;">&#91;</span><span style="color: #008080; font-weight: bold;">PS</span><span style="color: #000000;">&#93;</span> C:\Windows\system32<span style="color: pink;">&gt;</span></pre>
      </td>
    </tr>
  </table>
</div>

Done! Now when you create an appointment in Outlook 2007, in Scheduling Assistant, you can click the &#8220;Add Room&#8221; button to add a room. Hooray.