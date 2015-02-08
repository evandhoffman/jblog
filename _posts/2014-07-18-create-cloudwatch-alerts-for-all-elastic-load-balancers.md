---
title: Create CloudWatch alerts for all Elastic Load Balancers
author: Evan Hoffman
layout: post
permalink: /2014/07/18/create-cloudwatch-alerts-for-all-elastic-load-balancers/
dsq_thread_id:
  - 3023785549
categories:
  - Uncategorized
tags:
  - amazon
  - aws
  - aws-cli
  - cloudwatch
  - devops
  - elb
  - linux
  - sysadmin
---
I manage a bunch of ELBs but we were missing an alert on a pretty basic metric: how many errors the load balancer was returning.  Rather than wade through the UI to add these alerts I figured it would be easier to do it via the CLI.

Assuming aws-cli is installed and the ARN for your SNS topic (in my case, just an email alert) is $arn:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #000000; font-weight: bold;">for</span> i <span style="color: #000000; font-weight: bold;">in</span> <span style="color: #000000; font-weight: bold;">`</span>aws elb describe-load-balancers <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #c20cb9; font-weight: bold;">grep</span> LoadBalancerName <span style="color: #000000; font-weight: bold;">|</span> \
<span style="color: #c20cb9; font-weight: bold;">perl</span> <span style="color: #660033;">-ne</span> <span style="color: #ff0000;">'chomp; my @a=split(/\s+/); $a[2] =~ s/[\"\,]//g ; print "$a[2] ";'</span> <span style="color: #000000; font-weight: bold;">`</span> ; \
<span style="color: #000000; font-weight: bold;">do</span> aws cloudwatch put-metric-alarm <span style="color: #660033;">--alarm-name</span> <span style="color: #ff0000;">"<span style="color: #007800;">$i</span> ELB 5XX Errors"</span> <span style="color: #660033;">--alarm-description</span> \
<span style="color: #ff0000;">"High <span style="color: #007800;">$i</span> ELB 5XX error count"</span> <span style="color: #660033;">--metric-name</span> HTTPCode_ELB_5XX <span style="color: #660033;">--namespace</span> AWS<span style="color: #000000; font-weight: bold;">/</span>ELB \
<span style="color: #660033;">--statistic</span> Sum <span style="color: #660033;">--period</span> <span style="color: #000000;">300</span> <span style="color: #660033;">--evaluation-periods</span> <span style="color: #000000;">1</span> <span style="color: #660033;">--threshold</span> <span style="color: #000000;">50</span> \
<span style="color: #660033;">--comparison-operator</span> GreaterThanThreshold <span style="color: #660033;">--dimensions</span> <span style="color: #007800;">Name</span>=LoadBalancerName,<span style="color: #007800;">Value</span>=<span style="color: #007800;">$i</span> \
<span style="color: #660033;">--alarm-actions</span> <span style="color: #007800;">$arn</span> <span style="color: #660033;">--ok-actions</span> <span style="color: #007800;">$arn</span> ; <span style="color: #000000; font-weight: bold;">done</span></pre>
      </td>
    </tr>
  </table>
</div>

That huge one-liner creates a CloudWatch notification that sends an alarm when the number of 5XX errors returned by the ELB is greater than 50 over 5 minutes, and sends an &#8220;ok&#8221; message via the same SNS topic. The for loop creates/modifies the alarm for every ELB.

More info on put-metric-alarm available in the <a href="http://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-alarm.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-alarm.html', 'AWS docs']);" >AWS docs</a>.