---
title: Benchmarking DNS servers with Java
author: Evan Hoffman
layout: post
permalink: /2011/09/26/benchmarking-dns-servers-with-java/
image:
  - 
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
categories:
  - Uncategorized
tags:
  - benchmark
  - code
  - dns
  - java
  - programming
  - query
  - time
---
I&#8217;m currently in the process of moving our DNS over to another provider and I was curious as to whether the old or new provider offers faster lookups. `dig` shows query times, but I didn&#8217;t want to just run that over and over. I decided to write something to do this, in Java since I like Java. I found <a href="http://mowyourlawn.com/blog/?p=8" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://mowyourlawn.com/blog/?p=8', 'this post,']);" >this post,</a> which has the meat of the work done already. I also read some of Sun&#8217;s <a href="http://download.oracle.com/javase/1,5.0/docs/guide/jndi/jndi-dns.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://download.oracle.com/javase/1,5.0/docs/guide/jndi/jndi-dns.html', 'JNDI/DNS']);" >JNDI/DNS</a> lookup info, which was pretty dense. All I want to do is specify the name server&#8217;s IP and do the lookup. I don&#8217;t even really care about the result, just how long the query takes.

The thing I wrote only looks up `A` records, but can easily be modified to do CNAMEs or whatever. Here&#8217;s how you call it:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;">$ java <span style="color: #660033;">-jar</span> DNSTester.jar 4.2.2.2 www.google.com <span style="color: #000000;">25</span>
Resolved www.google.com to 74.125.235.19 against NS 4.2.2.2
Performed <span style="color: #000000;">25</span> lookups <span style="color: #000000; font-weight: bold;">in</span> <span style="color: #000000;">233.29</span> milliseconds.  Average 9.3316ms per lookup.
&nbsp;
$ java <span style="color: #660033;">-jar</span> DNSTester.jar 8.8.4.4 www.google.com <span style="color: #000000;">25</span>
Resolved www.google.com to 74.125.226.146 against NS 8.8.4.4
Performed <span style="color: #000000;">25</span> lookups <span style="color: #000000; font-weight: bold;">in</span> <span style="color: #000000;">450.034</span> milliseconds.  Average 18.00136ms per lookup.</pre>
      </td>
    </tr>
  </table>
</div>

Code is in github <a href="https://github.com/evandhoffman/DNSBench" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://github.com/evandhoffman/DNSBench', 'here']);" >here</a>. Jar is available <a href="http://www.evanhoffman.com/java/DNSTester.jar" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/java/DNSTester.jar', 'here']);" >here</a>.