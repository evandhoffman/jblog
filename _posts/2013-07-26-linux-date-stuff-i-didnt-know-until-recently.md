---
title: 'Linux &#8216;date&#8217; stuff I didn&#8217;t know until recently.'
author: Evan Hoffman
layout: post
permalink: /2013/07/26/linux-date-stuff-i-didnt-know-until-recently/
categories:
  - Uncategorized
tags:
  - bash
  - linux
---
I&#8217;ve been using Linux for a long time but I had no idea you could do this.

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #666666;">evan@evbox:~$ </span><span style="color: #c20cb9; font-weight: bold;">date</span> <span style="color: #660033;">-d</span> yesterday
Thu Jul <span style="color: #000000;">25</span> <span style="color: #000000;">12</span>:<span style="color: #000000;">44</span>:<span style="color: #000000;">39</span> EDT <span style="color: #000000;">2013</span>
<span style="color: #666666;">evan@evbox:~$ </span><span style="color: #c20cb9; font-weight: bold;">date</span> <span style="color: #660033;">-d</span> <span style="color: #ff0000;">'42 days ago'</span>
Fri Jun <span style="color: #000000;">14</span> <span style="color: #000000;">12</span>:<span style="color: #000000;">44</span>:<span style="color: #000000;">51</span> EDT <span style="color: #000000;">2013</span>
<span style="color: #666666;">evan@evbox:~$ </span><span style="color: #c20cb9; font-weight: bold;">date</span> <span style="color: #660033;">-d</span> <span style="color: #ff0000;">'65 minutes ago'</span>
Fri Jul <span style="color: #000000;">26</span> <span style="color: #000000;">11</span>:<span style="color: #000000;">41</span>:<span style="color: #000000;">18</span> EDT <span style="color: #000000;">2013</span></pre>
      </td>
    </tr>
  </table>
</div>

As epoch:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #666666;">evan@evbox:~$ </span><span style="color: #c20cb9; font-weight: bold;">date</span> <span style="color: #660033;">-d</span> <span style="color: #ff0000;">'65 minutes ago'</span> +<span style="color: #000000; font-weight: bold;">%</span>s
<span style="color: #000000;">1374853307</span></pre>
      </td>
    </tr>
  </table>
</div>

Amazing.