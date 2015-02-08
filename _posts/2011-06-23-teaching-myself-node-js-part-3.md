---
title: 'Teaching myself node.js: Part 3'
author: Evan Hoffman
excerpt: "Notes on MongoDB's ObjectID vs sequential identifiers."
layout: post
permalink: /2011/06/23/teaching-myself-node-js-part-3/
categories:
  - Uncategorized
tags:
  - code
  - express
  - findandmodify
  - javascript
  - mongo
  - mongodb
  - mongoskin
  - mongoskin sequence
  - node
  - node.js
  - nodejs
  - objectid
  - programming
  - sequence
  - serial
  - tutorial
---
I&#8217;ve been playing around with node, JavaScript &#038; MongoDB a bit with this project and one of the things I ran into was MongoDB&#8217;s lack of a sequential ID. MongoDB defaults to <a href="http://www.mongodb.org/display/DOCS/Object+IDs#ObjectIDs-TheBSONObjectIdDatatype" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.mongodb.org/display/DOCS/Object+IDs#ObjectIDs-TheBSONObjectIdDatatype', 'ObjectIDs']);" >ObjectIDs</a> for primary keys in collections, and they have good reasons for doing so, but in writing this app, I&#8217;d rather have URLs look like &#8220;/question/423&#8243; than &#8220;/question/3001024e521e9c6500000000&#8243;. 

Fortunately, there&#8217;s a relatively convenient workaround for this problem, detailed <a href="http://www.mongodb.org/display/DOCS/Object+IDs#ObjectIDs-SequenceNumbers" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.mongodb.org/display/DOCS/Object+IDs#ObjectIDs-SequenceNumbers', 'here']);" >here</a>. Essentially, create a collection in which each document is a counter. The ID for the document is its &#8220;name&#8221; and the property &#8220;next&#8221; contains the next value. This is similar to creating and using a <a href="http://www.postgresql.org/docs/8.3/static/sql-createsequence.html" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.postgresql.org/docs/8.3/static/sql-createsequence.html', 'sequence']);" >sequence</a> in PostgreSQL, though hopefully in the future they&#8217;ll provide an easier way to do this &#8211; perhaps a built-in sequence object.

First, in the mongo shell, I created the `counter(name)` function as specified in the Mongo wiki:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="javascript" style="font-family:monospace;"><span style="color: #339933;">&gt;</span> counter                                        
<span style="color: #000066; font-weight: bold;">function</span> counter<span style="color: #009900;">&#40;</span>name<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
    <span style="color: #000066; font-weight: bold;">var</span> ret <span style="color: #339933;">=</span> db.<span style="color: #660066;">counters</span>.<span style="color: #660066;">findAndModify</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#123;</span>query<span style="color: #339933;">:</span><span style="color: #009900;">&#123;</span>_id<span style="color: #339933;">:</span>name<span style="color: #009900;">&#125;</span><span style="color: #339933;">,</span> update<span style="color: #339933;">:</span><span style="color: #009900;">&#123;</span>$inc<span style="color: #339933;">:</span><span style="color: #009900;">&#123;</span>next<span style="color: #339933;">:</span><span style="color: #CC0000;">1</span><span style="color: #009900;">&#125;</span><span style="color: #009900;">&#125;</span><span style="color: #339933;">,</span> <span style="color: #3366CC;">'new'</span><span style="color: #339933;">:</span><span style="color: #003366; font-weight: bold;">true</span><span style="color: #339933;">,</span> upsert<span style="color: #339933;">:</span><span style="color: #003366; font-weight: bold;">true</span><span style="color: #009900;">&#125;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
    <span style="color: #000066; font-weight: bold;">return</span> ret.<span style="color: #660066;">next</span><span style="color: #339933;">;</span>
<span style="color: #009900;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>

Then I created the counter document for the &#8220;questions&#8221; collection (so questions can have numeric IDs):

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="javascript" style="font-family:monospace;">db.<span style="color: #660066;">counters</span>.<span style="color: #660066;">insert</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#123;</span>_id<span style="color: #339933;">:</span><span style="color: #3366CC;">"questions"</span><span style="color: #339933;">,</span> next<span style="color: #339933;">:</span> <span style="color: #CC0000;">1</span><span style="color: #009900;">&#125;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></pre>
      </td>
    </tr>
  </table>
