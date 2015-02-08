---
title: '&#8220;Call HostServiceSystem.Start for object XX on vCenter Server YY failed&#8221; when starting snmpd on ESXi 5.1'
author: Evan Hoffman
layout: post
permalink: /2013/08/05/call-hostservicesystem-start-for-object-xx-on-vcenter-server-yy-failed-when-starting-snmpd-on-esxi-5-1/
dsq_thread_id:
  - 2949522190
categories:
  - Uncategorized
tags:
  - 5.1
  - esx
  - esxcli
  - esxi
  - hostservicesystem
  - snmpd
  - vmware
---
SSH into the host and run these:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;">~ <span style="color: #666666; font-style: italic;"># esxcli system snmp set --communities &lt;community name&gt;</span>
~ <span style="color: #666666; font-style: italic;"># esxcli system snmp set --enable true</span></pre>
      </td>
    </tr>
  </table>
</div>

After that, make sure you have the port (161/udp) open in the firewall config and it should start up ok. (The root problem is the file /etc/vmware/snmp.xml &#8211; those commands fix it).