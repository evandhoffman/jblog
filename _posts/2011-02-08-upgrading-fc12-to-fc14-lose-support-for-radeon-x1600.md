---
title: Upgrading FC12 to FC14, lose support for Radeon X1600
author: Evan Hoffman
layout: post
permalink: /2011/02/08/upgrading-fc12-to-fc14-lose-support-for-radeon-x1600/
categories:
  - Uncategorized
tags:
  - f14
  - fc14
  - fedora
  - fglrx
  - linux
  - nc8430
  - radeon
  - 'RB554UT#ABA'
  - upgrade fc14
  - x1600
---
I&#8217;d been running FC12 for quite a while and I noticed the past few weeks have been devoid of yum updates (except for Google Chrome). I figured FC15 is on the horizon; now&#8217;s a good time to upgrade to FC14.

I followed <a href="http://fedoraproject.org/wiki/YumUpgradeFaq" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://fedoraproject.org/wiki/YumUpgradeFaq', 'the basic procedure laid out here']);" >the basic procedure laid out here</a> and went 12 -> 13 no problem. 13 -> 14: problem. The upgrade appeared to go well (loooong time to complete, downloading/installing 1+ gigs of packages, even over my 25 mbps home line) but X would not start no matter what. At this point I learned that <tt>system-config-display</tt> has been **deprecated** in FC14 and isn&#8217;t even there! I tried &#8220;Xorg &#8211;configure&#8221; but that configuration still didn&#8217;t work. I then tried doing a yum downgrade to FC13&#8230; let me save you the trouble: never try this.

