---
title: Use PowerShell to disconnect the CDROMs from all VMs in vCenter
author: Evan Hoffman
layout: post
permalink: /2013/04/20/use-powershell-to-disconnect-the-cdroms-from-all-vms-in-vcenter/
amazon-product-excerpt-hook-override:
  - 3
amazon-product-content-hook-override:
  - 2
amazon-product-newwindow:
  - 3
categories:
  - Uncategorized
tags:
  - bulk
  - cdrom
  - cli
  - powershell
  - scripting
  - vmware
  - vsphere
---
Recently I was moving all VMs from one NFS datastore to another so we could destroy the old volume. Storage vMotion took care of this for the most part, but even after moving the files, vCenter still showed that the VMs were using the old datastore. It turned out this was due to the VMs having mounted ISOs on that datastore. The solution was to eject/unmount the CDrom, but I didn&#8217;t want to do &#8220;Edit Settings&#8230;&#8221; and manually remove the CDrom for 200+ VMs. 

I found <a href="http://babblecode.blogspot.com/2013/02/batch-disconnect-all-connected-vm-cdrom.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://babblecode.blogspot.com/2013/02/batch-disconnect-all-connected-vm-cdrom.html', 'this page']);" >this page</a> which shows how to do almost exactly what I wanted via PowerShell. We&#8217;re using the vCenter Server Appliance though, which runs Linux, so I wasn&#8217;t sure how to run PowerShell stuff. But that was solved easily enough by installing <a href="https://my.vmware.com/group/vmware/get-download?downloadGroup=VSPS510-PCLI-510R2" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://my.vmware.com/group/vmware/get-download?downloadGroup=VSPS510-PCLI-510R2', 'VMWare&#8217;s PowerCLI package']);" >VMWare&#8217;s PowerCLI package</a> on my Windows desktop and connecting to the vCenter remotely:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;">PS C:\<span style="color: #c20cb9; font-weight: bold;">users</span>\evan\documents<span style="color: #000000; font-weight: bold;">&gt;</span> Connect-VIServer vcenter.example.com
&nbsp;
PS C:\<span style="color: #c20cb9; font-weight: bold;">users</span>\evan\documents<span style="color: #000000; font-weight: bold;">&gt;</span> Get-Datacenter <span style="color: #660033;">-name</span> <span style="color: #ff0000;">"Primary Datacenter"</span> <span style="color: #000000; font-weight: bold;">|</span> Get-VM <span style="color: #000000; font-weight: bold;">|</span> Get-CDDrive <span style="color: #000000; font-weight: bold;">|</span> Set-CDDrive <span style="color: #660033;">-Connected</span> <span style="color: #007800;">$false</span> <span style="color: #660033;">-NoMedia</span>
&nbsp;
&nbsp;
Confirm
Are you sure you want to perform this action?
Performing operation <span style="color: #ff0000;">"Setting Connected: False, NoMedia: True."</span> on Target <span style="color: #ff0000;">"CD/DVD drive 1"</span>.
<span style="color: #7a0874; font-weight: bold;">&#91;</span>Y<span style="color: #7a0874; font-weight: bold;">&#93;</span> Yes  <span style="color: #7a0874; font-weight: bold;">&#91;</span>A<span style="color: #7a0874; font-weight: bold;">&#93;</span> Yes to All  <span style="color: #7a0874; font-weight: bold;">&#91;</span>N<span style="color: #7a0874; font-weight: bold;">&#93;</span> No  <span style="color: #7a0874; font-weight: bold;">&#91;</span>L<span style="color: #7a0874; font-weight: bold;">&#93;</span> No to All  <span style="color: #7a0874; font-weight: bold;">&#91;</span>S<span style="color: #7a0874; font-weight: bold;">&#93;</span> Suspend  <span style="color: #7a0874; font-weight: bold;">&#91;</span>?<span style="color: #7a0874; font-weight: bold;">&#93;</span> Help <span style="color: #7a0874; font-weight: bold;">&#40;</span>default is <span style="color: #ff0000;">"Y"</span><span style="color: #7a0874; font-weight: bold;">&#41;</span>: y</pre>
      </td>
    </tr>
  </table>
</div>

Connect-VIServer pops up a username/password box where you put in your creds. The next command disconnects the cdrom for every VM in the &#8220;Primary Datacenter&#8221; datacenter in vCenter. I haven&#8217;t tried it but I think you can use different containers to scope the command however you want (cluster, resource pool, folder, etc). See <a href="https://www.vmware.com/support/developer/PowerCLI/PowerCLI41U1/html/VIContainer.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://www.vmware.com/support/developer/PowerCLI/PowerCLI41U1/html/VIContainer.html', 'here']);" >here</a> for other &#8220;location&#8221; options.