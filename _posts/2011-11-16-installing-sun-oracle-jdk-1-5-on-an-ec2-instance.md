---
title: Installing Sun (Oracle) JDK 1.5 on an EC2 instance
author: Evan Hoffman
layout: post
permalink: /2011/11/16/installing-sun-oracle-jdk-1-5-on-an-ec2-instance/
dsq_thread_id:
  - 2949521508
categories:
  - Uncategorized
tags:
  - 1.5
  - alternatives
  - devops
  - ec2
  - java
  - JAVA_HOME
  - jdk
  - jvm
  - linux
  - sun
  - work
  - x86_64
---
I&#8217;m currently working on moving a Tomcat-based application into EC2. The code was written for Java 5.0. While Java 6 would probably work, I&#8217;d like to keep everything as &#8220;same&#8221; as possible, since EC2 presents its own challenges. I spun up a couple of t1.micro instances and copied everything over, including the Java 5 JDK, <tt><a href="http://www.oracle.com/technetwork/java/javasebusiness/downloads/java-archive-downloads-javase5-419410.html#jdk-1.5.0_22-oth-JPR" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.oracle.com/technetwork/java/javasebusiness/downloads/java-archive-downloads-javase5-419410.html#jdk-1.5.0_22-oth-JPR', 'jdk-1_5_0_22-linux-amd64.rpm']);" >jdk-1_5_0_22-linux-amd64.rpm</a></tt>. Installing from RPM was easy, but the EC2 instance defaults to using OpenJDK 1.6:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #7a0874; font-weight: bold;">&#91;</span>root<span style="color: #000000; font-weight: bold;">@</span>ec2 ~<span style="color: #7a0874; font-weight: bold;">&#93;</span><span style="color: #666666; font-style: italic;"># java -version</span>
java version <span style="color: #ff0000;">"1.6.0_20"</span>
OpenJDK Runtime Environment <span style="color: #7a0874; font-weight: bold;">&#40;</span>IcedTea6 1.9.10<span style="color: #7a0874; font-weight: bold;">&#41;</span> <span style="color: #7a0874; font-weight: bold;">&#40;</span>amazon-52.1.9.10.40.amzn1-x86_64<span style="color: #7a0874; font-weight: bold;">&#41;</span>
OpenJDK <span style="color: #000000;">64</span>-Bit Server VM <span style="color: #7a0874; font-weight: bold;">&#40;</span>build <span style="color: #000000;">19.0</span>-b09, mixed mode<span style="color: #7a0874; font-weight: bold;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>

There were a couple of things I had to do to get the system to accept the Sun JDK as its &#8220;real&#8221; java.

### Alternatives

Red Hat&#8217;s &#8220;alternatives&#8221; system is designed to allow a system to have multiple versions of a program installed and make it easy to choose which one you want to run. Unfortunately I&#8217;ve found the syntax a bit strange and always have to Google it, so I figured I&#8217;d document it here for posterity.

So here&#8217;s the default:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #7a0874; font-weight: bold;">&#91;</span>root<span style="color: #000000; font-weight: bold;">@</span>ec2 ~<span style="color: #7a0874; font-weight: bold;">&#93;</span><span style="color: #666666; font-style: italic;"># alternatives --config java</span>
&nbsp;
There is <span style="color: #000000;">1</span> program that provides <span style="color: #ff0000;">'java'</span>.
&nbsp;
  Selection    Command
<span style="color: #660033;">-----------------------------------------------</span>
<span style="color: #000000; font-weight: bold;">*</span>+ <span style="color: #000000;">1</span>           <span style="color: #000000; font-weight: bold;">/</span>usr<span style="color: #000000; font-weight: bold;">/</span>lib<span style="color: #000000; font-weight: bold;">/</span>jvm<span style="color: #000000; font-weight: bold;">/</span>jre-1.6.0-openjdk.x86_64<span style="color: #000000; font-weight: bold;">/</span>bin<span style="color: #000000; font-weight: bold;">/</span>java
&nbsp;
Enter to keep the current selection<span style="color: #7a0874; font-weight: bold;">&#91;</span>+<span style="color: #7a0874; font-weight: bold;">&#93;</span>, or <span style="color: #7a0874; font-weight: bold;">type</span> selection number:</pre>
      </td>
    </tr>
  </table>
</div>

Here&#8217;s how to add Sun java, assuming the java binary is in <tt>/usr/java/jdk1.5.0_22/jre/bin/java</tt> (where the RPM puts it).

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #7a0874; font-weight: bold;">&#91;</span>root<span style="color: #000000; font-weight: bold;">@</span>ec2 ~<span style="color: #7a0874; font-weight: bold;">&#93;</span><span style="color: #666666; font-style: italic;"># alternatives --install /usr/bin/java java /usr/java/jdk1.5.0_22/jre/bin/java 1</span>
<span style="color: #7a0874; font-weight: bold;">&#91;</span>root<span style="color: #000000; font-weight: bold;">@</span>ec2 ~<span style="color: #7a0874; font-weight: bold;">&#93;</span><span style="color: #666666; font-style: italic;"># alternatives --config java</span>
There are <span style="color: #000000;">2</span> programs <span style="color: #c20cb9; font-weight: bold;">which</span> provide <span style="color: #ff0000;">'java'</span>.
&nbsp;
  Selection    Command
