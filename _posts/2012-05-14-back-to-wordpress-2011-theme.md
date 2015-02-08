---
title: Back to WordPress 2011 Theme
author: Evan Hoffman
layout: post
permalink: /2012/05/14/back-to-wordpress-2011-theme/
categories:
  - Uncategorized
---
For a few months I&#8217;d been using the WooThemes &#8220;Mainstream&#8221; theme and really liked it. I made a point of always keeping it updated, as I do with everything WordPress. A few minutes ago, however, on a whim I did a 

<pre>netstat -a</pre>

on my webserver, and saw a bunch of connections from my server to setlinks.ru:http. I quickly grepped the wordpress directory for &#8220;setlinks&#8221; and sure enough it looks like some trojaned code made it in. Under my 

<pre>/wp-content/themes/mainstream/cache/</pre>

dir there are these directories:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;">drwxr-xr-x <span style="color: #000000;">3</span> apache apache <span style="color: #000000;">4096</span> Dec <span style="color: #000000;">16</span> 00:07 setlinks_fa356<span style="color: #000000; font-weight: bold;">/</span>
drwxr-xr-x <span style="color: #000000;">3</span> apache apache <span style="color: #000000;">4096</span> Dec <span style="color: #000000;">16</span> 00:08 692ad897a15978e7cfd099ace86a56bf<span style="color: #000000; font-weight: bold;">/</span>
drwxr-xr-x <span style="color: #000000;">2</span> apache apache <span style="color: #000000;">4096</span> Dec <span style="color: #000000;">16</span> 00:08 12483e2d235715e4ad4c76c8cf04f0fd76c8c397<span style="color: #000000; font-weight: bold;">/</span></pre>
      </td>
    </tr>
  </table>
</div>

Under 692ad897a15978e7cfd099ace86a56bf there are a bunch of PHP scripts, including sape.php, which has a bunch of crap in it linking to db.linkfeed.ru.

So anyway, rather than investigate fully right now I&#8217;m scrapping the WooTheme altogether. So the site is going to look more boring, but oh well.