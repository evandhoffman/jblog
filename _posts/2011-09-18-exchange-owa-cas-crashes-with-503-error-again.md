---
title: 'Exchange (OWA) CAS crashes with 503 error &#8211; again'
author: Evan Hoffman
layout: post
permalink: /2011/09/18/exchange-owa-cas-crashes-with-503-error-again/
dsq_thread_id:
  - 2949517350
categories:
  - Uncategorized
tags:
  - 0xe053534f
  - 503
  - appcrash
  - crash
  - error
  - exchange
  - fun
  - KERNELBASE.dll
  - MSExchangeOWAAppPool
  - owa
  - w3wp.exe
  - windows
  - work
---
This just started happening <a href="http://www.evanhoffman.com/evan/?p=986" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=986', 'again']);" >again</a>, with these errors appearing in the event viewer:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="line_numbers">
        <pre>1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
</pre>
      </td>
      
      <td class="code">
        <pre class="xml" style="font-family:monospace;">Log Name: System
Source: Microsoft-Windows-WAS
Date: 9/18/2011 11:16:33 AM
Event ID: 5011
Task Category: None
Level: Warning
Keywords: Classic
User: N/A
Computer: exch2010fe1
Description:
A process serving application pool 'MSExchangeOWAAppPool' suffered a
fatal communication error with the Windows Process Activation Service.
The process id was '3760'. The data field contains the error number.
&nbsp;
Log Name: System
Source: Microsoft-Windows-WAS
Date: 9/17/2011 6:47:07 AM
Event ID: 5009
Task Category: None
Level: Warning
Keywords: Classic
User: N/A
Computer: exch2010fe1
Description:
A process serving application pool 'MSExchangeOWAAppPool' terminated
unexpectedly. The process id was '3108'. The process exit code was
'0x800703e9'.
&nbsp;
Log Name: Application
Source: Application Error
Date: 9/17/2011 6:46:30 AM
Event ID: 1000
Task Category: (100)
Level: Error
Keywords: Classic
User: N/A
Computer: exch2010fe1
Description:
Faulting application name: w3wp.exe, version: 7.5.7600.16385, time
stamp: 0x4a5bd0eb
Faulting module name: KERNELBASE.dll, version: 6.1.7600.16385, time
stamp: 0x4a5bdfe0
Exception code: 0xe053534f
Fault offset: 0x000000000000aa7d
Faulting process id: 0x%9
Faulting application start time: 0x%10
Faulting application path: %11
Faulting module path: %12
Report Id: %13</pre>
      </td>
    </tr>
  </table>
</div>

After reviewing the IIS logs and the event logs, I think it has to do with the WebReady document viewer &#8211; the thing in OWA that renders and lets you view .doc attachments within the browser rather than forcing you to open Word or Excel. I think users were attempting to open corrupted files and that was causing it to crash. I&#8217;ve disabled Webready in EMC (Server Config -> CAS) and I&#8217;ll see what happens. 

<a href="http://affiliate.godaddy.com/redirect/5F43C3ECBA841ACFC3859F4F4E6CA7DA64C271385B2D61A3AD6F3CCE83EB1DD8235E60DCD7D63BCD92E2429E79A75FAC" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://affiliate.godaddy.com/redirect/5F43C3ECBA841ACFC3859F4F4E6CA7DA64C271385B2D61A3AD6F3CCE83EB1DD8235E60DCD7D63BCD92E2429E79A75FAC', '']);" target="_blank"><img src="http://affiliate.godaddy.com/ads/5F43C3ECBA841ACFC3859F4F4E6CA7DA64C271385B2D61A3AD6F3CCE83EB1DD8235E60DCD7D63BCD92E2429E79A75FAC" border="0" width="468"  height="60" alt="Go Daddy $12.99 SSL Sale!" /></a>