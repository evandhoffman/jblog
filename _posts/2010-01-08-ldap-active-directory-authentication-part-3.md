---
title: LDAP-Active Directory authentication, Part 3
author: Evan Hoffman
layout: post
permalink: /2010/01/08/ldap-active-directory-authentication-part-3/
dsq_thread_id:
  - 2949517930
categories:
  - Uncategorized
tags:
  - active directory
  - apache
  - DSID-03190F00
  - DSID-031A0F44
  - ldap
  - linux
  - unicodePwd
  - work
---
So I got everything working with .htaccess and AD/LDAP authentication. Just add <tt>LDAPVerifyServerCert Off</tt> to the httpd config to let Apache authenticate against an AD server with a self-signed certificate (without dealing with the annoyance of putting the cert on each Apache server).

With that piece of the puzzle largely solved, I moved on to another: how will users change their passwords (which are all stored in Active Directory)? For users running Windows this is pretty trivial &#8212; they can do it right in Windows when they&#8217;re logged into the domain. But what about Linux users? I figured the easiest thing to do would be to make a web form to do this. The user would login (with the http/LDAP auth I previously setup) and the form would ask for their password (twice) and update it in Active Directory. Sounds pretty simple to me. I think if this were OpenLDAP it probably would be, but being AD, it&#8217;s not.

<!--more-->

I&#8217;d already spent an hour or two writing the script (in PHP) when I was able to test its basic functionality. What I got was this:

<pre>Warning: ldap_mod_replace() [function.ldap-mod-replace]: Modify: Insufficient access in /home/evan/public_html/authtest/index.php</pre>

After some hair pulling I realized that I was binding with my &#8220;dummy&#8221; user when attempting to change the password. I figured the solution would be to re-bind as the user whose password I was attempting to change, which is what I did. I verified that the new bind worked, but I still couldn&#8217;t change the password. I decided to fall back to command line and started issuing some ldapmodify commands to see what I could and couldn&#8217;t do as the user. For whatever reason, it appears that even though the user CAN change his password in AD (the &#8220;user cannot change password&#8221; setting is NOT selected &#8212; I checked), the user cannot change his password through LDAP.

<pre>$ ldapmodify -vv -x -d8 -D "CN=Boba Fett,OU=Utility,OU=Users,DC=example,DC=com" -w secret -H ldaps://activedirectory.example.com -f bfett.ldif
ldap_initialize( ldaps://activedirectory.example.com )
TLS certificate verification: Error, unable to get local issuer certificate
request done: ld 0x9dd86e8 msgid 1
replace unicodePwd:
        "
modifying entry "CN=Boba Fett,OU=Utility,OU=Users,DC=example,DC=com"
modify complete
request done: ld 0x9dd86e8 msgid 2
ldapmodify: Insufficient access (50)
        additional info: 00000005: SecErr: DSID-031A0F44, problem 4003 (INSUFF_ACCESS_RIGHTS), data 0
</pre>

I Googled for the error code &#8211; <a href="http://www.google.com/search?q=DSID-031A0F44" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.google.com/search?q=DSID-031A0F44', 'DSID-031A0F44']);" >DSID-031A0F44</a> &#8211; and found a couple of threads about people with the same problem, but no solutions.

The LDIF file I fed in looks like this:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="ldif" style="font-family:monospace;"><span style="color: #000066; font-weight: bold;">dn</span>:<span style="color: #FF0000;"> CN=Boba Fett,OU=Utility,OU=Users,DC=example,DC=com</span>
<span style="color: #000066; font-weight: bold;">changetype</span>:<span style="color: #FF0000;"> modify</span>
<span style="color: #000066; font-weight: bold;">replace</span>:<span style="color: #FF0000;"> unicodePwd</span>
<span style="color: #000066; font-weight: bold;">unicodePwd</span>:<span style="color: #FF0000;">:IgBhAGIAYwBkAGUAZgBnAGgAIgA=</span>
-</pre>
      </td>
    </tr>
  </table>
</div>

When I run the same command with my own DN/password for binding it works fine (though I did discover that you can assign multiple values to unicodePwd, meaning that, until I fixed it, the test user had **two valid passwords**, which seems like a bug on Microsoft&#8217;s part), I assume because I&#8217;m a domain admin. An &#8220;easy&#8221; out would be simply to have the script bind as a domain admin, but that means I&#8217;d be hardcoding a Domain Admin password in the script, which I&#8217;m against. I&#8217;ll have to put some more thought into this. Maybe Linux users will just have to log in to a Windows workstation to change their passwords. That&#8217;s not ideal, but it does already work.

Grr&#8230;

**Update** &#8211; Click the &#8220;ldap&#8221; tag below to see all the LDAP-related posts, including the solution to changing AD passwords via a browser.