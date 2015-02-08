---
title: Can I create an EC2 MySQL slave to an RDS master?
author: Evan Hoffman
layout: post
permalink: /2014/10/08/can-i-create-an-ec2-mysql-slave-to-an-rds-master/
categories:
  - Uncategorized
tags:
  - devops
  - ec2
  - external
  - linux
  - mysql
  - rds
  - replica
  - slave
  - sysadmin
  - work
---
# No.

Here&#8217;s what happens if you try:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;">mysql<span style="color: #000000; font-weight: bold;">&gt;</span> grant replication slave on <span style="color: #000000; font-weight: bold;">*</span>.<span style="color: #000000; font-weight: bold;">*</span> to <span style="color: #ff0000;">'ec2-slave'</span><span style="color: #000000; font-weight: bold;">@</span><span style="color: #ff0000;">'%'</span>;
ERROR <span style="color: #000000;">1045</span> <span style="color: #7a0874; font-weight: bold;">&#40;</span><span style="color: #000000;">28000</span><span style="color: #7a0874; font-weight: bold;">&#41;</span>: Access denied <span style="color: #000000; font-weight: bold;">for</span> user <span style="color: #ff0000;">'rds_root'</span><span style="color: #000000; font-weight: bold;">@</span><span style="color: #ff0000;">'%'</span> <span style="color: #7a0874; font-weight: bold;">&#40;</span>using password: YES<span style="color: #7a0874; font-weight: bold;">&#41;</span>
mysql<span style="color: #000000; font-weight: bold;">&gt;</span> update mysql.user <span style="color: #000000; font-weight: bold;">set</span> <span style="color: #007800;">Repl_slave_priv</span>=<span style="color: #ff0000;">'Y'</span> WHERE <span style="color: #007800;">user</span>=<span style="color: #ff0000;">'rds_root'</span> AND <span style="color: #007800;">host</span>=<span style="color: #ff0000;">'%'</span>;
ERROR <span style="color: #000000;">1054</span> <span style="color: #7a0874; font-weight: bold;">&#40;</span>42S22<span style="color: #7a0874; font-weight: bold;">&#41;</span>: Unknown column <span style="color: #ff0000;">'ERROR (RDS): REPLICA SLAVE PRIVILEGE CANNOT BE GRANTED OR MAINTAINED'</span> <span style="color: #000000; font-weight: bold;">in</span> <span style="color: #ff0000;">'field list'</span>
mysql<span style="color: #000000; font-weight: bold;">&gt;</span></pre>
      </td>
    </tr>
  </table>
</div>

Note: this is for MySQL 5.5, which is unfortunately what I&#8217;m currently stuck with.