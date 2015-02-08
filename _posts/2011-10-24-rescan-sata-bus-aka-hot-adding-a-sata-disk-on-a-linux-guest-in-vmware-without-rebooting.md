---
title: Rescan SATA bus (aka hot-adding a SATA disk on a Linux guest in VMware without rebooting)
author: Evan Hoffman
layout: post
permalink: /2011/10/24/rescan-sata-bus-aka-hot-adding-a-sata-disk-on-a-linux-guest-in-vmware-without-rebooting/
dsq_thread_id:
  - 2949525865
categories:
  - Uncategorized
tags:
  - bus
  - centos
  - command
  - devops
  - dmesg
  - hot-add
  - hot-plug
  - hotadd
  - hotplug
  - linux
  - rescan
  - sata
  - scsi
  - vmware
  - work
---
Linux supports hot-adding disks but whenever I add a new vdisk in VMware the new disk doesn&#8217;t show up unless I reboot, which defeats the purpose of hot-add. This command forces a rescan of the bus:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #7a0874; font-weight: bold;">echo</span> <span style="color: #ff0000;">"- - -"</span> <span style="color: #000000; font-weight: bold;">&gt;</span> <span style="color: #000000; font-weight: bold;">/</span>sys<span style="color: #000000; font-weight: bold;">/</span>class<span style="color: #000000; font-weight: bold;">/</span>scsi_host<span style="color: #000000; font-weight: bold;">/</span>host0<span style="color: #000000; font-weight: bold;">/</span>scan</pre>
      </td>
    </tr>
  </table>
</div>

<tt>dmesg</tt> shows the new disk has been found:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;">  Vendor: VMware    Model: Virtual disk      Rev: <span style="color: #000000;">1.0</span> 
  Type:   Direct-Access                      ANSI SCSI revision: 02
 target0:<span style="color: #000000;"></span>:<span style="color: #000000;">2</span>: Beginning Domain Validation
 target0:<span style="color: #000000;"></span>:<span style="color: #000000;">2</span>: Domain Validation skipping <span style="color: #c20cb9; font-weight: bold;">write</span> tests
 target0:<span style="color: #000000;"></span>:<span style="color: #000000;">2</span>: Ending Domain Validation
 target0:<span style="color: #000000;"></span>:<span style="color: #000000;">2</span>: FAST-<span style="color: #000000;">40</span> WIDE SCSI <span style="color: #000000;">80.0</span> MB<span style="color: #000000; font-weight: bold;">/</span>s ST <span style="color: #7a0874; font-weight: bold;">&#40;</span><span style="color: #000000;">25</span> ns, offset <span style="color: #000000;">127</span><span style="color: #7a0874; font-weight: bold;">&#41;</span>
SCSI device sdd: <span style="color: #000000;">1048576000</span> <span style="color: #000000;">512</span>-byte hdwr sectors <span style="color: #7a0874; font-weight: bold;">&#40;</span><span style="color: #000000;">536871</span> MB<span style="color: #7a0874; font-weight: bold;">&#41;</span>
sdd: Write Protect is off
sdd: Mode Sense: 03 00 00 00
sdd: cache data unavailable
sdd: assuming drive cache: <span style="color: #c20cb9; font-weight: bold;">write</span> through
SCSI device sdd: <span style="color: #000000;">1048576000</span> <span style="color: #000000;">512</span>-byte hdwr sectors <span style="color: #7a0874; font-weight: bold;">&#40;</span><span style="color: #000000;">536871</span> MB<span style="color: #7a0874; font-weight: bold;">&#41;</span>
sdd: Write Protect is off
sdd: Mode Sense: 03 00 00 00
sdd: cache data unavailable
sdd: assuming drive cache: <span style="color: #c20cb9; font-weight: bold;">write</span> through
 sdd: unknown partition table
sd <span style="color: #000000;"></span>:<span style="color: #000000;"></span>:<span style="color: #000000;">2</span>:<span style="color: #000000;"></span>: Attached scsi disk sdd
sd <span style="color: #000000;"></span>:<span style="color: #000000;"></span>:<span style="color: #000000;">2</span>:<span style="color: #000000;"></span>: Attached scsi generic sg3 <span style="color: #7a0874; font-weight: bold;">type</span> <span style="color: #000000;"></span></pre>
      </td>
    </tr>
  </table>
</div>

Now, why there&#8217;s no &#8220;rescan_sata&#8221; command is something I can&#8217;t fathom, but that&#8217;s Linux for you.