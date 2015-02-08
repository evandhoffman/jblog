---
title: Code to flatten a list in Java, with and without recursion
author: Evan Hoffman
layout: post
permalink: /2010/04/17/code-to-flatten-a-list-in-java-with-and-without-recursion/
dsq_thread_id:
  - 2952037080
categories:
  - Uncategorized
tags:
  - code
  - flatten
  - java
  - linux
  - list
  - non-recursive
  - recursion
  - recursive
---
Recently had someone ask me to solve this problem: assume you have a list of objects, some of which may be lists of arbitrary depths. Write a function to return the list &#8220;flattened,&#8221; so sublists are all in the &#8220;main&#8221; list. I solved it recursively pretty easily, but it took some thought to do it without recursion. My solutions are below.

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
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91
92
93
94
95
96
97
98
99
100
101
102
103
104
105
106
107
108
109
110
111
112
113
114
115
116
117
118
119
120
121
</pre>
      </td>
      
      <td class="code">
        <pre class="java" style="font-family:monospace;"><span style="color: #000000; font-weight: bold;">package</span> <span style="color: #006699;">com.evanhoffman.listflattener</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #000000; font-weight: bold;">import</span> <span style="color: #006699;">java.util.LinkedList</span><span style="color: #339933;">;</span>
<span style="color: #000000; font-weight: bold;">import</span> <span style="color: #006699;">java.util.List</span><span style="color: #339933;">;</span>
<span style="color: #000000; font-weight: bold;">import</span> <span style="color: #006699;">java.util.ListIterator</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #000000; font-weight: bold;">import</span> <span style="color: #006699;">org.apache.log4j.BasicConfigurator</span><span style="color: #339933;">;</span>
<span style="color: #000000; font-weight: bold;">import</span> <span style="color: #006699;">org.apache.log4j.Logger</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">class</span> ListFlattener <span style="color: #009900;">&#123;</span>
&nbsp;
        <span style="color: #000000; font-weight: bold;">private</span> <span style="color: #000000; font-weight: bold;">static</span> Logger logger <span style="color: #339933;">=</span> Logger.<span style="color: #006633;">getLogger</span><span style="color: #009900;">&#40;</span>ListFlattener.<span style="color: #000000; font-weight: bold;">class</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
        <span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">static</span> List<span style="color: #339933;">&lt;</span>Object<span style="color: #339933;">&gt;</span> flattenList<span style="color: #009900;">&#40;</span>List<span style="color: #339933;">&lt;</span>Object<span style="color: #339933;">&gt;</span> inList<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
                List<span style="color: #339933;">&lt;</span>Object<span style="color: #339933;">&gt;</span> newList <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> LinkedList<span style="color: #339933;">&lt;</span>Object<span style="color: #339933;">&gt;</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
                <span style="color: #000000; font-weight: bold;">for</span> <span style="color: #009900;">&#40;</span><span style="color: #003399;">Object</span> i <span style="color: #339933;">:</span> inList<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
                        <span style="color: #666666; font-style: italic;">// If it's not a list, just add it to the return list.</span>
                        <span style="color: #000000; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span><span style="color: #339933;">!</span><span style="color: #009900;">&#40;</span>i <span style="color: #000000; font-weight: bold;">instanceof</span> <span style="color: #003399;">List</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
                                newList.<span style="color: #006633;">add</span><span style="color: #009900;">&#40;</span>i<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                        <span style="color: #009900;">&#125;</span> <span style="color: #000000; font-weight: bold;">else</span> <span style="color: #009900;">&#123;</span>
                                <span style="color: #666666; font-style: italic;">// It's a list, so add each item to the return list.</span>
                                newList.<span style="color: #006633;">addAll</span><span style="color: #009900;">&#40;</span>flattenList<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#40;</span>List<span style="color: #339933;">&lt;</span>Object<span style="color: #339933;">&gt;</span><span style="color: #009900;">&#41;</span>i<span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                        <span style="color: #009900;">&#125;</span>
                <span style="color: #009900;">&#125;</span>
&nbsp;
                <span style="color: #000000; font-weight: bold;">return</span> newList<span style="color: #339933;">;</span>
        <span style="color: #009900;">&#125;</span>
&nbsp;
        <span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">static</span> List<span style="color: #339933;">&lt;</span>Object<span style="color: #339933;">&gt;</span> flattenListNoRecursion<span style="color: #009900;">&#40;</span>List<span style="color: #339933;">&lt;</span>Object<span style="color: #339933;">&gt;</span> inList<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
                List<span style="color: #339933;">&lt;</span>Object<span style="color: #339933;">&gt;</span> tempList <span style="color: #339933;">=</span> <span style="color: #000066; font-weight: bold;">null</span><span style="color: #339933;">;</span>
