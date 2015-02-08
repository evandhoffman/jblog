---
title: Integrating Amazon Simple Email Service with postfix for SMTP smarthost relaying.
author: Evan Hoffman
excerpt: "How I configured an EC2 instance to relay mail through Amazon's SES, and some of the bumps I encountered along the way."
layout: post
permalink: /2011/04/28/integrating-amazon-simple-email-service-with-postfix-for-smarthost-relaying/
dsq_thread_id:
  - 2949520810
categories:
  - Uncategorized
tags:
  - amazon
  - aws
  - cloud
  - code
  - credentials
  - domain
  - ec2
  - email
  - error
  - linux
  - "Missing final '@domain'"
  - perl
  - postfix
  - relay
  - sendmail
  - ses
  - ses-get-stats.pl
  - ses-send-email.pl
  - smtp
---
So, we&#8217;ve outgrown the 500 outbound messages/day limit imposed by Google Apps&#8217;s Standard tier. A wise friend suggested <a href="http://sendgrid.com/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://sendgrid.com/', 'SendGrid']);" >SendGrid</a>, but I figured it was worth looking into what options Amazon provides. I found <a href="http://aws.amazon.com/ses/faqs/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://aws.amazon.com/ses/faqs/', 'SES']);" >SES</a> and am in the process of setting it up. Hopefully I can set it up as a drop-in replacement, obviating the need for code changes to use it. SES is attractive for us because:

> Free Tier  
> If you are an Amazon EC2 user, you can get started with Amazon SES for free. You can send 2,000 messages for free each day when you call Amazon SES from an Amazon EC2 instance directly or through AWS Elastic Beanstalk. Many applications are able to operate entirely within this free tier limit.
> 
> Note: Data transfer fees still apply. For new AWS customers eligible for the AWS free usage tier, you receive 15 GB of data transfer in and 15 GB of data transfer out aggregated across all AWS services, which should cover your Amazon SES data transfer costs. In addition, all AWS customers receive 1GB of free data transfer per month. 

Free to try? Sounds good.

After signing up, the first thing I did was <a href="https://aws.amazon.com/code/Amazon-SES/8945574369528337" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://aws.amazon.com/code/Amazon-SES/8945574369528337', 'download the Perl scripts']);" >download the Perl scripts</a>. Create a credentials file with your AWS access key ID and Secret Key (credentials can be found <a href="https://aws-portal.amazon.com/gp/aws/developer/account/index.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://aws-portal.amazon.com/gp/aws/developer/account/index.html', 'here']);" >here</a> when logged in). The credentials file (aws-credentials) should look like this:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #007800;">AWSAccessKeyId</span>=022QF06E7MXBSH9DHM02
<span style="color: #007800;">AWSSecretKey</span>=kWcrlUX5JEDGM<span style="color: #000000; font-weight: bold;">/</span>LtmEENI<span style="color: #000000; font-weight: bold;">/</span>aVmYvHNif5zB+d9+ct</pre>
      </td>
    </tr>
  </table>
</div>

Make sure to <tt>chmod 0600 aws-credentials</tt>. To ensure it&#8217;s working, run:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #666666;">$ </span>.<span style="color: #000000; font-weight: bold;">/</span>ses-get-stats.pl <span style="color: #660033;">-k</span> aws-credentials <span style="color: #660033;">-s</span></pre>
      </td>
    </tr>
  </table>
</div>

If it doesn&#8217;t return anything it should be working correctly.

Next, you need to add at least one verified email address:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #666666;">$ </span>.<span style="color: #000000; font-weight: bold;">/</span>ses-verify-email-address.pl <span style="color: #660033;">-k</span> aws-credentials <span style="color: #660033;">--verbose</span> <span style="color: #660033;">-v</span> support<span style="color: #000000; font-weight: bold;">@</span>example.com</pre>
      </td>
    </tr>
  </table>
</div>

Amazon will send a verification message to support@example.com with a link you need to click to verify the address. Once you click, it&#8217;s verified. It&#8217;s important to note that **initially your account will only be able to send email to verified addresses**. According to <a href="https://forums.aws.amazon.com/thread.jspa?messageID=224245" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://forums.aws.amazon.com/thread.jspa?messageID=224245', 'this thread']);" >this thread</a>, you need to <a href="http://docs.amazonwebservices.com/ses/latest/DeveloperGuide/index.html?InitialSetup.Customer.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://docs.amazonwebservices.com/ses/latest/DeveloperGuide/index.html?InitialSetup.Customer.html', 'submit a production access request']);" >submit a production access request</a> to send to unverified To: addresses. I did this and got my &#8220;approval&#8221; email about 30 minutes later.

