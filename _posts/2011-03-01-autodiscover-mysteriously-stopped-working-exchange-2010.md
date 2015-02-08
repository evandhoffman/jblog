---
title: Autodiscover mysteriously stopped working (Exchange 2010)
author: Evan Hoffman
layout: post
permalink: /2011/03/01/autodiscover-mysteriously-stopped-working-exchange-2010/
categories:
  - Uncategorized
tags:
  - autodiscover
  - Autodiscover.xml
  - email
  - exchange
  - exchange 2010
  - externalurl
  - Get-AutodiscoverVirtualDirectory
  - internalurl
  - mail
  - microsoft
  - POST
  - Set-AutodiscoverVirtualDirectory
  - windows
  - _autodiscover
---
I had <a href="http://technet.microsoft.com/en-us/library/bb124251.aspx" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://technet.microsoft.com/en-us/library/bb124251.aspx', 'Autodiscover']);" >Autodiscover</a> working for months but recently it just stopped. I&#8217;m not sure why, but it may be related to removing the last Exchange 2003 servers from service recently. Maybe some setting got wiped from AD when I uninstalled Exchange 2003 (as per <a href="http://technet.microsoft.com/en-us/library/bb288905%28EXCHG.80%29.aspx" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://technet.microsoft.com/en-us/library/bb288905%28EXCHG.80%29.aspx', 'the procedure']);" >the procedure</a> Microsoft gives). Basically what was happening was that the email address field was being autopopulated by the user&#8217;s UPN rather than their email address. Since we have a <a href="http://support.microsoft.com/kb/300684" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://support.microsoft.com/kb/300684', 'single label domain']);" >single label domain</a>, the UPN isn&#8217;t a valid email address, and autodiscovery fails.

Anyway, I ran Get-AutodiscoverVirtualDirectory and it looks like the autodiscover URL isn&#8217;t set. Pretty sure I set this at some point.

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="powershell" style="font-family:monospace;"><span style="color: #000000;">&#91;</span><span style="color: #008080; font-weight: bold;">PS</span><span style="color: #000000;">&#93;</span> C:\Windows\system32<span style="color: pink;">&gt;</span>Get<span style="color: pink;">-</span>AutodiscoverVirtualDirectory <span style="color: pink;">-</span>server exch2010fe1  <span style="color: pink;">|</span> <span style="color: #008080; font-weight: bold;">fl</span> InternalUrl<span style="color: pink;">,</span>ExternalUrl
&nbsp;
InternalUrl :
ExternalUrl :
&nbsp;
<span style="color: #000000;">&#91;</span><span style="color: #008080; font-weight: bold;">PS</span><span style="color: #000000;">&#93;</span> C:\Windows\system32<span style="color: pink;">&gt;</span></pre>
      </td>
    </tr>
  </table>
</div>

I just piped this to Set-AutodiscoverVirtualDirectory to correct the problem:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="powershell" style="font-family:monospace;"><span style="color: #000000;">&#91;</span><span style="color: #008080; font-weight: bold;">PS</span><span style="color: #000000;">&#93;</span> C:\Windows\system32<span style="color: pink;">&gt;</span>Get<span style="color: pink;">-</span>AutodiscoverVirtualDirectory <span style="color: pink;">-</span>server exch2010fe1  <span style="color: pink;">|</span> Set<span style="color: pink;">-</span>AutodiscoverVirtualDirectory <span style="color: pink;">-</span>ExternalUrl <span style="color: #800000;">'https://webmail.example.com/Autodiscover/Autodiscover.xml'</span> <span style="color: pink;">-</span>InternalUrl <span style="color: #800000;">'https://webmail.example.com/Autodiscover/Autodiscover.xml'</span>
<span style="color: #000000;">&#91;</span><span style="color: #008080; font-weight: bold;">PS</span><span style="color: #000000;">&#93;</span> C:\Windows\system32<span style="color: pink;">&gt;</span>Get<span style="color: pink;">-</span>AutodiscoverVirtualDirectory <span style="color: pink;">-</span>server exch2010fe1  <span style="color: pink;">|</span> <span style="color: #008080; font-weight: bold;">fl</span> InternalUrl<span style="color: pink;">,</span>ExternalUrl
&nbsp;
&nbsp;
InternalUrl : https:<span style="color: pink;">//</span>webmail.example.com<span style="color: pink;">/</span>Autodiscover<span style="color: pink;">/</span>Autodiscover.xml
ExternalUrl : https:<span style="color: pink;">//</span>webmail.example.com<span style="color: pink;">/</span>Autodiscover<span style="color: pink;">/</span>Autodiscover.xml
&nbsp;
&nbsp;
<span style="color: #000000;">&#91;</span><span style="color: #008080; font-weight: bold;">PS</span><span style="color: #000000;">&#93;</span> C:\Windows\system32<span style="color: pink;">&gt;</span></pre>
      </td>
    </tr>
  </table>
</div>

After resetting the InternalURL and ExternalURL, autodiscover works again (we have <a href="http://www.evanhoffman.com/evan/?p=445" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=445', 'SRV records']);" >SRV records</a> that tell Outlook to look at webmail.example.com for the Autodiscover service).

Hooray!

<a href="http://affiliate.godaddy.com/redirect/5F43C3ECBA841ACFC3859F4F4E6CA7DA64C271385B2D61A3AD6F3CCE83EB1DD8235E60DCD7D63BCD92E2429E79A75FAC" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://affiliate.godaddy.com/redirect/5F43C3ECBA841ACFC3859F4F4E6CA7DA64C271385B2D61A3AD6F3CCE83EB1DD8235E60DCD7D63BCD92E2429E79A75FAC', '']);" target="_blank"><img src="http://affiliate.godaddy.com/ads/5F43C3ECBA841ACFC3859F4F4E6CA7DA64C271385B2D61A3AD6F3CCE83EB1DD8235E60DCD7D63BCD92E2429E79A75FAC" border="0" width="468"  height="60" alt="Go Daddy $12.99 SSL Sale!" /></a>