</div>

This is optional &#8211; the counter() function will create the document in the counters collection if needed, with an initial value of 1. 

That&#8217;s it. Each call to counter() will now return an incrementing number:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="javascript" style="font-family:monospace;"><span style="color: #339933;">&gt;</span> db.<span style="color: #660066;">counters</span>.<span style="color: #660066;">find</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
<span style="color: #009900;">&#123;</span> <span style="color: #3366CC;">"_id"</span> <span style="color: #339933;">:</span> <span style="color: #3366CC;">"questions"</span><span style="color: #339933;">,</span> <span style="color: #3366CC;">"next"</span> <span style="color: #339933;">:</span> <span style="color: #CC0000;">3</span> <span style="color: #009900;">&#125;</span>
<span style="color: #339933;">&gt;</span> counter<span style="color: #009900;">&#40;</span><span style="color: #3366CC;">"joe"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>    
<span style="color: #CC0000;">1</span>
<span style="color: #339933;">&gt;</span> db.<span style="color: #660066;">counters</span>.<span style="color: #660066;">find</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
<span style="color: #009900;">&#123;</span> <span style="color: #3366CC;">"_id"</span> <span style="color: #339933;">:</span> <span style="color: #3366CC;">"questions"</span><span style="color: #339933;">,</span> <span style="color: #3366CC;">"next"</span> <span style="color: #339933;">:</span> <span style="color: #CC0000;">3</span> <span style="color: #009900;">&#125;</span>
<span style="color: #009900;">&#123;</span> <span style="color: #3366CC;">"_id"</span> <span style="color: #339933;">:</span> <span style="color: #3366CC;">"joe"</span><span style="color: #339933;">,</span> <span style="color: #3366CC;">"next"</span> <span style="color: #339933;">:</span> <span style="color: #CC0000;">1</span> <span style="color: #009900;">&#125;</span>
<span style="color: #339933;">&gt;</span> counter<span style="color: #009900;">&#40;</span><span style="color: #3366CC;">"joe"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>    
<span style="color: #CC0000;">2</span>
<span style="color: #339933;">&gt;</span> counter<span style="color: #009900;">&#40;</span><span style="color: #3366CC;">"joe"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
<span style="color: #CC0000;">3</span></pre>
      </td>
    </tr>
  </table>
</div>

Cool. So how do we save the counter() function in the DB so we can use it in queries without having to define it every time? Fortunately Mongo makes <a href="http://www.mongodb.org/display/DOCS/Server-side+Code+Execution#Server-sideCodeExecution-Storingfunctionsserverside" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.mongodb.org/display/DOCS/Server-side+Code+Execution#Server-sideCodeExecution-Storingfunctionsserverside', 'saving functions on the server']);" >saving functions on the server</a> easy:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="javascript" style="font-family:monospace;"><span style="color: #339933;">&gt;</span> db.<span style="color: #660066;">system</span>.<span style="color: #660066;">js</span>.<span style="color: #660066;">save</span><span style="color: #009900;">&#40;</span> <span style="color: #009900;">&#123;</span> _id <span style="color: #339933;">:</span> <span style="color: #3366CC;">"counter"</span> <span style="color: #339933;">,</span> value <span style="color: #339933;">:</span> counter <span style="color: #009900;">&#125;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
<span style="color: #339933;">&gt;</span> db.<span style="color: #660066;">system</span>.<span style="color: #660066;">js</span>.<span style="color: #660066;">find</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
<span style="color: #009900;">&#123;</span> <span style="color: #3366CC;">"_id"</span> <span style="color: #339933;">:</span> <span style="color: #3366CC;">"counter"</span><span style="color: #339933;">,</span> <span style="color: #3366CC;">"value"</span> <span style="color: #339933;">:</span> <span style="color: #000066; font-weight: bold;">function</span> cf__2__f_counter<span style="color: #009900;">&#40;</span>name<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
    <span style="color: #000066; font-weight: bold;">var</span> ret <span style="color: #339933;">=</span> db.<span style="color: #660066;">counters</span>.<span style="color: #660066;">findAndModify</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#123;</span>query<span style="color: #339933;">:</span><span style="color: #009900;">&#123;</span>_id<span style="color: #339933;">:</span>name<span style="color: #009900;">&#125;</span><span style="color: #339933;">,</span> update<span style="color: #339933;">:</span><span style="color: #009900;">&#123;</span>$inc<span style="color: #339933;">:</span><span style="color: #009900;">&#123;</span>next<span style="color: #339933;">:</span><span style="color: #CC0000;">1</span><span style="color: #009900;">&#125;</span><span style="color: #009900;">&#125;</span><span style="color: #339933;">,</span> <span style="color: #3366CC;">'new'</span><span style="color: #339933;">:</span><span style="color: #003366; font-weight: bold;">true</span><span style="color: #339933;">,</span> upsert<span style="color: #339933;">:</span><span style="color: #003366; font-weight: bold;">true</span><span style="color: #009900;">&#125;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
    <span style="color: #000066; font-weight: bold;">return</span> ret.<span style="color: #660066;">next</span><span style="color: #339933;">;</span>
