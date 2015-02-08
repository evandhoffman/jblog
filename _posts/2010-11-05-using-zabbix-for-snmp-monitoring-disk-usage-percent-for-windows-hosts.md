---
title: Using Zabbix for SNMP monitoring disk usage percent for Windows hosts
author: Evan Hoffman
layout: post
permalink: /2010/11/05/using-zabbix-for-snmp-monitoring-disk-usage-percent-for-windows-hosts/
dsq_thread_id:
  - 2949493030
categories:
  - Uncategorized
tags:
  - dskPercent
  - linux
  - monitoring
  - snmp
  - windows
  - work
  - zabbix
---
A few years ago we moved from Nagios to Zabbix for our server monitoring needs. I wasn&#8217;t a big fan of Nagios, finding it a pain to manage with its myriad configuration files. It&#8217;s probably gotten better since I last toyed with it but since we moved to Zabbix I haven&#8217;t had much reason to look at Nagios again.  
<!--more-->

I also try to use SNMP monitoring for everything. SNMP is widely supported &#8211; all sorts of hardware has SNMP support, and with the net-snmp package you can pretty easily create your own SNMP-monitorable stuff on Linux. Since almost all of our stuff runs on Linux this has worked out pretty well, but our Exchange server is *probably* going to be running on Windows for the foreseeable future. Windows has SNMP support, it&#8217;s just not on by default. However, even when it&#8217;s enabled it doesn&#8217;t have the simple &#8220;dskPercent&#8221; monitoring I&#8217;ve come to know and love with net-snmp on Linux, which simply tells you how full a given disk is as a percent. This makes it easy to set alerts when a disk reaches 80% full.

