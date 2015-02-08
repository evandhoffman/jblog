---
title: Blocking comment spammers by IP
author: Evan Hoffman
layout: post
permalink: /2010/08/13/blocking-comment-spammers-by-ip/
dsq_thread_id:
  - 3055650437
categories:
  - Uncategorized
tags:
  - allow
  - apache
  - block
  - deny
  - iptables
  - linux
  - perl
  - regex
  - spam
---
I use <a href="http://akismet.com/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://akismet.com/', 'Akismet']);" >Akismet</a> to block comment spam, but it still annoys me that it even exists. Last night I put a simple IP ban into my httpd config. But who to block?

I used a grep & Perl to get a rough guess of which IPs were submitting the most comments (working on the assumption that one IP address submits many spam comments) It took me about 20 minutes to write this mess but it does what I wanted to do:  
`<br />
[root@lunix ~]# zgrep  POST /var/log/httpd/evanhoffman-access_log-201008??.gz  | grep comment | perl -ne 'chomp; $_ =~ m/(?:\d{1,3}\.){3}\d{1,3}/; print "$&\n";' | perl -e '%a = (); while (<>) { chomp;  $a{$_} += 1; } while (my ($key, $value) = each (%a)) { if ($value > 1) { print "$value\t=>\t$key\n";}}'<br />
2       =>      218.6.9.140<br />
180     =>      91.201.66.34<br />
2       =>      213.5.67.41<br />
2       =>      188.187.102.74<br />
[root@lunix ~]#<br />
`

That&#8217;s pretty hard to read. Here&#8217;s a quick explanation of each piece:

> zgrep POST /var/log/httpd/evanhoffman-access_log-201008??.gz

Use zgrep to search for the string &#8220;POST&#8221; in all of the gzipped Apache logs for August. Pipe the results (the matching lines) to the next part:

> grep comment

grep for the string &#8220;comment&#8221;. This isn&#8217;t really scientific, but I feel safe in assuming that if &#8220;POST&#8221; and &#8220;comment&#8221; both appear in the HTTP request, it&#8217;s probably someone posting a comment. Pipe the matches to&#8230;

> perl -ne &#8216;chomp; $_ =~ m/(?:\d{1,3}\.){3}\d{1,3}/; print &#8220;$&\n&#8221;;&#8217;

This is a perl one-liner that uses a regular expression to match an IP address in a given line and print it out. The original regex I used was `\d+\.\d+\.\d+\.\d+`, this one was slightly fancier but did the same work in this case. It&#8217;s worth noting that this will only print out the first match in the given line, but since the requester&#8217;s IP (REMOTE_ADDR) is the first field in <a href="http://httpd.apache.org/docs/2.0/logs.html#accesslog" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://httpd.apache.org/docs/2.0/logs.html#accesslog', 'Combined Log Format']);" >Combined Log Format</a>, that&#8217;s fine this case.

The output (the IPs from which comment posts have been made) is piped to&#8230;

> perl -e &#8216;%a = (); while (<>) { chomp; $a{$_} += 1; } while (my ($key, $value) = each (%a)) { if ($value > 1) { print &#8220;$value\t=>\t$key\n&#8221;;}}&#8217;

This is another perl one-liner. Basically, it maintains a hash of String=>count pairs, so each time it sees a string it increments a &#8220;counter&#8221; for that line. Then when it&#8217;s done receiving input (i.e. all the data has been processed) it prints out the contents of the hash for keys that have a value > 1 (i.e. IPs that have POSTed more than 1 comment).

The output shows pretty clearly where the spam is coming from:

> 2 => 218.6.9.140  
> 180 => 91.201.66.34  
> 2 => 213.5.67.41  
> 2 => 188.187.102.74

180 submits from 91.201.66.34. Out of curiosity I looked up that IP in whois:

<pre>[root@lunix ~]# whois 91.201.66.34
[Querying whois.ripe.net]
[whois.ripe.net]
% This is the RIPE Database query service.
% The objects are in RPSL format.
%
% The RIPE Database is subject to Terms and Conditions.
% See http://www.ripe.net/db/support/db-terms-conditions.pdf

% Note: This output has been filtered.
%       To receive output for a database update, use the "-B" flag.

% Information related to '91.201.64.0 - 91.201.67.255'

inetnum:        91.201.64.0 - 91.201.67.255
netname:        Donekoserv
descr:          DonEkoService Ltd
country:        RU
org:            ORG-DS41-RIPE
admin-c:        MNV32-RIPE
tech-c:         MNV32-RIPE
status:         ASSIGNED PI
mnt-by:         RIPE-NCC-END-MNT
mnt-by:         MNT-DONECO
mnt-by:         MNT-DONECO
mnt-lower:      RIPE-NCC-END-MNT
mnt-routes:     MHOST-MNT
mnt-routes:     MNT-PIN
mnt-domains:    MHOST-MNT
source:         RIPE # Filtered

organisation:   ORG-DS41-RIPE
org-name:       DonEko Service
org-type:       OTHER
address:        novocherkassk, ul stremyannaya d.6
e-mail:         admin@pinspb.ru
mnt-ref:        MNT-PIN
mnt-by:         MNT-PIN
source:         RIPE # Filtered

person:         Metluk Nikolay Valeryevich
address:        korp. 1a 40 Slavy ave.,
address:        St.-Petersburg, Russia
e-mail:         nm@internet-spb.ru
phone:          +7 812 4483863
fax-no:         +7 901 3149449
nic-hdl:        MNV32-RIPE
mnt-by:         MNT-PIN
source:         RIPE # Filtered

% Information related to '91.201.66.0/23AS21098'

route:          91.201.66.0/23
descr:          Route MHOST IDC
origin:         AS21098
mnt-by:         MHOST-MNT
source:         RIPE # Filtered

[root@lunix ~]#
</pre>

Not much info other than the IP is based in Russia. Well, anyway, I IP blocked 91.0.0.0/8 (sorry, Russia), so if you&#8217;re in that subnet you&#8217;re probably seeing a 403 now.

**Edit**: It occurred to me that I can accomplish the same thing while being less draconian if I wrap the Deny in a <Limit></Limit> clause. This way everyone can still see the site but certain IP ranges won&#8217;t be able to POST anything:

<pre>&lt;Limit POST PUT DELETE&gt;
Order Allow,Deny
Allow from all
Deny from 218.6.9.
Deny from 173.203.101.
Deny from 122.162.28.
Deny from 91.
Deny from 213.5
&lt;/Limit&gt;
</pre>