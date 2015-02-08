---
title: Single sign-on with Linux clients and Active Directory LDAP, Part 1
author: Evan Hoffman
layout: post
permalink: /2009/12/25/single-sign-on-with-linux-clients-and-active-directory-ldap-part-1/
amazon-product-excerpt-hook-override:
  - 3
amazon-product-content-hook-override:
  - 2
amazon-product-newwindow:
  - 3
dsq_thread_id:
  - 2949522416
categories:
  - Uncategorized
tags:
  - active directory
  - ad
  - ldap
  - linux
  - work
---
One project we&#8217;ve been working on for a while is single sign-on across all our servers and other services (e.g. SVN repository, a few other things). One thing I wanted to avoid, I guess for mostly religious reasons, was reliance on a Windows instance for any of our production environment. The logical part of my brain knows that people build huge websites with Windows farms and AD, but my gut still doesn&#8217;t trust it. So what I wanted to do was setup OpenLDAP as a &#8220;slave&#8221; to an Active Directory &#8220;master&#8221; and have all the LDAP info propagate over the slave whenever any changes were made in the master. I&#8217;ve done this with DNS &#8211; setup Bind as a slave to an AD server and everything basically works as I expect in a Bind-Bind master/slave scenario. Well, it turns out that it doesn&#8217;t work like that when it comes to LDAP. Apparently AD doesn&#8217;t follow the RFC for LDAP (surprise!) so many things that would be expected to work with OpenLDAP won&#8217;t.

<!--more-->

I thought to myself, well, I can just go ahead and write something that will sync AD -> OpenLDAP every 5 minutes. This would mostly work, except there&#8217;s apparently no way to retrieve the user&#8217;s password via LDAP &#8211; the field is write-only. This makes it so I could essentially clone the whole LDAP tree&#8230; except for the piece of info that would let people login, which is the only thing I want it for. This was frustrating.

I decided to explore a caching proxy. I was thinking if I could setup OpenLDAP as a caching proxy to Active Directory, and set a cache time of ~15 minutes, it would at least let me withstand a Windows reboot (it&#8217;s 2009 and I still feel like any Windows fix begins and ends with a reboot). I figured I&#8217;d start with a regular proxy first. This turned out to be relatively simple: in slapd.conf, at the bottom, add:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;">database ldap
suffix          <span style="color: #ff0000;">"dc=example,dc=com"</span>
uri <span style="color: #ff0000;">"ldap://activedirectory.example.com"</span>
acl-bind <span style="color: #007800;">bindmethod</span>=simple <span style="color: #007800;">binddn</span>=<span style="color: #ff0000;">"some user's DN"</span> <span style="color: #007800;">credentials</span>=password</pre>
      </td>
    </tr>
  </table>
</div>

Restart slapd and do an ldapsearch against the new ldap server and it will relay the request to the AD server and relay the response to you.

Caching is another story. It seemed like it should be straightforward &#8211; <tt>yum install openldap-servers-overlays</tt> &#8211; but on my FC11 box, the package didn&#8217;t exist, and on my CentOS 5.4 box, openldap-servers-overlays didn&#8217;t appear to contain the pcache overlay used for caching. So I gave up on that.

I ended up shelving the whole idea for a while and just created 2 Windows AD VMs that will serve as Production AD auth boxes. I got everything configured &#8211; here&#8217;s a sample .htaccess file that queries our AD server for a user (by sAMAccountName, e.g. the &#8220;user&#8221; part of a username in the user@domain login name) and checks that the user is in the Sysadmins group:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;">AuthBasicProvider ldap
AuthType basic
AuthName <span style="color: #ff0000;">"AD LDAP Test"</span>
AuthLDAPURL     <span style="color: #ff0000;">"ldap://activedirectory.example.com/OU=Users,DC=example,DC=com?sAMAccountName?sub"</span>
AuthzLDAPAuthoritative On
AuthLDAPGroupAttribute member
AuthLDAPBindDN ldapuser<span style="color: #000000; font-weight: bold;">@</span>example.com
AuthLDAPBindPassword password
Require ldap-group <span style="color: #007800;">CN</span>=Sysadmins,<span style="color: #007800;">OU</span>=Internal Groups,<span style="color: #007800;">OU</span>=Groups,<span style="color: #007800;">DC</span>=example,<span style="color: #007800;">DC</span>=com</pre>
      </td>
    </tr>
  </table>
</div>

This works (I&#8217;ve modified it so it doesn&#8217;t include our real info, though). It will let the user in if their credentials are OK and if they are in the required group. What I discovered, however, is that if you go into AD and disable the user&#8217;s account, they are still allowed to log in (assuming the user is still in the account). This seemed stupid. I did some more research and discovered <a href="http://support.microsoft.com/kb/305144" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://support.microsoft.com/kb/305144', 'userAccountControl']);" >userAccountControl</a>. Apparently you need to use bit masking against this attribute to determine if an account is disabled. This is as far as I&#8217;d gotten before vacation, but let me say this:

BRILLIANT, MICROSOFT!