---
title: Load balancing in EC2 with Nginx and HAProxy
author: Evan Hoffman
layout: post
permalink: /2012/01/09/load-balancing-in-ec2-with-nginx-and-haproxy/
image:
  - 
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
dsq_thread_id:
  - 2949526900
categories:
  - Uncategorized
tags:
  - amazon
  - aws
  - devops
  - ec2
  - elastic loadbalancer
  - elb
  - gslb
  - haproxy
  - http
  - linux
  - load balancer
  - nginx
  - proxy
  - reverse proxy
  - ssl
  - work
---
We wanted to setup a loadbalanced web cluster in AWS for expansion. My first inclination was to use <a href="http://aws.amazon.com/elasticloadbalancing/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://aws.amazon.com/elasticloadbalancing/', 'ELB']);" >ELB</a> for this, but I soon learned that ELB doesn&#8217;t let you allocate a static IP, requiring you to refer to it only by DNS name. This would be OK except for the fact that our current DNS provider, <a href="http://dyn.com/dns/dynect-managed-dns/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://dyn.com/dns/dynect-managed-dns/', 'Dyn']);" >Dyn</a>, requires IP addresses when using their GSLB (geo-based load balancer) service.

Rather than let this derail the whole project, I decided to look into the software options available for loadbalancing in EC2. I&#8217;ve been a fan of hardware load balancers for a while, sort of looking down at software-based solutions without any real rationale, but in this case I really had no choice so I figured I&#8217;d give it a try.

My first stop was Nginx. I&#8217;ve used it before in a reverse-proxy scenario and like it. The problem I had with it was that it doesn&#8217;t support active polling of nodes &#8211; the ability to send requests to the webserver and mark the node as up or down based on the response. As far as I can tell, using <a href="http://wiki.nginx.org/HttpUpstreamModule" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://wiki.nginx.org/HttpUpstreamModule', 'multiple upstream servers']);" >multiple upstream servers</a> in Nginx allows you to specify <tt>max_fails</tt> and <tt>fail_timeout</tt>, however a &#8220;fail&#8221; is determined when a real request comes in. I don&#8217;t want to risk losing a real request &#8211; I like active polling.  
<!--more-->

  
This led me to HAProxy. I&#8217;d never used HAProxy before but it seemed to be ideally suited to this (since it&#8217;s exclusively a load balancer). The <tt>option httpchk</tt> even allows for active polling of nodes &#8211; yay!

Unfortunately, HAProxy doesn&#8217;t support SSL. From <a href="http://haproxy.1wt.eu/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://haproxy.1wt.eu/', 'the HAProxy site']);" >the HAProxy site</a>:

> People often ask for SSL and Keep-Alive support. Both features will complicate the code and render it fragile for several releases. By the way, both features have a negative impact on performance :
> 
> Having SSL in the load balancer itself means that it becomes the bottleneck. When the load balancer&#8217;s CPU is saturated, the overall response times will increase and the only solution will be to multiply the load balancer with another load balancer in front of them. the only scalable solution is to have an SSL/Cache layer between the clients and the load balancer. Anyway for small sites it still makes sense to embed SSL, and it&#8217;s currently being studied. There has been some work on the CyaSSL library to ease integration with HAProxy, as it appears to be the only one out there to let you manage your memory yourself. 

Poop! I figured out a workaround however, by using both Nginx and HAProxy on the same instance. HAProxy listens on port 80 and 8443 (so that it can relay decrypted SSL traffic to the nodes on a separate port, so that the nodes are aware that it was originally SSL traffic). Nginx is configured as a reverse proxy, listens on port 443 only, and has the SSL cert &#038; key. The upstream for the Nginx is just localhost:8443 &#8211; HAProxy. 

This was pretty easy to setup and works very well. I benchmarked HAProxy on an EC2 t1.micro instance (in front of two m1.large instances running our webapp) using <tt>ab -n 5000 -c 50 -t 60</tt> and found it actually performed better than one of our hardware load balancers. That was pretty eye-opening (and sad).

The HAProxy and Nginx configs are below, in the hopes that it helps someone. The main warning I&#8217;d give is that using this will cause the logs on your nodes to interpret all requests as coming from the IP of the load balancer. I had to rewrite some code to have the app use the X-Forwarded-For address rather than the <a href="http://docs.oracle.com/javaee/5/api/javax/servlet/ServletRequest.html#getRemoteAddr%28%29" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://docs.oracle.com/javaee/5/api/javax/servlet/ServletRequest.html#getRemoteAddr%28%29', 'REMOTE_ADDR']);" >REMOTE_ADDR</a>, but other than that this has been working out pretty well.

/etc/nginx/nginx.conf  
Main thing is to make sure the server isn&#8217;t listening on port 80 (since HAProxy needs to).

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
</pre>
      </td>
      
      <td class="code">
        <pre class="txt" style="font-family:monospace;">user              nginx;
