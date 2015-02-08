---
title: 'JavaScript regex for stripping leading &#038; trailing whitespaces'
author: Evan Hoffman
excerpt: '<code>str.replace(/^(\s*)((\S+\s*?)*)(\s*)$/,"$2");</code>'
layout: post
permalink: /2011/06/22/javascript-regex-for-stripping-leading-trailing-whitespaces/
dsq_thread_id:
  - 2949516731
categories:
  - Uncategorized
tags:
  - code
  - javascript
  - node
  - node.js
  - nodejs
  - programming
  - strip
  - trim
  - whitespace
---
I&#8217;m sure there are a bunch of libraries that do this, but sometimes wrestling with regexes is fun.

<div class="wp_syntax">
  <table>
    <tr>
      <td class="line_numbers">
        <pre>1
2
3
4
5
</pre>
      </td>
      
      <td class="code">
        <pre class="javascript" style="font-family:monospace;"><span style="color: #339933;">&gt;</span> <span style="color: #000066; font-weight: bold;">var</span> str <span style="color: #339933;">=</span> <span style="color: #3366CC;">"           This string has some mighty fine whitespace.       "</span><span style="color: #339933;">;</span>
<span style="color: #339933;">&gt;</span> str
<span style="color: #3366CC;">'           This string has some mighty fine whitespace.       '</span>
<span style="color: #339933;">&gt;</span> str.<span style="color: #660066;">replace</span><span style="color: #009900;">&#40;</span><span style="color: #009966; font-style: italic;">/^(\s*)((\S+\s*?)*)(\s*)$/</span><span style="color: #339933;">,</span><span style="color: #3366CC;">"$2"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
<span style="color: #3366CC;">'This string has some mighty fine whitespace.'</span></pre>
      </td>
    </tr>
  </table>
</div>