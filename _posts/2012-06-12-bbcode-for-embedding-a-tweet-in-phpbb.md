---
title: BBCode for embedding a tweet in phpBB
author: Evan Hoffman
layout: post
permalink: /2012/06/12/bbcode-for-embedding-a-tweet-in-phpbb/
dsq_thread_id:
  - 2949517548
categories:
  - Uncategorized
tags:
  - bbcode
  - embed
  - phpbb
  - twitter
---
Probably not perfect, but it works.

BBCode:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="javascript" style="font-family:monospace;"><span style="color: #009900;">&#91;</span>tweet<span style="color: #009900;">&#93;</span><span style="color: #009900;">&#123;</span>IDENTIFIER<span style="color: #009900;">&#125;</span><span style="color: #009900;">&#91;</span><span style="color: #339933;">/</span>tweet<span style="color: #009900;">&#93;</span></pre>
      </td>
    </tr>
  </table>
</div>

Replacement:

<div class="wp_syntax">
  <table>
    <tr>
      <td class="code">
        <pre class="javascript" style="font-family:monospace;"><span style="color: #339933;">&lt;</span>script type<span style="color: #339933;">=</span><span style="color: #3366CC;">"text/javascript"</span><span style="color: #339933;">&gt;</span>
<span style="color: #000066; font-weight: bold;">function</span> loadx<span style="color: #009900;">&#40;</span>data<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
	document.<span style="color: #660066;">write</span><span style="color: #009900;">&#40;</span>data.<span style="color: #660066;">html</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
<span style="color: #009900;">&#125;</span>
<span style="color: #339933;">&lt;/</span>script<span style="color: #339933;">&gt;</span>
&lt;script type="text/javascript" src="https://api.twitter.com/1/statuses/oembed.json?id={IDENTIFIER}&callback=loadx"&gt;&lt;/script&gt;
&lt;div class='twitter' onLoad='loadx().html'/&gt;</pre>
      </td>
    </tr>
  </table>
</div>