worker_processes  1;
&nbsp;
error_log  /var/log/nginx/error.log;
&nbsp;
pid        /var/run/nginx.pid;
&nbsp;
events {
    worker_connections  1024;
}
&nbsp;
http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
&nbsp;
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
&nbsp;
    access_log  /var/log/nginx/access.log  main;
&nbsp;
    sendfile        on;
    keepalive_timeout  65;
&nbsp;
    #
    # The default server
    #
    server {
        listen       81;
        server_name  _;
&nbsp;
        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm;
        }
&nbsp;
        error_page  404              /404.html;
        location = /404.html {
            root   /usr/share/nginx/html;
        }
&nbsp;
        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
&nbsp;
    }
&nbsp;
    # Load config files from the /etc/nginx/conf.d directory
    include /etc/nginx/conf.d/*.conf;
&nbsp;
}</pre>
      </td>
    </tr>
  </table>
</div>

/etc/nginx/conf.d/ssl-offloader.conf

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
</pre>
      </td>
      
      <td class="code">
        <pre class="txt" style="font-family:monospace;">upstream haproxy {
        server localhost:8443 ;
}
&nbsp;
server {
        listen       443;
        server_name f.q.d.n 1.2.3.4 ; # I put the FQDN and IP here, but maybe "_" will work too
#  server_name  _;
&nbsp;
        ssl                  on;
        ssl_certificate      /etc/nginx/ssl-cert/cert.pem;
        ssl_certificate_key  /etc/nginx/ssl-cert/cert.key;
&nbsp;
        ssl_session_timeout  5m;
&nbsp;
        ssl_protocols  SSLv3 TLSv1;
        ssl_ciphers     ECDHE-RSA-AES256-SHA384:AES256-SHA256:RC4:HIGH:!MD5:!aNULL:!EDH:!AESGCM;
        ssl_prefer_server_ciphers   on;
&nbsp;
        location / {
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header Host $http_host;
                proxy_set_header X-NginX-Proxy true;
&nbsp;
                proxy_pass http://haproxy/;
                proxy_redirect default;
                proxy_redirect http://$host/ https://$host/;
                proxy_redirect http://hostname/ https://$host/;
&nbsp;
                proxy_read_timeout 15s;
                proxy_connect_timeout 15s;
        }
&nbsp;
}</pre>
      </td>
    </tr>
  </table>
</div>

/etc/haproxy/haproxy.cfg

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
</pre>
      </td>
      
      <td class="code">
        <pre class="txt" style="font-family:monospace;">#---------------------------------------------------------------------
# Global settings
#---------------------------------------------------------------------
global
    log         127.0.0.1 local2
&nbsp;
    chroot      /var/lib/haproxy
    pidfile     /var/run/haproxy.pid
    maxconn     4000
    user        haproxy
    group       haproxy
    daemon
&nbsp;
    # turn on stats unix socket
    stats socket /var/lib/haproxy/stats
&nbsp;
#---------------------------------------------------------------------
# common defaults that all the 'listen' and 'backend' sections will
# use if not designated in their block
#---------------------------------------------------------------------
defaults
    mode                    http
    log                     global
    option                  httplog
    option                  dontlognull
    option http-server-close
    option forwardfor       except 127.0.0.0/8
    option                  redispatch
    retries                 3
    timeout http-request    3s
    timeout queue           1m
    timeout connect         2s
    timeout client          5s
    timeout server          5s
    timeout http-keep-alive 1s
    timeout check           10s
    maxconn                 3000
&nbsp;
       stats enable
       stats auth evan:change_me_brother
&nbsp;
#---------------------------------------------------------------------
# main frontend which proxys to the backends
#---------------------------------------------------------------------
frontend  main_http *:80
        option forwardfor except 127.0.0.1  
        option httpclose
        default_backend         web_http
&nbsp;
frontend main_https *:8443
        option forwardfor except 127.0.0.1  
        option httpclose
        default_backend         web_https
&nbsp;
#---------------------------------------------------------------------
# round robin balancing between the various backends
#---------------------------------------------------------------------
backend web_http
    balance     roundrobin
#       option httpchk GET / HTTP/1.1\r\nHost:\ host.com
        option httpchk
    server  node1 192.168.1.20:80 check port 80
    server  node2 192.168.1.30:80 check port 80
    server  node3 192.168.1.40:80 check port 80
&nbsp;
&nbsp;
backend web_https
    balance     roundrobin
#       option httpchk GET / HTTP/1.1\r\nHost:\ host.com
        option httpchk
    server  node1 192.168.1.20:8443 check port 8443
    server  node2 192.168.1.30:8443 check port 8443
    server  node3 192.168.1.40:8443 check port 8443</pre>
      </td>
    </tr>
  </table>
</div>