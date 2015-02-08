---
title: Setting hostname in an EC2 instance from the name tag
author: Evan Hoffman
layout: post
permalink: /2014/05/19/setting-hostname-in-an-ec2-instance-from-the-name-tag/
dsq_thread_id:
  - 2963120797
categories:
  - Uncategorized
tags:
  - aws
  - cli
  - ec2
  - hostname
  - tags
---
<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="bash" style="font-family:monospace;"><span style="color: #666666; font-style: italic;"># pip install awscli</span>
<span style="color: #666666; font-style: italic;"># HOSTNAME=`aws ec2 describe-tags --region us-east-1 --filters Name=resource-id,Values=\`curl http://169.254.169.254/latest/meta-data/instance-id 2&gt; /dev/null\` Name=key,Values=Name --output text --query 'Tags[*].Value'`</span>
<span style="color: #666666; font-style: italic;"># hostname $HOSTNAME</span>
<span style="color: #666666; font-style: italic;"># hostname &gt; /etc/hostname</span></pre>
      </td>
    </tr>
  </table>
</div>