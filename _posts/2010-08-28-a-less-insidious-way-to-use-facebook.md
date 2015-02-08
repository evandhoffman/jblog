---
title: A less insidious way to use Facebook?
author: Evan Hoffman
layout: post
permalink: /2010/08/28/a-less-insidious-way-to-use-facebook/
dsq_thread_id:
  - 3019442797
categories:
  - Uncategorized
tags:
  - facebook
  - google
  - privacy
  - rant
  - rants
---
I deactivated my Facebook account a couple of months ago. I just kind of got tired of seeing silly updates from friends and &#8220;friends&#8221; &#8211; people I&#8217;d friended but wasn&#8217;t really friends with. I was also frustrated by the privacy implications of using such a service: you tell it about yourself, you tell it about who you know and how you know them, you keep adding more information about you and your friends to its huge brain that it&#8217;s free to use or abuse however it wants.

I don&#8217;t know if I&#8217;m anti-&#8220;Social&#8221; or just antisocial but most of the info streaming into my Facebook feed was just not interesting to me. I could have hidden those people, but then it seemed like it would make more sense simply to remove the connection to them, if I didn&#8217;t want to see their updates. I actually went through my list of connections and started removing people &#8211; people I knew from high school and hadn&#8217;t spoke to since then until they added me on Facebook, and then continued not talking to them, and other people who I knew but didn&#8217;t really interact with, online or offline. I didn&#8217;t really care about what they had to say and it occurred to me that they didn&#8217;t care what I had to say. Why did we friend even each other in the first place? Well, the friend suggester (suggestor?) makes it easy to friend people who are only tangentially related, since its whole purpose is to find new people for you to add.

I remember there was one person from school whom I hadn&#8217;t spoken to since probably 4th grade. This person attempted to friend me 5 times on FB (Soandso wants to be your friend&#8230;) and each time I clicked &#8220;Ignore,&#8221; but on the 6th time I finally relented. After 2 weeks of inane updates I unfriended the person. Within a month I was getting requests to refriend. Why? I don&#8217;t know you, you don&#8217;t know me, what&#8217;s to be gained by us pretending to be e-friends?

So I had some fundamental problems with Facebook. In addition to the friending of barely-friends, the feeding of so much information into the Facebook brain was starting to bother me. This is pretty similar to my [worries about Google&#8217;s reach][1]; basically every bit of information you post to Facebook to share with &#8220;friends&#8221; is also being added to Facebook&#8217;s marketing profile about you and your friends. The more you use the service, the more they know about you. And all those &#8220;Like&#8221; buttons all over the internet &#8211; a way for you to inform your Facebook friends that you like a blog post or news story &#8211; those are just a way for Facebook to know what sites you&#8217;re visiting. Whether you click the &#8220;like&#8221; button or not, your browser is loading the button of their servers, which means Facebook is reading your cookie and knows that YOU visited the page. This annoyed me so much that I edited my /etc/hosts file to redirect www.facebook.com to 127.0.0.1 (my own computer) where I&#8217;m running Apache, so the Like buttons just render as 404 errors now:

[<img src="http://evanhoffman.com/evan/wp-content/uploads/2010/08/NotFound.png" alt="" title="NotFound" width="655" height="159" class="aligncenter size-full wp-image-580" />][2]

But I&#8217;m fine with that. I&#8217;ve also set my browser to reject all cookies from *.facebook.com. I realize this is just a drop in the ocean of data for Facebook, but screw them. Even with my account disabled they were collecting data about me, and that just pissed me off. But much like Google, Facebook&#8217;s tracking ability transcends browsers and computers, since in order to use their service you need to log in, and thus your movements around the internet can be tracked regardless of which computer or device you&#8217;re using.

Facebook wasn&#8217;t a completely worthless service for me. I found the photo album feature very useful. It was a great way to upload pictures and share them instantly with whomever wanted to see them. In my case this was usually my family plus a few friends. I doubt anything will top Facebook for this because these people are already on Facebook, and for something to come along that&#8217;s better at this than Facebook, these people would need to move to the new platform, which as of today doesn&#8217;t seem likely.

Photo sharing is the one thing I miss. I haven&#8217;t stopped taking pictures but it&#8217;s a much clumsier process now to share them with people. I put them in an album in Picasa, upload it to PicasaWeb, set the permissions on the album, send out the invitations. The recipients then have to click on a private link to get to the pictures, and if they want to see them again in the future, they need to dig through their inbox to find the link and click on it again. Not everybody uses Gmail, and even for those who do, this is just a clunky process. With Facebook albums, if the album is shared with someone, all they have to do is click on me and then click on my list of albums to see the pictures. Easy. I&#8217;m considering returning to Facebook just to get the photo album back.

So I was thinking that if I could restrict myself to using only the Facebook iPhone app, I&#8217;d still be able to take the occasional picture with the phone, upload it for people to see, and not fall prey to the tracking cookie problems I described above, since (I&#8217;m assuming) the Facebook app and Safari don&#8217;t share data. At least, not yet.

That idea prompted me to write this post in the first place, but as I&#8217;ve been writing it it occurred to me that it&#8217;s not really a workable plan. If I&#8217;m using it I&#8217;ll eventually feel the need to login via browser, meaning I&#8217;ll have to tear down all the walls I&#8217;ve erected &#8211; the hosts file entry, the cookie blocking &#8211; and I&#8217;ll be right back where I was, feeding them all my info and letting them track me everywhere I go. So I guess it&#8217;s going to come down to a question of whether or not the costs outweigh the benefits, as it always does.

Unless I can just write a browser plugin to strip the &#8220;Like&#8221; button from non-Facebook websites. Maybe AdBlock can do this. Hmm&#8230; The dog woke me up early today and everyone else is asleep still, and this all sounded a lot better in my head before I started writing it down.

 [1]: /evan/?p=515
 [2]: http://evanhoffman.com/evan/wp-content/uploads/2010/08/NotFound.png