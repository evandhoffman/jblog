---
title: 'Victory!  Change Active Directory Password via LDAP through browser'
author: Evan Hoffman
layout: post
permalink: /2010/01/13/victory-change-active-directory-password-via-ldap-through-browser/
dsq_thread_id:
  - 2949518043
categories:
  - Uncategorized
tags:
  - active directory
  - change password
  - howto
  - ldap
  - linux
  - password
  - perl
  - php
  - unicodePwd
  - victory
  - windows
  - work
---
I had to give up on PHP and go to Perl, but it turned out not to be so bad. Users can now change their Active Directory passwords via a self-service web page that doesn&#8217;t require admin credentials. The Perl code is below.  Authentication to the script is done via .htaccess LDAP authentication, so the REMOTE_USER env variable is assumed to contain the user&#8217;s username (sAMAccountName) by the time this script is called.  There is a simple check for $ENV{HTTPS} to ensure the script is called via SSL, and AD requires password changes to be done via ldaps, so the whole thing *should* be encrypted end to end.



(Edited 5/14/2010 to replace the inlined Perl script with a link to the script as a text file.)

(<ins datetime="2011-10-06T19:39:21+00:00">Edited 10/6/2011 to replace link to script with link to gist</ins>)