To send a test email:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;">$ .<span style="color: #000000; font-weight: bold;">/</span>ses-send-email.pl <span style="color: #660033;">--verbose</span> <span style="color: #660033;">-k</span> aws-credentials <span style="color: #660033;">-s</span> <span style="color: #ff0000;">"Test from SES"</span> <span style="color: #660033;">-f</span> support<span style="color: #000000; font-weight: bold;">@</span>example.com evan<span style="color: #000000; font-weight: bold;">@</span>example.com
This is a <span style="color: #7a0874; font-weight: bold;">test</span> message from SES.</pre>
      </td>
    </tr>
  </table>
</div>

(Press ctrl-D to send.)

The next step is integrating the script with sendmail/postfix. The first thing I did was move my scripts to /opt/ (out of /root/) and attempt to run them with absolute pathnames (rather than ./ses-send-email.pl) and I got perl @INC errors:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #7a0874; font-weight: bold;">&#91;</span>root<span style="color: #000000; font-weight: bold;">@</span>web2 ~<span style="color: #7a0874; font-weight: bold;">&#93;</span>$ <span style="color: #c20cb9; font-weight: bold;">mv</span> amazon-email<span style="color: #000000; font-weight: bold;">/</span> <span style="color: #000000; font-weight: bold;">/</span>opt<span style="color: #000000; font-weight: bold;">/</span>
<span style="color: #7a0874; font-weight: bold;">&#91;</span>root<span style="color: #000000; font-weight: bold;">@</span>web2 ~<span style="color: #7a0874; font-weight: bold;">&#93;</span>$ <span style="color: #000000; font-weight: bold;">/</span>opt<span style="color: #000000; font-weight: bold;">/</span>ses-get-stats.pl <span style="color: #660033;">-k</span> aws-credentials <span style="color: #660033;">-s</span>
-bash: <span style="color: #000000; font-weight: bold;">/</span>opt<span style="color: #000000; font-weight: bold;">/</span>ses-get-stats.pl: No such <span style="color: #c20cb9; font-weight: bold;">file</span> or directory
<span style="color: #7a0874; font-weight: bold;">&#91;</span>root<span style="color: #000000; font-weight: bold;">@</span>web2 ~<span style="color: #7a0874; font-weight: bold;">&#93;</span>$ <span style="color: #000000; font-weight: bold;">/</span>opt<span style="color: #000000; font-weight: bold;">/</span>amazon-email<span style="color: #000000; font-weight: bold;">/</span>ses-get-stats.pl <span style="color: #660033;">-k</span> aws-credentials <span style="color: #660033;">-s</span>
Can<span style="color: #ff0000;">'t locate SES.pm in @INC (@INC contains: /usr/lib64/perl5/site_perl/5.8.8/x86_64-linux-thread-multi /usr/lib64/perl5/site_perl/5.8.7/x86_64-linux-thread-multi /usr/lib64/perl5/site_perl/5.8.6/x86_64-linux-thread-multi /usr/lib64/perl5/site_perl/5.8.5/x86_64-linux-thread-multi /usr/lib/perl5/site_perl/5.8.8 /usr/lib/perl5/site_perl/5.8.7 /usr/lib/perl5/site_perl/5.8.6 /usr/lib/perl5/site_perl/5.8.5 /usr/lib/perl5/site_perl /usr/lib64/perl5/vendor_perl/5.8.8/x86_64-linux-thread-multi /usr/lib64/perl5/vendor_perl/5.8.7/x86_64-linux-thread-multi /usr/lib64/perl5/vendor_perl/5.8.6/x86_64-linux-thread-multi /usr/lib64/perl5/vendor_perl/5.8.5/x86_64-linux-thread-multi /usr/lib/perl5/vendor_perl/5.8.8 /usr/lib/perl5/vendor_perl/5.8.7 /usr/lib/perl5/vendor_perl/5.8.6 /usr/lib/perl5/vendor_perl/5.8.5 /usr/lib/perl5/vendor_perl /usr/lib64/perl5/5.8.8/x86_64-linux-thread-multi /usr/lib/perl5/5.8.8 .) at /opt/amazon-email/ses-get-stats.pl line 23.
BEGIN failed--compilation aborted at /opt/amazon-email/ses-get-stats.pl line 23.</span></pre>
      </td>
    </tr>
  </table>
</div>

The problem is that SES.pm isn&#8217;t in perl&#8217;s include path. To solve this, I tried adding the directory to the PERL5LIB environment var:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #7a0874; font-weight: bold;">&#91;</span>root<span style="color: #000000; font-weight: bold;">@</span>web2 amazon-email<span style="color: #7a0874; font-weight: bold;">&#93;</span>$ <span style="color: #007800;">PERL5LIB</span>=<span style="color: #000000; font-weight: bold;">/</span>opt<span style="color: #000000; font-weight: bold;">/</span>amazon-email<span style="color: #000000; font-weight: bold;">/</span>
<span style="color: #7a0874; font-weight: bold;">&#91;</span>root<span style="color: #000000; font-weight: bold;">@</span>web2 amazon-email<span style="color: #7a0874; font-weight: bold;">&#93;</span>$ <span style="color: #7a0874; font-weight: bold;">echo</span> <span style="color: #007800;">$PERL5LIB</span>
<span style="color: #000000; font-weight: bold;">/</span>opt<span style="color: #000000; font-weight: bold;">/</span>amazon-email<span style="color: #000000; font-weight: bold;">/</span>
<span style="color: #7a0874; font-weight: bold;">&#91;</span>root<span style="color: #000000; font-weight: bold;">@</span>web2 amazon-email<span style="color: #7a0874; font-weight: bold;">&#93;</span>$ <span style="color: #7a0874; font-weight: bold;">cd</span>
<span style="color: #7a0874; font-weight: bold;">&#91;</span>root<span style="color: #000000; font-weight: bold;">@</span>web2 ~<span style="color: #7a0874; font-weight: bold;">&#93;</span>$ <span style="color: #7a0874; font-weight: bold;">export</span> PERL5LIB
<span style="color: #7a0874; font-weight: bold;">&#91;</span>root<span style="color: #000000; font-weight: bold;">@</span>web2 ~<span style="color: #7a0874; font-weight: bold;">&#93;</span>$ <span style="color: #000000; font-weight: bold;">/</span>opt<span style="color: #000000; font-weight: bold;">/</span>amazon-email<span style="color: #000000; font-weight: bold;">/</span>ses-get-stats.pl <span style="color: #660033;">-k</span> aws-credentials <span style="color: #660033;">-s</span>
Cannot open credentials <span style="color: #c20cb9; font-weight: bold;">file</span> <span style="color: #000000; font-weight: bold;">&lt;</span>aws-credentials<span style="color: #000000; font-weight: bold;">&gt;</span>. at <span style="color: #000000; font-weight: bold;">/</span>opt<span style="color: #000000; font-weight: bold;">/</span>amazon-email<span style="color: #000000; font-weight: bold;">//</span>SES.pm line <span style="color: #000000;">54</span>.
<span style="color: #7a0874; font-weight: bold;">&#91;</span>root<span style="color: #000000; font-weight: bold;">@</span>web2 ~<span style="color: #7a0874; font-weight: bold;">&#93;</span>$ <span style="color: #000000; font-weight: bold;">/</span>opt<span style="color: #000000; font-weight: bold;">/</span>amazon-email<span style="color: #000000; font-weight: bold;">/</span>ses-get-stats.pl <span style="color: #660033;">-k</span> <span style="color: #000000; font-weight: bold;">/</span>opt<span style="color: #000000; font-weight: bold;">/</span>amazon-email<span style="color: #000000; font-weight: bold;">/</span>aws-credentials <span style="color: #660033;">-s</span>
Timestamp               DeliveryAttempts        Rejects Bounces Complaints      
<span style="color: #000000;">2011</span>-04-27T20:<span style="color: #000000;">27</span>:00Z    <span style="color: #000000;">1</span>                       <span style="color: #000000;"></span>       <span style="color: #000000;"></span>       <span style="color: #000000;"></span>               
<span style="color: #7a0874; font-weight: bold;">&#91;</span>root<span style="color: #000000; font-weight: bold;">@</span>web2 ~<span style="color: #7a0874; font-weight: bold;">&#93;</span>$</pre>
      </td>
    </tr>
  </table>
</div>

This worked for setting all users&#8217; PERL5LIB &#8230; but didn&#8217;t allow postfix to send the message. After a couple more attempts at doing this &#8220;the right way,&#8221; I just ended up dropping a symlink to SES.pm in <tt>/usr/lib/perl5/site_perl</tt> and the @INC error went away.

After following Amazon&#8217;s instructions for editing main.cf and master.cf, I still was unable to send mail through Postfix, even though I could send directly through the perl scripts. I kept getting this error:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;">Apr <span style="color: #000000;">28</span> <span style="color: #000000;">11</span>:<span style="color: #000000;">26</span>:<span style="color: #000000;">32</span> web2 postfix<span style="color: #000000; font-weight: bold;">/</span>pipe<span style="color: #7a0874; font-weight: bold;">&#91;</span><span style="color: #000000;">27226</span><span style="color: #7a0874; font-weight: bold;">&#93;</span>: A2AD33C9A6: <span style="color: #007800;">to</span>=<span style="color: #000000; font-weight: bold;">&lt;</span>user<span style="color: #000000; font-weight: bold;">@</span>domain.com<span style="color: #000000; font-weight: bold;">&gt;</span>, <span style="color: #007800;">relay</span>=aws-email, <span style="color: #007800;">delay</span>=<span style="color: #000000;">0.35</span>, <span style="color: #007800;">delays</span>=<span style="color: #000000;">0.01</span><span style="color: #000000; font-weight: bold;">/</span><span style="color: #000000;"></span><span style="color: #000000; font-weight: bold;">/</span><span style="color: #000000;"></span><span style="color: #000000; font-weight: bold;">/</span><span style="color: #000000;">0.34</span>, <span style="color: #007800;">dsn</span>=5.3.0, <span style="color: #007800;">status</span>=bounced <span style="color: #7a0874; font-weight: bold;">&#40;</span>Command died with status <span style="color: #000000;">1</span>: <span style="color: #ff0000;">"/opt/amazon-email/ses-send-email.pl"</span>. Command output: Missing final <span style="color: #ff0000;">'@domain'</span> <span style="color: #7a0874; font-weight: bold;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>

Google led me to <a href="http://www.bashbang.com/geek/aws-ses-mta/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.bashbang.com/geek/aws-ses-mta/', 'this blog post']);" >this blog post</a> which led me to <a href="http://benjisimon.blogspot.com/2011/02/gotcha-of-day-ec2-postfix-amazon-simple.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://benjisimon.blogspot.com/2011/02/gotcha-of-day-ec2-postfix-amazon-simple.html', 'this other blog post']);" >this other blog post</a> which illuminated the problem: apparently the Postfix pipe macro ${sender} uses the user@hostname of the mail sender. Since the hostname of an EC2 machine is usually something crazy like dom11-22-33-44.internal, this is not likely a validated sending email address. So the solution proposed by Ben Simon was to create a regex to map user@internal to user@realdomain.com and have postfix map everything. This didn&#8217;t work for me or the bashbang.com guys, who changed it to map from user@internal to validuser@realdomain.com. I found that you can eliminate the need for the mapping entirely by changing the master.cf entry to this:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;">  <span style="color: #007800;">flags</span>=R <span style="color: #007800;">user</span>=mailuser <span style="color: #007800;">argv</span>=<span style="color: #000000; font-weight: bold;">/</span>opt<span style="color: #000000; font-weight: bold;">/</span>amazon-email<span style="color: #000000; font-weight: bold;">/</span>ses-send-email.pl <span style="color: #660033;">-r</span> <span style="color: #660033;">-k</span> <span style="color: #000000; font-weight: bold;">/</span>opt<span style="color: #000000; font-weight: bold;">/</span>amazon-email<span style="color: #000000; font-weight: bold;">/</span>aws-credentials <span style="color: #660033;">-e</span> https:<span style="color: #000000; font-weight: bold;">//</span>email.us-east-<span style="color: #000000;">1</span>.amazonaws.com <span style="color: #660033;">-f</span> support<span style="color: #000000; font-weight: bold;">@</span>example.com <span style="color: #800000;">${recipient}</span></pre>
      </td>
    </tr>
  </table>
</div>

The only difference between the above line and Amazon&#8217;s suggestion is that this replaces &#8220;-f ${sender}&#8221; with &#8220;support@example.com&#8221; which is a validated email address.

After this I was able to relay email successfully through SES. Whew!

<ins datetime="2011-05-26T16:32:24+00:00">Update 5/26/2011</ins>: We&#8217;ve been relaying through SES without issues for a few weeks now. I recently ran ses-get-stats.pl to see how many messages we&#8217;re actually sending and it&#8217;s a lot lower than expected. I&#8217;m still glad we moved to SES though, since it has no hard cap like Google Apps does:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;">$ <span style="color: #000000; font-weight: bold;">/</span>opt<span style="color: #000000; font-weight: bold;">/</span>amazon-email<span style="color: #000000; font-weight: bold;">/</span>ses-get-stats.pl <span style="color: #660033;">-k</span> <span style="color: #000000; font-weight: bold;">/</span>opt<span style="color: #000000; font-weight: bold;">/</span>amazon-email<span style="color: #000000; font-weight: bold;">/</span>aws-credentials <span style="color: #660033;">-q</span>
SentLast24Hours Max24HourSend   MaxSendRate     
<span style="color: #000000;">317</span>             <span style="color: #000000;">10000</span>           <span style="color: #000000;">5</span></pre>
      </td>
    </tr>
  </table>
</div>