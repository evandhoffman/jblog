---
title: Making sure SSLv2 is disabled in Apache (and Nginx)
author: Evan Hoffman
layout: post
permalink: /2011/09/20/making-sure-sslv2-is-disabled-in-apache/
image:
  - 
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
dsq_thread_id:
  - 2949517385
categories:
  - Uncategorized
tags:
  - apache
  - beast
  - cipher
  - devops
  - encryption
  - httpd
  - linux
  - mitm
  - nginx
  - openssl
  - security
  - ssl
  - sslciphersuite
  - sslv2
  - tls
  - work
---
<ins datetime="2012-01-24T03:50:52+00:00"><br /> <strong>Edit Jan 24, 2012: </strong> Deleted all the crap from this story and just left the recommended Apache and Nginx SSL cipher suites for maximum security without SSLv2 and without BEAST vulnerability (at least according to Qualys).<br /> </ins>  
**Apache httpd**

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="txt" style="font-family:monospace;">SSLProtocol -ALL +SSLv3 +TLSv1
SSLCipherSuite ECDHE-RSA-AES256-SHA384:AES256-SHA256:RC4:HIGH:!MD5:!aNULL:!EDH:!AESGCM;
SSLHonorCipherOrder on</pre>
      </td>
    </tr>
  </table>
</div>

**nginx**

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="txt" style="font-family:monospace;">        ssl_protocols  SSLv3 TLSv1;
        ssl_ciphers     ECDHE-RSA-AES256-SHA384:AES256-SHA256:RC4:HIGH:!MD5:!aNULL:!EDH:!AESGCM;
        ssl_prefer_server_ciphers   on;</pre>
      </td>
    </tr>
  </table>
</div>

Source: 

  * <a href="https://community.qualys.com/blogs/securitylabs/2011/10/17/mitigating-the-beast-attack-on-tls" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://community.qualys.com/blogs/securitylabs/2011/10/17/mitigating-the-beast-attack-on-tls', 'Qualys']);" >Qualys</a>
  * <a href="https://www.ssllabs.com/ssldb/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://www.ssllabs.com/ssldb/', 'SSL checker']);" >SSL checker</a>

<a href="http://affiliate.godaddy.com/redirect/5F43C3ECBA841ACFC3859F4F4E6CA7DA64C271385B2D61A3AD6F3CCE83EB1DD8235E60DCD7D63BCD92E2429E79A75FAC" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://affiliate.godaddy.com/redirect/5F43C3ECBA841ACFC3859F4F4E6CA7DA64C271385B2D61A3AD6F3CCE83EB1DD8235E60DCD7D63BCD92E2429E79A75FAC', '']);" target="_blank"><img src="http://affiliate.godaddy.com/ads/5F43C3ECBA841ACFC3859F4F4E6CA7DA64C271385B2D61A3AD6F3CCE83EB1DD8235E60DCD7D63BCD92E2429E79A75FAC" border="0" width="468"  height="60" alt="Go Daddy $12.99 SSL Sale!" /></a>