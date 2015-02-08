---
title: 'Who pays the most tax?  Who pays the least tax?'
author: Evan Hoffman
layout: post
permalink: /2011/01/31/who-pays-the-most-tax/
image:
  - 
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
dsq_thread_id:
  - 2949526447
categories:
  - Uncategorized
tags:
  - bracket
  - fun
  - irs
  - money
  - percentage
  - population
  - tax
  - taxes
---
Why do I care? Maybe I don&#8217;t. But this is fun anyway.

Go to the IRS.gov website to the <a href="http://www.irs.gov/taxstats/indtaxstats/article/0,,id=134951,00.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.irs.gov/taxstats/indtaxstats/article/0,,id=134951,00.html', 'SOI Tax Stats']);" >SOI Tax Stats</a> page.

Download one of the Excel spreadsheets under **Basic Tables: Returns Filed and Sources of Income**. I used <a href="http://www.irs.gov/pub/irs-soi/08in12ms.xls" onclick="_gaq.push(['_trackEvent','download','http://www.irs.gov/pub/irs-soi/08in12ms.xls']);" ><b>All Returns: Adjusted Gross Income, Exemptions, Deductions, and Tax Items</b> for 2008</a>.

Look at spreadsheet columns I through N. I &#038; J show the amount of taxable income in each bracket and the number of returns in that bracket. M &#038; N show the number of returns that had tax and the total amount of tax in each bracket.

At the top of column J, the number is 5,652,925,474. Dollar values are in thousands, so this is about $5.6 trillion of taxable income earned by US taxpayers in 2008. At the top of column N it shows that to total tax paid by all these people is 1,031,580,923, or about $1 trillion. So of all the taxable income earned in 2008, 18.2% was paid in federal income tax.

I added a new column, to the right of N, and in cell O10 entered the formula `=N10/N$9`. Then I formatted the column to show results as a percent. Column O now shows the percentage of tax paid by each income bracket:

<a href="http://evanhoffman.com/evan/wp-content/uploads/2011/01/percent-of-total.png" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://evanhoffman.com/evan/wp-content/uploads/2011/01/percent-of-total.png', '']);" ><img src="http://www.evanhoffman.com/evan/wp-content/uploads/2011/01/percent-of-total-300x127.png" alt="" title="percent of total" width="300" height="127" class="aligncenter size-medium wp-image-999" /></a>

I then added another column to the right of I, and in cell J10 entered the formula `=I10/I$9` and formatted it for a percent. Column J now shows the percentage of the population (of taxpayers) in each tax bracket:

<a href="http://evanhoffman.com/evan/wp-content/uploads/2011/01/percentage2.png" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://evanhoffman.com/evan/wp-content/uploads/2011/01/percentage2.png', '']);" ><img src="http://www.evanhoffman.com/evan/wp-content/uploads/2011/01/percentage2-300x152.png" alt="" title="percentage2" width="300" height="152" class="aligncenter size-medium wp-image-1001" /></a>

With these extra columns it&#8217;s pretty easy to see where the tax revenue comes from. Cells P23 through P28 sum to 33.24% of revenue. J23 through J28 sum to 0.8% &#8211; meaning that the top 0.8% of taxpayers (those with over $500k taxable income) really do account pay 33% of the tax in the USA. The next 62.2% of tax revenue is paid by 54.5% of the population &#8211; those between $40,000 and $500,000. The bottom 44.7% of the population pays 4.5% of the tax revenue.

The different population segments are color coded below:

<a href="http://evanhoffman.com/evan/wp-content/uploads/2011/01/color-coded.png" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://evanhoffman.com/evan/wp-content/uploads/2011/01/color-coded.png', '']);" ><img src="http://www.evanhoffman.com/evan/wp-content/uploads/2011/01/color-coded-300x132.png" alt="" title="color coded" width="300" height="132" class="aligncenter size-medium wp-image-1002" /></a>

It&#8217;s interesting (and strange) that the 13,400 people who made over $10m in 2008 contributed more revenue than the bottom 54.7% &#8211; 59,000,000 people.

The table with my additions is available here:

<a href="http://evanhoffman.com/evan/wp-content/uploads/2011/01/08in12ms.ods" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://evanhoffman.com/evan/wp-content/uploads/2011/01/08in12ms.ods', '08in12ms']);" >08in12ms</a>  
<!--more-->