---
title: Setting up InspIRCd as an internal IRC server for companywide chat
author: Evan Hoffman
layout: post
permalink: /2010/10/28/setting-up-inspircd-as-an-internal-irc-server-for-companywide-chat/
dsq_thread_id:
  - 2949528021
categories:
  - Uncategorized
tags:
  - centos
  - chat
  - compile
  - inspircd
  - irc
  - ircd
  - ldap
  - linux
  - ssl
  - work
---
I got this idea (<a href="http://teddziuba.com/2010/01/break-my-concentration-and-i-b.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://teddziuba.com/2010/01/break-my-concentration-and-i-b.html', 'from this blog post']);"  target="_blank">from this blog post</a>) that it would be cool and great if we had an IRC server to facilitate communication within our team.  I started checking out some different IRC servers to see which ones supported the main features I wanted (LDAP auth & SSL).  I started out with ratbox but I didn&#8217;t really like it.  I found InspIRCd and after some config tweaking it&#8217;s working well.  Here&#8217;s the info from modules.conf for authentication against Active Directory (so people login with their AD usernames):

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
</pre>
      </td>
      
      <td class="code">
        <pre class="xml" style="font-family:monospace;"><span style="color: #009900;"><span style="color: #000000; font-weight: bold;">&lt;module</span> <span style="color: #000066;">name</span>=<span style="color: #ff0000;">"m_ldapauth.so"</span><span style="color: #000000; font-weight: bold;">&gt;</span></span>
   <span style="color: #009900;"><span style="color: #000000; font-weight: bold;">&lt;ldapauth</span> <span style="color: #000066;">baserdn</span>=<span style="color: #ff0000;">"OU=Users,DC=example,DC=com"</span></span>
<span style="color: #009900;">   <span style="color: #000066;">attribute</span>=<span style="color: #ff0000;">"sAMAccountName"</span></span>
<span style="color: #009900;">   <span style="color: #000066;">server</span>=<span style="color: #ff0000;">"ldap://activedirectory.example.com"</span></span>
<span style="color: #009900;">   <span style="color: #000066;">killreason</span>=<span style="color: #ff0000;">"LDAP auth failed"</span></span>
<span style="color: #009900;">   <span style="color: #000066;">searchscope</span>=<span style="color: #ff0000;">"subtree"</span></span>
<span style="color: #009900;">   <span style="color: #000066;">binddn</span>=<span style="color: #ff0000;">"CN=binduser,OU=Users,DC=example,DC=com"</span></span>
<span style="color: #009900;">   <span style="color: #000066;">bindauth</span>=<span style="color: #ff0000;">"password"</span></span>
<span style="color: #009900;">   <span style="color: #000066;">verbose</span>=<span style="color: #ff0000;">"yes"</span></span>
<span style="color: #009900;">   <span style="color: #000066;">userfield</span>=<span style="color: #ff0000;">"yes"</span><span style="color: #000000; font-weight: bold;">&gt;</span></span></pre>
      </td>
    </tr>
  </table>
</div>

Here&#8217;s the configure line (since I had to build from source):

<pre>./configure --prefix=/usr/local/inspircd --enable-gnutls --uid 101 --enable-extras=m_ldapauth.cpp --enable-extras=m_ldapoper.cpp</pre>

I still have some more customization to do to make ChanServ work, but since this is internal I don&#8217;t know if that even matters.  Also I can&#8217;t seem to get SSL working &#8211; I tried compiling with &#8211;enable-openssl but that failed, so I went with &#8211;enable-gnutls, which worked, but I can&#8217;t connect via ircs:// in my client.  If it looks like people are interested in this I may fix it up, but I have a feeling most people won&#8217;t be as enthused about it as I am.