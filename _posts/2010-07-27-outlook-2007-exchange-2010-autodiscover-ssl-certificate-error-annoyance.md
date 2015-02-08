---
title: 'Outlook 2007 &amp; Exchange 2010 Autodiscover SSL certificate error annoyance'
author: Evan Hoffman
layout: post
permalink: /2010/07/27/outlook-2007-exchange-2010-autodiscover-ssl-certificate-error-annoyance/
dsq_thread_id:
  - 2949522721
categories:
  - Uncategorized
tags:
  - autodiscover
  - certificate
  - dns
  - email
  - error
  - exchange
  - exchange 2010
  - godaddy
  - microsoft
  - mismatch
  - srv
  - ssl
  - windows
  - work
  - _autodiscover
---
One of the more annoying side effects of migrating my mailbox to Exchange 2010 has been the nagging of Outlook 2007&#8217;s Autodiscovery feature. Now, every time I start Outlook I get hit with a certificate error for autodiscover.domain.com. Now, autodiscover.domain.com is a CNAME to mail.domain.com, which is the OWA URL for the CAS. The SSL certificate is valid &#8211; but it&#8217;s valid for mail.domain.com. I could buy <a href="http://www.godaddy.com/ssl/ssl-certificates.aspx?ci=21754&#038;isc=IAPssl5" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.godaddy.com/ssl/ssl-certificates.aspx?ci=21754&isc=IAPssl5', 'a SSL certificate from GoDaddy for $12.99']);"  title="Go Daddy $12.99 SSL Sale!">a SSL certificate from GoDaddy for $12.99</a> (an insanely great price, btw) for &#8220;autodiscover&#8221; but that would also require using another IP address on the CAS (since you can can only bind one SSL certificate to an IP:port pair), and that seems like a waste of an IP address.

I found a possible solution in <a href="http://support.microsoft.com/kb/940726" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://support.microsoft.com/kb/940726', 'KB 940726']);" >KB 940726</a>. Basically you use this cmdlet to change the Autodiscover URI for internal clients:

> Set-ClientAccessServer –AutodiscoverServiceInternalUri https://**mail.contoso.com**/autodiscover/autodiscover.xml 

You&#8217;d replace mail.contoso.com with the external URL of your OWA server (in my case, mail.domain.com). I&#8217;ve made the changes but I think I need to wait for AD propagation. Hopefully this will resolve it, because I don&#8217;t want to move everyone&#8217;s mailboxes over until this thing is &#8220;perfect,&#8221; whatever that means.

**Edit:** I also needed to add a SRV record so Outlook would know what host to check for autodiscovery when outside the domain.

**Edit 2:**: Also need to install a hotfix or be running Outlook 2007 SP1 or later for the SRV functionality.

**Edit 3**: It occurs to me that a simpler fix for this issue may be simply to delete the DNS record for autodiscover entirely. That way, when Outlook attempts to open the SSL connection to autodiscover.domain.com, it gets a NXDOMAIN error (should) silently skip it. Unfortunately we have wildcard DNS active for our domain.

Other useful resources:

  * <a href="http://msexchangeteam.com/archive/2007/09/21/447067.aspx" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://msexchangeteam.com/archive/2007/09/21/447067.aspx', 'MS Exchange Team blog post comparing the various autodiscover schemes.']);" >MS Exchange Team blog post comparing the various autodiscover schemes.</a>
  * <a href="http://technet.microsoft.com/en-us/library/bb125157.aspx" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://technet.microsoft.com/en-us/library/bb125157.aspx', 'Set-ClientAccessServer']);" >Set-ClientAccessServer</a>.
  *Test Exchange Connectivity<a></li> 
    
    <li>
      <a href="http://support.microsoft.com/kb/940881" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://support.microsoft.com/kb/940881', 'Setting Autodiscover URL via DNS SRV record']);" >Setting Autodiscover URL via DNS SRV record</a>
    </li>
    <li>
      <a href="http://technet.microsoft.com/en-us/library/bb332063%28EXCHG.80%29.aspx" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://technet.microsoft.com/en-us/library/bb332063%28EXCHG.80%29.aspx', 'Autodiscover whitepaper']);" >Autodiscover whitepaper</a>. <li>
        <a href="http://www.lengoldenstein.com/2008/09/29/creating-an-outlook-2007sp1-autodiscover-dns-record-with-bind/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.lengoldenstein.com/2008/09/29/creating-an-outlook-2007sp1-autodiscover-dns-record-with-bind/', 'Example Autodiscover BIND record']);" >Example Autodiscover BIND record</a> &#8211; <tt>_autodiscover._tcp.domain.com. SRV 0 0 443 webmail.domain.com.</tt>
      </li>
      <li>
        <a href="http://blog.tiensivu.com/aaron/archives/1398-Little-known-Outlook-feature-Hold-down-Ctrl,-right-click-on-tray-icon-Connection-Status-appears.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://blog.tiensivu.com/aaron/archives/1398-Little-known-Outlook-feature-Hold-down-Ctrl,-right-click-on-tray-icon-Connection-Status-appears.html', 'Debug Autodiscover']);" >Debug Autodiscover</a> by right-clicking the Outlook icon in the system tray while holding down Ctrl
      </li>
      <li>
        <a href="http://support.microsoft.com/kb/816587" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://support.microsoft.com/kb/816587', 'Verifying SRV records exist with nslookup']);" >Verifying SRV records exist with nslookup</a>
      </li>
      <li>
        <a href="http://support.microsoft.com/kb/928116" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://support.microsoft.com/kb/928116', 'What version of Outlook am I running?']);" >What version of Outlook am I running?</a> You need SP1 or later for the SRV hack.
      </li>
      <li>
        <a href="http://support.microsoft.com/kb/939184/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://support.microsoft.com/kb/939184/', 'Hotfix for Outlook 2007 (pre-SP1) to use SRV records for autodiscovery']);" >Hotfix for Outlook 2007 (pre-SP1) to use SRV records for autodiscovery</a>
      </li></ul> 
      <p>
        <a href="http://affiliate.godaddy.com/redirect/5F43C3ECBA841ACFC3859F4F4E6CA7DA64C271385B2D61A3AD6F3CCE83EB1DD8235E60DCD7D63BCD92E2429E79A75FAC" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://affiliate.godaddy.com/redirect/5F43C3ECBA841ACFC3859F4F4E6CA7DA64C271385B2D61A3AD6F3CCE83EB1DD8235E60DCD7D63BCD92E2429E79A75FAC', '']);" target="_blank"><img src="http://affiliate.godaddy.com/ads/5F43C3ECBA841ACFC3859F4F4E6CA7DA64C271385B2D61A3AD6F3CCE83EB1DD8235E60DCD7D63BCD92E2429E79A75FAC" border="0" width="468"  height="60" alt="Go Daddy $12.99 SSL Sale!" /></a>
      </p>