<span style="color: #009900;">&#125;</span> <span style="color: #009900;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>

Calling the stored procedure directly in the shell is sort of strange, but it works:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="javascript" style="font-family:monospace;"><span style="color: #339933;">&gt;</span> db.<span style="color: #660066;">eval</span><span style="color: #009900;">&#40;</span><span style="color: #3366CC;">"return counter('question')"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
<span style="color: #CC0000;">3</span>
<span style="color: #339933;">&gt;</span> db.<span style="color: #660066;">eval</span><span style="color: #009900;">&#40;</span><span style="color: #3366CC;">"return counter('question')"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
<span style="color: #CC0000;">4</span>
<span style="color: #339933;">&gt;</span> bye
<span style="color: #009900;">&#91;</span>Wed Jun <span style="color: #CC0000;">22</span> <span style="color: #CC0000;">13</span><span style="color: #339933;">:</span><span style="color: #CC0000;">14</span><span style="color: #339933;">:</span><span style="color: #CC0000;">10</span> evan<span style="color: #339933;">@</span>EvanMBP <span style="color: #CC0000;">3</span> ~<span style="color: #009900;">&#93;</span>$ ~<span style="color: #339933;">/</span>Downloads<span style="color: #339933;">/</span>mongodb<span style="color: #339933;">-</span>osx<span style="color: #339933;">-</span>x86_64<span style="color: #339933;">-</span>1.8.1<span style="color: #339933;">/</span>bin<span style="color: #339933;">/</span>mongo questionsMongoDB shell version<span style="color: #339933;">:</span> 1.8.1
connecting to<span style="color: #339933;">:</span> questions
<span style="color: #339933;">&gt;</span> db.<span style="color: #660066;">eval</span><span style="color: #009900;">&#40;</span><span style="color: #3366CC;">"return counter('question')"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
<span style="color: #CC0000;">5</span></pre>
      </td>
    </tr>
  </table>
</div>

