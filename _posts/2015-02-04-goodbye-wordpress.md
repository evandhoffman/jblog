---
title: Bye Bye Wordpress
author: Evan Hoffman
layout: post
permalink: /2015/02/04/bye-bye-wordpress/
categories:
  - Uncategorized
tags:
  - wordpress
  - jekyll
  - meta 
---
I've moved this site off WordPress and onto <a href="http://jekyllrb.com/">Jekyll</a>.  WordPress is nice, but I'm tired of having to update everything all the time, and already had this site compromised due to the
awfulness of PHP.  Jekyll's just static HTML files, which is more than sufficient for this crappy site (especially since I rarely update it these days), and I handed off commenting to Disqus so I don't have to deal with
spam comments at all any more.  Only real drawback is inbound links that will stop working, but eh, oh well.  Google will either recrawl and find them, or it won't.  

Looking in the nginx log, here's a great reason to leave WordPress:
{% highlight text linenos %}
62.210.200.6 - - [04/Feb/2015:21:06:02 +0000] "POST /wp-login.php HTTP/1.0" 404 295 "-" "-" "-" "-" "text/html; charset=iso-8859-1" "-" 0.075
62.210.200.6 - - [04/Feb/2015:21:06:02 +0000] "POST /wp-login.php HTTP/1.0" 404 295 "-" "-" "-" "-" "text/html; charset=iso-8859-1" "-" 0.101
62.210.200.6 - - [04/Feb/2015:21:06:02 +0000] "POST /wp-login.php HTTP/1.0" 404 295 "-" "-" "-" "-" "text/html; charset=iso-8859-1" "-" 0.102
62.210.200.6 - - [04/Feb/2015:21:06:02 +0000] "POST /wp-login.php HTTP/1.0" 404 295 "-" "-" "-" "-" "text/html; charset=iso-8859-1" "-" 0.087
62.210.200.6 - - [04/Feb/2015:21:06:02 +0000] "POST /wp-login.php HTTP/1.0" 404 295 "-" "-" "-" "-" "text/html; charset=iso-8859-1" "-" 0.074
62.210.200.6 - - [04/Feb/2015:21:06:03 +0000] "POST /wp-login.php HTTP/1.0" 404 295 "-" "-" "-" "-" "text/html; charset=iso-8859-1" "-" 0.088
62.210.200.6 - - [04/Feb/2015:21:06:03 +0000] "POST /wp-login.php HTTP/1.0" 404 295 "-" "-" "-" "-" "text/html; charset=iso-8859-1" "-" 0.074
62.210.200.6 - - [04/Feb/2015:21:06:03 +0000] "POST /wp-login.php HTTP/1.0" 404 295 "-" "-" "-" "-" "text/html; charset=iso-8859-1" "-" 0.088
62.210.200.6 - - [04/Feb/2015:21:06:03 +0000] "POST /wp-login.php HTTP/1.0" 404 295 "-" "-" "-" "-" "text/html; charset=iso-8859-1" "-" 0.074
62.210.200.6 - - [04/Feb/2015:21:06:03 +0000] "POST /wp-login.php HTTP/1.0" 404 295 "-" "-" "-" "-" "text/html; charset=iso-8859-1" "-" 0.088
62.210.200.6 - - [04/Feb/2015:21:06:03 +0000] "POST /wp-login.php HTTP/1.0" 404 295 "-" "-" "-" "-" "text/html; charset=iso-8859-1" "-" 0.101
62.210.200.6 - - [04/Feb/2015:21:06:04 +0000] "POST /wp-login.php HTTP/1.0" 404 295 "-" "-" "-" "-" "text/html; charset=iso-8859-1" "-" 0.088
62.210.200.6 - - [04/Feb/2015:21:06:04 +0000] "POST /wp-login.php HTTP/1.0" 404 295 "-" "-" "-" "-" "text/html; charset=iso-8859-1" "-" 0.073
62.210.200.6 - - [04/Feb/2015:21:06:04 +0000] "POST /wp-login.php HTTP/1.0" 404 295 "-" "-" "-" "-" "text/html; charset=iso-8859-1" "-" 0.087
62.210.200.6 - - [04/Feb/2015:21:06:04 +0000] "POST /wp-login.php HTTP/1.0" 404 295 "-" "-" "-" "-" "text/html; charset=iso-8859-1" "-" 0.087
62.210.200.6 - - [04/Feb/2015:21:06:04 +0000] "POST /wp-login.php HTTP/1.0" 404 295 "-" "-" "-" "-" "text/html; charset=iso-8859-1" "-" 0.086
{% endhighlight %}
