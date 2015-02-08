---
title: Relaying through Google Apps using Sendmail to bypass EC2 spam blockage
author: Evan Hoffman
layout: post
permalink: /2010/12/01/relaying-through-google-apps-using-sendmail-to-bypass-ec2-spam-blockage/
dsq_thread_id:
  - 2949528226
categories:
  - Uncategorized
tags:
  - amazon
  - ec2
  - email
  - gmail
  - google
  - google apps
  - linux
  - mail
  - relay
  - sendmail
  - smart host
  - smart_host
  - smtp
  - spam
  - tls
---
<ins datetime="2011-05-03T14:13:21+00:00">Update 3 May 2011</ins>: I&#8217;ve subsequently modified our EC2 systems to <a href="http://www.evanhoffman.com/evan/?p=1220" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=1220', 'relay SMTP mail through Amazon&#8217;s SES']);" >relay SMTP mail through Amazon&#8217;s SES</a> which doesn&#8217;t have the 500 messages per day limit that Google Apps does.

A few months ago I moved a site into EC2. I didn&#8217;t want to move the existing IMAP server (ugh) so I moved the email to Google Apps. There are only about 10 mailboxes so we went with &#8220;Standard&#8221; edition (free). Once we completed the move to EC2 we discovered that emails from our webserver were bouncing due to our EC2 IP address being listed in a spam RBL. This sucked, so I looked into relaying the mail from the EC2 webserver through our Google Apps account. Fortunately this turned out to be pretty easy.

<a href="http://www.scalix.com/wiki/index.php?title=Configuring_Sendmail_with_smarthost_Ubuntu_Gutsy#Configuring_a_smarthost_for_sendmail" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.scalix.com/wiki/index.php?title=Configuring_Sendmail_with_smarthost_Ubuntu_Gutsy#Configuring_a_smarthost_for_sendmail', 'This wiki page']);" >This wiki page</a> on scalix.com has a procedure for setting up SMTP relaying in Ubuntu with TLS &#038; auth. I&#8217;m not running Ubuntu so the paths were different but it was basically the same procedure:

  * Create the file <tt>/etc/mail/client-info</tt> with these contents: `AuthInfo:smtp.gmail.com "U:bounces@example.com" "I:bounces@example.com" "P:superpassword"`, where &#8220;example.com&#8221; is your Google Apps domain, &#8220;bounces&#8221; is a valid account, and the password is the account&#8217;s password. Mail relayed with these credentials will show &#8220;bounces@example.com&#8221; in the From: field of the message. 
      * In /etc/mail, run <tt>makemap hash client-info < client-info</tt> 
          * Edit /etc/mail/sendmail.mc, adding or uncommenting these lines: 
            <pre>define(`SMART_HOST', `smtp.gmail.com')dnl
define(`confAUTH_MECHANISMS', `EXTERNAL GSSAPI DIGEST-MD5 CRAM-MD5 LOGIN PLAIN')dnl
FEATURE(`authinfo', `hash /etc/mail/client-info')dnl
</pre>
            
              * Recompile sendmail.cf: <tt>m4 sendmail.mc > sendmail.cf</tt> . I got this error: "/etc/mail/sendmail.mc:10: m4: Cannot open /usr/share/sendmail-cf/m4/cf.m4" when running the command, but I resolved it by doing <tt>yum install sendmail-cf</tt> 
                  * Restart sendmail. </ul> 
                    Once this was done I sent myself a test message from the command line and received it; I checked the SMTP headers and sure enough it went through Google's mail server. One nice side effect is that all the mail sent by the webserver appears in the "Sent" folder for the Google Apps username provided in the client-info file. Hopefully this will resolve the spam issues, since the mail is now coming from Google's IP block.