On Windows I found these objects that can be used to get something similar:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #7a0874; font-weight: bold;">&#91;</span>evan<span style="color: #000000; font-weight: bold;">@</span>monitoring02 <span style="color: #000000;">14</span>:<span style="color: #000000;">41</span>:<span style="color: #000000;">24</span> ~<span style="color: #7a0874; font-weight: bold;">&#93;</span>$ snmpwalk <span style="color: #660033;">-v</span> 2c <span style="color: #660033;">-c</span> community 192.168.1.20 <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #c20cb9; font-weight: bold;">grep</span> <span style="color: #660033;">-i</span> storage
HOST-RESOURCES-MIB::hrStorageIndex.1 = INTEGER: <span style="color: #000000;">1</span>
HOST-RESOURCES-MIB::hrStorageIndex.2 = INTEGER: <span style="color: #000000;">2</span>
HOST-RESOURCES-MIB::hrStorageIndex.3 = INTEGER: <span style="color: #000000;">3</span>
HOST-RESOURCES-MIB::hrStorageIndex.4 = INTEGER: <span style="color: #000000;">4</span>
HOST-RESOURCES-MIB::hrStorageIndex.5 = INTEGER: <span style="color: #000000;">5</span>
HOST-RESOURCES-MIB::hrStorageIndex.6 = INTEGER: <span style="color: #000000;">6</span>
HOST-RESOURCES-MIB::hrStorageType.1 = OID: HOST-RESOURCES-TYPES::hrStorageRemovableDisk
HOST-RESOURCES-MIB::hrStorageType.2 = OID: HOST-RESOURCES-TYPES::hrStorageFixedDisk
HOST-RESOURCES-MIB::hrStorageType.3 = OID: HOST-RESOURCES-TYPES::hrStorageCompactDisc
HOST-RESOURCES-MIB::hrStorageType.4 = OID: HOST-RESOURCES-TYPES::hrStorageFixedDisk
HOST-RESOURCES-MIB::hrStorageType.5 = OID: HOST-RESOURCES-TYPES::hrStorageVirtualMemory
HOST-RESOURCES-MIB::hrStorageType.6 = OID: HOST-RESOURCES-TYPES::hrStorageRam
HOST-RESOURCES-MIB::hrStorageDescr.1 = STRING: A:\
HOST-RESOURCES-MIB::hrStorageDescr.2 = STRING: C:\ Label:  Serial Number b78d19
HOST-RESOURCES-MIB::hrStorageDescr.3 = STRING: D:\ Label:EXCH201064  Serial Number xxxxxxxx
HOST-RESOURCES-MIB::hrStorageDescr.4 = STRING: E:\ Label:Exchange2010  Serial Number xxxxxxxx
HOST-RESOURCES-MIB::hrStorageDescr.5 = STRING: Virtual Memory
HOST-RESOURCES-MIB::hrStorageDescr.6 = STRING: Physical Memory
HOST-RESOURCES-MIB::hrStorageAllocationUnits.1 = INTEGER: <span style="color: #000000;"></span> Bytes
HOST-RESOURCES-MIB::hrStorageAllocationUnits.2 = INTEGER: <span style="color: #000000;">4096</span> Bytes
HOST-RESOURCES-MIB::hrStorageAllocationUnits.3 = INTEGER: <span style="color: #000000;">2048</span> Bytes
HOST-RESOURCES-MIB::hrStorageAllocationUnits.4 = INTEGER: <span style="color: #000000;">4096</span> Bytes
HOST-RESOURCES-MIB::hrStorageAllocationUnits.5 = INTEGER: <span style="color: #000000;">65536</span> Bytes
HOST-RESOURCES-MIB::hrStorageAllocationUnits.6 = INTEGER: <span style="color: #000000;">65536</span> Bytes
HOST-RESOURCES-MIB::hrStorageSize.1 = INTEGER: <span style="color: #000000;"></span>
HOST-RESOURCES-MIB::hrStorageSize.2 = INTEGER: <span style="color: #000000;">10459647</span>
HOST-RESOURCES-MIB::hrStorageSize.3 = INTEGER: <span style="color: #000000;">546570</span>
HOST-RESOURCES-MIB::hrStorageSize.4 = INTEGER: <span style="color: #000000;">104824319</span>
HOST-RESOURCES-MIB::hrStorageSize.5 = INTEGER: <span style="color: #000000;">393172</span>
HOST-RESOURCES-MIB::hrStorageSize.6 = INTEGER: <span style="color: #000000;">196600</span>
HOST-RESOURCES-MIB::hrStorageUsed.1 = INTEGER: <span style="color: #000000;"></span>
HOST-RESOURCES-MIB::hrStorageUsed.2 = INTEGER: <span style="color: #000000;">5885720</span>
HOST-RESOURCES-MIB::hrStorageUsed.3 = INTEGER: <span style="color: #000000;">546570</span>
HOST-RESOURCES-MIB::hrStorageUsed.4 = INTEGER: <span style="color: #000000;">44650892</span>
HOST-RESOURCES-MIB::hrStorageUsed.5 = INTEGER: <span style="color: #000000;">166057</span>
HOST-RESOURCES-MIB::hrStorageUsed.6 = INTEGER: <span style="color: #000000;">152902</span></pre>
      </td>
    </tr>
  </table>
</div>

I thought initially that the hrStorageUsed and hrStorageSize values were being reported in bytes, but according to <a href="http://msdn.microsoft.com/en-us/library/ms926911.aspx" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://msdn.microsoft.com/en-us/library/ms926911.aspx', 'this MSDN article,']);" >this MSDN article,</a> the units are reported in &#8220;allocation units,&#8221; which I assume are being reported under hrStorageAllocationUnits, so you just need to multiply the values by the allocation units.

In Zabbix, I check hrStorageUsed every 15 minutes as &#8220;disk\_1\_used&#8221;. I check hrStorageSize every 2 hours (since the actual size of the disk/partition isn&#8217;t likely to change that often) as &#8220;disk\_1\_size&#8221;. To calculate the percentage, I created a &#8220;Calculated&#8221; item with this formula:

`100*(last("disk_1_used") / last("disk_1_size"))`<figure id="attachment_912" style="width: 569px;" class="wp-caption aligncenter">

