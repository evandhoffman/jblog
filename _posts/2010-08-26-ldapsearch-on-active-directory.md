---
title: ldapsearch example on Active Directory
author: Evan Hoffman
layout: post
permalink: /2010/08/26/ldapsearch-on-active-directory/
dsq_thread_id:
  - 2949527822
categories:
  - Uncategorized
tags:
  - active directory
  - ad
  - code
  - example
  - ldap
  - ldapsearch
  - linux
  - windows
  - work
---
Just putting this here for safekeeping since I couldn&#8217;t remember the exact syntax.

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #7a0874; font-weight: bold;">&#91;</span>evan<span style="color: #000000; font-weight: bold;">@</span>ehoffman <span style="color: #000000;">10</span>:<span style="color: #000000;">35</span>:<span style="color: #000000;">50</span> ~<span style="color: #7a0874; font-weight: bold;">&#93;</span>$ ldapsearch <span style="color: #660033;">-x</span> <span style="color: #660033;">-LLL</span> <span style="color: #660033;">-D</span> <span style="color: #ff0000;">"ldapuser@example.com"</span> <span style="color: #660033;">-w</span> password <span style="color: #660033;">-b</span> <span style="color: #ff0000;">"OU=Users,DC=example,DC=com"</span> <span style="color: #660033;">-s</span> sub <span style="color: #660033;">-H</span> ldaps:<span style="color: #000000; font-weight: bold;">//</span>activedirectory.example.com <span style="color: #ff0000;">"(sn=hoffman)"</span> cn mail displayName samaccountname
dn: <span style="color: #007800;">CN</span>=Evan Hoffman,<span style="color: #007800;">OU</span>=Tech,<span style="color: #007800;">OU</span>=Users,<span style="color: #007800;">DC</span>=example,<span style="color: #007800;">DC</span>=com
cn: Evan Hoffman
displayName: Evan D. Hoffman
sAMAccountName: ehoffman
mail: Evan.Hoffman<span style="color: #000000; font-weight: bold;">@</span>example.com</pre>
      </td>
    </tr>
  </table>
</div>

<ins datetime="2011-05-10T20:24:56+00:00">Explanation:</ins> Connect to **activedirectory.example.com** using ldaps (SSL) with simple authentication, binding as **ldapuser@example.com** with password **password**; search for **(sn=hoffman)** within the **OU=Users,DC=example,DC=com** search base (branch), and search the **sub**tree. Return the **cn**, **displayName**, and **samaccountname** fields.

Refer to the <a href="http://linux.die.net/man/1/ldapsearch" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://linux.die.net/man/1/ldapsearch', 'ldapsearch']);" >ldapsearch</a> man page for more options.