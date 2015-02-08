---
title: 'VMware Workstation 7 &#8211; virtual ethernet fails to start after changing vmnet8 subnet'
author: Evan Hoffman
layout: post
permalink: /2011/02/23/vmware-workstation-7-virtual-ethernet-fails-to-start-after-changing-vmnet8-subnet/
dsq_thread_id:
  - 2949520422
categories:
  - Uncategorized
tags:
  - dhcp
  - dhcpd
  - error
  - failed
  - linux
  - service
  - virtual ethernet
  - vmnet8
  - vmware
  - vnic
  - workstation
---
After my recent wipe of my laptop, I reinstalled VMware Workstation and my Win XP VM was working fine. The one wrinkle I faced was that the subnet for the vmnet8 (NAT) vnic had changed from 192.168.250.0/24 to 173.16.132.0/24. The host machine had been 192.168.250.1 so rather than reconfiguring everything on the guest to point to a new IP for the host I figured it would be easier to change the subnet for vmnet8. I went into the Virtual Network Editor and just changed the subnet. Seemed to work correctly, but after doing a release/renew in Win XP I couldn&#8217;t get an IP. 

I tried disconnecting the vnic and reconnecting it; the guest recognized that the &#8220;cable was unplugged,&#8221; but still couldn&#8217;t get an IP. I rebooted the guest &#8211; same thing. Restarted the vmware service and saw this: 

<pre>[root@ehoffman ~]# /etc/init.d/vmware restart
Stopping VMware services:
   VMware USB Arbitrator                                   [  OK  ]
   VM communication interface socket family                [  OK  ]
   Virtual machine communication interface                 [  OK  ]
   Virtual machine monitor                                 [  OK  ]
   Blocking file system                                    [  OK  ]
Starting VMware services:
   VMware USB Arbitrator                                   [  OK  ]
   Virtual machine monitor                                 [  OK  ]
   Virtual machine communication interface                 [  OK  ]
   VM communication interface socket family                [  OK  ]
   Blocking file system                                    [  OK  ]
   Virtual ethernet                                        [FAILED]
[root@ehoffman ~]# 
</pre>

That&#8217;s weird. The vnic is up with the specified IP:

<pre>[root@ehoffman vmnet8]# ifconfig
vmnet1    Link encap:Ethernet  HWaddr 00:50:56:C0:00:01  
          inet addr:172.16.3.1  Bcast:172.16.3.255  Mask:255.255.255.0
          inet6 addr: fe80::250:56ff:fec0:1/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:38 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 b)  TX bytes:0 (0.0 b)

vmnet8    Link encap:Ethernet  HWaddr 00:50:56:C0:00:08  
          inet addr:192.168.250.1  Bcast:192.168.250.255  Mask:255.255.255.0
          inet6 addr: fe80::250:56ff:fec0:8/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:36 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 b)  TX bytes:0 (0.0 b)
</pre>

Checked the /var/log/vnetlib logfile and it appears to be some problem starting DHCP on vmnet8:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="syslog" style="font-family:monospace;">Internet Software Consortium DHCP Server 2.0
Copyright 1995, 1996, 1997, 1998, 1999 The Internet Software Consortium.
All rights reserved.
&nbsp;
Please contribute if you find this software useful.
For info, please visit http://www.isc.org/dhcp-contrib.html
&nbsp;
Configured subnet: 172.16.3.0
Setting vmnet-dhcp IP address: 172.16.3.254
Opened: /dev/vmnet1
Recving on     VNet/vmnet1/172.16.3.0
Sending on     VNet/vmnet1/172.16.3.0
Internet Software Consortium DHCP Server 2.0
Copyright 1995, 1996, 1997, 1998, 1999 The Internet Software Consortium.
All rights reserved.
&nbsp;
Please contribute if you find this software useful.
For info, please visit http://www.isc.org/dhcp-contrib.html
&nbsp;
Address range 192.168.250.128 to 192.168.250.254 not on net 192.168.250.1/255.255.255.0!
exiting.
Failed to start DHCP service on vmnet8
Failed to start some/all services
Feb 23 09:28:46 VNL_Load - LOG_ERR logged
Feb 23 09:28:46 VNL_Load - LOG_WRN logged
Feb 23 09:28:46 VNL_Load - LOG_OK logged
Feb 23 09:28:46 VNL_Load - Successfully initialized Vnetlib
Feb 23 09:28:46 VNL_StartService - Started "Bridge" service for vnet: vmnet0
Feb 23 09:28:47 VNL_CheckSubnetAvailability - Subnet: 172.16.3.0 on vnet: vmnet1 is available
Feb 23 09:28:47 VNL_CheckSubnetAvailability - Subnet: 192.168.250.0 on vnet: vmnet8 is available
Feb 23 09:28:47 VNL_StartService - Started "DHCP" service for vnet: vmnet1
Feb 23 09:28:47 VNL_EnableNetworkAdapter - Successfully enabled hostonly adapter on vnet: vmnet1
Feb 23 09:28:47 VNLServiceStart - Daemon process did not report status, returning failure
Feb 23 09:28:47 VNL_StartService - Failure in starting "DHCP" service for vnet: vmnet8
Feb 23 09:28:47 VNL_StartService - Started "NAT" service for vnet: vmnet8
Feb 23 09:28:47 VNL_EnableNetworkAdapter - Successfully enabled hostonly adapter on vnet: vmnet8
Feb 23 09:28:47 VNLServiceStatus - pid: 13512 for Netdetect service daemon on vnet: 0 is stale
Feb 23 09:28:47 VNL_StartService - Started "Netdetect" service for vnet: vmnet0
Feb 23 09:28:47 VNL_Unload - Vnetlib unloaded.
Started Bridge networking on vmnet0
Started DHCP service on vmnet1
Enabled hostonly virtual adapter on vmnet1
Started NAT service on vmnet8
Enabled hostonly virtual adapter on vmnet8
Started Network detection service</pre>
      </td>
    </tr>
  </table>
</div>

The important line there is **Address range 192.168.250.128 to 192.168.250.254 not on net 192.168.250.1/255.255.255.0!** Apparently DHCP is misconfigured. The config file for dhcpd for vmnet8 is /etc/vmware/vmnet8/dhcpd/dhcpd.conf . Here&#8217;s what it looked like:

<pre>subnet 192.168.250.1 netmask 255.255.255.0 {
        range 192.168.250.128 192.168.250.250;
        option broadcast-address 192.168.250.255;
        option domain-name-servers 192.168.250.1;
        option domain-name localdomain;
        default-lease-time 1800;                # default is 30 minutes
        max-lease-time 7200;                    # default is 2 hours
        option routers 192.168.250.2;
}
</pre>

I changed the range to 192.168.250.2 to 192.168.250.127, thinking that was the problem, but it turned out to be the &#8220;subnet&#8221; line &#8211; the subnet should be &#8220;192.168.250.0 netmask 255.255.255.0&#8243; rather than &#8220;192.168.250.1 &#8230;&#8221; After changing that, everything Worked As Intended.