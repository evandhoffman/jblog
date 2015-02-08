---
title: When the admin of your online community dies
author: Evan Hoffman
layout: post
permalink: /2012/03/13/when-the-admin-of-your-online-community-dies/
image:
  - 
seo_follow:
  - 'false'
seo_noindex:
  - 'false'
categories:
  - Uncategorized
tags:
  - everquest
  - gaming
  - meta
  - phpbb
  - tarew marr
  - wow
---
In 2000 or 2001 I started playing EverQuest. I was relatively late to the game, joining after the 3rd expansion (Luclin) was already out. I played it for a few years, at one point obsessively. In truth, I was never really that good, but I was in a guild that I loved and for the most part we all had fun. Like in other MMOs, when you start playing EverQuest you need to choose which server to play on. Basically I picked a random one to create my character on: Tarew Marr (most servers in EQ were named after the deities in the game).  
<!--more-->

  
For anyone unfamiliar with MMO games (as I was at the time), your server choice affects almost every aspect of your gameplay experience. Each game server is essentially a parallel universe with the same buildings, NPCs (characters), quests, and items, but completely different sets of actual human beings inhabiting them. Servers develop their own personalities as a result of the players inhabiting them. Like any community, there are nice people, morons, trolls, elite players, people who totally sucked at the game, grownups, children, racists and any other variety of person you can imagine. This, of course, is exactly the appeal of any MMO. You can play a single-player game in complete safety, but when you add 2000 random strangers in to the mix things get pretty interesting. There was plenty of drama, and Tarew Marr was actually one of the crazier servers.

What&#8217;s probably unsurprising in 2012, but I found pretty strange in 2001, is that there was an &#8220;offline&#8221; community for Tarew Marr in the form of a phpBB message board. Once again, I found the site pretty late but the forum was addictive. Server goings-on were discussed there and other general topics were incorporated daily. It was a self-selecting group of people with ages ranging from 15 to 50 (more or less) from all over the world whose only real commonality was playing Everquest on the same server. People came and went but the community was relatively cohesive. Eventually the phpBB forum became more interesting than the game it was created to serve. As people stopped playing EQ and moved to other games (or just gave up gaming entirely), they kept their roots in the forum. There was plenty of drama in the forum as well, with the requisite crazy people, trolls and idiots making life interesting for everybody.

When World of Warcraft came out, I too quit EverQuest. I played WoW for a few years and eventually quit that as well, all the while regularly checking the forum, which had long since lost its gaming focus, and just became an &#8220;internet community&#8221; of unique people. The site itself underwent several incarnations as the domain in one case was sold by the admin, and in another the subsequent admin got tired of running the site and shut it all down. The most recent incarnation was hosted by a member of the community who acquired the MySQL dump of the DB from the previous admin. Once everything was back up and running, it was nice to be &#8220;back.&#8221; It&#8217;s hard having a community ripped away.

Things went ok for a few years. The newest admin was a good guy who knew what he was doing. In response to a flood of spam accounts that were created, he ended up disabling registrations (each account had to be manually approved by him). Since our site was closed to the outside world (you had to be logged in to see anything) and the game that was the original focus of the site was <a href="http://www.google.com/insights/search/#q=everquest&#038;cmpt=q" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.google.com/insights/search/#q=everquest&cmpt=q', 'basically dead']);" >basically dead</a>, this pretty much guaranteed we&#8217;d never have any new members in our community. This was kind of sad, but there wasn&#8217;t really anything anyone could do about it.

Then one day last year I went to the site and it was down with a message from Hostgator saying &#8220;Hey, your account&#8217;s expired.&#8221; It was like that for a few days. Eventually people got concerned and after some digging, it was discovered that the admin had killed himself. Another member of the forum contacted Hostgator and paid to turn the site back on. He tried to get admin access to the Hostgator account but apparently that requires the original user&#8217;s credit card info, so basically we were out of luck. We all contributed via paypal and kept the site up indefinitely (in the now-deceased admin&#8217;s name) until we could hopefully get admin access to either phpBB or MySQL and get a mysqldump and move it to a new site. 

Unfortunately, this didn&#8217;t happen in time, and this morning the domain expired in Godaddy. I don&#8217;t know if Godaddy will allow someone else to pay the bill, but I figured now was a good time to see if there was a way to download an archived version of the site. That way, even if the site is gone forever, at least the history will exist somewhere. I tried this a few months ago with some flags to `wget --mirror` but wget would get stuck in this endless loop appending new session IDs and save hundreds of copies of each page/post/topic. 

What I ended up doing that&#8217;s actually successful is iterating over the topic IDs starting at 1 up to the maximum (that I know of) and saving the output to disk. My script for doing this is below. It might not be ideal but it does get the job done for me. 

But on a more &#8220;meta&#8221; level, this is a really strange situation. There&#8217;s a community of 30-40 people relatively used to each other. We could move to another site, but many people didn&#8217;t want to do this because they didn&#8217;t want to lose the history. Maybe now that I have the site archived, people will feel more comfortable moving. I&#8217;m still able to access the site because, being the nerd that I am, I did a `dig` against Hostgator&#8217;s nameservers, which still had the correct IP address of the site, and like other shared hosting platforms you can access the site via `http://(ip address)/~(username)/ `, and that&#8217;s how my perl script is able to fetch the data. But the other 30-40 people aren&#8217;t going to have any idea how to do that, so even if we move to a new site now, most people won&#8217;t know what that new site is. I suppose we could buy an ad in AdWords so if people search for the old site name the new one comes up, but that seems like a mess.

What a strange 21st-century problem. I imagine this has happened before in other online communities; the founder dies, or just leaves and the rest of the community is left just kind of drifting. Anyway, here&#8217;s my script. In order to fetch actual data as a logged-in user, I used <a href="https://addons.mozilla.org/en-US/firefox/addon/live-http-headers/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://addons.mozilla.org/en-US/firefox/addon/live-http-headers/', 'LiveHTTPHeaders']);" >LiveHTTPHeaders</a> to see what headers my browser was actually sending and copied them directly into the script. You&#8217;ll need at least your SID. For whatever reason, when I logged in via IP address, my browser wasn&#8217;t sending any `Cookie:` headers, so those aren&#8217;t included in the script.