I ended up downloading the FC14 DVD ISO and burning it, and doing a fresh install (repair, of course, just drops you to a shell &#8211; the equivalent of throwing you a life preserver in the middle of the ocean and leaving you there) without touching any of my existing partitions. This ended up being pretty painless and after it was complete everything worked. *Or so it seemed&#8230;*

I did the FC14 fresh install on Saturday (after having borked the laptop on Friday). At work on Monday I plugged in my secondary monitor and immediately noticed weirdness. Lines all over both displays, the Gnome panel unusable, any window that was created wasn&#8217;t drawing properly. And the topper was rebooting didn&#8217;t work &#8211; it would go to this colorful jumbley screen and just sit there until I did a hard poweroff. If I unplugged the external monitor and turned the computer on, it worked fine.

I&#8217;ve tried several things &#8211; installing the akmod-catalyst stuff and x11-catalyst-drv stuff via yum, but none of that seems to make much of a difference. livna-display-config throws an error that I don&#8217;t have the radeon drivers installed. I&#8217;ve tried the official ATI <a href="http://support.amd.com/us/gpudownload/linux/Pages/radeon_linux.aspx" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://support.amd.com/us/gpudownload/linux/Pages/radeon_linux.aspx', 'radeon driver for X1600']);" >radeon driver for X1600</a>, which complains that it can&#8217;t find XFree86. I tried to get around this by symlinking /usr/bin/Xorg to /usr/bin/XFree86, which got me past that hurdle, but then it complained that I have no adapter. I tried <a href="http://support.amd.com/us/gpudownload/linux/Pages/radeon_linux.aspx" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://support.amd.com/us/gpudownload/linux/Pages/radeon_linux.aspx', 'the other Linux radeon driver']);" >the other Linux radeon driver</a>, which didn&#8217;t complain about XFree86 at least, but also said it didn&#8217;t find any AMD/ATI adapters.

At this point it seems these are my options:

  * Live with one monitor (annoying) 
      * Downgrade/reinstall to FC13 (eventual obsolescence) 
          * Switch OS (Not an Ubuntu fan; Win7 doesn&#8217;t really make sense) 
              * Switch to Mac (can&#8217;t imagine using one for work) 
                  * Buy a new computer and put FC14 on it (my C2D T7200 **is** kind of old&#8230;) </ul> 
                    None of these options is very good. For now I&#8217;m just sticking with 1 monitor. Not the end of the world, but it&#8217;s annoying to run a WinXP VM and do my regular tasks inside a single screen.
                    
                    dmesg below showing stupid errors:
                    
                    <pre>[root@ehoffman ~]# aticonfig
aticonfig: No supported adapters detected
[root@ehoffman ~]# uname -a
Linux ehoffman 2.6.35.10-74.fc14.x86_64 #1 SMP Thu Dec 23 16:04:50 UTC 2010 x86_64 x86_64 x86_64 GNU/Linux
[root@ehoffman ~]# lspci | grep -i radeon
01:00.0 VGA compatible controller: ATI Technologies Inc M56P [Radeon Mobility X1600]
[root@ehoffman ~]# dmesg | grep -i radeon
[   14.717757] [drm] radeon defaulting to kernel modesetting.
[   14.717761] [drm] radeon kernel modesetting enabled.
[   14.717827] radeon 0000:01:00.0: setting latency timer to 64
[   14.723052] radeon 0000:01:00.0: VRAM: 256M 0x00000000 - 0x0FFFFFFF (256M used)
[   14.723056] radeon 0000:01:00.0: GTT: 512M 0x10000000 - 0x2FFFFFFF
[   14.723167] radeon 0000:01:00.0: irq 48 for MSI/MSI-X
[   14.723174] radeon 0000:01:00.0: radeon: using MSI.
[   14.723211] [drm] radeon: irq initialized.
[   14.723322] [drm] radeon: 256M of VRAM memory ready
[   14.723325] [drm] radeon: 512M of GTT memory ready.
[   14.725519] [drm] radeon: 1 quad pipes, 2 z pipes initialized.
[   14.733804] [drm] radeon: ring at 0x0000000010000000
[   14.734017] [drm] radeon: ib pool ready.
[   14.734446] [drm] Radeon Display Connectors
[   14.844393] [drm] radeon: power management initialized
[   15.295827] fbcon: radeondrmfb (fb0) is primary device
[   15.296451] fb0: radeondrmfb frame buffer device
[   15.296546] [drm] Initialized radeon 2.5.0 20080528 for 0000:01:00.0 on minor 0
[root@ehoffman ~]#
</pre>
                    
                    **Edit**: I should probably mention that as soon as I install the xorg-x11-drv-catalyst stuff, X won&#8217;t start. If I remove it, it boots up fine (though the 2nd monitor screws causes it to freak out). This is pretty strange; I&#8217;ve done it several times to confirm:
                    
                    <pre>Feb 08 11:35:09 Erased: fglrx64_6_9_0
Feb 08 11:36:53 Updated: libuuid-2.18-4.8.fc14.i686
Feb 08 11:36:54 Updated: postgresql-libs-8.4.7-1.fc14.x86_64
Feb 08 11:36:54 Updated: libuuid-devel-2.18-4.8.fc14.x86_64
Feb 08 11:41:52 Updated: google-chrome-stable-9.0.597.94-73967.x86_64
Feb 08 11:44:11 Installed: fglrx64_7_5_0-8.812-1.x86_64
Feb 08 11:48:21 Erased: fglrx64_7_5_0
Feb 08 12:25:01 Installed: xorg-x11-drv-catalyst-libs-10.12-2.fc14.x86_64
Feb 08 12:25:07 Installed: xorg-x11-drv-catalyst-10.12-2.fc14.x86_64
Feb 08 12:25:08 Installed: akmod-catalyst-10.12-1.fc14.x86_64
Feb 08 12:39:16 Erased: kmod-catalyst-2.6.35.10-74.fc14.x86_64
Feb 08 12:39:20 Erased: xorg-x11-drv-catalyst-libs
Feb 08 12:39:21 Erased: xorg-x11-drv-catalyst
Feb 08 12:39:21 Erased: akmod-catalyst
</pre>