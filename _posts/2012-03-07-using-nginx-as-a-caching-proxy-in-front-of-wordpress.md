---
title: Using Nginx as a caching proxy in front of WordPress
author: Evan Hoffman
excerpt: Why you may want to use Nginx as a caching reverse-proxy in front of Apache for Wordpress, and config examples for doing so.
layout: post
permalink: /2012/03/07/using-nginx-as-a-caching-proxy-in-front-of-wordpress/
image:
  - 
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
dsq_thread_id:
  - 2949521615
categories:
  - Uncategorized
tags:
  - apache
  - cache
  - devops
  - linux
  - meta
  - nginx
  - proxy
  - wordpress
---
When I first started monkeying around with Nginx about a year ago, I approached it as a typical Apache fanboy. I&#8217;ve used Apache for 10+ years and it&#8217;s been a champ most of the time. Anyone familiar with Apache knows that you can do just about anything with it. It&#8217;s really the Swiss-army knife of webservers.  
<!--more-->

  
So it was with some skepticism that I installed nginx and started playing around with it. The first thing that struck me was the simplicity of the configuration. The second was the speed. I read <a href="http://arstechnica.com/business/news/2011/11/a-faster-web-server-ripping-out-apache-for-nginx.ars" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://arstechnica.com/business/news/2011/11/a-faster-web-server-ripping-out-apache-for-nginx.ars', 'this story on Ars Technica']);" >this story on Ars Technica</a> which detailed one guy&#8217;s experience replacing Apache with Nginx for serving zillions of static files &#8211; something at which Nginx excels. The third thing that struck me about Nginx was the philosophy of the project. From <a href="http://wiki.nginx.org/WhyUseIt" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://wiki.nginx.org/WhyUseIt', '&#8220;Why Use It&#8221;']);" >&#8220;Why Use It&#8221;</a>

> Apache is like Microsoft Word, it has a million options but you only need six. Nginx does those six things, and it does five of them 50 times faster than Apache.
> 
> &#8212; Chris Lea 

So, to put in less pejorative terms, Apache is the Swiss-army knife while Nginx is an Xacto knife.

I quickly became a fan of Nginx, but I wasn&#8217;t going to dump Apache. Apache&#8217;s still great, and sometimes it&#8217;s just not worth the effort to gut a site and rebuild it with Apache. But after some more reading about Nginx features and use-cases, I started to realize I didn&#8217;t necessarily drop Apache to leverage some of Nginx&#8217;s awesomeness. 

One of the most common uses for Nginx is as a <a href="http://www.evanhoffman.com/evan/?p=1764" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=1764', 'reverse-proxy']);" >reverse-proxy</a>, sometimes as a load-balancer. But Nginx also makes it really easy to use it as a caching reverse-proxy. This is commonly done for caching static content in front of another webserver (sometimes Apache), letting you leverage the awesome performance of Nginx without losing any of Apache&#8217;s functionality. This is what I really wanted to do&#8230; I had a couple of Apache-based sites that were getting swamped, in some cases apparently due to the volume of image requests &#8211; prime candidate for reverse-proxying. Another was a WordPress blog on a severely underpowered server.

The config for caching reverse-proxy is really trivial; there are <a href="http://wiki.nginx.org/HttpProxyModule" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://wiki.nginx.org/HttpProxyModule', 'examples']);" >examples</a> all over the internet, but here&#8217;s the gist:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="config" style="font-family:monospace;">proxy_cache_path  /var/lib/nginx/cache/staticfiles  levels=1:2   keys_zone=staticfilecache:60m inactive=90m  max_size=50m;
proxy_temp_path /var/lib/nginx/proxy;
proxy_connect_timeout 30;
proxy_read_timeout 120;
proxy_send_timeout 120;
&nbsp;
upstream blah {
        server  127.0.0.1:88;
}
&nbsp;
server {
 &lt;blah blah&gt;
        location ~* /evan/.+\.(jpg|png|gif|jpeg|css|js|mp3|wav|swf|mov|ico)$ {
                        proxy_cache_valid 200 120m;
                        expires 864000;
                        proxy_pass http://blah;
                        proxy_cache staticfilecache;
        }
}</pre>
      </td>
    </tr>
  </table>
