---
title: Java compound interest calculator
author: Evan Hoffman
layout: post
permalink: /2008/01/10/java-compound-interest-calculator/
categories:
  - Uncategorized
tags:
  - code
  - java
  - old
---
I wrote a thing to calculate compound interest and print out how much you&#8217;ve accrued after each period. It&#8217;s pretty basic but I was bored and didn&#8217;t want to do it via calculator. The code is in [InterestCalculator.java][1], sample output is below: </p> 

<pre>Initial principal: 5000.00
Interest rate:     0.05 (4.93%)
# of ann. periods: 4
Total years:       5
Annual Addition:   500.0
Starting year 1 with $5000.00
Year 1, period 1, accrued $61.62, value is now: $5061.62
Year 1, period 2, accrued $62.38, value is now: $5124.01
Year 1, period 3, accrued $63.15, value is now: $5187.16
Year 1, period 4, accrued $63.93, value is now: $5251.09
Added $500.00 for the end of year 1, total is now: 5751.09
Starting year 2 with $5751.09
Year 2, period 1, accrued $70.88, value is now: $5821.98
Year 2, period 2, accrued $71.76, value is now: $5893.73
Year 2, period 3, accrued $72.64, value is now: $5966.37
Year 2, period 4, accrued $73.54, value is now: $6039.91
Added $500.00 for the end of year 2, total is now: 6539.91
Starting year 3 with $6539.91
Year 3, period 1, accrued $80.60, value is now: $6620.51
Year 3, period 2, accrued $81.60, value is now: $6702.11
Year 3, period 3, accrued $82.60, value is now: $6784.71
Year 3, period 4, accrued $83.62, value is now: $6868.34
Added $500.00 for the end of year 3, total is now: 7368.34
Starting year 4 with $7368.34
Year 4, period 1, accrued $90.81, value is now: $7459.15
Year 4, period 2, accrued $91.93, value is now: $7551.08
Year 4, period 3, accrued $93.07, value is now: $7644.15
Year 4, period 4, accrued $94.21, value is now: $7738.37
Added $500.00 for the end of year 4, total is now: 8238.37
Starting year 5 with $8238.37
Year 5, period 1, accrued $101.54, value is now: $8339.90
Year 5, period 2, accrued $102.79, value is now: $8442.69
Year 5, period 3, accrued $104.06, value is now: $8546.75
Year 5, period 4, accrued $105.34, value is now: $8652.09
Added $500.00 for the end of year 5, total is now: 9152.09
Final value: $9152.09
</pre>

 [1]: /InterestCalculator.java