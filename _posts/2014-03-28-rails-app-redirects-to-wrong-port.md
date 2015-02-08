---
title: Rails app redirects to wrong port?
author: Evan Hoffman
layout: post
permalink: /2014/03/28/rails-app-redirects-to-wrong-port/
dsq_thread_id:
  - 3104598519
categories:
  - Uncategorized
tags:
  - 302
  - apache
  - http
  - nginx
  - port
  - rails
  - redirect
  - unicorn
---
Ran into a situation in which a rails application was redirecting to /login to force a user to log in, but the Location header said &#8220;http://site.com:8085/login&#8221;, because nginx was listening on port 8085 on that server. At first I looked to see if there was something in the application code that was doing this, or maybe some setting I could change to fix it, but came up blank. After some Googling I found the answer right <a href="http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_redirect" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_redirect', 'in the Nginx docs']);" >in the Nginx docs</a> (below is my slightly-modified solution that handles https urls as well):

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;">   proxy_redirect ~^<span style="color: #7a0874; font-weight: bold;">&#40;</span>http<span style="color: #7a0874; font-weight: bold;">&#40;</span>s?<span style="color: #7a0874; font-weight: bold;">&#41;</span>:<span style="color: #000000; font-weight: bold;">//</span><span style="color: #7a0874; font-weight: bold;">&#91;</span>^:<span style="color: #7a0874; font-weight: bold;">&#93;</span>+<span style="color: #7a0874; font-weight: bold;">&#41;</span>:\d+<span style="color: #7a0874; font-weight: bold;">&#40;</span><span style="color: #000000; font-weight: bold;">/</span>.+<span style="color: #7a0874; font-weight: bold;">&#41;</span>$ <span style="color: #007800;">$1</span><span style="color: #007800;">$3</span>;</pre>
      </td>
    </tr>
  </table>
</div>

That simply removes the port number from the Location: header, so whatever kind of proxy magic you&#8217;re doing will &#8220;just work.&#8221;