&nbsp;
                <span style="color: #666666; font-style: italic;">// Clone the input list to newList</span>
                List<span style="color: #339933;">&lt;</span>Object<span style="color: #339933;">&gt;</span> newList <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> LinkedList<span style="color: #339933;">&lt;</span>Object<span style="color: #339933;">&gt;</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                newList.<span style="color: #006633;">addAll</span><span style="color: #009900;">&#40;</span>inList<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
                ListIterator<span style="color: #339933;">&lt;</span>Object<span style="color: #339933;">&gt;</span> iterator <span style="color: #339933;">=</span> newList.<span style="color: #006633;">listIterator</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
&nbsp;
                <span style="color: #000066; font-weight: bold;">int</span> currentPosition <span style="color: #339933;">=</span> <span style="color: #cc66cc;"></span><span style="color: #339933;">;</span>
&nbsp;
                <span style="color: #000000; font-weight: bold;">while</span> <span style="color: #009900;">&#40;</span>iterator.<span style="color: #006633;">hasNext</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
                        <span style="color: #003399;">Object</span> i <span style="color: #339933;">=</span> iterator.<span style="color: #006633;">next</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                        <span style="color: #000000; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span><span style="color: #339933;">!</span><span style="color: #009900;">&#40;</span>i <span style="color: #000000; font-weight: bold;">instanceof</span> <span style="color: #003399;">List</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
                                <span style="color: #666666; font-style: italic;">// If it's not a list, advance the position.  Don't advance position if this IS a list.</span>
                                currentPosition<span style="color: #339933;">++;</span>
                        <span style="color: #009900;">&#125;</span> <span style="color: #000000; font-weight: bold;">else</span> <span style="color: #009900;">&#123;</span>
                                <span style="color: #666666; font-style: italic;">// If the current item is a list, save it to a temp var.</span>
                                tempList <span style="color: #339933;">=</span> <span style="color: #009900;">&#40;</span>List<span style="color: #339933;">&lt;</span>Object<span style="color: #339933;">&gt;</span><span style="color: #009900;">&#41;</span> i<span style="color: #339933;">;</span>
&nbsp;
                                <span style="color: #666666; font-style: italic;">// Delete the list from the list</span>
                                iterator.<span style="color: #006633;">remove</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
                                <span style="color: #666666; font-style: italic;">// Add each item from the temp list to the master list at the same position the sublist was removed.</span>
                                <span style="color: #000000; font-weight: bold;">for</span> <span style="color: #009900;">&#40;</span><span style="color: #003399;">Object</span> obj <span style="color: #339933;">:</span> tempList<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
                                        iterator.<span style="color: #006633;">add</span><span style="color: #009900;">&#40;</span>obj<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                                <span style="color: #009900;">&#125;</span>
&nbsp;
                                <span style="color: #666666; font-style: italic;">// reset the iterator to re-walk the list that was just inserted (within the master) to check for more lists.</span>
                                iterator <span style="color: #339933;">=</span> newList.<span style="color: #006633;">listIterator</span><span style="color: #009900;">&#40;</span>currentPosition<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                        <span style="color: #009900;">&#125;</span>
                <span style="color: #009900;">&#125;</span>
                <span style="color: #000000; font-weight: bold;">return</span> newList<span style="color: #339933;">;</span>
        <span style="color: #009900;">&#125;</span>
&nbsp;
        <span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">static</span> <span style="color: #000066; font-weight: bold;">void</span> printList<span style="color: #009900;">&#40;</span>List<span style="color: #339933;">&lt;</span>Object<span style="color: #339933;">&gt;</span> list<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
                <span style="color: #000066; font-weight: bold;">int</span> i <span style="color: #339933;">=</span> <span style="color: #cc66cc;"></span><span style="color: #339933;">;</span>
                <span style="color: #000000; font-weight: bold;">for</span> <span style="color: #009900;">&#40;</span><span style="color: #003399;">Object</span> item <span style="color: #339933;">:</span> list<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
                        logger.<span style="color: #006633;">debug</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"List item #"</span><span style="color: #339933;">+</span>i <span style="color: #339933;">+</span><span style="color: #0000ff;">": "</span><span style="color: #339933;">+</span>item<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                        i<span style="color: #339933;">++;</span>
                <span style="color: #009900;">&#125;</span>
        <span style="color: #009900;">&#125;</span>
&nbsp;
        <span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">static</span> <span style="color: #000066; font-weight: bold;">void</span> main<span style="color: #009900;">&#40;</span><span style="color: #003399;">String</span><span style="color: #009900;">&#91;</span><span style="color: #009900;">&#93;</span> args<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
