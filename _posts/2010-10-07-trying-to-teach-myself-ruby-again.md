---
title: Trying to teach myself Ruby (again)
author: Evan Hoffman
layout: post
permalink: /2010/10/07/trying-to-teach-myself-ruby-again/
dsq_thread_id:
  - 2949524894
categories:
  - Uncategorized
tags:
  - 1.9.2-p0
  - error
  - hello world
  - linux
  - ruby
  - sinatra
  - sinatra exits
  - "sinatra won't start"
---
Now that I have a real web project to work on I figured it&#8217;s a good time to try and teach myself Ruby again. Sinatra seems to be the new hotness so that&#8217;s what I&#8217;m trying, but so far things aren&#8217;t going quite as I expected. Everything appears to be installed, but when I run the &#8220;Hello, World!&#8221; script, the webserver doesn&#8217;t start as it&#8217;s apparently supposed to do:

<pre>[evan@ehoffman 16:59:34 ~]$ ruby --version
ruby 1.9.2p0 (2010-08-18 revision 29036) [x86_64-linux]
[evan@ehoffman 16:59:39 ~]$ irb
irb(main):001:0> require 'sinatra'
=> true
irb(main):002:0>
You have new mail in /var/spool/mail/evan
[evan@ehoffman 17:00:20 ~]$ cat hi.rb
#require 'rubygems'
require 'sinatra'

get '/' do
	"Hello World"
end
[evan@ehoffman 17:00:24 ~]$ ruby hi.rb
[evan@ehoffman 17:00:33 ~]$
</pre>

I&#8217;ve tried this on two different systems with the same result. I&#8217;ve seen this same sample code in 20 different places, including <a href="http://www.sinatrarb.com" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.sinatrarb.com', 'sinatrarb.com']);" >sinatrarb.com</a>, but for some reason it&#8217;s not working for me. Rather than firing up a webserver on port 4567, running &#8220;ruby hi.rb&#8221; simply exits immediately.

Edit: According to <a href="http://groups.google.com/group/comp.lang.ruby/browse_thread/thread/75fd01c34551331b/107ac640dedac751?lnk=raot" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://groups.google.com/group/comp.lang.ruby/browse_thread/thread/75fd01c34551331b/107ac640dedac751?lnk=raot', 'a post on comp.lang.ruby']);" >a post on comp.lang.ruby</a> entitled **1.9.2-rc2 -> 1.9.2-p0 breaks Sinatra**, server.rb needs to be edited:

> Sinatra&#8217;s behaviour has appeared to change in the 1.9 compatible  
> versions.  
> I need to add the following line in my server.rb:  
> set :run, true

I&#8217;m debating whether it&#8217;s worth doing this (I have 8 different <tt>server.rb</tt>s in my /usr dir) or just reinstalling an older version of ruby that doesn&#8217;t have this issue.

Edit 2: Resolved by <a href="http://www.evanhoffman.com/evan/?p=803" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=803', 'installing Ruby 1.9.2-rc2']);" >installing Ruby 1.9.2-rc2</a>.