---
title: 'Exchange 2010 &#8211; Out-of-office response (OOF) won&#039;t turn off?'
author: Evan Hoffman
layout: post
permalink: /2010/08/11/exchange-2010-out-of-office-response-oof-wont-turn-off/
dsq_thread_id:
  - 2949521808
categories:
  - Uncategorized
tags:
  - disable
  - exchange
  - exchange 2010
  - Get-MailboxAutoReplyConfiguration
  - oof
  - out-of-office
  - turn off
  - windows
  - work
---
Two users reported the same problem this week: they turned on their out-of-office reply while they were out, then came back and turned it off. Except even after they turned it off, the autoreply was still being sent out. I had them log in to OWA and make sure it was off (maybe some weird bug with Outlook not registering the change in the server), which it was in both cases. I Googled hard and fast and couldn&#8217;t find anyone with this same problem.

I went in with Powershell and checked their autoreply status via **Get-MailboxAutoReplyConfiguration** and it appears that it is, in fact, disabled:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="powershell" style="font-family:monospace;"><span style="color: #000000;">&#91;</span><span style="color: #008080; font-weight: bold;">PS</span><span style="color: #000000;">&#93;</span> C:\Windows\system32<span style="color: pink;">&gt;</span>Get<span style="color: pink;">-</span>mailbox <span style="color: pink;">-</span>identity username <span style="color: pink;">|</span> Get<span style="color: pink;">-</span>MailboxAutoReplyConfiguration
RunspaceId       : 7ad7e9af<span style="color: pink;">-</span>cd57<span style="color: pink;">-</span><span style="color: #804000;">4572</span><span style="color: pink;">-</span>a4fd<span style="color: pink;">-</span>c1e999e4b9a5
AutoReplyState   : Disabled
EndTime          : <span style="color: #804000;">8</span><span style="color: pink;">/</span><span style="color: #804000;">12</span><span style="color: pink;">/</span><span style="color: #804000;">2010</span> <span style="color: #804000;">12</span>:00:00 PM
ExternalAudience : All
ExternalMessage  :
InternalMessage  :
StartTime        : <span style="color: #804000;">8</span><span style="color: pink;">/</span><span style="color: #804000;">11</span><span style="color: pink;">/</span><span style="color: #804000;">2010</span> <span style="color: #804000;">12</span>:00:00 PM
MailboxOwnerId   :  <span style="color: #000000;">&#91;</span>removed<span style="color: #000000;">&#93;</span>
Identity         :  <span style="color: #000000;">&#91;</span>removed<span style="color: #000000;">&#93;</span>
IsValid          : True</pre>
      </td>
    </tr>
  </table>
</div>

I used **Set-MailboxAutoReplyConfiguration** to set the messages to &#8220;&#8221; (empty string) and it&#8217;s *still* sending the user&#8217;s autoresponse, from *before* I blanked it out. My working theory right now is that the out-of-office message was set on both the Exchange 2010 server *and* the Exchange 2003 server (where the mailboxes were before I migrated them to 2010).

What a fun problem! It&#8217;s hard to test whether I&#8217;ve fixed it, since each sender only receives the message once, so I have to keep creating new test email addresses to send test messages.

Also, as an aside, why is &#8220;out-of-office&#8221; abbreviated &#8220;OOF&#8221; in Microsoft&#8217;s docs?

**Edit 1:** I had one user verify the message was off in OWA and then start Outlook via Start -> Run&#8230; &#8220;outlook /cleanrules&#8221; and this seemed to resolve the issue. Hopefully this isn&#8217;t required every time&#8230;