</div>

This instructs Nginx to cache static content (images, js, audio, video) requests that result in a 200 OK from the upstream for 120 minutes. The <tt>proxy_cache_path</tt> directive describes the &#8220;caching area,&#8221; specifying the on-disk path for cached objects, how many objects to cache (60m), the maximum size of the cache (50 MB), and after how much inactivity to delete them (90 minutes). That&#8217;s a whole lot of awesome in a couple of config lines.

So, this all works, but I wanted to cache the actual WordPress content pages as well, since each page requires hitting the MySQL DB, and most of the sites I manage are rarely updated. I created another key zone (caching area) for PHP content and told it to cache for 20 minutes. There were some problems with this though:

  1. I didn&#8217;t want to cache (or serve cached versions of) the WordPress admin pages. It&#8217;s important to distinguish between caching (the temporary storage on the proxy of the content generated by the source server) and serving of cached content, since Nginx lets you specify settings for both actions independently. The easiest workaround I found for this was putting a .htaccess file in the wp-admin directory with the following:</p> <div class="wp_syntax">
      <table>
        <tr>
          <td class="code">
            <pre class="bash" style="font-family:monospace;">SSLRequireSSL
Header <span style="color: #000000; font-weight: bold;">set</span> Cache-Control <span style="color: #ff0000;">"no-cache"</span>
Header <span style="color: #000000; font-weight: bold;">set</span> X-Accel-Expires <span style="color: #ff0000;">"0"</span>
Header <span style="color: #000000; font-weight: bold;">set</span> Expires <span style="color: #ff0000;">"Wed, 1 Jun 2011 20:00:00 GMT"</span></pre>
          </td>
        </tr>
      </table>
    </div>
    
    Any of these headers should be enough to force Nginx not to cache the contents; I have them all in there just to be safe. SSLRequireSSL forces https for admin. </li> 
    
      * I didn&#8217;t want to cache (or serve cached versions of) any page while I was logged in. When logged in, there&#8217;s a black bar at the top of the page with links to &#8220;edit post&#8221; or &#8220;go to dashboard,&#8221; and I wasn&#8217;t it them in some cases (I was being shown the non-logged-in cached page), and in some cases it would cache the logged-in version of the page, showing it to non-logged-in people, both bad situations. Based on <a href="http://jeradbitner.com/blog/2012/02/09/nginx-do-not-cache-logged-in-drupal-or-wordpress-users/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://jeradbitner.com/blog/2012/02/09/nginx-do-not-cache-logged-in-drupal-or-wordpress-users/', 'this example']);" >this example</a>, I added this to my Nginx config: <div class="wp_syntax">
          <table>
            <tr>
              <td class="code">
                <pre class="bash" style="font-family:monospace;">map <span style="color: #007800;">$http_cookie</span> <span style="color: #007800;">$logged_in</span> <span style="color: #7a0874; font-weight: bold;">&#123;</span>
    default <span style="color: #000000;"></span>;
    ~wordpress_logged_in <span style="color: #000000;">1</span>; <span style="color: #666666; font-style: italic;"># Wordpress session cookie</span>
<span style="color: #7a0874; font-weight: bold;">&#125;</span>
server <span style="color: #7a0874; font-weight: bold;">&#123;</span>
<span style="color: #000000; font-weight: bold;">&lt;</span>bla bla<span style="color: #000000; font-weight: bold;">&gt;</span>
        location <span style="color: #000000; font-weight: bold;">/</span> <span style="color: #7a0874; font-weight: bold;">&#123;</span>
                proxy_pass http:<span style="color: #000000; font-weight: bold;">//</span>blah<span style="color: #000000; font-weight: bold;">/</span>;
                proxy_redirect  http:<span style="color: #000000; font-weight: bold;">//</span>blah<span style="color: #000000; font-weight: bold;">/</span>  http:<span style="color: #000000; font-weight: bold;">//</span><span style="color: #007800;">$host</span><span style="color: #000000; font-weight: bold;">/</span>;
