---
title: Everything works on my laptop except Bluetooth
author: Evan Hoffman
layout: post
permalink: /2009/11/06/everything-works-on-my-laptop-except-bluetooth/
dsq_thread_id:
  - 2949522308
categories:
  - Uncategorized
tags:
  - bluetooth
  - fc11
  - fedora
  - laptop
  - linux
---
This is pretty weird. Bluetooth worked fine under Win XP. The computer is an HP/Compaq nc8430. When I go to the Bluetooth control panel I get &#8220;Your computer does not have any Bluetooth adapters plugged in.&#8221; The weird thing is, sometimes I&#8217;ll see the bluetooth logo in the taskbar at the top, but then when I look again it&#8217;s gone and I have no idea how to get it back. When I booted it up this morning I noticed the icon was there so I ran some of the debugging tools for Bluetooth and checked dmesg and this time at least it looks like at least there&#8217;s proof that Linux CAN see the Bluetooth adapter, I just don&#8217;t get why it then stops recognizing it after just a minute or two, and a reboot doesn&#8217;t even consistently bring it back.

<!--more-->

Here&#8217;s what lspci -nn showed while the it was up:

<pre>[root@ehoffman ~]# cat lspci-bluetooth
00:00.0 Host bridge [0600]: Intel Corporation Mobile 945GM/PM/GMS, 943/940GML and 945GT Express Memory Controller Hub [8086:27a0] (rev 03)
00:01.0 PCI bridge [0604]: Intel Corporation Mobile 945GM/PM/GMS, 943/940GML and 945GT Express PCI Express Root Port [8086:27a1] (rev 03)
00:1b.0 Audio device [0403]: Intel Corporation 82801G (ICH7 Family) High Definition Audio Controller [8086:27d8] (rev 01)
00:1c.0 PCI bridge [0604]: Intel Corporation 82801G (ICH7 Family) PCI Express Port 1 [8086:27d0] (rev 01)
00:1c.1 PCI bridge [0604]: Intel Corporation 82801G (ICH7 Family) PCI Express Port 2 [8086:27d2] (rev 01)
00:1c.3 PCI bridge [0604]: Intel Corporation 82801G (ICH7 Family) PCI Express Port 4 [8086:27d6] (rev 01)
00:1d.0 USB Controller [0c03]: Intel Corporation 82801G (ICH7 Family) USB UHCI Controller #1 [8086:27c8] (rev 01)
00:1d.1 USB Controller [0c03]: Intel Corporation 82801G (ICH7 Family) USB UHCI Controller #2 [8086:27c9] (rev 01)
00:1d.2 USB Controller [0c03]: Intel Corporation 82801G (ICH7 Family) USB UHCI Controller #3 [8086:27ca] (rev 01)
00:1d.3 USB Controller [0c03]: Intel Corporation 82801G (ICH7 Family) USB UHCI Controller #4 [8086:27cb] (rev 01)
00:1d.7 USB Controller [0c03]: Intel Corporation 82801G (ICH7 Family) USB2 EHCI Controller [8086:27cc] (rev 01)
00:1e.0 PCI bridge [0604]: Intel Corporation 82801 Mobile PCI Bridge [8086:2448] (rev e1)
00:1f.0 ISA bridge [0601]: Intel Corporation 82801GBM (ICH7-M) LPC Interface Bridge [8086:27b9] (rev 01)
00:1f.1 IDE interface [0101]: Intel Corporation 82801G (ICH7 Family) IDE Controller [8086:27df] (rev 01)
00:1f.2 SATA controller [0106]: Intel Corporation 82801GBM/GHM (ICH7 Family) SATA AHCI Controller [8086:27c5] (rev 01)
01:00.0 VGA compatible controller [0300]: ATI Technologies Inc M56P [Radeon Mobility X1600] [1002:71c5]
02:06.0 CardBus bridge [0607]: Texas Instruments PCIxx12 Cardbus Controller [104c:8039]
02:06.1 FireWire (IEEE 1394) [0c00]: Texas Instruments PCIxx12 OHCI Compliant IEEE 1394 Host Controller [104c:803a]
02:06.2 Mass storage controller [0180]: Texas Instruments 5-in-1 Multimedia Card Reader (SD/MMC/MS/MS PRO/xD) [104c:803b]
02:06.3 SD Host controller [0805]: Texas Instruments PCIxx12 SDA Standard Compliant SD Host Controller [104c:803c]
02:06.4 Communication controller [0780]: Texas Instruments PCIxx12 GemCore based SmartCard controller [104c:803d]
08:00.0 Ethernet controller [0200]: Broadcom Corporation NetXtreme BCM5753M Gigabit Ethernet PCI Express [14e4:16fd] (rev 21)
10:00.0 Network controller [0280]: Intel Corporation PRO/Wireless 3945ABG [Golan] Network Connection [8086:4222] (rev 02)
</pre>

When I did &#8220;hcitool dev&#8221; it actually showed something:

