---
title: 'Exchange CAS crashes, users get 503 when accessing OWA&#8230; but ActiveSync works'
author: Evan Hoffman
layout: post
permalink: /2011/01/17/exchange-cas-crashes-users-get-503-when-accessing-owa-but-activesync-works/
dsq_thread_id:
  - 2949504723
categories:
  - Uncategorized
tags:
  - 0x800703e9
  - 5011
  - 503
  - appcrash
  - crash
  - KERNELBASE.dll
  - MSExchangeOWAAppPool
  - outlook
  - owa
---
<ins datetime="2011-09-18T16:30:23+00:00">Updated 9/18/2011</ins>: This happened to me again, see <a href="http://www.evanhoffman.com/evan/?p=1599" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=1599', 'my latest post about this issue']);" >my latest post about this issue</a>.

Since migrating to Exchange 2010, every couple of weeks it looks like IIS crashes on the CAS. Going to the OWA URL in a browser would yield a 503 error. Strangely enough, ActiveSync worked fine (though Outlook Anywhere over HTTPS didn&#8217;t). Rebooting the CAS resolved the issue.

This is what the logs show (<a href="http://www.websitepulse.com/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.websitepulse.com/', 'Website Pulse']);" >Website Pulse</a> reported the outage around 11:45):

<pre>Information	1/16/2011 11:59	EventLog	6013	None	The system uptime is 1726976 seconds.
Error	1/16/2011 11:43	Microsoft-Windows-WAS	5002	None	Application pool 'MSExchangeOWAAppPool' is being automatically disabled due to a series of failures in the process(es) serving that application pool.
Warning	1/16/2011 11:43	Microsoft-Windows-WAS	5011	None	A process serving application pool 'MSExchangeOWAAppPool' suffered a fatal communication error with the Windows Process Activation Service. The process id was '13180'. The data field contains the error number.
Warning	1/16/2011 11:43	Microsoft-Windows-WAS	5011	None	A process serving application pool 'MSExchangeOWAAppPool' suffered a fatal communication error with the Windows Process Activation Service. The process id was '12856'. The data field contains the error number.
Warning	1/16/2011 11:42	Microsoft-Windows-WAS	5011	None	A process serving application pool 'MSExchangeOWAAppPool' suffered a fatal communication error with the Windows Process Activation Service. The process id was '15328'. The data field contains the error number.
Warning	1/16/2011 11:41	Microsoft-Windows-WAS	5011	None	A process serving application pool 'MSExchangeOWAAppPool' suffered a fatal communication error with the Windows Process Activation Service. The process id was '11724'. The data field contains the error number.
Warning	1/16/2011 11:41	Microsoft-Windows-WAS	5009	None	A process serving application pool 'MSExchangeOWAAppPool' terminated unexpectedly. The process id was '15868'. The process exit code was '0x800703e9'.
Information	1/16/2011 11:21	Service Control Manager	7036	None	The WinHTTP Web Proxy Auto-Discovery Service service entered the stopped state.</pre>

This happened before we had any antivirus on the machine (antivirus has been suggested as a culprit). The machine is Win 2008r2 x64 with all updates running Exchange 2010 with all updates.

<a href="http://social.technet.microsoft.com/forums/en-US/exchangesvrclients/thread/dab00947-b77f-45e7-b97d-4ad551010bb2/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://social.technet.microsoft.com/forums/en-US/exchangesvrclients/thread/dab00947-b77f-45e7-b97d-4ad551010bb2/', 'This MS Technet article']);" >This MS Technet article</a> seems to suggest Automatic Updates may be related, but I don&#8217;t have Automatic Updates running.

<a href="http://www.experts-exchange.com/Software/Server_Software/Web_Servers/Microsoft_IIS/Q_26705666.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.experts-exchange.com/Software/Server_Software/Web_Servers/Microsoft_IIS/Q_26705666.html', 'This Experts-Exchange article']);" >This Experts-Exchange article</a> seems to suggest it&#8217;s a COM permissions issue. I actually have no idea what that means:

> This seems to be COM permissions.  
> Command prompt->dcomcnfg->component services->computers->right click on my computer->properties.  
> Go to COM security  
> Under launch and activation permissions  
> Add the SID S-1-5-82-3006700770-424185619-1745488364-794895919-4004696415 and provide local launch permissions.
> 
> then try and check if IISRESET still throws same error in eventlog.

I suppose I can try that next time though. I just have no idea why it&#8217;s crashing out of nowhere randomly.

From the Application log:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="xml" style="font-family:monospace;">Log Name:      Application
Source:        Application Error
Date:          1/16/2011 11:41:17 AM
Event ID:      1000
Task Category: (100)
Level:         Error
Keywords:      Classic
User:          N/A
Computer:      exch2010fe1
Description:
Faulting application name: w3wp.exe, version: 7.5.7600.16385, time stamp: 0x4a5bd0eb
Faulting module name: KERNELBASE.dll, version: 6.1.7600.16385, time stamp: 0x4a5bdfe0
Exception code: 0xe053534f
Fault offset: 0x000000000000aa7d
Faulting process id: 0x%9
Faulting application start time: 0x%10
Faulting application path: %11
Faulting module path: %12
Report Id: %13
Event Xml:
&nbsp;
    1000
    2
    100
    0x80000000000000
&nbsp;
    31050
    Application
    exch2010fe1
&nbsp;
    w3wp.exe
    7.5.7600.16385
    4a5bd0eb
    KERNELBASE.dll
    6.1.7600.16385
    4a5bdfe0
    e053534f
    000000000000aa7d</pre>
      </td>
    </tr>
  </table>
</div>

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="xml" style="font-family:monospace;">Log Name:      Application
Source:        Windows Error Reporting
Date:          1/16/2011 11:41:18 AM
Event ID:      1001
Task Category: None
Level:         Information
Keywords:      Classic
User:          N/A
Computer:      exch2010fe1
Description:
Fault bucket , type 0
Event Name: APPCRASH
Response: Not available
Cab Id: 0
&nbsp;
Problem signature:
P1: w3wp.exe
P2: 7.5.7600.16385
P3: 4a5bd0eb
P4: KERNELBASE.dll
P5: 6.1.7600.16385
P6: 4a5bdfe0
P7: e053534f
P8: 000000000000aa7d
P9:
P10:
&nbsp;
Attached files:
&nbsp;
These files may be available here:
C:\ProgramData\Microsoft\Windows\WER\ReportQueue\AppCrash_w3wp.exe_32ada61695ee2297c903c5b1ee2ccc1df1ba4d8_416794b1
&nbsp;
Analysis symbol:
Rechecking for solution: 0
Report Id: 7031214d-218f-11e0-8593-00505697272d
Report Status: 4
Event Xml:
&nbsp;
    1001
    4
    0
    0x80000000000000
&nbsp;
    31051
    Application
    exch2010fe1
&nbsp;
    0
    APPCRASH
    Not available
    0
    w3wp.exe
    7.5.7600.16385
    4a5bd0eb
    KERNELBASE.dll
    6.1.7600.16385
    4a5bdfe0
    e053534f
    000000000000aa7d
&nbsp;
    C:\ProgramData\Microsoft\Windows\WER\ReportQueue\AppCrash_w3wp.exe_32ada61695ee2297c903c5b1ee2ccc1df1ba4d8_416794b1
&nbsp;
    0
    7031214d-218f-11e0-8593-00505697272d
    4</pre>
      </td>
    </tr>
  </table>
</div>