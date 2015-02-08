---
title: Calculating SHA-1 sums in Java
author: Evan Hoffman
excerpt: Java code for calculating SHA-1 sums from arbitrary InputStreams, files, or Strings and printing them as strings.
layout: post
permalink: /2011/05/25/calculating-sha-1-sums-in-java/
categories:
  - Uncategorized
tags:
  - byte array
  - code
  - devops
  - git
  - hex string
  - java
  - linux
  - sha-1
---
Java has functionality for calculating SHA-1 and MD5 checksums but not for displaying them as the 40-char hex strings I&#8217;m used to. I want to update my photo reorganizer so it doesn&#8217;t create duplicate files so I figured this would be a handy thing to write. I wrote one for MD5 a while ago (the only code you really need to write is the <a href="https://github.com/evandhoffman/Java-SHA-1-Hasher/blob/0e7fc9f3013a20eeecc30b523d1400c0ad5db41d/src/com/evanhoffman/messagedigest/SHA1.java#L31" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://github.com/evandhoffman/Java-SHA-1-Hasher/blob/0e7fc9f3013a20eeecc30b523d1400c0ad5db41d/src/com/evanhoffman/messagedigest/SHA1.java#L31', 'byte-array-to-hex-string converter']);" >byte-array-to-hex-string converter</a>) but I guess I lost it. Anyway, here it is.

<ins datetime="2011-05-26T14:24:13+00:00">Edit:</ins> Rather than paste a huge useless block of code here, I put it on github: <a href="https://github.com/evandhoffman/Java-SHA-1-Hasher" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://github.com/evandhoffman/Java-SHA-1-Hasher', 'https://github.com/evandhoffman/Java-SHA-1-Hasher']);" >https://github.com/evandhoffman/Java-SHA-1-Hasher</a>.

Output appears to match that of the command-line utility:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #7a0874; font-weight: bold;">&#91;</span>evan<span style="color: #000000; font-weight: bold;">@</span>EvanMBP bin<span style="color: #7a0874; font-weight: bold;">&#93;</span>$ java <span style="color: #660033;">-jar</span> SHA1.jar ~<span style="color: #000000; font-weight: bold;">/</span>text.txt 
3bbed58697cc1775e8fadcf1324e26d4d092e7c2        <span style="color: #000000; font-weight: bold;">/</span>Users<span style="color: #000000; font-weight: bold;">/</span>evan<span style="color: #000000; font-weight: bold;">/</span>text.txt
<span style="color: #7a0874; font-weight: bold;">&#91;</span>evan<span style="color: #000000; font-weight: bold;">@</span>EvanMBP bin<span style="color: #7a0874; font-weight: bold;">&#93;</span>$ shasum ~<span style="color: #000000; font-weight: bold;">/</span>text.txt 
3bbed58697cc1775e8fadcf1324e26d4d092e7c2  <span style="color: #000000; font-weight: bold;">/</span>Users<span style="color: #000000; font-weight: bold;">/</span>evan<span style="color: #000000; font-weight: bold;">/</span>text.txt</pre>
      </td>
    </tr>
  </table>
</div>

It&#8217;s not as fast as shasum (as I expected) but that may partially be due to reading data in 1k chunks.