---
title: Graphing SSH dictionary attacks with HighCharts
author: Evan Hoffman
layout: post
permalink: /2011/11/18/graphing-ssh-dictionary-attacks-with-highcharts/
image:
  - 
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
categories:
  - Uncategorized
tags:
  - code
  - fun
  - geo ip
  - hack
  - javascript
  - maxmind
  - meta
  - perl
  - php
  - postgresql
  - regex
  - secure
  - security
  - sql
  - ssh
---
After my 10-year-old basement Linux server died this week from a power outage, I took the sad step of giving up on it. It&#8217;s died before and I&#8217;ve patched it back together with a new power supply here or an addon PCI SATA card there, but I finally decided to throw in the towel since I had a newer old computer that had been idle for several years. The one that died was an Athlon K7 750 MHz with 512 MB ram. The new one is an Athlon 2 GHz (3200+) with 1 gig. For my uses, specs don&#8217;t really matter that much, but it&#8217;s nice to have more power for free.

I put CentOS 6 on it and configured Samba and copied all the data off the old machine and was back up and running within a few hours. Since I forward ports through my FiOS router to this box I did my standard lockdown procedure, including adding myself to the AllowUsers in sshd_config. Afterwards I took a look in /var/log/secure and saw the typical flood of dictionary attacks trying to get in as root or bob or tfeldman or jweisz. I have iptables configured to rate-limit SSH connections to 2 per 5 seconds per IP so the box doesn&#8217;t get DoSed out of existence, but some stuff does make it through to sshd. 

Looking through /var/log/secure, I got to thinking it would be interesting if there was some way to visualize the attacks in a handy graph. Then I remembered, oh, wait, I can do that.

I wrote a perl script to parse out the attacks from /var/log/secure and insert them into a Postgres DB. This turned out to be pretty easy. Then I thought it would be more interesting to tie the IP of each attack to its originating country. I&#8217;ve used <a href="http://www.maxmind.com/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.maxmind.com/', 'MaxMind&#8217;s']);" >MaxMind&#8217;s</a> GeoIP DB pretty extensively before, but I was looking something free. That&#8217;s when I remembered that MaxMind has a free GeoIP DB: <a href="http://www.maxmind.com/app/geolitecity" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.maxmind.com/app/geolitecity', 'GeoLiteCity']);" >GeoLiteCity</a>. I grabbed it and yum-installed the <a href="http://search.cpan.org/~borisz/Geo-IP-1.40/lib/Geo/IP.pm" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://search.cpan.org/~borisz/Geo-IP-1.40/lib/Geo/IP.pm', 'Perl lib']);" >Perl lib</a> and added the geo data to the attack DB. Rather than worry about normalizing the schema I just shoved the info into the same table. Life is easier this way, and it&#8217;s just a for-fun project.

So I got that all working and parsed it against the existing /var/log/secures via

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #7a0874; font-weight: bold;">&#91;</span>root<span style="color: #000000; font-weight: bold;">@</span>lunix2011 ~<span style="color: #7a0874; font-weight: bold;">&#93;</span><span style="color: #666666; font-style: italic;"># zcat /var/log/secure-20111117.gz | perl parse-secure.pl</span></pre>
      </td>
    </tr>
  </table>
</div>

I wrote <a href="https://github.com/evandhoffman/parse-secure/blob/master/php/ssh.php" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://github.com/evandhoffman/parse-secure/blob/master/php/ssh.php', 'ssh.php']);" >ssh.php</a> to see what&#8217;s in the table:<figure id="attachment_1684" style="width: 400px;" class="wp-caption aligncenter">

<a href="http://www.evanhoffman.com/evan/2011/11/18/graphing-ssh-dictionary-attacks-with-highcharts/screen-shot-2011-11-18-at-1-38-07-pm/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/2011/11/18/graphing-ssh-dictionary-attacks-with-highcharts/screen-shot-2011-11-18-at-1-38-07-pm/', '']);"  rel="attachment wp-att-1684"><img src="http://www.evanhoffman.com/evan/wp-content/uploads/2011/11/Screen-shot-2011-11-18-at-1.38.07-PM.png" alt="ssh.php list of hacking attempts" title="ssh.php list of hacking attempts" width="300" class="size-full wp-image-1684" /></a><figcaption class="wp-caption-text">ssh.php list of hacking attempts</figcaption></figure> 

So now that the data was all in place, time to move on to the graphs, which is what I really wanted to do. Last time I wanted to graph data programmatically I used <a href="http://jpgraph.net/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://jpgraph.net/', 'JPGraph']);" >JPGraph</a>, which does everything in PHP and is super versatile. But I wanted something&#8230; cooler. Maybe something interactive. A little Googling turned up <a href="http://www.highcharts.com/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.highcharts.com/', 'Highcharts']);" >Highcharts</a> which is absolutely awesome, and does everything in JavaScript. I basically modified some of their example charts and pumped my data into them and got the charts below.

Pie chart of attacks grouped by country for the past 30 days:<figure id="attachment_1687" style="width: 300px;" class="wp-caption aligncenter">

<a href="http://www.evanhoffman.com/evan/2011/11/18/graphing-ssh-dictionary-attacks-with-highcharts/screen-shot-2011-11-18-at-2-01-46-pm/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/2011/11/18/graphing-ssh-dictionary-attacks-with-highcharts/screen-shot-2011-11-18-at-2-01-46-pm/', '']);"  rel="attachment wp-att-1687"><img src="http://www.evanhoffman.com/evan/wp-content/uploads/2011/11/Screen-shot-2011-11-18-at-2.01.46-PM.png" alt="Pie chart by country" title="Pie chart by country" width="300"  class="size-full wp-image-1687" /></a><figcaption class="wp-caption-text">Pie chart by country</figcaption></figure> 

Bar graph of attacks per day:<figure id="attachment_1688" style="width: 300px;" class="wp-caption aligncenter">

<a href="http://www.evanhoffman.com/evan/2011/11/18/graphing-ssh-dictionary-attacks-with-highcharts/screen-shot-2011-11-18-at-2-01-30-pm/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/2011/11/18/graphing-ssh-dictionary-attacks-with-highcharts/screen-shot-2011-11-18-at-2-01-30-pm/', '']);"  rel="attachment wp-att-1688"><img src="http://www.evanhoffman.com/evan/wp-content/uploads/2011/11/Screen-shot-2011-11-18-at-2.01.30-PM.png" alt="Bar graph of daily attacks" title="Bar graph of daily attacks" width="300"  class="size-full wp-image-1688" /></a><figcaption class="wp-caption-text">Bar graph of daily attacks</figcaption></figure> 

So, that&#8217;s that. Code is in <a href="https://github.com/evandhoffman/parse-secure" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://github.com/evandhoffman/parse-secure', 'github']);" >github</a> if anyone wants to play around with it. I&#8217;ve cronned <a href="https://github.com/evandhoffman/parse-secure/blob/master/perl/parse-secure.pl" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://github.com/evandhoffman/parse-secure/blob/master/perl/parse-secure.pl', 'parse-secure.pl']);" >parse-secure.pl</a> to run every 5 minutes so the data gets updated automatically.