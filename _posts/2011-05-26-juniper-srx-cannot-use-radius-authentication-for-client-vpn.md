---
title: Juniper SRX cannot use RADIUS authentication for client VPN?
author: Evan Hoffman
excerpt: "Apparently using RADIUS authentication with Juniper SRX VPN is strange voodoo magic that's never been tried before."
layout: post
permalink: /2011/05/26/juniper-srx-cannot-use-radius-authentication-for-client-vpn/
dsq_thread_id:
  - 2949521248
categories:
  - Uncategorized
tags:
  - active directory
  - authentication
  - igx
  - jtac
  - juniper
  - junos
  - radius
  - srx
  - vpn
---
We&#8217;ve been trying now literally for weeks to configure our Juniper SRX VPN using RADIUS authentication. We previously had a Cisco ASA 5510 and it worked fine but we moved to an SRX because it was much easier to administer and was about 1/3 the cost. For the most part we&#8217;ve been pretty happy with the SRX vs the ASA &#8211; common-sense features like saving previous configs for trivial rollback, a web gui that&#8217;s way easier to use than ASDM, etc. But this VPN thing is a real fiasco. Basically what we want to do is use the SRX to authenticate against our Active Directory using IAS. 

The closest we&#8217;ve gotten so far is having the SRX authenticate via RADIUS but apparently we still need to maintain a local user list on the SRX itself, defeating the purpose of central authentication. We were sold 10 VPN licenses with the understanding that this meant we could have up to 10 simultaneous VPN connections open. Apparently that&#8217;s true, but we&#8217;d have to specify which 10 users can access the VPN, which won&#8217;t work for us. 

This doesn&#8217;t seem like we&#8217;re trying to do anything exotic here but according to the vendor and JTAC support, they&#8217;re not aware of anyone doing this, and have no idea how to do it. The ticket&#8217;s been open with Juniper for over 1 month now. We&#8217;re at the point where we just want a refund for the VPN licenses rather than continuing to bang our heads against the wall with this. The vendor originally wanted us to buy an SA VPN appliance in addition to the SRX, but assured us the SRX would do what we needed with the caveat that the JunOS Pulse client for the SRX VPN was kind of crappy.

So after opening tickets in JTAC and the vendor&#8217;s support system, the net result apparently is that this is actually not possible. Not only is it not possible but everybody acts like we&#8217;re trying to do some strange voodoo. I mean, we have other stuff hooked into AD IAS already &#8211; wifi access points for one, and the old ASA AnyConnect VPN for another. We even went so far as to create a FreeRadius server to serve as an intermediary between the SRX and AD so we could get better log messages, and that didn&#8217;t work either.

So hopefully we can get a refund on the 10 useless VPN licenses and use our old ASA for AnyConnect VPN. I&#8217;m not holding my breath though. Fortunately we&#8217;ve eliminated the need for VPN for almost everything, the local fileserver is really the only thing left that requires VPN access.