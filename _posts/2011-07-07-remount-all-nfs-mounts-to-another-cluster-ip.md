---
title: Remount all NFS mounts to another cluster IP
author: Evan Hoffman
excerpt: Putting this here for safekeeping.
layout: post
permalink: /2011/07/07/remount-all-nfs-mounts-to-another-cluster-ip/
dsq_thread_id:
  - 2949520714
categories:
  - Uncategorized
tags:
  - devops
  - isilon
  - linux
  - nfs
  - remount
  - work
  - xargs
---
<div class="wp_syntax">
  <table>
    <tr>
      <td class="line_numbers">
        <pre>1
</pre>
      </td>
      
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #c20cb9; font-weight: bold;">mount</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #c20cb9; font-weight: bold;">grep</span> 10.0.0.73 <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #c20cb9; font-weight: bold;">cut</span> <span style="color: #660033;">-f3</span> -d\  <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #c20cb9; font-weight: bold;">xargs</span> <span style="color: #c20cb9; font-weight: bold;">mount</span> <span style="color: #660033;">-o</span> remount,rw,<span style="color: #007800;">rsize</span>=<span style="color: #000000;">32768</span>,<span style="color: #007800;">wsize</span>=<span style="color: #000000;">32768</span>,<span style="color: #007800;">nfsvers</span>=<span style="color: #000000;">3</span>,<span style="color: #7a0874; font-weight: bold;">bg</span>,intr,tcp,<span style="color: #007800;">retrans</span>=<span style="color: #000000;"></span>,<span style="color: #007800;">addr</span>=10.0.0.74</pre>
      </td>
    </tr>
  </table>
</div>

Not much to say. This seems to work even when the disk is in use. Using it with Isilon to remount off of a quiesced node.