Unfortunately, I was unable to figure out how to call the stored function from within the <a href="https://github.com/guileen/node-mongoskin" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://github.com/guileen/node-mongoskin', 'mongoskin']);" >mongoskin</a> driver, and ended up writing analogous code, calling `findAndModify()` within the app code. <a href="https://github.com/evandhoffman/Quaro/blob/a8cbaa302760a210abe17f5cade5af521e2be9c3/app.js#L145" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://github.com/evandhoffman/Quaro/blob/a8cbaa302760a210abe17f5cade5af521e2be9c3/app.js#L145', 'I finally got it:']);" >I finally got it:</a>

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="javascript" style="font-family:monospace;">        db.<span style="color: #660066;">collection</span><span style="color: #009900;">&#40;</span><span style="color: #3366CC;">'counters'</span><span style="color: #009900;">&#41;</span>.<span style="color: #660066;">findAndModify</span><span style="color: #009900;">&#40;</span>
                <span style="color: #009900;">&#123;</span>_id<span style="color: #339933;">:</span><span style="color: #3366CC;">'questions'</span><span style="color: #009900;">&#125;</span><span style="color: #339933;">,</span> 
                <span style="color: #009900;">&#91;</span><span style="color: #009900;">&#93;</span><span style="color: #339933;">,</span> 
                <span style="color: #009900;">&#123;</span>$inc <span style="color: #339933;">:</span> <span style="color: #009900;">&#123;</span>next<span style="color: #339933;">:</span> <span style="color: #CC0000;">1</span><span style="color: #009900;">&#125;</span><span style="color: #009900;">&#125;</span><span style="color: #339933;">,</span>
                <span style="color: #003366; font-weight: bold;">true</span><span style="color: #339933;">,</span> 
                <span style="color: #003366; font-weight: bold;">true</span><span style="color: #339933;">,</span>
                <span style="color: #000066; font-weight: bold;">function</span><span style="color: #009900;">&#40;</span>err<span style="color: #339933;">,</span> counter<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
                        <span style="color: #000066; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span>err<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span> <span style="color: #000066; font-weight: bold;">throw</span> <span style="color: #000066; font-weight: bold;">new</span> Error<span style="color: #009900;">&#40;</span>err<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span> <span style="color: #009900;">&#125;</span>
                        <span style="color: #000066; font-weight: bold;">var</span> ins <span style="color: #339933;">=</span> <span style="color: #009900;">&#123;</span> date<span style="color: #339933;">:</span> <span style="color: #000066; font-weight: bold;">new</span> <span style="">Date</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">,</span>
                                author<span style="color: #339933;">:</span> req.<span style="color: #660066;">body</span>.<span style="color: #660066;">author</span><span style="color: #339933;">,</span> body<span style="color: #339933;">:</span> req.<span style="color: #660066;">body</span>.<span style="color: #660066;">body</span><span style="color: #339933;">,</span>
                                tags<span style="color: #339933;">:</span> tags<span style="color: #339933;">,</span> tag_count<span style="color: #339933;">:</span> tagCount<span style="color: #339933;">,</span>
                                answers<span style="color: #339933;">:</span> <span style="color: #009900;">&#91;</span><span style="color: #009900;">&#93;</span><span style="color: #339933;">,</span> votes<span style="color: #339933;">:</span> <span style="color: #CC0000;"></span><span style="color: #339933;">,</span>
                                _id<span style="color: #339933;">:</span> counter.<span style="color: #660066;">next</span>
                        <span style="color: #009900;">&#125;</span><span style="color: #339933;">;</span>  
                        db.<span style="color: #660066;">collection</span><span style="color: #009900;">&#40;</span><span style="color: #3366CC;">'questions'</span><span style="color: #009900;">&#41;</span>.<span style="color: #660066;">insert</span><span style="color: #009900;">&#40;</span>ins<span style="color: #339933;">,</span> <span style="color: #009900;">&#123;</span><span style="color: #009900;">&#125;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                        res.<span style="color: #660066;">end</span><span style="color: #009900;">&#40;</span><span style="color: #3366CC;">'Added new question: '</span><span style="color: #339933;">+</span>req.<span style="color: #660066;">body</span>.<span style="color: #660066;">body</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                <span style="color: #009900;">&#125;</span>   
        <span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></pre>
      </td>
    </tr>
  </table>
</div>

<figure id="attachment_1362" style="width: 394px;" class="wp-caption aligncenter"><a href="http://www.evanhoffman.com/evan/2011/06/23/teaching-myself-node-js-part-3/question-detail2/" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.evanhoffman.com/evan/2011/06/23/teaching-myself-node-js-part-3/question-detail2/', '']);"  rel="attachment wp-att-1362"><img src="http://www.evanhoffman.com/evan/wp-content/uploads/2011/06/question-detail2.png" alt="Question detail" title="Question detail" width="394" height="308" class="size-full wp-image-1362" /></a><figcaption class="wp-caption-text">Question detail</figcaption></figure>  
I tried throwing some code in there to query by either ObjectId() or integer but it didn&#8217;t really work <ins datetime="2011-06-24T01:16:32+00:00">(Edit: I tried this again on Linux and it worked fine)</ins>, so I just deleted all the documents with ObjectIds and everything seems to work fine now. I think the next step is going to have to be some sort of authentication/session stuff, because typing your name in every time kind of sucks (and makes it hard to do things like list questions by user).

Full code for this revision is <a href="https://github.com/evandhoffman/Quaro/tree/a8cbaa302760a210abe17f5cade5af521e2be9c3" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'https://github.com/evandhoffman/Quaro/tree/a8cbaa302760a210abe17f5cade5af521e2be9c3', 'here']);" >here</a>.