<pre>[root@ehoffman ~]# cat hcit.dev
Devices:
	hci0	00:16:41:BA:C6:xx
</pre>

Here&#8217;s where it looks like it started working and then stopped working from /var/log/messages:

<pre>Nov  6 08:57:58 localhost dbus-daemon: Rejected send message, 2 matched rules; type="method_call", sender=":1.47" (uid=42 pid=2844 comm="/usr/bin/pulseaudio --start --log-target=syslog ") interface="org.bluez.Manager" member="ListAdapters" error name="(unset)" requested_reply=0 destination="org.bluez" (uid=0 pid=2390 comm="/usr/sbin/bluetoothd "))
Nov  6 08:57:58 localhost pulseaudio[2844]: bluetooth-util.c: Error from ListAdapters reply: org.freedesktop.DBus.Error.AccessDenied
Nov  6 08:58:03 localhost kernel: fuse init (API version 7.11)
Nov  6 08:58:03 localhost pulseaudio[3108]: pid.c: Daemon already running.
Nov  6 08:58:15 localhost kernel: usb 2-1: new full speed USB device using uhci_hcd and address 2
Nov  6 08:58:16 localhost kernel: usb 2-1: New USB device found, idVendor=03f0, idProduct=171d
Nov  6 08:58:16 localhost kernel: usb 2-1: New USB device strings: Mfr=1, Product=2, SerialNumber=0
Nov  6 08:58:16 localhost kernel: usb 2-1: Product: HP Integrated Module
Nov  6 08:58:16 localhost kernel: usb 2-1: Manufacturer: Broadcom Corp
Nov  6 08:58:16 localhost kernel: usb 2-1: configuration #1 chosen from 1 choice
Nov  6 08:58:16 localhost kernel: Bluetooth: Generic Bluetooth USB driver ver 0.5
Nov  6 08:58:16 localhost kernel: usbcore: registered new interface driver btusb
Nov  6 08:58:16 localhost bluetoothd[2390]: HCI dev 0 registered
Nov  6 08:58:16 localhost bluetoothd[2390]: HCI dev 0 up
Nov  6 08:58:16 localhost bluetoothd[2390]: Starting security manager 0
Nov  6 08:58:16 localhost kernel: Bluetooth: RFCOMM socket layer initialized
Nov  6 08:58:16 localhost kernel: Bluetooth: RFCOMM TTY layer initialized
Nov  6 08:58:16 localhost kernel: Bluetooth: RFCOMM ver 1.11
Nov  6 08:58:16 localhost bluetoothd[2390]: Adapter /org/bluez/2390/hci0 has been enabled
Nov  6 08:58:16 localhost dbus-daemon: Rejected send message, 2 matched rules; type="method_call", sender=":1.47" (uid=42 pid=2844 comm="/usr/bin/pulseaudio --start --log-target=syslog ") interface="org.bluez.Adapter" member="ListDevices" error name="(unset)" requested_reply=0 destination="org.bluez" (uid=0 pid=2390 comm="/usr/sbin/bluetoothd "))
Nov  6 08:58:16 localhost pulseaudio[2844]: bluetooth-util.c: Error from ListDevices reply: org.freedesktop.DBus.Error.AccessDenied
Nov  6 08:58:21 localhost NetworkManager: &lt;info>  Wireless now enabled by radio killswitch
Nov  6 08:58:21 localhost NetworkManager: &lt;info>  (wlan1): bringing up device.
&lt;snip>
Nov  6 09:01:05 localhost kernel: usb 2-1: USB disconnect, address 2
Nov  6 09:01:05 localhost kernel: btusb_intr_complete: hci0 urb ffff8800899c73c0 failed to resubmit (19)
Nov  6 09:01:05 localhost kernel: btusb_bulk_complete: hci0 urb ffff8800899c7540 failed to resubmit (19)
Nov  6 09:01:05 localhost kernel: btusb_bulk_complete: hci0 urb ffff8800899c7600 failed to resubmit (19)
Nov  6 09:01:05 localhost kernel: btusb_send_frame: hci0 urb ffff88008f43a840 submission failed
Nov  6 09:01:05 localhost bluetoothd[2390]: HCI dev 0 down
Nov  6 09:01:05 localhost bluetoothd[2390]: Adapter /org/bluez/2390/hci0 has been disabled
Nov  6 09:01:05 localhost bluetoothd[2390]: Stopping security manager 0
Nov  6 09:01:05 localhost bluetoothd[2390]: HCI dev 0 unregistered
Nov  6 09:01:05 localhost bluetoothd[2390]: Unregister path: /org/bluez/2390/hci0
Nov  6 09:01:05 localhost kernel: usb 2-1: new full speed USB device using uhci_hcd and address 3
Nov  6 09:01:10 localhost kernel: hub 2-0:1.0: unable to enumerate USB device on port 1
</pre>

It looks like it worked until I turned on wireless via the hardware switch. But even if I start up with wireless off it doesn&#8217;t work. Hmm.