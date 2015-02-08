---
title: Ruby/Sinatra part 2
author: Evan Hoffman
layout: post
permalink: /2010/10/08/ruby-sinatra-part-2/
dsq_thread_id:
  - 3469916661
categories:
  - Uncategorized
tags:
  - 1.9.2
  - 1.9.2-p0
  - error
  - linux
  - ruby
  - sinatra
---
Following up on <a href="http://www.evanhoffman.com/evan/?p=791" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=791', 'my previous post']);" >my previous post</a>, I decided to go the easier route and just install an older version of Ruby that didn&#8217;t have the problem with Sinatra, since I wanted a setup I could replicate easily, and editing the server.rb each time I installed wasn&#8217;t what I was going for. I downloaded 1.9.2-rc2 from <a href="http://ftp.ruby-lang.org//pub/ruby/1.9/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://ftp.ruby-lang.org//pub/ruby/1.9/', 'http://ftp.ruby-lang.org//pub/ruby/1.9/']);" >http://ftp.ruby-lang.org//pub/ruby/1.9/</a>, compiled it and it works:

<pre>[evan@evanfc12 ~]$ ruby --version
ruby 1.9.2dev (2010-07-11 revision 28618) [x86_64-linux]
[evan@evanfc12 ~]$ ruby hi.rb ^C
[evan@evanfc12 ~]$ cat hi.rb

require 'sinatra'

get '/' do
	"Hello World!"
end
[evan@evanfc12 ~]$ ruby hi.rb
== Sinatra/1.0 has taken the stage on 4567 for development with backup from WEBrick
[2010-10-08 15:32:23] INFO  WEBrick 1.3.1
[2010-10-08 15:32:23] INFO  ruby 1.9.2 (2010-07-11) [x86_64-linux]
[2010-10-08 15:32:23] INFO  WEBrick::HTTPServer#start: pid=21758 port=4567
</pre>

Speed bumps like this are so much fun!