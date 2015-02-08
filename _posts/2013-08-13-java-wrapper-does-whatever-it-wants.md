---
title: Java Wrapper does whatever it wants
author: Evan Hoffman
layout: post
permalink: /2013/08/13/java-wrapper-does-whatever-it-wants/
dsq_thread_id:
  - 2949521851
categories:
  - Uncategorized
tags:
  - 4096
  - 64-bit
  - activemq
  - java
  - maxheap
  - maxmemory
  - wrapper
  - wrapper.conf
---
Yesterday I set out to increase the memory settings for a bunch of our ActiveMQ machines. Most of the time these queues work pretty efficiently and their steady-state memory usage is just a few MB, but with some upcoming maintenance we expect these queues to get considerably fuller, so I was growing them preemptively. Anyway, our ActiveMQ installation gets bootstrapped using <a href="http://activemq.apache.org/java-service-wrapper.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://activemq.apache.org/java-service-wrapper.html', 'Java Service Wrapper']);" >Java Service Wrapper</a> and the JVM settings for ActiveMQ are managed in some wrapper.conf file. What I wanted to do was pretty straightforward: I increased the VM&#8217;s &#8216;physical&#8217; memory to 16 GB and intended to increase Java&#8217;s MaxHeap from its current setting (1536MB aka 1.5GB) to 8192MB (8 GB). In wrapper.conf, I found the &#8220;wrapper.java.maxmemory&#8221; setting (which was set to 1536), set it to 8192, and restarted ActiveMQ. Here&#8217;s where it got weird.

To verify that I had changed the setting correctly, I checked ps -ef and looked at the process args for Java. It showed that Java was started with -Xmx4096m. Huh? I double-checked wrapper.conf and sure enough I had correctly set it to 8192. My first thought was that maybe I wasn&#8217;t running a 64-bit VM, but I confirmed that it&#8217;s a 64-bit Sun JVM (the identical version we use on other machines). Just to be completely sure that this was the actual JVM being used by ActiveMQ, I connected via JMX and saw that the running JVM was indeed 1.6.0_14 amd64. I restarted ActiveMQ a few more times and each time it came up with a ceiling of 4096MB. Values below 4096 were set properly.

As a workaround, I tried setting the config like so:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="config" style="font-family:monospace;">wrapper.java.additional.13=-Xmx8192m
wrapper.java.maxmemory=0</pre>
      </td>
    </tr>
  </table>
</div>

When I attempted to restart ActiveMQ, it looked like it worked, but the process wasn&#8217;t running. In the wrapper.log file I saw this:

<pre>ERROR  | wrapper  | 2013/08/12 14:28:10 | JVM exited while loading the application.
INFO   | jvm 4    | 2013/08/12 14:28:10 | Invalid maximum heap size: -Xmx=8192m
INFO   | jvm 4    | 2013/08/12 14:28:10 | Could not create the Java virtual machine.
STATUS | wrapper  | 2013/08/12 14:28:14 | Launching a JVM...
ERROR  | wrapper  | 2013/08/12 14:28:14 | JVM exited while loading the application.
INFO   | jvm 5    | 2013/08/12 14:28:14 | Invalid maximum heap size: -Xmx=8192m
INFO   | jvm 5    | 2013/08/12 14:28:14 | Could not create the Java virtual machine.
FATAL  | wrapper  | 2013/08/12 14:28:14 | There were 5 failed launches in a row, each lasting less than 300 seconds.  Giving up.
FATAL  | wrapper  | 2013/08/12 14:28:14 |   There may be a configuration problem: please check the logs.
STATUS | wrapper  | 2013/08/12 14:28:15 | &lt;-- Wrapper Stopped
</pre>

At this point I confirmed that we were using the 64-bit version of wrapper (it ships with both 32 and 64-bit) and later we even renamed the 32-bit version to ensure it wasn't being used, but nothing seemed to work. What we found that *appeared* to work was appending the -Xmx to another argument, like so:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;">wrapper.java.additional.9=-Djava.util.logging.config.file=logging.properties -XX:<span style="color: #007800;">PermSize</span>=256m <span style="color: #660033;">-Xms8192m</span> <span style="color: #660033;">-Xmx8192m</span>
wrapper.java.maxmemory=<span style="color: #000000;"></span></pre>
      </td>
    </tr>
  </table>
</div>

As I said, this *appeared* to work. The -Xmx8192m shows up in ps, but when connecting through jvisualvm it shows the Max heap size as 3,685,416,960B:  
<a href="http://www.evanhoffman.com/evan/2013/08/13/java-wrapper-does-whatever-it-wants/jvv/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/2013/08/13/java-wrapper-does-whatever-it-wants/jvv/', '']);"  rel="attachment wp-att-2217"><img src="http://www.evanhoffman.com/evan/wp-content/uploads/2013/08/jvv.png" alt="jvv" width="873" height="530" class="aligncenter size-full wp-image-2217" /></a>  
So, I don't have any happy ending to this story right now. Maybe I'll try not using Wrapper at all. But this is pretty weird so I figured I'd waste some internet real estate talking about it.