&nbsp;
                proxy_cache_bypass <span style="color: #007800;">$logged_in</span>;
                proxy_no_cache <span style="color: #007800;">$logged_in</span>;
&nbsp;
                proxy_cache php;
                proxy_cache_valid <span style="color: #000000;">200</span> 30m;
                expires 5m;
        <span style="color: #7a0874; font-weight: bold;">&#125;</span>
<span style="color: #7a0874; font-weight: bold;">&#125;</span></pre>
              </td>
            </tr>
          </table>
        </div>
        
        If logged in, both proxy\_cache\_bypass and proxy\_no\_cache are set to &#8220;1&#8221;, so all my logged-in request bypass the cache entirely. </li> 
        
          * I have the WPTouch plugin installed so iPhone users see a more iPhone-friendly version (rather than the regular site shrunk down to the iPhone&#8217;s screen size. I didn&#8217;t want desktop users seeing cached iPhone versions of the pages, and vice versa. To solve this, I set a $mobile var if &#8220;iphone&#8221; or &#8220;android&#8221; appear in $http\_user\_agent and incorporate that value into the proxy\_cache\_key. This way every page is still cached, but mobile users see the mobile version, and desktop users see the desktop version: <div class="wp_syntax">
              <table>
                <tr>
                  <td class="code">
                    <pre class="bash" style="font-family:monospace;">        <span style="color: #000000; font-weight: bold;">set</span> <span style="color: #007800;">$mobile</span> <span style="color: #ff0000;">"_not_mobile_"</span>;
        <span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">&#40;</span><span style="color: #007800;">$http_user_agent</span> ~<span style="color: #000000; font-weight: bold;">*</span> <span style="color: #ff0000;">"iPhone"</span><span style="color: #7a0874; font-weight: bold;">&#41;</span> <span style="color: #7a0874; font-weight: bold;">&#123;</span>
                <span style="color: #000000; font-weight: bold;">set</span> <span style="color: #007800;">$mobile</span> <span style="color: #ff0000;">"mobile"</span>;
        <span style="color: #7a0874; font-weight: bold;">&#125;</span>
        <span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">&#40;</span><span style="color: #007800;">$http_user_agent</span> ~<span style="color: #000000; font-weight: bold;">*</span> <span style="color: #ff0000;">"Android"</span><span style="color: #7a0874; font-weight: bold;">&#41;</span> <span style="color: #7a0874; font-weight: bold;">&#123;</span>
                <span style="color: #000000; font-weight: bold;">set</span> <span style="color: #007800;">$mobile</span> <span style="color: #ff0000;">"mobile"</span>;
        <span style="color: #7a0874; font-weight: bold;">&#125;</span>
        location <span style="color: #000000; font-weight: bold;">/</span> <span style="color: #7a0874; font-weight: bold;">&#123;</span>
                proxy_pass http:<span style="color: #000000; font-weight: bold;">//</span>blah<span style="color: #000000; font-weight: bold;">/</span>;
                proxy_redirect  http:<span style="color: #000000; font-weight: bold;">//</span>blah<span style="color: #000000; font-weight: bold;">/</span>  http:<span style="color: #000000; font-weight: bold;">//</span><span style="color: #007800;">$host</span><span style="color: #000000; font-weight: bold;">/</span>;
&nbsp;
                proxy_cache_key <span style="color: #ff0000;">"<span style="color: #007800;">$mobile</span>.<span style="color: #007800;">$scheme</span><span style="color: #007800;">$host</span><span style="color: #007800;">$request_uri</span>"</span>;
&nbsp;
                proxy_cache_bypass <span style="color: #007800;">$logged_in</span>;
                proxy_no_cache <span style="color: #007800;">$logged_in</span>;
&nbsp;
                proxy_cache php;
                proxy_cache_valid <span style="color: #000000;">200</span> 30m;
                expires 5m;
        <span style="color: #7a0874; font-weight: bold;">&#125;</span></pre>
                  </td>
                </tr>
              </table>
            </div></ol> 
        
        So far this config has been working out pretty well. Uncached pages still load slow, but subsequent cached loads are pretty quick. I tweak it regularly, but here&#8217;s what I have currently, with Apache listening on 8888:
        
        <div class="wp_syntax">
          <table>
            <tr>
              <td class="line_numbers">
                <pre>1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
</pre>
              </td>
              
              <td class="code">
                <pre class="config" style="font-family:monospace;"># Wordpress reverse-proxy config
proxy_cache_path  /var/lib/nginx/cache/staticfiles  levels=1:2   keys_zone=staticfilecache:60m inactive=90m  max_size=50m;
proxy_cache_path  /var/lib/nginx/cache/php levels=2:2 keys_zone=php:30m inactive=60m max_size=50m;
proxy_temp_path /var/lib/nginx/proxy;
proxy_connect_timeout 30;
proxy_read_timeout 120;
proxy_send_timeout 120;
&nbsp;
proxy_cache_key "$scheme$host$request_uri";
# http://wp-performance.com/2010/10/nginx-reverse-proxy-cache-wordpress-apache/
&nbsp;
map $http_cookie $logged_in {
    default 0;
    ~wordpress_logged_in 1; # Wordpress session cookie
}
&nbsp;
upstream apache {
        server  127.0.0.1:8888;
}
&nbsp;
server {
        proxy_cache_valid 200 20m;
&nbsp;
        listen 80 default_server;
        server_name _;
&nbsp;
        access_log  /var/log/nginx/combined-access.log combined;
&nbsp;
        proxy_set_header X-Real-IP  $remote_addr;
&nbsp;
        proxy_set_header Host $host;
&nbsp;
        proxy_set_header X-Forwarded-For $remote_addr;
&nbsp;
        proxy_set_header X-NginX-Proxy true;
&nbsp;
        set $mobile "_not_mobile_";
        if ($http_user_agent ~* "iPhone") {
                set $mobile "iphone";
        }
        if ($http_user_agent ~* "Android") {
                set $mobile "android";
        }
&nbsp;
        location / {
                proxy_pass http://apache/;
                proxy_redirect  http://apache/  http://$host/;
        }
&nbsp;
        location /evan/ {
                proxy_pass http://apache/evan/;
                proxy_redirect  http://apache/  http://$host/;
&nbsp;
                proxy_cache_key "$mobile.$scheme$host$request_uri";
&nbsp;
                proxy_cache_bypass $logged_in;
                proxy_no_cache $logged_in;
&nbsp;
                proxy_cache php;
                proxy_cache_valid 200 30m;
                expires 5m;
        }
&nbsp;
        location /evan/wp-admin/ {
                proxy_pass http://apache/evan/wp-admin/;
                proxy_redirect  http://apache/  http://$host/;
        }
&nbsp;
        location ~* /evan/.+\.(jpg|png|gif|jpeg|css|js|mp3|wav|swf|mov|ico)$ {
                        proxy_cache_valid 200 120m;
                        expires 864000;
                        proxy_pass http://apache;
                        proxy_cache staticfilecache;
        }
&nbsp;
        location = /50x.html {
                root   /var/www/nginx-default;
        }
&nbsp;
        # No access to .htaccess files.
        location ~ /\.ht {
                deny  all;
        }
&nbsp;
        }</pre>
              </td>
            </tr>
          </table>
        </div>