<span style="color: #660033;">-----------------------------------------------</span>
<span style="color: #000000; font-weight: bold;">*</span>+ <span style="color: #000000;">1</span>           <span style="color: #000000; font-weight: bold;">/</span>usr<span style="color: #000000; font-weight: bold;">/</span>lib<span style="color: #000000; font-weight: bold;">/</span>jvm<span style="color: #000000; font-weight: bold;">/</span>jre-1.6.0-openjdk.x86_64<span style="color: #000000; font-weight: bold;">/</span>bin<span style="color: #000000; font-weight: bold;">/</span>java
   <span style="color: #000000;">2</span>           <span style="color: #000000; font-weight: bold;">/</span>usr<span style="color: #000000; font-weight: bold;">/</span>java<span style="color: #000000; font-weight: bold;">/</span>jdk1.5.0_22<span style="color: #000000; font-weight: bold;">/</span>jre<span style="color: #000000; font-weight: bold;">/</span>bin<span style="color: #000000; font-weight: bold;">/</span>java
&nbsp;
Enter to keep the current selection<span style="color: #7a0874; font-weight: bold;">&#91;</span>+<span style="color: #7a0874; font-weight: bold;">&#93;</span>, or <span style="color: #7a0874; font-weight: bold;">type</span> selection number: <span style="color: #000000;">2</span>
<span style="color: #7a0874; font-weight: bold;">&#91;</span>root<span style="color: #000000; font-weight: bold;">@</span>ec2 ~<span style="color: #7a0874; font-weight: bold;">&#93;</span><span style="color: #666666; font-style: italic;"># java -version</span>
java version <span style="color: #ff0000;">"1.5.0_22"</span>
Java<span style="color: #7a0874; font-weight: bold;">&#40;</span>TM<span style="color: #7a0874; font-weight: bold;">&#41;</span> <span style="color: #000000;">2</span> Runtime Environment, Standard Edition <span style="color: #7a0874; font-weight: bold;">&#40;</span>build 1.5.0_22-b03<span style="color: #7a0874; font-weight: bold;">&#41;</span>
Java HotSpot<span style="color: #7a0874; font-weight: bold;">&#40;</span>TM<span style="color: #7a0874; font-weight: bold;">&#41;</span> <span style="color: #000000;">64</span>-Bit Server VM <span style="color: #7a0874; font-weight: bold;">&#40;</span>build 1.5.0_22-b03, mixed mode<span style="color: #7a0874; font-weight: bold;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>

Yay! Unfortunately this doesn&#8217;t help with the other problem I had with Tomcat, which was that EC2 instances set the JAVA_HOME var to OpenJDK as well (<tt>/usr/lib/jvm/jre</tt>). Fortunately this is an easy fix as well.

### Setting JAVA_HOME

The JAVA_HOME var is set in <tt>/etc/profile.d/aws-apitools-common.sh</tt>. Comment out this line:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #7a0874; font-weight: bold;">export</span> <span style="color: #007800;">JAVA_HOME</span>=<span style="color: #000000; font-weight: bold;">/</span>usr<span style="color: #000000; font-weight: bold;">/</span>lib<span style="color: #000000; font-weight: bold;">/</span>jvm<span style="color: #000000; font-weight: bold;">/</span>jre</pre>
      </td>
    </tr>
  </table>
</div>

Create a new file, <tt>/etc/profile.d/sun-java.sh</tt>, and put this in it:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #7a0874; font-weight: bold;">export</span> <span style="color: #007800;">JAVA_HOME</span>=<span style="color: #000000; font-weight: bold;">/</span>usr<span style="color: #000000; font-weight: bold;">/</span>java<span style="color: #000000; font-weight: bold;">/</span>jdk1.5.0_22<span style="color: #000000; font-weight: bold;">/</span>jre</pre>
      </td>
    </tr>
  </table>
</div>

Also in that file I added the following to instruct the JVM to process all dates in America/New_York, since that&#8217;s the timezone all of our other servers use, and it makes reading log files easier when all dates are in the same tz:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #7a0874; font-weight: bold;">export</span> <span style="color: #007800;">TZ</span>=America<span style="color: #000000; font-weight: bold;">/</span>New_York</pre>
      </td>
    </tr>
  </table>
</div>

(I found I had to do this even after pointing /etc/localtime to the correct zoneinfo &#8211; Java was stuck on UTC even after the rest of the system was using America/New_York.)