&nbsp;
                BasicConfigurator.<span style="color: #006633;">configure</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
                <span style="color: #666666; font-style: italic;">// List of strings</span>
                LinkedList<span style="color: #339933;">&lt;</span>String<span style="color: #339933;">&gt;</span> stringList <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> LinkedList<span style="color: #339933;">&lt;</span>String<span style="color: #339933;">&gt;</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                <span style="color: #000000; font-weight: bold;">for</span> <span style="color: #009900;">&#40;</span><span style="color: #003399;">Integer</span> i <span style="color: #339933;">=</span> <span style="color: #cc66cc;"></span><span style="color: #339933;">;</span> i <span style="color: #339933;">&lt;</span> <span style="color: #cc66cc;">10</span><span style="color: #339933;">;</span> i<span style="color: #339933;">++</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
                        stringList.<span style="color: #006633;">add</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"String #"</span> <span style="color: #339933;">+</span>i.<span style="color: #006633;">toString</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                <span style="color: #009900;">&#125;</span>
&nbsp;
                <span style="color: #666666; font-style: italic;">// List of integers</span>
                LinkedList<span style="color: #339933;">&lt;</span>Integer<span style="color: #339933;">&gt;</span> intList <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> LinkedList<span style="color: #339933;">&lt;</span>Integer<span style="color: #339933;">&gt;</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                <span style="color: #000000; font-weight: bold;">for</span> <span style="color: #009900;">&#40;</span><span style="color: #003399;">Integer</span> i <span style="color: #339933;">=</span> <span style="color: #cc66cc;">10</span><span style="color: #339933;">;</span> i <span style="color: #339933;">&lt;</span> <span style="color: #cc66cc;">20</span><span style="color: #339933;">;</span> i<span style="color: #339933;">++</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
                        intList.<span style="color: #006633;">add</span><span style="color: #009900;">&#40;</span>i<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                <span style="color: #009900;">&#125;</span>
&nbsp;
                <span style="color: #666666; font-style: italic;">// Nested Lists</span>
                LinkedList<span style="color: #339933;">&lt;</span>Object<span style="color: #339933;">&gt;</span> nestedList1 <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> LinkedList<span style="color: #339933;">&lt;</span>Object<span style="color: #339933;">&gt;</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                LinkedList<span style="color: #339933;">&lt;</span>Object<span style="color: #339933;">&gt;</span> nestedList2 <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> LinkedList<span style="color: #339933;">&lt;</span>Object<span style="color: #339933;">&gt;</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                LinkedList<span style="color: #339933;">&lt;</span>String<span style="color: #339933;">&gt;</span> nestedList3 <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> LinkedList<span style="color: #339933;">&lt;</span>String<span style="color: #339933;">&gt;</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
                nestedList3.<span style="color: #006633;">add</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"Nested String 1"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                nestedList3.<span style="color: #006633;">add</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"Nested String 2"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                nestedList2.<span style="color: #006633;">add</span><span style="color: #009900;">&#40;</span>nestedList3<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                nestedList1.<span style="color: #006633;">add</span><span style="color: #009900;">&#40;</span>nestedList2<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
                LinkedList<span style="color: #339933;">&lt;</span>Object<span style="color: #339933;">&gt;</span> bigList <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> LinkedList<span style="color: #339933;">&lt;</span>Object<span style="color: #339933;">&gt;</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
                bigList.<span style="color: #006633;">add</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"First item"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                bigList.<span style="color: #006633;">add</span><span style="color: #009900;">&#40;</span>stringList<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                bigList.<span style="color: #006633;">add</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"Third Item"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                bigList.<span style="color: #006633;">add</span><span style="color: #009900;">&#40;</span>intList<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                bigList.<span style="color: #006633;">add</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"Fifth Item"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                bigList.<span style="color: #006633;">add</span><span style="color: #009900;">&#40;</span>nestedList1<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                bigList.<span style="color: #006633;">add</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"Seventh Item"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #666666; font-style: italic;">//              List&lt;Object&gt; flattenedList = flattenList(bigList);</span>
                List<span style="color: #339933;">&lt;</span>Object<span style="color: #339933;">&gt;</span> flattenedListRecursion <span style="color: #339933;">=</span> flattenList<span style="color: #009900;">&#40;</span>bigList<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                List<span style="color: #339933;">&lt;</span>Object<span style="color: #339933;">&gt;</span> flattenedList <span style="color: #339933;">=</span> flattenListNoRecursion<span style="color: #009900;">&#40;</span>bigList<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
                logger.<span style="color: #006633;">debug</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"Original list:                 "</span><span style="color: #339933;">+</span>bigList.<span style="color: #006633;">toString</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
<span style="color: #666666; font-style: italic;">//              printList(bigList);</span>
                logger.<span style="color: #006633;">debug</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"Flattened list (w/ recursion): "</span><span style="color: #339933;">+</span>flattenedListRecursion.<span style="color: #006633;">toString</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
                logger.<span style="color: #006633;">debug</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"Flattened list (no recursion): "</span><span style="color: #339933;">+</span>flattenedList.<span style="color: #006633;">toString</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
<span style="color: #666666; font-style: italic;">//              printList(flattenedList);</span>
&nbsp;
        <span style="color: #009900;">&#125;</span>
<span style="color: #009900;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>