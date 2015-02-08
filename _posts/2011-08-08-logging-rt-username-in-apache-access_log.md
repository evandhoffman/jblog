---
title: Logging RT username in Apache access_log
author: Evan Hoffman
layout: post
permalink: /2011/08/08/logging-rt-username-in-apache-access_log/
dsq_thread_id:
  - 2997720557
categories:
  - Uncategorized
tags:
  - access_log
  - apache
  - httpd
  - httpd.conf
  - log
  - logging
  - mod_perl
  - perl
  - rt
  - rtuser
  - username
  - work
---
<a href="http://bestpractical.com/rt/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://bestpractical.com/rt/', 'RT']);" >RT</a> has its own internal accounting &#038; tracking system for logging activity, but I was interested in even more granular stuff, like seeing who looked at which tickets. I figured it wouldn&#8217;t be that hard to log this in Apache. Well, I was kind of right, in that it wasn&#8217;t &#8220;hard,&#8221; but it took me a long time to find the right place to do it. I did finally get it though.  
<!--more-->

### httpd.conf

In `httpd.conf` I created a new LogFormat:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="conf" style="font-family:monospace;">LogFormat "%h %l %{RTUSER}e %t \"%r\" %&gt;s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined-rt</pre>
      </td>
    </tr>
  </table>
</div>

So instead of the HTTP-auth user, it puts the RT user in this field. Make sure to update your VirtualHost config to use the combined-rt LogFormat.

### /usr/share/rt3/html/autohandler

In `/usr/share/rt3/html/autohandler`, right under this section:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="perl" style="font-family:monospace;"><span style="color: #666666; font-style: italic;"># If we've got credentials, let's serve the file up.</span>
<span style="color: #b1b100;">if</span> <span style="color: #009900;">&#40;</span>    <span style="color: #009900;">&#40;</span> <span style="color: #000066;">defined</span> <span style="color: #0000ff;">$session</span><span style="color: #009900;">&#123;</span><span style="color: #ff0000;">'CurrentUser'</span><span style="color: #009900;">&#125;</span> <span style="color: #009900;">&#41;</span>
    <span style="color: #b1b100;">and</span> <span style="color: #009900;">&#40;</span> <span style="color: #0000ff;">$session</span><span style="color: #009900;">&#123;</span><span style="color: #ff0000;">'CurrentUser'</span><span style="color: #009900;">&#125;</span><span style="color: #339933;">-&gt;</span><span style="color: #006600;">Id</span> <span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#41;</span>
<span style="color: #009900;">&#123;</span></pre>
      </td>
    </tr>
  </table>
</div>

Add this:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="perl" style="font-family:monospace;">        <span style="color: #0000ff;">$ENV</span><span style="color: #009900;">&#123;</span><span style="color: #ff0000;">'RTUSER'</span><span style="color: #009900;">&#125;</span> <span style="color: #339933;">=</span> <span style="color: #0000ff;">$session</span><span style="color: #009900;">&#123;</span><span style="color: #ff0000;">'CurrentUser'</span><span style="color: #009900;">&#125;</span><span style="color: #339933;">-&gt;</span><span style="color: #006600;">UserObj</span><span style="color: #339933;">-&gt;</span><span style="color: #006600;">EmailAddress</span><span style="color: #339933;">;</span></pre>
      </td>
    </tr>
  </table>
</div>

This populates the RTUSER environment variable with the currently-logged-in user&#8217;s email address.

Restart httpd and the RT user&#8217;s email address should now appear in `access_log`. Note that it will only appear for Perl pages (not gifs/jpgs or other static content, since Perl doesn&#8217;t process those):

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="log" style="font-family:monospace;">10.0.0.10 - evan@example.com [08/Aug/2011:17:30:34 -0400] "GET /rt3/index.html HTTP/1.1" 200 46809 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.6; rv:5.0.1) Gecko/20100101 Firefox/5.0.1"
10.0.0.10 - - [08/Aug/2011:17:30:34 -0400] "GET /rt3/NoAuth/images//css/rolldown-arrow.gif HTTP/1.1" 200 83 "https://help.example.com/rt3/NoAuth/css/3.5-default/main-squished.css" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.6; rv:5.0.1) Gecko/20100101 Firefox/5.0.1"</pre>
      </td>
    </tr>
  </table>
</div>