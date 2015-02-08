---
title: Forcing WordPress administration over SSL
author: Evan Hoffman
layout: post
permalink: /2010/09/20/forcing-wordpress-administration-over-ssl/
dsq_thread_id:
  - 2949524653
categories:
  - Uncategorized
tags:
  - admin
  - apache
  - howto
  - linux
  - mod_rewrite
  - ssl
  - wordpress
  - work
---
I never like typing a password into a non-SSL site, no matter how trivial it is. In order to give my own site this ability I simply used mod_rewrite to force requests to WordPress&#8217;s admin pages to go over SSL.

The .htaccess file for the site looks like this:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="apache" style="font-family:monospace;"><span style="color: #adadad; font-style: italic;"># BEGIN WordPress</span>
&lt;<span style="color: #000000; font-weight:bold;">ifModule</span> mod_rewrite.c&gt;
<span style="color: #00007f;">RewriteEngine</span> <span style="color: #0000ff;">On</span>
<span style="color: #00007f;">RewriteBase</span> /evan/
<span style="color: #00007f;">RewriteRule</span> ^index\.php$ - [L]
<span style="color: #00007f;">RewriteCond</span> %{REQUEST_FILENAME} !-f
<span style="color: #00007f;">RewriteCond</span> %{REQUEST_FILENAME} !-d
<span style="color: #00007f;">RewriteRule</span> . /evan/index.php [L]
&lt;/<span style="color: #000000; font-weight:bold;">ifModule</span>&gt;
&nbsp;
<span style="color: #adadad; font-style: italic;"># END WordPress</span></pre>
      </td>
    </tr>
  </table>
</div>

To force the admin pages to SSL, just add these lines under **RewriteEngine On**:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="apache" style="font-family:monospace;"><span style="color: #00007f;">RewriteCond</span> %{HTTPS} !=<span style="color: #0000ff;">on</span>
<span style="color: #00007f;">RewriteRule</span> ^wp-(.*)$ https://%{HTTP_HOST}%{REQUEST_URI}      [R,L]</pre>
      </td>
    </tr>
  </table>
</div>

**Edit** &#8211; The above code screws up uploads (which go into the /wp-content directory). I replaced that with the following and it Worked As Intended.

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="apache" style="font-family:monospace;"><span style="color: #00007f;">RewriteCond</span> %{HTTPS} !=<span style="color: #0000ff;">on</span>
<span style="color: #00007f;">RewriteRule</span> ^wp-login(.*)$ https://%{HTTP_HOST}%{REQUEST_URI}      [R,L]
<span style="color: #00007f;">RewriteCond</span> %{HTTPS} !=<span style="color: #0000ff;">on</span>
<span style="color: #00007f;">RewriteRule</span> ^wp-admin(.*)$ https://%{HTTP_HOST}%{REQUEST_URI}      [R,L]</pre>
      </td>
    </tr>
  </table>
</div>

That&#8217;s pretty much it. If your request starts with &#8220;wp-&#8221; it&#8217;ll redirect you to the same URL, but starting with https://. Problem solved. You do need to make sure you have an SSL VirtualHost pointing to your WordPress DocumentRoot so that https://yoursite.com goes to the same place as http://yoursite.com.

<a href="http://affiliate.godaddy.com/redirect/5F43C3ECBA841ACFC3859F4F4E6CA7DA64C271385B2D61A3AD6F3CCE83EB1DD8235E60DCD7D63BCD92E2429E79A75FAC" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://affiliate.godaddy.com/redirect/5F43C3ECBA841ACFC3859F4F4E6CA7DA64C271385B2D61A3AD6F3CCE83EB1DD8235E60DCD7D63BCD92E2429E79A75FAC', '']);" target="_blank"><img src="http://affiliate.godaddy.com/ads/5F43C3ECBA841ACFC3859F4F4E6CA7DA64C271385B2D61A3AD6F3CCE83EB1DD8235E60DCD7D63BCD92E2429E79A75FAC" border="0" width="468"  height="60" alt="Go Daddy $12.99 SSL Sale!" /></a>