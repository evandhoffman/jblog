---
title: Monitoring SSL certificate expiration with ssl-cert-check
author: Evan Hoffman
layout: post
permalink: /2009/08/31/monitoring-ssl-certificate-expiration-with-ssl-cert-check/
dsq_thread_id:
  - 2950656174
categories:
  - Uncategorized
tags:
  - apache
  - certificates
  - ssl
---
I was about to write a script using OpenSSL to monitor the SSL certificate expiration dates for a few servers when it dawned on me that someone had probably done this already.

The <a href="http://prefetch.net/articles/checkcertificate.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://prefetch.net/articles/checkcertificate.html', 'ssl-cert-check']);" >ssl-cert-check</a> shell script takes a list of hosts/ports and prints out the expiration date (and how many days away that date is). It can also be configured to email a message for any certificates expiring in less than N days for easy cronibility (?). Seems lovely!

<a href="http://affiliate.godaddy.com/redirect/5F43C3ECBA841ACFC3859F4F4E6CA7DA64C271385B2D61A3AD6F3CCE83EB1DD8235E60DCD7D63BCD92E2429E79A75FAC" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://affiliate.godaddy.com/redirect/5F43C3ECBA841ACFC3859F4F4E6CA7DA64C271385B2D61A3AD6F3CCE83EB1DD8235E60DCD7D63BCD92E2429E79A75FAC', '']);" target="_blank"><img src="http://affiliate.godaddy.com/ads/5F43C3ECBA841ACFC3859F4F4E6CA7DA64C271385B2D61A3AD6F3CCE83EB1DD8235E60DCD7D63BCD92E2429E79A75FAC" border="0" width="468"  height="60" alt="Go Daddy $12.99 SSL Sale!" /></a>