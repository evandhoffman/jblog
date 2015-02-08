---
title: 'Teaching myself node.js: Part 1'
author: Evan Hoffman
excerpt: In which I try to learn node.js by building a Quora-like site (since it seems everyone is building a blog).
layout: post
permalink: /2011/06/14/teaching-myself-node-js-part-1/
categories:
  - Uncategorized
tags:
  - code
  - devops
  - javascript
  - linux
  - mongo
  - mongodb
  - node
  - node.js
  - nodejs
  - npm
  - programming
  - tutorial
  - work
---
I&#8217;ve been meaning for a while to learn node.js. I read a couple of books and plenty of blog posts but in the end I find, as usual, I can only learn by doing. I have most of the fundamentals to get started, I just need to have a project. The typical tutorials I&#8217;ve found focus on creating a blog; I&#8217;m thinking of something more along the lines of a Quora question/answer site. I&#8217;ll be trying to write this as I go, which will hopefully prod me into actually doing it this time. (I have a tendency to bounce around different tenses and I expect this will be worse than usual &#8211; stuff will likely be written in past/present/future at various points).  
<!--more-->

  
Installing node is pretty easy, I just <a href="http://nodejs.org/dist/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://nodejs.org/dist/', 'downloaded the source']);" >downloaded the source</a> (I used 0.4.7), configured &#038; compiled it. For MongoDB I use the <a href="http://www.mongodb.org/display/DOCS/CentOS+and+Fedora+Packages" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.mongodb.org/display/DOCS/CentOS+and+Fedora+Packages', '10gen']);" >10gen</a> CentOS/Fedora repository. With node installed, I installed <a href="http://npmjs.org/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://npmjs.org/', 'npm']);" >npm</a> to facilitate adding other packages. I started mongod with 

<pre>/etc/init.d/mongodb start</pre>

With everything installed and mongod running I thought about the schema of my object. One of the coolest things about MongoDB is the fact that it&#8217;s &#8220;schemaless&#8221; and you can basically put anything into the DB, so you just need to think about what your objects look like and you can basically shove them into Mongo in that exact format. I&#8217;ve never been a fan of ORM stuff for SQL and the Mongo document-store type of DB seems like it eliminates the need for that almost entirely, which is great. So here&#8217;s what I was thinking would be the central object in this q-a site:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="line_numbers">
        <pre>1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
</pre>
      </td>
      
      <td class="code">
        <pre class="javascript" style="font-family:monospace;"><span style="color: #009900;">&#123;</span>
        <span style="color: #3366CC;">"_id"</span> <span style="color: #339933;">:</span> ObjectId<span style="color: #009900;">&#40;</span><span style="color: #3366CC;">"4defd2713fce2e9bc35d09f0"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">,</span>
        <span style="color: #3366CC;">"author"</span> <span style="color: #339933;">:</span> <span style="color: #3366CC;">"Bob"</span><span style="color: #339933;">,</span>
        <span style="color: #3366CC;">"date"</span> <span style="color: #339933;">:</span> ISODate<span style="color: #009900;">&#40;</span><span style="color: #3366CC;">"2011-06-08T19:49:48.809Z"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">,</span>
        <span style="color: #3366CC;">"body"</span> <span style="color: #339933;">:</span> <span style="color: #3366CC;">"Is it hot in here?"</span><span style="color: #339933;">,</span>
        <span style="color: #3366CC;">"votes"</span> <span style="color: #339933;">:</span> <span style="color: #CC0000;"></span><span style="color: #339933;">,</span>
        <span style="color: #3366CC;">"answers"</span> <span style="color: #339933;">:</span> <span style="color: #009900;">&#91;</span>
                <span style="color: #009900;">&#123;</span>
                        <span style="color: #3366CC;">"author"</span> <span style="color: #339933;">:</span> <span style="color: #3366CC;">"evan"</span><span style="color: #339933;">,</span>
                        <span style="color: #3366CC;">"date"</span> <span style="color: #339933;">:</span> ISODate<span style="color: #009900;">&#40;</span><span style="color: #3366CC;">"2011-06-08T19:48:54.388Z"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">,</span>
                        <span style="color: #3366CC;">"body"</span> <span style="color: #339933;">:</span> <span style="color: #3366CC;">"I think so."</span><span style="color: #339933;">,</span>
                        <span style="color: #3366CC;">"votes"</span> <span style="color: #339933;">:</span> <span style="color: #CC0000;"></span>
                <span style="color: #009900;">&#125;</span><span style="color: #339933;">,</span>
                <span style="color: #009900;">&#123;</span>
                        <span style="color: #3366CC;">"author"</span> <span style="color: #339933;">:</span> <span style="color: #3366CC;">"Jim"</span><span style="color: #339933;">,</span>
                        <span style="color: #3366CC;">"date"</span> <span style="color: #339933;">:</span> ISODate<span style="color: #009900;">&#40;</span><span style="color: #3366CC;">"2011-06-08T19:51:04.714Z"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">,</span>
                        <span style="color: #3366CC;">"body"</span> <span style="color: #339933;">:</span> <span style="color: #3366CC;">"Could be."</span><span style="color: #339933;">,</span>
                        <span style="color: #3366CC;">"votes"</span> <span style="color: #339933;">:</span> <span style="color: #CC0000;"></span>
                <span style="color: #009900;">&#125;</span><span style="color: #339933;">,</span>
                <span style="color: #009900;">&#123;</span>
                        <span style="color: #3366CC;">"author"</span> <span style="color: #339933;">:</span> <span style="color: #3366CC;">"Aaron"</span><span style="color: #339933;">,</span>
                        <span style="color: #3366CC;">"date"</span> <span style="color: #339933;">:</span> ISODate<span style="color: #009900;">&#40;</span><span style="color: #3366CC;">"2011-06-08T19:51:33.313Z"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">,</span>
                        <span style="color: #3366CC;">"body"</span> <span style="color: #339933;">:</span> <span style="color: #3366CC;">"No"</span><span style="color: #339933;">,</span>
                        <span style="color: #3366CC;">"votes"</span> <span style="color: #339933;">:</span> <span style="color: #CC0000;"></span>
                <span style="color: #009900;">&#125;</span>
        <span style="color: #009900;">&#93;</span><span style="color: #339933;">,</span>
        <span style="color: #3366CC;">"tags"</span> <span style="color: #339933;">:</span> <span style="color: #009900;">&#91;</span>
                <span style="color: #3366CC;">"question"</span><span style="color: #339933;">,</span>
                <span style="color: #3366CC;">"silly"</span><span style="color: #339933;">,</span>
                <span style="color: #3366CC;">"stupid"</span><span style="color: #339933;">,</span>
                <span style="color: #3366CC;">"microsoft"</span>
        <span style="color: #009900;">&#93;</span>
