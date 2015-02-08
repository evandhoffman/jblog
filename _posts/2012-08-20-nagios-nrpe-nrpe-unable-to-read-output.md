---
title: 'Nagios / NRPE &#8220;NRPE: Unable to read output&#8221;'
author: Evan Hoffman
layout: post
permalink: /2012/08/20/nagios-nrpe-nrpe-unable-to-read-output/
categories:
  - Uncategorized
tags:
  - nagios
  - nrpe
  - unable to read output
  - zenoss
---
New server, copying over some existing NRPE-based checks in Zenoss, but they all error out with &#8220;NRPE: Unable to read output&#8221; despite all of the commands actually working. /etc/sudoers has the nagios user listed properly, so what&#8217;s the problem? 

The problem is this line in /etc/sudoers:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;">Defaults    requiretty</pre>
      </td>
    </tr>
  </table>
</div>

Comment that line out, restart nrpe and it works.