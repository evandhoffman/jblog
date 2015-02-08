---
title: Wake-on-LAN is so cool, when it works.
author: Evan Hoffman
layout: post
permalink: /2011/04/17/wake-on-lan-is-so-cool-when-it-works/
dsq_thread_id:
  - 2949516292
categories:
  - Uncategorized
tags:
  - ether-wake
  - linux
  - wake-on-lan
  - wol
---
<pre>[evan@lunix ~]$ sudo -i
[root@lunix ~]# ether-wake -i eth2 00:1F:D0:D3:AE:9C
[root@lunix ~]# ping 192.168.1.79
PING 192.168.1.79 (192.168.1.79) 56(84) bytes of data.
From 192.168.1.80 icmp_seq=2 Destination Host Unreachable
From 192.168.1.80 icmp_seq=3 Destination Host Unreachable
From 192.168.1.80 icmp_seq=4 Destination Host Unreachable
From 192.168.1.80 icmp_seq=6 Destination Host Unreachable
From 192.168.1.80 icmp_seq=7 Destination Host Unreachable
From 192.168.1.80 icmp_seq=8 Destination Host Unreachable
From 192.168.1.80 icmp_seq=10 Destination Host Unreachable
From 192.168.1.80 icmp_seq=11 Destination Host Unreachable
From 192.168.1.80 icmp_seq=12 Destination Host Unreachable
From 192.168.1.80 icmp_seq=14 Destination Host Unreachable
From 192.168.1.80 icmp_seq=15 Destination Host Unreachable
From 192.168.1.80 icmp_seq=16 Destination Host Unreachable
From 192.168.1.80 icmp_seq=18 Destination Host Unreachable
From 192.168.1.80 icmp_seq=19 Destination Host Unreachable
From 192.168.1.80 icmp_seq=20 Destination Host Unreachable
From 192.168.1.80 icmp_seq=22 Destination Host Unreachable
From 192.168.1.80 icmp_seq=23 Destination Host Unreachable
From 192.168.1.80 icmp_seq=24 Destination Host Unreachable
From 192.168.1.80 icmp_seq=26 Destination Host Unreachable
From 192.168.1.80 icmp_seq=27 Destination Host Unreachable
From 192.168.1.80 icmp_seq=28 Destination Host Unreachable
From 192.168.1.80 icmp_seq=30 Destination Host Unreachable
From 192.168.1.80 icmp_seq=31 Destination Host Unreachable
From 192.168.1.80 icmp_seq=32 Destination Host Unreachable
64 bytes from 192.168.1.79: icmp_seq=33 ttl=64 time=1001 ms
64 bytes from 192.168.1.79: icmp_seq=34 ttl=64 time=1.26 ms
64 bytes from 192.168.1.79: icmp_seq=35 ttl=64 time=0.193 ms
64 bytes from 192.168.1.79: icmp_seq=36 ttl=64 time=0.194 ms
64 bytes from 192.168.1.79: icmp_seq=37 ttl=64 time=0.175 ms
64 bytes from 192.168.1.79: icmp_seq=38 ttl=64 time=0.172 ms

--- 192.168.1.79 ping statistics ---
38 packets transmitted, 6 received, +24 errors, 84% packet loss, time 36994ms
rtt min/avg/max/mdev = 0.172/167.184/1001.102/372.939 ms, pipe 3
[root@lunix ~]# logout
[evan@lunix ~]$ ssh 192.168.1.79
evan@192.168.1.79's password:
Last login: Tue Apr 12 17:30:58 2011 from 192.168.1.80
[evan@evanfc12 ~]$
</pre>

Just saved me a trip downstairs!