<a href="http://evanhoffman.com/evan/wp-content/uploads/2010/11/Fullscreen-capture-1152010-92032-AM.jpg" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://evanhoffman.com/evan/wp-content/uploads/2010/11/Fullscreen-capture-1152010-92032-AM.jpg', '']);" ><img src="http://evanhoffman.com/evan/wp-content/uploads/2010/11/Fullscreen-capture-1152010-92032-AM.jpg" alt="Windows disk usage percent" title="Windows disk usage percent" width="569" height="433" class="size-full wp-image-912" /></a><figcaption class="wp-caption-text">Windows disk usage percent</figcaption></figure> 

The values for disk\_1\_used and disk\_1\_size are in Storage Allocation Units, but since this is a percentage that doesn&#8217;t matter. However, I do also like to get an idea of the actual disk space being consumed; luckily this is also relatively easy to obtain in Zabbix using Calculated items. I monitor hrStorageAllocationUnits as &#8220;disk\_1\_allocunit&#8221; (every 7200 seconds since this too is unlikely to change much) and then the formula for the calculated used disk space is simply:

`last("disk_1_used") * last("disk_1_allocunit")`<figure id="attachment_914" style="width: 581px;" class="wp-caption aligncenter">

<a href="http://evanhoffman.com/evan/wp-content/uploads/2010/11/Fullscreen-capture-1152010-92011-AM.jpg" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://evanhoffman.com/evan/wp-content/uploads/2010/11/Fullscreen-capture-1152010-92011-AM.jpg', '']);" ><img src="http://evanhoffman.com/evan/wp-content/uploads/2010/11/Fullscreen-capture-1152010-92011-AM.jpg" alt="Windows disk used (bytes)" title="Windows disk used (bytes)" width="581" height="427" class="size-full wp-image-914" /></a><figcaption class="wp-caption-text">Windows disk used (bytes)</figcaption></figure> 

Once all the work is done, here&#8217;s what the result looks like:<figure id="attachment_916" style="width: 300px;" class="wp-caption aligncenter">

<a href="http://evanhoffman.com/evan/wp-content/uploads/2010/11/Fullscreen-capture-1152010-92648-AM.jpg" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://evanhoffman.com/evan/wp-content/uploads/2010/11/Fullscreen-capture-1152010-92648-AM.jpg', '']);" ><img src="http://www.evanhoffman.com/evan/wp-content/uploads/2010/11/Fullscreen-capture-1152010-92648-AM-300x48.jpg" alt="Zabbix SNMP monitored Windows disk items" title="Zabbix SNMP monitored Windows disk items" width="300" height="48" class="size-medium wp-image-916" /></a><figcaption class="wp-caption-text">Zabbix SNMP monitored Windows disk items</figcaption></figure> 

When I log in to the actual machine (my vCenter VM in this case) and check disk usage, the numbers match what Zabbix&#8217;s calculated values show, though Zabbix seems to be reporting values in &#8220;mebibytes&#8221; rather than &#8220;megabytes&#8221;:  
<figure id="attachment_920" style="width: 369px;" class="wp-caption aligncenter"><a href="http://evanhoffman.com/evan/wp-content/uploads/2010/11/vcenter-disk.png" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://evanhoffman.com/evan/wp-content/uploads/2010/11/vcenter-disk.png', '']);" ><img src="http://evanhoffman.com/evan/wp-content/uploads/2010/11/vcenter-disk.png" alt="Actual Windows disk usage" title="Actual Windows disk usage" width="369" height="494" class="size-full wp-image-920" /></a><figcaption class="wp-caption-text">Actual Windows disk usage</figcaption></figure>

I created a template in Zabbix which monitors these data for disks 1-5 and then applied it to all Windows servers; now I just need to apply some alert triggers and mission accomplished.

**Edit 2011-01-14**: here&#8217;s the template exported from Zabbix: <a href="http://www.evanhoffman.com/evan/wp-content/uploads/2010/11/zbx_template_windows_disk.xml_.gz" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/wp-content/uploads/2010/11/zbx_template_windows_disk.xml_.gz', 'Zabbix Windows disk usage template']);" >Zabbix Windows disk usage template</a>