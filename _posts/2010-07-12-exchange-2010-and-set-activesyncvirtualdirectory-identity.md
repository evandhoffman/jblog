---
title: Exchange 2010 and Set-ActiveSyncVirtualDirectory Identity
author: Evan Hoffman
layout: post
permalink: /2010/07/12/exchange-2010-and-set-activesyncvirtualdirectory-identity/
dsq_thread_id:
  - 2949524284
categories:
  - Uncategorized
tags:
  - active sync
  - activesync
  - cas
  - client access
  - exchange
  - exchange 2003
  - exchange 2010
  - Get-ActiveSyncVirtualDirectory
  - identity
  - legacy
  - microsoft
  - Set-ActiveSyncVirtualDirectory
  - upgrade
  - work
---
I don&#8217;t really know why I put this blog up, but generally I write stuff here after I muddle through some ridiculous problem that may have ended up being easily resolved, but whose solution was hard to find. That&#8217;s definitely the case with this post. Currently I&#8217;m in the middle of moving my company&#8217;s email from Exchange 2003 to Exchange 2010. Microsoft has provided some pretty good <a href="http://technet.microsoft.com/en-us/library/aa998604.aspx" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://technet.microsoft.com/en-us/library/aa998604.aspx', 'documentation']);" >documentation</a> on how to do this, but they do assume a certain level of familiarity with the product. For example, I probably spent 30 minutes trying to run Exchange cmdlets in Powershell before I realized there&#8217;s a special shell just for Exchange, the Exchange Management Shell.

Anyway, I&#8217;m trying to setup a Client Access Server to replace our Exchange 2003 Outlook Web Access (webmail) system. Again, Microsoft&#8217;s <a href="http://technet.microsoft.com/en-us/library/ee332348.aspx" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://technet.microsoft.com/en-us/library/ee332348.aspx', 'walkthrough']);" >walkthrough</a> is pretty good, and everything seemed to be working until I got to section 4c of their instructions:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="powershell" style="font-family:monospace;"><span style="color: pink;">&gt;</span>Exchange ActiveSync: Set<span style="color: pink;">-</span>ActiveSyncVirtualDirectory <span style="color: pink;">-</span>Identity <span style="color: pink;">&lt;</span>cas2010<span style="color: pink;">&gt;</span>\Microsoft<span style="color: pink;">-</span>Server<span style="color: pink;">-</span>ActiveSync <span style="color: pink;">-</span>ExternalURL https:<span style="color: pink;">//</span>mail.contoso.com</pre>
      </td>
    </tr>
  </table>
</div>

For the other examples they provided, I had been replacing <cas2010> with the internal name of my new CAS, &#8220;EXCH2010FE1,&#8221; so that&#8217;s what I attempted to do here as well, however it threw this error:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="powershell" style="font-family:monospace;"><span style="color: #000000;">&#91;</span><span style="color: #008080; font-weight: bold;">PS</span><span style="color: #000000;">&#93;</span> C:\Windows\system32<span style="color: pink;">&gt;</span>Set<span style="color: pink;">-</span>ActiveSyncVirtualDirectory <span style="color: pink;">-</span>Identity EXCH2010FE1\Microsoft<span style="color: pink;">-</span>Server<span style="color: pink;">-</span>ActiveSync  <span style="color: pink;">-</span>ExternalURL https:<span style="color: pink;">//</span>webmail.example.com
The operation couldn<span style="color: #800000;">'t be performed because object
  '</span>EXCH2010FE1\Microsoft<span style="color: pink;">-</span>Server<span style="color: pink;">-</span>ActiveSync<span style="color: #800000;">' couldn'</span>t be found on <span style="color: #800000;">'activedir.example.com'</span>.
    <span style="color: pink;">+</span> CategoryInfo          : NotSpecified: <span style="color: #000000;">&#40;</span><span style="color: #804000;"></span>:Int32<span style="color: #000000;">&#41;</span> <span style="color: #000000;">&#91;</span>Set<span style="color: pink;">-</span>ActiveSyncVirtualDirectory<span style="color: #000000;">&#93;</span><span style="color: pink;">,</span> ManagementObjectNotFoundException
    <span style="color: pink;">+</span> FullyQualifiedErrorId : B33731BE<span style="color: pink;">,</span>Microsoft.Exchange.Management.SystemConfigurationTasks. SetMobileSyncVirtualDirectory
