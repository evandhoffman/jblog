---
title: 'Amazon SES &#8211; &#8220;Illegal Header&#8221; errors'
author: Evan Hoffman
excerpt: 'My thoughts on the "illegal header" error some people are encountering when relaying mail through postfix/SES.  Plus some perl code that should help fix it.'
layout: post
permalink: /2011/05/16/amazon-ses-illegal-header-errors/
dsq_thread_id:
  - 2949520616
categories:
  - Uncategorized
tags:
  - amazon
  - auto-submitted
  - email
  - error
  - illegal header
  - linux
  - organization
  - perl
  - precedence
  - ses
  - ses-send-email.pl
  - smtp
---
<ins datetime="2011-08-02T21:31:58+00:00">Update 8/2/2011:</ins> please see <a href="http://www.evanhoffman.com/evan/?p=1486" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=1486', ' the followup post']);" > the followup post</a> for a possible workaround.

A few people have inquired about the &#8220;Illegal header&#8221; error when attempting to relay email through SES. <a href="http://www.evanhoffman.com/evan/2011/04/28/integrating-amazon-simple-email-service-with-postfix-for-smarthost-relaying/#comment-675" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/2011/04/28/integrating-amazon-simple-email-service-with-postfix-for-smarthost-relaying/#comment-675', '&#8220;Oncle Tom&#8221;']);" >&#8220;Oncle Tom&#8221;</a> pointed to <a href="https://forums.aws.amazon.com/thread.jspa?threadID=59098" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://forums.aws.amazon.com/thread.jspa?threadID=59098', 'a thread']);" >a thread</a> on Amazon&#8217;s forums about a similar problem which led to a list of headers Amazon will accept. The list is below; if you need to add headers outside this list, you can do so using &#8220;X-Headers.&#8221;

Some people have posted sample code in the thread with modifications to ses-send-email.pl to replace &#8220;illegal&#8221; headers read from STDIN with an equivalent X-Header. I wrote something generic that takes the list of **legal** headers and replaces anything not on that list with its X-Header equivalent. **Disclaimer:** I haven&#8217;t tested it, but it seems like this type of solution would be more adaptable since you don&#8217;t have to keep fixing it every time a new application attempts to send email with a new header. I haven&#8217;t worked this into ses-send-email.pl (since I&#8217;m not having any problems, I don&#8217;t want to touch it <img src="http://www.evanhoffman.com/evan/wp-includes/images/smilies/icon_smile.gif" alt=":)" class="wp-smiley" /> ) but I fed in an email header and the output looked correct.



Input email:

<pre>MIME-Version: 1.0
Received: by 10.142.136.15 with HTTP; Mon, 16 May 2011 09:32:44 -0700 (PDT)
Date: Mon, 16 May 2011 12:32:44 -0400
Delivered-To: evandhoffman@gmail.com
Message-ID: &lt;BANLkTimTOHR03Ln8CRmBxXQAhtHR9otzUQ@mail.gmail.com>
Subject: Hi friend.
From: "Evan D. Hoffman" &lt;evandhoffman@gmail.com>
To: "Evan D. Hoffman (Personal)" &lt;evandhoffman@gmail.com>
Content-Type: text/plain; charset=ISO-8859-1

Email Test.

</pre>

Output email:

<pre>[evan@EvanMBP ~]$ perl replace-headers.pl test-email.txt 
MIME-Version: 1.0
Received: by 10.142.136.15 with HTTP; Mon, 16 May 2011 09:32:44 -0700 (PDT)
Date: Mon, 16 May 2011 12:32:44 -0400
X-Delivered-To: evandhoffman@gmail.com
X-Message-ID: &lt;BANLkTimTOHR03Ln8CRmBxXQAhtHR9otzUQ@mail.gmail.com>
Subject: Hi friend.
From: "Evan D. Hoffman" &lt;evandhoffman@gmail.com>
To: "Evan D. Hoffman (Personal)" &lt;evandhoffman@gmail.com>
Content-Type: text/plain; charset=ISO-8859-1

Email Test.
</pre>

Hope this helps someone.

Here&#8217;s the legal header list, from <a href="http://docs.amazonwebservices.com/ses/2010-12-01/DeveloperGuide/index.html?AppendixHeaders.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://docs.amazonwebservices.com/ses/2010-12-01/DeveloperGuide/index.html?AppendixHeaders.html', 'http://docs.amazonwebservices.com/ses/2010-12-01/DeveloperGuide/index.html?AppendixHeaders.html']);" >http://docs.amazonwebservices.com/ses/2010-12-01/DeveloperGuide/index.html?AppendixHeaders.html</a>

  * <p class="simpara">
      Accept-Language
    </p>

  * <p class="simpara">
      Bcc
    </p>

  * <p class="simpara">
      Cc
    </p>

  * <p class="simpara">
      Comments
    </p>

  * <p class="simpara">
      Content-Type
    </p>

  * <p class="simpara">
      Content-Transfer-Encoding
    </p>

  * <p class="simpara">
      Content-ID
    </p>

  * <p class="simpara">
      Content-Description
    </p>

  * <p class="simpara">
      Content-Disposition
    </p>

  * <p class="simpara">
      Content-Language
    </p>

  * <p class="simpara">
      Date
    </p>

  * <p class="simpara">
      DKIM-Signature
    </p>

  * <p class="simpara">
      DomainKey-Signature
    </p>

  * <p class="simpara">
      From
    </p>

  * <p class="simpara">
      In-Reply-To
    </p>

  * <p class="simpara">
      Keywords
    </p>

  * <p class="simpara">
      List-Archive
    </p>

  * <p class="simpara">
      List-Help
    </p>

  * <p class="simpara">
      List-Id
    </p>

  * <p class="simpara">
      List-Owner
    </p>

  * <p class="simpara">
      List-Post
    </p>

  * <p class="simpara">
      List-Subscribe
    </p>

  * <p class="simpara">
      List-Unsubscribe
    </p>

  * <p class="simpara">
      Message-Id
    </p>

  * <p class="simpara">
      MIME-Version
    </p>

  * <p class="simpara">
      Received
    </p>

  * <p class="simpara">
      References
    </p>

  * <p class="simpara">
      Reply-To
    </p>

  * <p class="simpara">
      Return-Path
    </p>

  * <p class="simpara">
      Sender
    </p>

  * <p class="simpara">
      Subject
    </p>

  * <p class="simpara">
      Thread-Index
    </p>

  * <p class="simpara">
      Thread-Topic
    </p>

  * <p class="simpara">
      To
    </p>

  * <p class="simpara">
      User-Agent
    </p></ul>