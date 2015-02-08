---
title: Displaying currently-playing iTunes track in the Mac menu bar
author: Evan Hoffman
layout: post
permalink: /2011/10/21/displaying-currently-playing-itunes-track-in-the-mac-menu-bar/
categories:
  - Uncategorized
tags:
  - app
  - cocoa
  - code
  - current track
  - iphone
  - itunes
  - mac
  - menulet
  - meta
  - music
  - programming
  - utility
  - whine
  - xcode
---
In an attempt to teach myself Objective C, and because I couldn&#8217;t find anything that did what I wanted, I wrote a little utility to display the currently-playing iTunes track in the Mac taskbar. Originally I had it display the full track name right in the taskbar but it was too much text for such a small space (especially on a 1440&#215;900 screen), so now you click a little musical note and it shows you the info in a menu. 

Here&#8217;s a screenshot:

<a href="http://www.evanhoffman.com/evan/2011/10/21/displaying-currently-playing-itunes-track-in-the-mac-menu-bar/screen-shot-2011-10-20-at-8-54-49-pm/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/2011/10/21/displaying-currently-playing-itunes-track-in-the-mac-menu-bar/screen-shot-2011-10-20-at-8-54-49-pm/', '']);"  rel="attachment wp-att-1642"><img src="http://www.evanhoffman.com/evan/wp-content/uploads/2011/10/Screen-shot-2011-10-20-at-8.54.49-PM.png" alt="" title="Screen shot 2011-10-20 at 8.54.49 PM" width="365" height="416" class="aligncenter size-full wp-image-1642" /></a>

The code is all in <a href="https://github.com/evandhoffman/iTunes-Song-Title" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://github.com/evandhoffman/iTunes-Song-Title', 'github']);" >github</a>. If you&#8217;re looking for a similar utility, and are brave enough to try my first-ever Obj-C app, you can download it <a href="http://www.evanhoffman.com/evan/2011/10/21/displaying-currently-playing-itunes-track-in-the-mac-menu-bar/itunes-current-track-app/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/2011/10/21/displaying-currently-playing-itunes-track-in-the-mac-menu-bar/itunes-current-track-app/', 'here.']);"  rel='attachment wp-att-1646'>here.</a> But the freshest version will probably be in the github project. 

As an aside, I was surprised at how easy it was to cobble this together having never written ObjC before. I found some good examples that I mostly ripped off, but it was still remarkably easy to have the app listen to iTunes for track changes, etc.