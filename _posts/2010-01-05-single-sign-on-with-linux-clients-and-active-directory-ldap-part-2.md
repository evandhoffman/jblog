---
title: Single sign-on with Linux clients and Active Directory LDAP, Part 2
author: Evan Hoffman
layout: post
permalink: /2010/01/05/single-sign-on-with-linux-clients-and-active-directory-ldap-part-2/
dsq_thread_id:
  - 2963199722
categories:
  - Uncategorized
tags:
  - active directory
  - ldap
  - linux
---
Following up on <a href="http://www.evanhoffman.com/evan/2009/12/25/single-sign-on-with-linux-clients-and-active-directory-ldap-part-1/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/2009/12/25/single-sign-on-with-linux-clients-and-active-directory-ldap-part-1/', 'my previous post']);" >my previous post</a>, it turned out not to be as big of a deal as I&#8217;d originally expected to have Apache authenticate against AD and only allow users whose accounts weren&#8217;t disabled. In a nutshell, here&#8217;s what I did:

<!--more-->

In your .htaccess file:

<pre>AuthBasicProvider ldap
AuthType basic
AuthName "AD LDAP Test"
AuthLDAPURL     "ldap://activedirectory.example.com/OU=Users,DC=example,DC=com?sAMAccountName?sub?(!(userAccountControl:1.2.840.113556.1.4.803:=2))"
AuthzLDAPAuthoritative On
AuthLDAPGroupAttribute member
AuthLDAPBindDN ldapuser@example.com
AuthLDAPBindPassword password
Require ldap-group CN=Sysadmins,OU=Internal Groups,OU=Groups,DC=example,DC=com
</pre>

The key here is this LDAP filter: **(!(userAccountControl:1.2.840.113556.1.4.803:=2))**. This is the bitwise &#8220;AND&#8221; of the userAccountControl field and the decimal number 2, which is Microsoft&#8217;s value for &#8220;account is disabled.&#8221; The codes are listed here: <a href="http://support.microsoft.com/kb/305144" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://support.microsoft.com/kb/305144', 'http://support.microsoft.com/kb/305144']);" >http://support.microsoft.com/kb/305144</a>

In httpd.conf (or some other server config file &#8211; I did it in /etc/httpd/conf.d/mod\_authz\_ldap.conf inside the <ifModule> section), add this directive:

<pre>LDAPOpCacheEntries 0</pre>

This tells Apache not to cache the results of the LDAP op. If you don&#8217;t put this in there, the server will cache the result of the user&#8217;s login for whatever the TTL is, and the user will be able to login even after you disable the account (until the cache expires). There may be other ways around this issue, but this works for me.

This works pretty well so far. Now I can create a &#8220;SVN Users&#8221; group in Active Directory, put the people I want in that group, use the above method for authentication and everyone&#8217;s SVN login will be the same as their domain login. Single sign-on one step closer. Yay!