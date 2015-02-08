---
title: How (the hell) do you set up Splunk Cloud on Linux?
author: Evan Hoffman
layout: post
permalink: /2014/10/29/how-the-hell-do-you-set-up-splunk-cloud-on-linux/
categories:
  - Uncategorized
tags:
  - ec2
  - linux
  - logging
  - splunk
  - splunk cloud
---
This took me way longer than I would&#8217;ve thought, mostly due to horrible documentation. Here&#8217;s my TL;DR version:

  1. Sign up for <a href="http://www.splunk.com/getsplunk/cloudtrial" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.splunk.com/getsplunk/cloudtrial', 'Splunk Cloud']);" >Splunk Cloud</a>
  2. Download and install the forwarder binary from <a href="http://www.splunk.com/download/universalforwarder" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.splunk.com/download/universalforwarder', 'here']);" >here</a>. 
      * Log in <a href="https://www.splunk.com/page/cloudtrial_status" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://www.splunk.com/page/cloudtrial_status', 'here']);" >here</a> and note the URL of your Splunk instance: 
        <a href="http://www.evanhoffman.com/evan/wp-content/uploads/2014/10/splunk_cloud.png" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/wp-content/uploads/2014/10/splunk_cloud.png', '']);" ><img src="http://www.evanhoffman.com/evan/wp-content/uploads/2014/10/splunk_cloud.png" alt="splunk_cloud" width="506" height="278" class="aligncenter size-full wp-image-2434" /></a>  
        In the above picture, assume the URL is **https://prd-p-jxxxxxxxx.splunk6.splunktrial.com**. </li> 
        
          * Make sure your instances can connect to port tcp/9997 on your input host. Your input host is the hostname from above with &#8220;input-&#8221; prepended to it. So in our example, the input host is **input-prd-p-jxxxxxxxx.splunk6.splunktrial.com**. To ensure you can connect, try `telnet input-prd-p-jxxxxxxxx.splunk6.splunktrial.com 9997`. If it can&#8217;t connect you may need to adjust your firewall rules / Security groups to allow outbound tcp/9997</ol> 
        
        Below are the actual commands I used to get data into our Splunk Cloud trial instance:
        
        <div class="wp_syntax">
          <table>
            <tr>
              <td class="code">
                <pre class="bash" style="font-family:monospace;">$ curl <span style="color: #660033;">-O</span> http:<span style="color: #000000; font-weight: bold;">//</span>download.splunk.com<span style="color: #000000; font-weight: bold;">/</span>products<span style="color: #000000; font-weight: bold;">/</span>splunk<span style="color: #000000; font-weight: bold;">/</span>releases<span style="color: #000000; font-weight: bold;">/</span>6.2.0<span style="color: #000000; font-weight: bold;">/</span>universalforwarder<span style="color: #000000; font-weight: bold;">/</span>linux<span style="color: #000000; font-weight: bold;">/</span>splunkforwarder-6.2.0-<span style="color: #000000;">237341</span>-linux-<span style="color: #000000;">2.6</span>-amd64.deb
$ <span style="color: #c20cb9; font-weight: bold;">sudo</span> <span style="color: #c20cb9; font-weight: bold;">dpkg</span> <span style="color: #660033;">-i</span> splunkforwarder-6.2.0-<span style="color: #000000;">237341</span>-linux-<span style="color: #000000;">2.6</span>-amd64.deb
$ <span style="color: #c20cb9; font-weight: bold;">sudo</span> <span style="color: #000000; font-weight: bold;">/</span>opt<span style="color: #000000; font-weight: bold;">/</span>splunkforwarder<span style="color: #000000; font-weight: bold;">/</span>bin<span style="color: #000000; font-weight: bold;">/</span>splunk add forward-server input-prd-p-jxxxxxxxx.splunk6.splunktrial.com:<span style="color: #000000;">9997</span>
This appears to be your first <span style="color: #000000; font-weight: bold;">time</span> running this version of Splunk.
Added forwarding to: input-prd-p-jxxxxxxxx.splunk6.splunktrial.com:<span style="color: #000000;">9997</span>.
$ <span style="color: #c20cb9; font-weight: bold;">sudo</span> <span style="color: #000000; font-weight: bold;">/</span>opt<span style="color: #000000; font-weight: bold;">/</span>splunkforwarder<span style="color: #000000; font-weight: bold;">/</span>bin<span style="color: #000000; font-weight: bold;">/</span>splunk add monitor <span style="color: #ff0000;">'/var/log/postgresql/*.log'</span>
Added monitor of <span style="color: #ff0000;">'/var/log/postgresql/*.log'</span>.
$ <span style="color: #c20cb9; font-weight: bold;">sudo</span> <span style="color: #000000; font-weight: bold;">/</span>opt<span style="color: #000000; font-weight: bold;">/</span>splunkforwarder<span style="color: #000000; font-weight: bold;">/</span>bin<span style="color: #000000; font-weight: bold;">/</span>splunk list forward-server
Splunk username: admin
Password:
Active forwards:
	input-prd-p-jxxxxxxxx.splunk6.splunktrial.com:<span style="color: #000000;">9997</span>
Configured but inactive forwards:
	None
$ <span style="color: #c20cb9; font-weight: bold;">sudo</span> <span style="color: #000000; font-weight: bold;">/</span>opt<span style="color: #000000; font-weight: bold;">/</span>splunkforwarder<span style="color: #000000; font-weight: bold;">/</span>bin<span style="color: #000000; font-weight: bold;">/</span>splunk list monitor
Monitored Directories:
		<span style="color: #7a0874; font-weight: bold;">&#91;</span>No directories monitored.<span style="color: #7a0874; font-weight: bold;">&#93;</span>
Monitored Files:
	<span style="color: #000000; font-weight: bold;">/</span>var<span style="color: #000000; font-weight: bold;">/</span>log<span style="color: #000000; font-weight: bold;">/</span>postgresql<span style="color: #000000; font-weight: bold;">/*</span>.log
$ <span style="color: #c20cb9; font-weight: bold;">sudo</span> <span style="color: #000000; font-weight: bold;">/</span>opt<span style="color: #000000; font-weight: bold;">/</span>splunkforwarder<span style="color: #000000; font-weight: bold;">/</span>bin<span style="color: #000000; font-weight: bold;">/</span>splunk restart</pre>
              </td>
            </tr>
          </table>
        </div>