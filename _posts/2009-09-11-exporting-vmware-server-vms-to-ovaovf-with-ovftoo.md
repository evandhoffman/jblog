---
title: Exporting VMWare Server VMs to OVA/OVF with ovftool
author: Evan Hoffman
layout: post
permalink: /2009/09/11/exporting-vmware-server-vms-to-ovaovf-with-ovftoo/
image:
  - 
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
dsq_thread_id:
  - 2949867242
categories:
  - Uncategorized
tags:
  - computers
  - ovftool
  - updated
  - vmware
---
Well, I thought this was going to be a huge pain in the ass, but it turns out not to be a really big deal. I found <a href="http://www.thoughtpolice.co.uk/vmware/howto/create-and-convert-ovf-files.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.thoughtpolice.co.uk/vmware/howto/create-and-convert-ovf-files.html', 'this post']);" >this post</a> which includes instructions and a link to download VMWare&#8217;s &#8220;ovftool&#8221;. There&#8217;s a win32 version and a 32-bit and 64-bit linux version:

<!--more-->

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="txt" style="font-family:monospace;">-rwxr--r-- 1 evan evan 19326184 Sep 11 17:16 VMware-ovftool-installer-1.0.0-166674-lin.i386.sh*
-rwxr--r-- 1 evan evan 17212108 Sep 11 17:16 VMware-ovftool-installer-1.0.0-166674-lin.x86_64.sh*
-rwxr-xr-x 1 evan evan 43833393 Sep 11 17:33 jdk-1_5_0_13-linux-amd64.bin*</pre>
      </td>
    </tr>
  </table>
</div>

I installed JDK 1.5.0_r13 because that&#8217;s what I had laying around. The 64-bit installer for ovftool ran without a hitch. I haven&#8217;t yet tried importing the .ova to my vCenter cluster (the export was from our Vmware Server (free) environment) but the conversion itself seemed fine. The one problem I encountered was that it was complaining that the cdrom was connected. The VM in question had an ISO configured as its cdrom drive, but it wasn&#8217;t connected, yet the ovftool still complained about it. I shutdown the VM, deleted the device completely, and then the conversion went fine.

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="txt" style="font-family:monospace;">[root@thardus ~]# /opt/vmware/ovftool/ovftool /nfsroot/VM/Monitoring/Monitoring.vmx /nfsroot/OVF/test.ova
Opening VMX source: /nfsroot/VM/Monitoring/Monitoring.vmx
Opening OVA target: /nfsroot/OVF/test.ova
Target: /nfsroot/OVF/test.ova
Disk Transfer Completed
Completed successfully</pre>
      </td>
    </tr>
  </table>
</div>

The whole thing took about 10-15 minutes I guess for a 20 gig VM. Actually that&#8217;s another interesting thing: the source directory with the VMDKs was ~20 gigs:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="txt" style="font-family:monospace;">[root@thardus ~]# du -sh /nfsroot/VM/Monitoring/
21G     /nfsroot/VM/Monitoring/</pre>
      </td>
    </tr>
  </table>
</div>

But the OVA that was created was only 2.5 gigs.

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="txt" style="font-family:monospace;">[root@thardus ~]# ls -lh /nfsroot/OVF/test.ova
-rw------- 1 root root 2.5G Sep 11 18:45 /nfsroot/OVF/test.ova</pre>
      </td>
    </tr>
  </table>
</div>

I assume this is due to compression and only including the parts of the VMDK that have actual data (basically thinly provisioning it). I haven&#8217;t restored yet, which would be the real test, but so far it looks like it&#8217;s a pretty easy tool that does what it&#8217;s supposed to.

**Update 2009-11-25**: With the current version of ovftool you can&#8217;t create .ova files with ovftool for VMDKs larger than 8 gb. See <a href="http://communities.vmware.com/message/1409396#1409396" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://communities.vmware.com/message/1409396#1409396', 'this post on the VMware forums']);" >this post on the VMware forums</a> for more info.