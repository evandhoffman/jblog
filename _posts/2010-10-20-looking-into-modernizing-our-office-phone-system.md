---
title: Looking into modernizing our office phone system
author: Evan Hoffman
layout: post
permalink: /2010/10/20/looking-into-modernizing-our-office-phone-system/
dsq_thread_id:
  - 2949525604
categories:
  - Uncategorized
tags:
  - asterisk
  - pbx
  - phone
  - trixbox
  - work
---
I&#8217;m researching replacing our current office phone system with a modern one. Currently we have a pretty old system (not managed by my department): POTS lines into an old PBX, old Toshiba <a href="http://www.ttechnologyinc.com/phonesystems/toshiba/dkt2010sd.asp" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.ttechnologyinc.com/phonesystems/toshiba/dkt2010sd.asp', 'DKT2010 handsets']);" >DKT2010 handsets</a>, lousy voicemail, and no direct-dial to each desk. You have to call in, talk to the receptionist, and get transferred. We also have no caller ID, and when you move from desk A to desk B, your extension changes from 305 to 309 (or whatever) &#8211; your extension is not bound to the phone, but to the jack. In addition to being retarded, this means your business cards now have the wrong extension on them

If I&#8217;m going to be taking over the phone system then I&#8217;m replacing it. I don&#8217;t know anything about phone systems except that VoIP and Asterisk are my starting points. Here&#8217;s what I want:

  * <a href="http://en.wikipedia.org/wiki/Direct_inward_dialing" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://en.wikipedia.org/wiki/Direct_inward_dialing', 'DID']);" >DID</a> to each desk 
      * Caller ID 
          * Voicemail emailed to your inbox as MP3/WAV 
              * Jabber integration if possible &#8211; if your Jabber status is &#8220;away&#8221; the call goes straight to voicemail. 
                  * Extension bound to the phone&#8230; this seems like an easy one, since DHCP already does this for computers. 
                      * Faxes delivered to users&#8217; inboxes &#8211; complete email/fax conversion would be idea. 
                    We have Exchange 2010 which has Unified Messaging; I don&#8217;t know if we&#8217;d need to use that but I guess it&#8217;s an option. A friend pointed me at MS <a href="http://www.microsoft.com/responsepoint/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.microsoft.com/responsepoint/', 'Response Point']);" >Response Point</a>, which I was going to look at, but Microsoft apparently ended that product, so I didn&#8217;t bother.
                    
                    Currently I&#8217;m checking out <a href="http://www.trixbox.com/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.trixbox.com/', 'Trixbox']);" >Trixbox</a>, specifically their <a href="http://www.trixbox.com/products/appliance/specifications" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.trixbox.com/products/appliance/specifications', 'trixbox appliance']);" >trixbox appliance</a>. Though, I usually like to &#8220;roll my own&#8221; box when first playing around with new technology to learn how it works.