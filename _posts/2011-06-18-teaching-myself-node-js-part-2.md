---
title: 'Teaching myself node.js: Part 2'
author: Evan Hoffman
excerpt: More work learning node.
layout: post
permalink: /2011/06/18/teaching-myself-node-js-part-2/
categories:
  - Uncategorized
tags:
  - code
  - express
  - jade
  - javascript
  - mongo
  - mongodb
  - node
  - node.js
  - tutorial
---
I did a little more work today on my little node.js project and added the ability to click on a tag and have the app list all questions with that tag, and a super basic method for adding new questions. Neither of these were really complicated; the most &#8220;challenging&#8221; part was figuring out how to create links and form inputs with jade. In the end I was surprised at how easy it really was. 

For the tag search I just copied the `app.get('/questions')` route to a new `app.get('/tags/:tag')` route in <a href="https://github.com/evandhoffman/Quaro/blob/c5a7064648c87c89f7dcd5dec32cdaaa257479b1/app.js" onclick="_gaq.push(['_trackEvent','download','https://github.com/evandhoffman/Quaro/blob/c5a7064648c87c89f7dcd5dec32cdaaa257479b1/app.js']);" >app.js</a> and modified the mongo find() query to search for the tag. I created a <a href="https://github.com/evandhoffman/Quaro/blob/c5a7064648c87c89f7dcd5dec32cdaaa257479b1/views/taglist.jade" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://github.com/evandhoffman/Quaro/blob/c5a7064648c87c89f7dcd5dec32cdaaa257479b1/views/taglist.jade', 'new Jade template']);" >new Jade template</a> for this, but I suppose the same list.jade template would have worked, since the only thing that was changed was the contents of the `questions` array.

For adding a new question I created an `app.get('/question/new')` route that loaded a jade template with the form (<a href="https://github.com/evandhoffman/Quaro/blob/c5a7064648c87c89f7dcd5dec32cdaaa257479b1/views/addquestion.jade" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://github.com/evandhoffman/Quaro/blob/c5a7064648c87c89f7dcd5dec32cdaaa257479b1/views/addquestion.jade', 'addquestion.jade']);" >addquestion.jade</a>), and which did a POST back to /question and the corresponding `app.post('/question')`. Right now the form just takes text input fields, but eventually there&#8217;ll need to be some notion of users, and other fun functionality. Also after doing the insert into Mongo, it just spits back some plaintext response, but it gets the job done. 

I should add that this is my first time writing a webapp using &#8220;routes.&#8221; It&#8217;s been a while since I really did any front-end coding at all and I&#8217;m used to the &#8220;old school&#8221; method of one script per function, where the &#8220;add a new question&#8221; function would be handled by &#8220;addQuestion.php&#8221; for instance. Using routes and MVC are part of what I&#8217;m trying to learn in doing this.

Commit for this version is <a href="https://github.com/evandhoffman/Quaro/tree/c5a7064648c87c89f7dcd5dec32cdaaa257479b1" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://github.com/evandhoffman/Quaro/tree/c5a7064648c87c89f7dcd5dec32cdaaa257479b1', 'here']);" >here</a>