&nbsp;
<span style="color: #000000;">&#91;</span><span style="color: #008080; font-weight: bold;">PS</span><span style="color: #000000;">&#93;</span> C:\Windows\system32<span style="color: pink;">&gt;</span></pre>
      </td>
    </tr>
  </table>
</div>

I racked my brain on this for a while. I discovered the **<a href="http://technet.microsoft.com/en-us/library/aa996042.aspx" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://technet.microsoft.com/en-us/library/aa996042.aspx', 'Get-ActiveSyncVirtualDirectory']);" >Get-ActiveSyncVirtualDirectory</a>** command, hoping it would magically solve the problem (telling me what the &#8220;Identity&#8221; was), but it didn&#8217;t &#8211; at least not at first:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="powershell" style="font-family:monospace;"><span style="color: #000000;">&#91;</span><span style="color: #008080; font-weight: bold;">PS</span><span style="color: #000000;">&#93;</span> C:\Windows\system32<span style="color: pink;">&gt;</span>Get<span style="color: pink;">-</span>ActiveSyncVirtualDirectory <span style="color: pink;">-</span>server exch2010fe1
&nbsp;
Name                                    Server                                  InternalUrl
<span style="color: pink;">----</span>                                    <span style="color: pink;">------</span>                                  <span style="color: pink;">-----------</span>
Microsoft<span style="color: pink;">-</span>Server<span style="color: pink;">-</span>ActiveSync <span style="color: #000000;">&#40;</span>Default... EXCH2010FE1                             https:<span style="color: pink;">//</span>exch2010fe1.example.com<span style="color: pink;">/</span>Microsoft<span style="color: pink;">-</span>Se...
&nbsp;
<span style="color: #000000;">&#91;</span><span style="color: #008080; font-weight: bold;">PS</span><span style="color: #000000;">&#93;</span> C:\Windows\system32<span style="color: pink;">&gt;</span></pre>
      </td>
    </tr>
  </table>
</div>

It was showing me the server, but not the Identity, which is what I wanted. Having never used Powershell before, I figured there had to be a way to get that property out of the command, but I had no idea what it was. Some more Googling finally helped me resolve it:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="powershell" style="font-family:monospace;"><span style="color: #000000;">&#91;</span><span style="color: #008080; font-weight: bold;">PS</span><span style="color: #000000;">&#93;</span> C:\Windows\system32<span style="color: pink;">&gt;</span>Get<span style="color: pink;">-</span>ActiveSyncVirtualDirectory <span style="color: pink;">-</span>server exch2010fe1 <span style="color: pink;">|</span> <span style="color: #008080; font-weight: bold;">Select-Object</span> Identity
&nbsp;
Identity
<span style="color: pink;">--------</span>
EXCH2010FE1\Microsoft<span style="color: pink;">-</span>Server<span style="color: pink;">-</span>ActiveSync <span style="color: #000000;">&#40;</span>Default Web Site<span style="color: #000000;">&#41;</span>
&nbsp;
<span style="color: #000000;">&#91;</span><span style="color: #008080; font-weight: bold;">PS</span><span style="color: #000000;">&#93;</span> C:\Windows\system32<span style="color: pink;">&gt;</span></pre>
      </td>
    </tr>
  </table>
</div>

Once I supplied &#8220;EXCH2010FE1\Microsoft-Server-ActiveSync (Default Web Site)&#8221; for the Identity parameter the command completed correctly. I also tried piping the Get-ActiveSyncVirtualDirectory command directly to Set-ActiveSyncVirtualDirectory, like this:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="powershell" style="font-family:monospace;"><span style="color: #000000;">&#91;</span><span style="color: #008080; font-weight: bold;">PS</span><span style="color: #000000;">&#93;</span> C:\Windows\system32<span style="color: pink;">&gt;</span>Get<span style="color: pink;">-</span>ActiveSyncVirtualDirectory <span style="color: pink;">-</span>Server exch2010fe1 <span style="color: pink;">|</span> Set<span style="color: pink;">-</span>ActiveSyncVirtualDirectory <span style="color: pink;">-</span>ExternalURL https:<span style="color: pink;">//</span>webmail.example.com</pre>
      </td>
    </tr>
  </table>
</div>

This appeared to execute successfully, but I don&#8217;t know if it actually did what I intended, so I stuck with specifying the identity manually.

The Exchange 2010 CAS is properly redirecting users to legacy.example.com, but ActiveSync isn&#8217;t working (I&#8217;m testing with my iPhone), so I guess the problem I was having above wasn&#8217;t the source of all my ills, sadly. The battle continues&#8230;