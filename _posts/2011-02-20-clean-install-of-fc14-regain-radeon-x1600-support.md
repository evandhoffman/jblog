---
title: Clean install of FC14, regain Radeon X1600 support (!)
author: Evan Hoffman
layout: post
permalink: /2011/02/20/clean-install-of-fc14-regain-radeon-x1600-support/
categories:
  - Uncategorized
tags:
  - f14
  - fc14
  - fedora
  - radeon
  - 'RB554UT#ABA'
  - x1600
---
So I was <a href="http://www.evanhoffman.com/evan/?p=1010" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/?p=1010', 'complaining the other day about losing support for the Radeon X1600']);" >complaining the other day about losing support for the Radeon X1600</a> when I upgraded my <a href="http://www.superwarehouse.com/HP_Compaq_Business_Notebook_nc8430/RB554UTABA/p/1489320" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.superwarehouse.com/HP_Compaq_Business_Notebook_nc8430/RB554UTABA/p/1489320', 'work laptop']);" >work laptop</a> from FC12 to FC14. I was on the verge of ordering a new laptop (ThinkPad W510 with quadcore i7 and 1GB Quadro looks niiiice&#8230;) when I figured it was worth at least giving a true clean install a shot. I backed up all my stuff to an external USB drive, repartitioned my drive (finally done in a sane way), did a fresh FC14 install, and miraculously everything seemed to work. rsynced all my stuff back over and it was almost perfect. I still have a weird issue when trying to unlock my screen after it goes into screensaver &#8211; basically I can&#8217;t, it just stays locked and I&#8217;ve resorted to doing &#8220;init 3&#8243; and then &#8220;init 5&#8243; to make it work again. That&#8217;s a minor annoyance though at this point. So, yay again for Fedora! I wouldn&#8217;t go with ATi for a Linux machine again though.