<span style="color: #009900;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>

So this is a &#8220;question&#8221; and it has an author, a date, an array of tags, a title, a vote count, a &#8220;body&#8221; (maybe a longer version of the question), and an array of &#8220;answers&#8221;. Answers are embedded objects, each with an author, date, body, and votes. This seems like a decent place to start.

The way the site would work is someone posts a question and other people answer it. The answers would just be attached to the post in the &#8220;answers&#8221; array. Other users can vote on answers and the answer with the most votes would be the &#8220;winner&#8221;. Not exactly groundbreaking, but seems like it should be pretty easy to implement.

I used npm to install the following packages to aid in development:

<pre>$ npm install express jade mongoskin underscore</pre>

Express is a framework that handles request routing and jade is a templating framework. I used <a href="http://jekjek1989.wordpress.com/2011/03/06/express-with-jade-on-node-js/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://jekjek1989.wordpress.com/2011/03/06/express-with-jade-on-node-js/', 'this quick walkthrough']);" >this quick walkthrough</a> to setup a basic express/jade app that works and demonstrates most of what I need to know to get this started. Another good tutorial is <a href="http://dailyjs.com/2010/11/08/node-tutorial-2/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://dailyjs.com/2010/11/08/node-tutorial-2/', 'http://dailyjs.com/2010/11/08/node-tutorial-2/']);" >http://dailyjs.com/2010/11/08/node-tutorial-2/</a>.

After some mucking around I figured out how to use Express properly and it made things much easier. I used mongoskin as the MongoDB library and jade for the template and managed to rig up something that responds to a &#8220;list&#8221; request at http://server:3000/questions by listing all the questions in the &#8220;questions&#8221; collection. My code as of this point is available on github <a href="https://github.com/evandhoffman/Quaro/tree/f1ea545719c2284d3993f54592eb276563dc1c8d" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://github.com/evandhoffman/Quaro/tree/f1ea545719c2284d3993f54592eb276563dc1c8d', 'here']);" >here</a>. The <a href="https://github.com/evandhoffman/Quaro/blob/f1ea545719c2284d3993f54592eb276563dc1c8d/views/list.jade" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://github.com/evandhoffman/Quaro/blob/f1ea545719c2284d3993f54592eb276563dc1c8d/views/list.jade', 'views/list.jade']);" >views/list.jade</a> has the template for the question list, and the code that calls the template is in <a href="https://github.com/evandhoffman/Quaro/blob/f1ea545719c2284d3993f54592eb276563dc1c8d/app.js#L47" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://github.com/evandhoffman/Quaro/blob/f1ea545719c2284d3993f54592eb276563dc1c8d/app.js#L47', 'app.js']);" >app.js</a>.

When viewing in a web browser, it currently looks like this (YMMV based on what&#8217;s in your mongodb, of course):<figure id="attachment_1335" style="width: 646px;" class="wp-caption aligncenter">

<a href="http://www.evanhoffman.com/evan/2011/06/14/teaching-myself-node-js-part-1/fullscreen-capture-6142011-124430-am/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/2011/06/14/teaching-myself-node-js-part-1/fullscreen-capture-6142011-124430-am/', '']);"  rel="attachment wp-att-1335"><img src="http://www.evanhoffman.com/evan/wp-content/uploads/2011/06/Fullscreen-capture-6142011-124430-AM.jpg" alt="nodeQuestionList.jpg" title="nodeQuestionList.jpg" width="646" height="609" class="size-full wp-image-1335" /></a><figcaption class="wp-caption-text">nodeQuestionList.jpg</figcaption></figure> 

That&#8217;s enough for today. So far I&#8217;m enjoying node.js now that I think I can finally wrap my head around the callback stuff. Hopefully I can keep up the momentum.