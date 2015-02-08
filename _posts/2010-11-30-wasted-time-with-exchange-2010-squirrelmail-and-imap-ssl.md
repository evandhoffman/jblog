---
title: Wasted time with Exchange 2010, SquirrelMail, and IMAP-SSL
author: Evan Hoffman
layout: post
permalink: /2010/11/30/wasted-time-with-exchange-2010-squirrelmail-and-imap-ssl/
dsq_thread_id:
  - 2949528150
categories:
  - Uncategorized
tags:
  - 140790E5
  - 143
  - 2010
  - 993
  - 995
  - certificate
  - configure
  - connect
  - email
  - error
  - exchange
  - imap
  - imap.rb
  - imaps
  - initialize
  - linux
  - mail
  - openssl
  - ruby
  - squirrelmail
  - ssl
  - SSL23_WRITE
  - sslerror
  - tls
  - windows
  - work
---
I&#8217;m setting up SquirrelMail to point to my Exchange 2010 server via IMAP (don&#8217;t ask) and couldn&#8217;t get SM to talk to Exchange on port 993 (imaps). Even though the servers on the same subnet, any time passwords are being sent over the network I like to opt for SSL. I found a couple of sites suggesting that the problem was that there was no SSL certificate installed, but I knew for a fact there was a valid certificate because I could get to https://webmail.example.com/ for OWA.

Some of the errors SquirrelMail was reporting were &#8220;Error connecting to IMAP server xxxx Server error: (0)&#8221; and &#8220;Error connecting to IMAP server: tls://xxxx:993. 0: &#8221;

Nothing would actually work on port 993. Telnet to 993 got this:

<pre>$ telnet 10.0.20.18 993
Trying 10.0.20.18...
Connected to 10.0.20.18.
Escape character is '^]'.
* BYE Connection is closed. 14
Connection closed by foreign host.
</pre>

After too much poking, I decided to go down to a lower level and do a simple openssl certificate retrieval and see what came back:

<pre>$ openssl s_client -connect 10.0.20.18:993
CONNECTED(00000003)
140281653434184:error:140770FC:SSL routines:SSL23_GET_SERVER_HELLO:unknown protocol:s23_clnt.c:699:
---
no peer certificate available
---
No client certificate CA names sent
---
SSL handshake has read 7 bytes and written 113 bytes
---
New, (NONE), Cipher is (NONE)
Secure Renegotiation IS NOT supported
Compression: NONE
Expansion: NONE
---
</pre>

That didn&#8217;t look right, so I ran it against the same server on port 443 and got back a real certificate. Same for port 995 (pop3s):

<pre>$ openssl s_client -connect 10.0.20.18:443
CONNECTED(00000003)
depth=3 L = ValiCert Validation Network, O = "ValiCert, Inc.", OU = ValiCert Class 2 Policy Validation Authority, CN = http://www.valicert.com/, emailAddress = info@valicert.com
verify return:1
depth=2 C = US, O = "The Go Daddy Group, Inc.", OU = Go Daddy Class 2 Certification Authority
verify return:1

(snip)
</pre>

So there&#8217;s just something wrong with SSL on port 993. To make a long story short, I had to use the <a href="http://technet.microsoft.com/en-us/library/aa997231.aspx" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://technet.microsoft.com/en-us/library/aa997231.aspx', 'Enable-ExchangeCertificate']);" >Enable-ExchangeCertificate</a> to apply the SSL certificate to port 993. First, run &#8220;Get-ExchangeCertificate&#8221; to list the available certificates and retrieve the Thumbprint.

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="powershell" style="font-family:monospace;"><span style="color: #000000;">&#91;</span><span style="color: #008080; font-weight: bold;">PS</span><span style="color: #000000;">&#93;</span> C:\Windows\system32<span style="color: pink;">&gt;</span>Get<span style="color: pink;">-</span>ExchangeCertificate
&nbsp;
Thumbprint                                Services   Subject
<span style="color: pink;">----------</span>                                <span style="color: pink;">--------</span>   <span style="color: pink;">-------</span>
yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy  .P....     CN<span style="color: pink;">=</span>exch2010fe1
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx  I..W..     CN<span style="color: pink;">=</span>webmail.example.com<span style="color: pink;">,</span> OU<span style="color: pink;">=</span>Domain Control Validated<span style="color: pink;">,</span> O<span style="color: pink;">=</span>webmail.ex...</pre>
      </td>
    </tr>
  </table>
</div>

Copy &#038; paste the thumbprint for whichever cert you want to use into Enable-ExchangeCertificate:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="powershell" style="font-family:monospace;"><span style="color: #000000;">&#91;</span><span style="color: #008080; font-weight: bold;">PS</span><span style="color: #000000;">&#93;</span> C:\Windows\system32<span style="color: pink;">&gt;</span>Enable<span style="color: pink;">-</span>ExchangeCertificate <span style="color: pink;">-</span>ThumbPrint xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx <span style="color: pink;">-</span>Services IIS<span style="color: pink;">,</span>P
OP<span style="color: pink;">,</span>IMAP <span style="color: pink;">-</span>DoNotRequireSSL
<span style="color: #000000;">&#91;</span><span style="color: #008080; font-weight: bold;">PS</span><span style="color: #000000;">&#93;</span> C:\Windows\system32<span style="color: pink;">&gt;</span>Get<span style="color: pink;">-</span>ExchangeCertificate
&nbsp;
Thumbprint                                Services   Subject
<span style="color: pink;">----------</span>                                <span style="color: pink;">--------</span>   <span style="color: pink;">-------</span>
yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy  ......     CN<span style="color: pink;">=</span>exch2010fe1
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx  IP.W..     CN<span style="color: pink;">=</span>webmail.example.com<span style="color: pink;">,</span> OU<span style="color: pink;">=</span>Domain Control Validated<span style="color: pink;">,</span> O<span style="color: pink;">=</span>webmail.ex...</pre>
      </td>
    </tr>
  </table>
</div>

After running that, imaps on port 993 worked perfectly. I can connect to it with both SquirrelMail and Thunderbird.

The SquirrelMail config looks like this:

<pre>IMAP Settings
--------------
4.  IMAP Server            : webmail.example.com
5.  IMAP Port              : 993
6.  Authentication type    : login
7.  Secure IMAP (TLS)      : true
8.  Server software        : exchange
9.  Delimiter              : detect
</pre>

**Edit Feb 15, 2011**: I just renewed the SSL cert and ran into a problem with a Ruby script that was suddenly unable to check a mailbox over IMAPS. The error received was:

<pre>/usr/lib/ruby/1.8/net/imap.rb:898:in `connect': unknown protocol (OpenSSL::SSL::SSLError)
        from /usr/lib/ruby/1.8/net/imap.rb:898:in `initialize'
</pre>

After a few minutes, I remembered this blog post and ran Enable-ExchangeCertificate and it worked again. Glad I wrote it down.

CONNECTED(00000003) 26831:error:140790E5:SSL routines:SSL23\_WRITE:ssl handshake failure:s23\_lib.c:188: