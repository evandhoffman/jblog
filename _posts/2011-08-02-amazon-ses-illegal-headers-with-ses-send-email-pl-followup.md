---
title: 'Amazon SES: &#8220;illegal headers&#8221; with ses-send-email.pl (followup)'
author: Evan Hoffman
layout: post
permalink: /2011/08/02/amazon-ses-illegal-headers-with-ses-send-email-pl-followup/
dsq_thread_id:
  - 2949526627
categories:
  - Uncategorized
tags:
  - amazon
  - auto-submitted
  - bugzilla
  - code
  - errors
  - illegal headers
  - organization
  - perl
  - ses
  - ses-send-email.pl
  - smtp
  - smtp headers
  - work
  - x-header
---
A few people have emailed me asking me to integrate <a href="http://www.evanhoffman.com/evan/?p=1270" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=1270', 'the perl code snippet into I wrote to strip illegal headers']);" >the perl code snippet into I wrote to strip illegal headers</a> when sending email via Amazon SES into something actually usable. I&#8217;ve done so! I haven&#8217;t really tested this beyond sending some test emails, but here it is. Use this at your own risk, I make no warranty, blah blah blah.  
<!--more-->

  
This fix requires editing `ses-send-email.pl`, so I&#8217;d advise making a backup copy, though I imagine you can always get a fresh version from Amazon if necessary.

Open ses-send-email.pl in a text editor and find the `read_message` method. It should look like this:  


Delete that and paste this in its place:  


I tried it on the command line and it gave the output I expected &#8211; I haven&#8217;t tried integrating it with sendmail/postfix since I haven&#8217;t encountered this problem. Here&#8217;s the test message I attempted to send:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;">From: evan<span style="color: #000000; font-weight: bold;">@</span>example.com
To: evan<span style="color: #000000; font-weight: bold;">@</span>example.com
Subject: Email
Chicken: yummy
Cats: yucky
X-Zombies: <span style="color: #c20cb9; font-weight: bold;">kill</span> them<span style="color: #000000; font-weight: bold;">!</span>
&nbsp;
Now we<span style="color: #ff0000;">'re out of the header, into the body.  Grand!</span></pre>
      </td>
    </tr>
  </table>
</div>

`From`, `To`, and `Subject` are required headers. `Chicken` &#038; `Cats` are illegal, `X-Zombies` should be ok since it&#8217;s X-ified. Here&#8217;s what happened when I tried to send with the unmodified ses-send-email.pl:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #7a0874; font-weight: bold;">&#91;</span>Tue Aug 02 <span style="color: #000000;">14</span>:<span style="color: #000000;">14</span>:<span style="color: #000000;">51</span> evan<span style="color: #000000; font-weight: bold;">@</span>EvanMBP <span style="color: #000000;">62</span> amazon-email<span style="color: #7a0874; font-weight: bold;">&#93;</span>$ .<span style="color: #000000; font-weight: bold;">/</span>ses-send-email.pl <span style="color: #660033;">--verbose</span> <span style="color: #660033;">-k</span> aws-credentials <span style="color: #660033;">-r</span>
From: evan<span style="color: #000000; font-weight: bold;">@</span>example.com
To: evan<span style="color: #000000; font-weight: bold;">@</span>example.com
Subject: Email
Chicken: yummy
Cats: yucky
X-Zombies: <span style="color: #c20cb9; font-weight: bold;">kill</span> them<span style="color: #000000; font-weight: bold;">!</span>
&nbsp;
Now we<span style="color: #ff0000;">'re out of the header, into the body.  Grand!
&nbsp;
&nbsp;
&lt;ErrorResponse xmlns="http://ses.amazonaws.com/doc/2010-12-01/"&gt;
  &lt;Error&gt;
    &lt;Type&gt;Sender&lt;/Type&gt;
    &lt;Code&gt;InvalidParameterValue&lt;/Code&gt;
    &lt;Message&gt;Illegal header '</span>Chicken<span style="color: #ff0000;">'.&lt;/Message&gt;
  &lt;/Error&gt;
  &lt;RequestId&gt;5ffad294-bd33-11e0-b6e3-affca9ad1eb5&lt;/RequestId&gt;
&lt;/ErrorResponse&gt;
Illegal header '</span>Chicken<span style="color: #ff0000;">'.</span></pre>
      </td>
    </tr>
  </table>
</div>

Illegal header error. Now with the modified version:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #7a0874; font-weight: bold;">&#91;</span>Tue Aug 02 <span style="color: #000000;">14</span>:<span style="color: #000000;">15</span>:<span style="color: #000000;">17</span> evan<span style="color: #000000; font-weight: bold;">@</span>EvanMBP <span style="color: #000000;">63</span> amazon-email<span style="color: #7a0874; font-weight: bold;">&#93;</span>$ .<span style="color: #000000; font-weight: bold;">/</span>ses-send-email-x-headers.pl <span style="color: #660033;">--verbose</span> <span style="color: #660033;">-k</span> aws-credentials <span style="color: #660033;">-r</span>
From: evan<span style="color: #000000; font-weight: bold;">@</span>example.com
To: evan<span style="color: #000000; font-weight: bold;">@</span>example.com
Subject: Email
Chicken: yummy
Cats: yucky
X-Zombies: <span style="color: #c20cb9; font-weight: bold;">kill</span> them<span style="color: #000000; font-weight: bold;">!</span>
&nbsp;
Now we<span style="color: #ff0000;">'re out of the header, into the body.  Grand!
&nbsp;
&nbsp;
&lt;SendRawEmailResponse xmlns="http://ses.amazonaws.com/doc/2010-12-01/"&gt;
  &lt;SendRawEmailResult&gt;
    &lt;MessageId&gt;000001318bb51442-c2cfb780-0604-4363-925a-54a57015e567-000000&lt;/MessageId&gt;
  &lt;/SendRawEmailResult&gt;
  &lt;ResponseMetadata&gt;
    &lt;RequestId&gt;64e75a47-bd33-11e0-9d09-8f08f31615ad&lt;/RequestId&gt;
  &lt;/ResponseMetadata&gt;
&lt;/SendRawEmailResponse&gt;</span></pre>
      </td>
    </tr>
  </table>
</div>

No errors, and I received the email below (extraneous SMTP headers added by Barracuda/Exchange removed):

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;">From: <span style="color: #000000; font-weight: bold;">&lt;</span>evan<span style="color: #000000; font-weight: bold;">@</span>example.com<span style="color: #000000; font-weight: bold;">&gt;</span>
To: <span style="color: #000000; font-weight: bold;">&lt;</span>evan<span style="color: #000000; font-weight: bold;">@</span>example.com<span style="color: #000000; font-weight: bold;">&gt;</span>
Subject: Email
X-Chicken: yummy
X-ASG-Orig-Subj: Email
X-Cats: yucky
X-Zombies: <span style="color: #c20cb9; font-weight: bold;">kill</span> them<span style="color: #000000; font-weight: bold;">!</span>
Date: Tue, <span style="color: #000000;">2</span> Aug <span style="color: #000000;">2011</span> <span style="color: #000000;">18</span>:<span style="color: #000000;">15</span>:<span style="color: #000000;">25</span> +0000
Message-ID: <span style="color: #000000; font-weight: bold;">&lt;</span>000001318bb51442-c2cfb780-0604-<span style="color: #000000;">4363</span>-925a-54a57015e567-000000<span style="color: #000000; font-weight: bold;">@</span>email.amazonses.com<span style="color: #000000; font-weight: bold;">&gt;</span>
Content-Type: text<span style="color: #000000; font-weight: bold;">/</span>plain
MIME-Version: <span style="color: #000000;">1.0</span>
&nbsp;
Now we<span style="color: #ff0000;">'re out of the header, into the body.  Grand!</span></pre>
      </td>
    </tr>
  </table>
</div>

Illegal headers have been X-ified. Hope this helps someone.