---
layout: post
title:  "High CPU steal on EC2 m3.medium"
date:   2014-08-12 13:51
categories: ec2 sysadmin devops
author: Evan Hoffman
permalink: /2014/08/12/the-m3-medium-is-terrible/
author_twitter: evanhoffman
---

I've been doing some testing of various instance types in our staging environment, originally just to see if Amazon's `t2.*` line of instances is usable in a real-world scenario.  In the end, I found that not only are the `t2.medium`s viable for what I want them to do, but they're far better suited than the m3.medium, which I wouldn't use for anything that you ever expect to reach any load.  

Here are the conditions for my test:

* Rails application (unicorn) fronted by nginx. 
+   The number of unicorn processes is controlled by chef, currently set to (CPU count * 2), so a 2 CPU instance has 4 unicorn workers.
+   All instances are running Ubuntu 14.04 LTS (AMI `ami-864d84ee` for HVM, `ami-018c9568` for paravirtual) with kernel `3.13.0-29-generic #53-Ubuntu SMP Wed Jun 4 21:00:20 UTC 2014 x86_64`.
+   The test used [loader.io](http://loader.io) to simulate 65 concurrent clients hitting the API (adding products to cart) as fast as possible for 600 seconds (10 minutes).
+   The instances were all behind an Elastic Load Balancer, which routes traffic based on its own algorithm (supposedly the instances with the lowest CPU always gets the next request).

The below charts summarize the findings.  
![Chart 1]({{ site.url}}/assets/request_time.png)

This chart shows each server's performance as reported by nginx (you can get these values by adding `$request_time` to your nginx access_log format).  The values are the average time to service each request and the standard deviation.  While I expected the m3.large to outperform the m3.medium, I didn't expect the difference to be so dramatic.  The performance of the t2.medium is the real surprise, however.

    # _sourcehost _avg  _stddev
    1 m3.large  6.30324 3.84421
    2 m3.medium 15.88136  9.29829
    3 t2.medium 4.80078 2.71403

These charts show the CPU activity for each instance during the test (data as per [CopperEgg](http://www.copperegg.com/).)  

![m3.large]({{ site.url}}/assets/m3.large_.png)

![t2.medium]({{ site.url}}/assets/t2.medium.png)

![m3.large]({{ site.url}}/assets/m3.medium.png)

As you can see, the m3.medium has a huge amount of [CPU Steal](http://blog.scoutapp.com/articles/2013/07/25/understanding-cpu-steal-time-when-should-you-be-worried), which I'm guessing accounts for its horrible performance.  Anecdotally, in my own experience m3.medium far more prone to CPU steal than other instance types.  Moving from m3.medium to c3.large (essentially the same instance with 2 cpus) eliminates the CPU steal issue.  However, since the t2.medium performs as well as the c3.large or m3.large and costs half of the c3.large (or nearly 1/3 of the m3.large) I'm going to try running most of my backend fleet on t2.medium.  

What about [CPU credits](http://aws.amazon.com/blogs/aws/low-cost-burstable-ec2-instances/)?  Well, in my testing I barely made a dent in the CPU credit balance across the server pool.  Even with a load test that was 100 times the traffic we'd expect to see on a busy day we only spent 1 or 2 credits per instance.  Our production server pool is much larger than the staging pool that I tested, so the total pool of CPU credits we have to spend is also much higher in production.  In short, I don't see CPU credits as being a problem for us.  If it does become an issue, it's relatively painless for us to spin up new "fixed CPU" instances (c3.large, for example), but we'll have plenty of time to do that before we exhaust the credit pool.

As far as I'm concerned, the m3.medium should really never be used if the t2.medium is available (t2.medium is only available inside a VPC, not in public AWS).  The t2.medium is significantly more powerful (two CPUs) and doesn't suffer from CPU steal the way the m3.medium does - and the t2.medium is 25% cheaper than the m3.medium.

