---
title: 'Putting up a &quot;down for maintenance&quot; message using mod_rewrite'
author: Evan Hoffman
layout: post
permalink: /2010/10/08/putting-up-a-down-for-maintenance-message-using-mod_rewrite/
dsq_thread_id:
  - 3113055062
categories:
  - Uncategorized
tags:
  - .htaccess
  - apache
  - mod_rewrite
  - redirect
---
Putting this here for safekeeping so my future self can find it. Mod_rewrite is one of my favorite tools, but it&#8217;s easy to spend 30 minutes crafting a 2-line directive that actually does what you want. I put this in a .htaccess file in the DocumentRoot of the server, put a &#8220;We&#8217;re down&#8221; message in maintenance.html (or whatever), and all requests will get a 302 redirect to /maintenance.html, except requests for /maintenance.html (for obvious reasons). It appends the original request in case you want to do something with it but that&#8217;s not really important. It also doesn&#8217;t do the redirect for images/js/css so those can actually be used in the maintenance message.

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="apache" style="font-family:monospace;"><span style="color: #00007f;">RewriteEngine</span> <span style="color: #0000ff;">on</span>
<span style="color: #00007f;">RewriteRule</span> ^maintenance.html	-	[PT,L]
&nbsp;
<span style="color: #00007f;">RewriteCond</span> %{REQUEST_URI}	!(gif|jpg|css|js)$
<span style="color: #00007f;">RewriteRule</span> (.*) /maintenance.html?redir=true&originalRequest=%{REQUEST_URI} [R=<span style="color: #ff0000;">302</span>]</pre>
      </td>
    </tr>
  </table>
</div>