---
title: 'Java Code: searching a Binary Search Tree for the largest value strictly less than X'
author: Evan Hoffman
excerpt: Java code for creating and searching a Binary Search Tree of any element implementing the Comparable interface.
layout: post
permalink: /2010/05/09/java-code-searching-a-binary-search-tree-for-the-largest-value-strictly-less-than-x/
dsq_thread_id:
  - 2959764325
categories:
  - Uncategorized
tags:
  - algorithm
  - binary search tree
  - bst
  - code
  - java
  - linux
  - programming
---
Someone recently asked me how to do this, so I figured I&#8217;d write it up in Java since it&#8217;s still the language I&#8217;m most comfortable with:

Node.java

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
</pre>
      </td>
      
      <td class="code">
        <pre class="java" style="font-family:monospace;"><span style="color: #000000; font-weight: bold;">package</span> <span style="color: #006699;">com.evanhoffman.bst</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #000000; font-weight: bold;">import</span> <span style="color: #006699;">org.apache.log4j.Logger</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #008000; font-style: italic; font-weight: bold;">/**
 * Node in a binary search tree in which values &lt; are in the left branch and 
 * values &gt;= are in the right branch.
 * @author evan
 *
 * @param &lt;V&gt;
 */</span>
<span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">class</span> Node<span style="color: #339933;">&lt;</span>V <span style="color: #000000; font-weight: bold;">extends</span> Comparable<span style="color: #339933;">&lt;</span>V<span style="color: #339933;">&gt;&gt;</span> <span style="color: #009900;">&#123;</span>
	<span style="color: #000000; font-weight: bold;">private</span> <span style="color: #000000; font-weight: bold;">static</span> Logger logger <span style="color: #339933;">=</span> Logger.<span style="color: #006633;">getLogger</span><span style="color: #009900;">&#40;</span>Node.<span style="color: #000000; font-weight: bold;">class</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
	<span style="color: #000000; font-weight: bold;">private</span> Node<span style="color: #339933;">&lt;</span>V<span style="color: #339933;">&gt;</span> left <span style="color: #339933;">=</span> <span style="color: #000066; font-weight: bold;">null</span><span style="color: #339933;">;</span>
	<span style="color: #000000; font-weight: bold;">private</span> Node<span style="color: #339933;">&lt;</span>V<span style="color: #339933;">&gt;</span> right <span style="color: #339933;">=</span> <span style="color: #000066; font-weight: bold;">null</span><span style="color: #339933;">;</span>
	<span style="color: #000000; font-weight: bold;">private</span> V value <span style="color: #339933;">=</span> <span style="color: #000066; font-weight: bold;">null</span><span style="color: #339933;">;</span>
&nbsp;
	<span style="color: #000000; font-weight: bold;">public</span> Node<span style="color: #009900;">&#40;</span>V t<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
		<span style="color: #000000; font-weight: bold;">this</span>.<span style="color: #006633;">value</span> <span style="color: #339933;">=</span> t<span style="color: #339933;">;</span>
	<span style="color: #009900;">&#125;</span>
&nbsp;
	<span style="color: #000000; font-weight: bold;">public</span> V getValue<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
		<span style="color: #000000; font-weight: bold;">return</span> value<span style="color: #339933;">;</span>
	<span style="color: #009900;">&#125;</span>
	<span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000066; font-weight: bold;">boolean</span> isLeaf<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
		<span style="color: #000000; font-weight: bold;">return</span> <span style="color: #009900;">&#40;</span>left <span style="color: #339933;">==</span> <span style="color: #000066; font-weight: bold;">null</span> <span style="color: #339933;">&&</span> right <span style="color: #339933;">==</span> <span style="color: #000066; font-weight: bold;">null</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
	<span style="color: #009900;">&#125;</span>
&nbsp;
&nbsp;
	<span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000066; font-weight: bold;">void</span> addNode<span style="color: #009900;">&#40;</span>Node<span style="color: #339933;">&lt;</span>V<span style="color: #339933;">&gt;</span> node<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
		<span style="color: #000000; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span>node.<span style="color: #006633;">getValue</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span>.<span style="color: #006633;">compareTo</span><span style="color: #009900;">&#40;</span>value<span style="color: #009900;">&#41;</span> <span style="color: #339933;">&lt;</span> <span style="color: #cc66cc;"></span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
			<span style="color: #000000; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span>left <span style="color: #339933;">==</span> <span style="color: #000066; font-weight: bold;">null</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
				left <span style="color: #339933;">=</span> node<span style="color: #339933;">;</span>
				logger.<span style="color: #006633;">debug</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"Assigning "</span><span style="color: #339933;">+</span>node.<span style="color: #006633;">getValue</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">+</span><span style="color: #0000ff;">" "</span><span style="color: #339933;">+</span>
						<span style="color: #0000ff;">"to left child of node "</span><span style="color: #339933;">+</span>value<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
			<span style="color: #009900;">&#125;</span> <span style="color: #000000; font-weight: bold;">else</span> <span style="color: #009900;">&#123;</span>
				left.<span style="color: #006633;">addNode</span><span style="color: #009900;">&#40;</span>node<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
			<span style="color: #009900;">&#125;</span>
		<span style="color: #009900;">&#125;</span> <span style="color: #000000; font-weight: bold;">else</span> <span style="color: #009900;">&#123;</span>
			<span style="color: #000000; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span>right <span style="color: #339933;">==</span> <span style="color: #000066; font-weight: bold;">null</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
				right <span style="color: #339933;">=</span> node<span style="color: #339933;">;</span>
				logger.<span style="color: #006633;">debug</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"Assigning "</span><span style="color: #339933;">+</span>node.<span style="color: #006633;">getValue</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">+</span><span style="color: #0000ff;">" "</span><span style="color: #339933;">+</span>
						<span style="color: #0000ff;">"to right child of node "</span><span style="color: #339933;">+</span>value<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
			<span style="color: #009900;">&#125;</span> <span style="color: #000000; font-weight: bold;">else</span> <span style="color: #009900;">&#123;</span>
				right.<span style="color: #006633;">addNode</span><span style="color: #009900;">&#40;</span>node<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
			<span style="color: #009900;">&#125;</span>
		<span style="color: #009900;">&#125;</span>
&nbsp;
	<span style="color: #009900;">&#125;</span>
&nbsp;
	<span style="color: #000000; font-weight: bold;">public</span> V findMaxStrictlyLessThan<span style="color: #009900;">&#40;</span>V t<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
		<span style="color: #000000; font-weight: bold;">return</span> findMaxStrictlyLessThan<span style="color: #009900;">&#40;</span>t, <span style="color: #000066; font-weight: bold;">null</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
	<span style="color: #009900;">&#125;</span>
&nbsp;
	<span style="color: #008000; font-style: italic; font-weight: bold;">/**
	 * Returns the greatest value in the tree strictly
	 * less than (as defined by {@link Comparable#compareTo(Object)} the given value.
	 * @param t
	 * @return
	 */</span>
	<span style="color: #000000; font-weight: bold;">private</span> V findMaxStrictlyLessThan<span style="color: #009900;">&#40;</span>V t, V currentMax<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
		<span style="color: #000000; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span>t.<span style="color: #006633;">compareTo</span><span style="color: #009900;">&#40;</span>value<span style="color: #009900;">&#41;</span> <span style="color: #339933;">&lt;</span> <span style="color: #cc66cc;"></span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
			logger.<span style="color: #006633;">debug</span><span style="color: #009900;">&#40;</span>t <span style="color: #339933;">+</span><span style="color: #0000ff;">" is less than current node "</span><span style="color: #339933;">+</span>value<span style="color: #339933;">+</span><span style="color: #0000ff;">", checking left (if it exists)"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
			<span style="color: #000000; font-weight: bold;">return</span> left <span style="color: #339933;">==</span> <span style="color: #000066; font-weight: bold;">null</span> <span style="color: #339933;">?</span> currentMax <span style="color: #339933;">:</span> left.<span style="color: #006633;">findMaxStrictlyLessThan</span><span style="color: #009900;">&#40;</span>t, currentMax<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
		<span style="color: #009900;">&#125;</span> <span style="color: #000000; font-weight: bold;">else</span> <span style="color: #009900;">&#123;</span>
			logger.<span style="color: #006633;">debug</span><span style="color: #009900;">&#40;</span>t <span style="color: #339933;">+</span><span style="color: #0000ff;">" is &gt;= current node "</span><span style="color: #339933;">+</span>value<span style="color: #339933;">+</span><span style="color: #0000ff;">", checking right (if exists)"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
			currentMax <span style="color: #339933;">=</span> value<span style="color: #339933;">;</span>
			<span style="color: #000000; font-weight: bold;">return</span> right <span style="color: #339933;">==</span> <span style="color: #000066; font-weight: bold;">null</span> <span style="color: #339933;">?</span> currentMax <span style="color: #339933;">:</span> right.<span style="color: #006633;">findMaxStrictlyLessThan</span><span style="color: #009900;">&#40;</span>t, currentMax<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
		<span style="color: #009900;">&#125;</span>
&nbsp;
	<span style="color: #009900;">&#125;</span>
&nbsp;
	@Override
	<span style="color: #000000; font-weight: bold;">public</span> <span style="color: #003399;">String</span> toString<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
		<span style="color: #000000; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span>isLeaf<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
			<span style="color: #000000; font-weight: bold;">return</span> <span style="color: #0000ff;">"{value="</span><span style="color: #339933;">+</span>value<span style="color: #339933;">+</span><span style="color: #0000ff;">"}"</span><span style="color: #339933;">;</span>
		<span style="color: #009900;">&#125;</span> <span style="color: #000000; font-weight: bold;">else</span> <span style="color: #009900;">&#123;</span>
			<span style="color: #000000; font-weight: bold;">return</span> <span style="color: #0000ff;">"{value="</span><span style="color: #339933;">+</span>value<span style="color: #339933;">+</span><span style="color: #0000ff;">", left="</span><span style="color: #339933;">+</span>left<span style="color: #339933;">+</span><span style="color: #0000ff;">", right="</span><span style="color: #339933;">+</span>right<span style="color: #339933;">+</span><span style="color: #0000ff;">"}"</span><span style="color: #339933;">;</span>
		<span style="color: #009900;">&#125;</span>
	<span style="color: #009900;">&#125;</span>
&nbsp;
<span style="color: #009900;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>

IntBstBuilder.java:

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
</pre>
      </td>
      
      <td class="code">
        <pre class="java" style="font-family:monospace;"><span style="color: #000000; font-weight: bold;">package</span> <span style="color: #006699;">com.evanhoffman.bst</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #000000; font-weight: bold;">import</span> <span style="color: #006699;">java.util.Random</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #000000; font-weight: bold;">import</span> <span style="color: #006699;">org.apache.log4j.BasicConfigurator</span><span style="color: #339933;">;</span>
<span style="color: #000000; font-weight: bold;">import</span> <span style="color: #006699;">org.apache.log4j.Logger</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">class</span> IntBstBuilder <span style="color: #009900;">&#123;</span>
&nbsp;
	<span style="color: #000000; font-weight: bold;">private</span> <span style="color: #000000; font-weight: bold;">static</span> <span style="color: #000066; font-weight: bold;">int</span> min <span style="color: #339933;">=</span> <span style="color: #cc66cc;"></span><span style="color: #339933;">;</span>
	<span style="color: #000000; font-weight: bold;">private</span> <span style="color: #000000; font-weight: bold;">static</span> <span style="color: #000066; font-weight: bold;">int</span> max <span style="color: #339933;">=</span> <span style="color: #cc66cc;">200</span><span style="color: #339933;">;</span>
	<span style="color: #000000; font-weight: bold;">private</span> <span style="color: #000000; font-weight: bold;">static</span> <span style="color: #000066; font-weight: bold;">int</span> size <span style="color: #339933;">=</span> <span style="color: #cc66cc;">10</span><span style="color: #339933;">;</span>
&nbsp;
	<span style="color: #000000; font-weight: bold;">private</span> <span style="color: #000000; font-weight: bold;">static</span> <span style="color: #003399;">Random</span> rng <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> <span style="color: #003399;">Random</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
	<span style="color: #000000; font-weight: bold;">private</span> <span style="color: #000000; font-weight: bold;">static</span> Logger logger <span style="color: #339933;">=</span> Logger.<span style="color: #006633;">getLogger</span><span style="color: #009900;">&#40;</span>IntBstBuilder.<span style="color: #000000; font-weight: bold;">class</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
	<span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">static</span> <span style="color: #000066; font-weight: bold;">void</span> main<span style="color: #009900;">&#40;</span><span style="color: #003399;">String</span> args<span style="color: #009900;">&#91;</span><span style="color: #009900;">&#93;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
		BasicConfigurator.<span style="color: #006633;">configure</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
		Node<span style="color: #339933;">&lt;</span>Integer<span style="color: #339933;">&gt;</span> root <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> Node<span style="color: #339933;">&lt;</span>Integer<span style="color: #339933;">&gt;</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#40;</span>max <span style="color: #339933;">-</span> min<span style="color: #009900;">&#41;</span><span style="color: #339933;">/</span><span style="color: #cc66cc;">2</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
		<span style="color: #000000; font-weight: bold;">for</span> <span style="color: #009900;">&#40;</span><span style="color: #000066; font-weight: bold;">int</span> i <span style="color: #339933;">=</span> <span style="color: #cc66cc;"></span><span style="color: #339933;">;</span> i <span style="color: #339933;">&lt;</span> size<span style="color: #339933;">;</span> i<span style="color: #339933;">++</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
			<span style="color: #000066; font-weight: bold;">int</span> num <span style="color: #339933;">=</span> <span style="color: #009900;">&#40;</span><span style="color: #003399;">Math</span>.<span style="color: #006633;">abs</span><span style="color: #009900;">&#40;</span>rng.<span style="color: #006633;">nextInt</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span> <span style="color: #339933;">%</span> <span style="color: #009900;">&#40;</span>max <span style="color: #339933;">-</span> min<span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span> <span style="color: #339933;">+</span> min<span style="color: #339933;">;</span>
			Node<span style="color: #339933;">&lt;</span>Integer<span style="color: #339933;">&gt;</span> newNode <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> Node<span style="color: #339933;">&lt;</span>Integer<span style="color: #339933;">&gt;</span><span style="color: #009900;">&#40;</span>num<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
			root.<span style="color: #006633;">addNode</span><span style="color: #009900;">&#40;</span>newNode<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
		<span style="color: #009900;">&#125;</span>
&nbsp;
		logger.<span style="color: #006633;">info</span><span style="color: #009900;">&#40;</span>root<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
		logger.<span style="color: #006633;">info</span><span style="color: #009900;">&#40;</span>root.<span style="color: #006633;">findMaxStrictlyLessThan</span><span style="color: #009900;">&#40;</span><span style="color: #cc66cc;">50</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
		logger.<span style="color: #006633;">info</span><span style="color: #009900;">&#40;</span>root.<span style="color: #006633;">findMaxStrictlyLessThan</span><span style="color: #009900;">&#40;</span><span style="color: #cc66cc;">90</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
		logger.<span style="color: #006633;">info</span><span style="color: #009900;">&#40;</span>root.<span style="color: #006633;">findMaxStrictlyLessThan</span><span style="color: #009900;">&#40;</span><span style="color: #cc66cc;">130</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
		logger.<span style="color: #006633;">info</span><span style="color: #009900;">&#40;</span>root.<span style="color: #006633;">findMaxStrictlyLessThan</span><span style="color: #009900;">&#40;</span><span style="color: #cc66cc;">220</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
		logger.<span style="color: #006633;">info</span><span style="color: #009900;">&#40;</span>root.<span style="color: #006633;">findMaxStrictlyLessThan</span><span style="color: #009900;">&#40;</span><span style="color: #339933;">-</span><span style="color: #cc66cc;">100</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
	<span style="color: #009900;">&#125;</span>
<span style="color: #009900;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>

Output:

<pre lang="" line="1">0 [main] DEBUG com.evanhoffman.bst.Node  - Assigning 192 to right child of node 100
1 [main] DEBUG com.evanhoffman.bst.Node  - Assigning 140 to left child of node 192
1 [main] DEBUG com.evanhoffman.bst.Node  - Assigning 162 to right child of node 140
1 [main] DEBUG com.evanhoffman.bst.Node  - Assigning 54 to left child of node 100
1 [main] DEBUG com.evanhoffman.bst.Node  - Assigning 155 to left child of node 162
1 [main] DEBUG com.evanhoffman.bst.Node  - Assigning 118 to left child of node 140
2 [main] DEBUG com.evanhoffman.bst.Node  - Assigning 52 to left child of node 54
2 [main] DEBUG com.evanhoffman.bst.Node  - Assigning 184 to right child of node 162
2 [main] DEBUG com.evanhoffman.bst.Node  - Assigning 41 to left child of node 52
2 [main] DEBUG com.evanhoffman.bst.Node  - Assigning 161 to right child of node 155
2 [main] INFO com.evanhoffman.bst.IntBstBuilder  - {value=100, left={value=54, left={value=52, left={value=41}, right=null}, right=null}, right={value=192, left={value=140, left={value=118}, right={value=162, left={value=155, left=null, right={value=161}}, right={value=184}}}, right=null}}
2 [main] DEBUG com.evanhoffman.bst.Node  - 50 is less than current node 100, checking left (if it exists)
2 [main] DEBUG com.evanhoffman.bst.Node  - 50 is less than current node 54, checking left (if it exists)
2 [main] DEBUG com.evanhoffman.bst.Node  - 50 is less than current node 52, checking left (if it exists)
2 [main] DEBUG com.evanhoffman.bst.Node  - 50 is >= current node 41, checking right (if exists)
2 [main] INFO com.evanhoffman.bst.IntBstBuilder  - 41
2 [main] DEBUG com.evanhoffman.bst.Node  - 90 is less than current node 100, checking left (if it exists)
2 [main] DEBUG com.evanhoffman.bst.Node  - 90 is >= current node 54, checking right (if exists)
2 [main] INFO com.evanhoffman.bst.IntBstBuilder  - 54
2 [main] DEBUG com.evanhoffman.bst.Node  - 130 is >= current node 100, checking right (if exists)
2 [main] DEBUG com.evanhoffman.bst.Node  - 130 is less than current node 192, checking left (if it exists)
3 [main] DEBUG com.evanhoffman.bst.Node  - 130 is less than current node 140, checking left (if it exists)
3 [main] DEBUG com.evanhoffman.bst.Node  - 130 is >= current node 118, checking right (if exists)
3 [main] INFO com.evanhoffman.bst.IntBstBuilder  - 118
3 [main] DEBUG com.evanhoffman.bst.Node  - 220 is >= current node 100, checking right (if exists)
3 [main] DEBUG com.evanhoffman.bst.Node  - 220 is >= current node 192, checking right (if exists)
3 [main] INFO com.evanhoffman.bst.IntBstBuilder  - 192
3 [main] DEBUG com.evanhoffman.bst.Node  - -100 is less than current node 100, checking left (if it exists)
3 [main] DEBUG com.evanhoffman.bst.Node  - -100 is less than current node 54, checking left (if it exists)
3 [main] DEBUG com.evanhoffman.bst.Node  - -100 is less than current node 52, checking left (if it exists)
3 [main] DEBUG com.evanhoffman.bst.Node  - -100 is less than current node 41, checking left (if it exists)
3 [main] INFO com.evanhoffman.bst.IntBstBuilder  - 

</pre>

StringBstBuilder.java:

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
</pre>
      </td>
      
      <td class="code">
        <pre class="java" style="font-family:monospace;"><span style="color: #000000; font-weight: bold;">package</span> <span style="color: #006699;">com.evanhoffman.bst</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #000000; font-weight: bold;">import</span> <span style="color: #006699;">java.io.BufferedReader</span><span style="color: #339933;">;</span>
<span style="color: #000000; font-weight: bold;">import</span> <span style="color: #006699;">java.io.File</span><span style="color: #339933;">;</span>
<span style="color: #000000; font-weight: bold;">import</span> <span style="color: #006699;">java.io.FileNotFoundException</span><span style="color: #339933;">;</span>
<span style="color: #000000; font-weight: bold;">import</span> <span style="color: #006699;">java.io.FileReader</span><span style="color: #339933;">;</span>
<span style="color: #000000; font-weight: bold;">import</span> <span style="color: #006699;">java.io.IOException</span><span style="color: #339933;">;</span>
<span style="color: #000000; font-weight: bold;">import</span> <span style="color: #006699;">java.util.ArrayList</span><span style="color: #339933;">;</span>
<span style="color: #000000; font-weight: bold;">import</span> <span style="color: #006699;">java.util.Random</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #000000; font-weight: bold;">import</span> <span style="color: #006699;">org.apache.log4j.BasicConfigurator</span><span style="color: #339933;">;</span>
<span style="color: #000000; font-weight: bold;">import</span> <span style="color: #006699;">org.apache.log4j.Logger</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">class</span> StringBstBuilder <span style="color: #009900;">&#123;</span>
&nbsp;
	<span style="color: #000000; font-weight: bold;">private</span> <span style="color: #000000; font-weight: bold;">static</span> <span style="color: #000066; font-weight: bold;">int</span> size <span style="color: #339933;">=</span> <span style="color: #cc66cc;">100</span><span style="color: #339933;">;</span>
&nbsp;
	<span style="color: #000000; font-weight: bold;">private</span> <span style="color: #000000; font-weight: bold;">static</span> <span style="color: #003399;">Random</span> rng <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> <span style="color: #003399;">Random</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
	<span style="color: #000000; font-weight: bold;">private</span> <span style="color: #000000; font-weight: bold;">static</span> Logger logger <span style="color: #339933;">=</span> Logger.<span style="color: #006633;">getLogger</span><span style="color: #009900;">&#40;</span>StringBstBuilder.<span style="color: #000000; font-weight: bold;">class</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
	<span style="color: #000000; font-weight: bold;">private</span> ArrayList<span style="color: #339933;">&lt;</span>String<span style="color: #339933;">&gt;</span> words <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> ArrayList<span style="color: #339933;">&lt;</span>String<span style="color: #339933;">&gt;</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
	<span style="color: #000000; font-weight: bold;">public</span> ArrayList<span style="color: #339933;">&lt;</span>String<span style="color: #339933;">&gt;</span> getWords<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
		<span style="color: #000000; font-weight: bold;">return</span> words<span style="color: #339933;">;</span>
	<span style="color: #009900;">&#125;</span>
&nbsp;
&nbsp;
&nbsp;
	StringBstBuilder<span style="color: #009900;">&#40;</span><span style="color: #003399;">File</span> f, <span style="color: #000066; font-weight: bold;">int</span> maxLines<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
&nbsp;
		logger.<span style="color: #006633;">debug</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"Creating "</span><span style="color: #339933;">+</span><span style="color: #000000; font-weight: bold;">this</span>.<span style="color: #006633;">getClass</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">+</span><span style="color: #0000ff;">" with word list file: "</span><span style="color: #339933;">+</span>f<span style="color: #339933;">+</span><span style="color: #0000ff;">" up to "</span><span style="color: #339933;">+</span>maxLines<span style="color: #339933;">+</span><span style="color: #0000ff;">" lines"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
		<span style="color: #000066; font-weight: bold;">int</span> i <span style="color: #339933;">=</span> <span style="color: #cc66cc;"></span><span style="color: #339933;">;</span>
		<span style="color: #000000; font-weight: bold;">try</span> <span style="color: #009900;">&#123;</span>
			<span style="color: #003399;">BufferedReader</span> br <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> <span style="color: #003399;">BufferedReader</span><span style="color: #009900;">&#40;</span><span style="color: #000000; font-weight: bold;">new</span> <span style="color: #003399;">FileReader</span><span style="color: #009900;">&#40;</span>f<span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
			<span style="color: #003399;">String</span> line <span style="color: #339933;">=</span> <span style="color: #000066; font-weight: bold;">null</span><span style="color: #339933;">;</span>
			<span style="color: #000000; font-weight: bold;">while</span> <span style="color: #009900;">&#40;</span><span style="color: #009900;">&#40;</span>i <span style="color: #339933;">&lt;</span> maxLines<span style="color: #009900;">&#41;</span> <span style="color: #339933;">&&</span> <span style="color: #009900;">&#40;</span>line <span style="color: #339933;">=</span> br.<span style="color: #006633;">readLine</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span> <span style="color: #339933;">!=</span> <span style="color: #000066; font-weight: bold;">null</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
				words.<span style="color: #006633;">add</span><span style="color: #009900;">&#40;</span>line<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
				i<span style="color: #339933;">++;</span>
			<span style="color: #009900;">&#125;</span>
			logger.<span style="color: #006633;">debug</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"Added "</span><span style="color: #339933;">+</span>words.<span style="color: #006633;">size</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">+</span><span style="color: #0000ff;">" words to list"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
		<span style="color: #009900;">&#125;</span> <span style="color: #000000; font-weight: bold;">catch</span> <span style="color: #009900;">&#40;</span><span style="color: #003399;">FileNotFoundException</span> fe<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
			logger.<span style="color: #006633;">error</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"File not found: "</span><span style="color: #339933;">+</span>f,fe<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
			<span style="color: #000000; font-weight: bold;">throw</span> <span style="color: #000000; font-weight: bold;">new</span> <span style="color: #003399;">RuntimeException</span><span style="color: #009900;">&#40;</span>fe<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
		<span style="color: #009900;">&#125;</span> <span style="color: #000000; font-weight: bold;">catch</span> <span style="color: #009900;">&#40;</span><span style="color: #003399;">IOException</span> ie<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
			logger.<span style="color: #006633;">error</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"IO Exception: "</span><span style="color: #339933;">+</span>ie.<span style="color: #006633;">getMessage</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span>,ie<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
			<span style="color: #000000; font-weight: bold;">throw</span> <span style="color: #000000; font-weight: bold;">new</span> <span style="color: #003399;">RuntimeException</span><span style="color: #009900;">&#40;</span>ie<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
		<span style="color: #009900;">&#125;</span>
	<span style="color: #009900;">&#125;</span>
&nbsp;
&nbsp;
&nbsp;
	<span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">static</span> <span style="color: #000066; font-weight: bold;">void</span> main<span style="color: #009900;">&#40;</span><span style="color: #003399;">String</span> args<span style="color: #009900;">&#91;</span><span style="color: #009900;">&#93;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
		BasicConfigurator.<span style="color: #006633;">configure</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
		StringBstBuilder sbt <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> StringBstBuilder<span style="color: #009900;">&#40;</span><span style="color: #000000; font-weight: bold;">new</span> <span style="color: #003399;">File</span><span style="color: #009900;">&#40;</span>args<span style="color: #009900;">&#91;</span><span style="color: #cc66cc;"></span><span style="color: #009900;">&#93;</span><span style="color: #009900;">&#41;</span>, <span style="color: #003399;">Integer</span>.<span style="color: #006633;">parseInt</span><span style="color: #009900;">&#40;</span>args<span style="color: #009900;">&#91;</span><span style="color: #cc66cc;">1</span><span style="color: #009900;">&#93;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
		ArrayList<span style="color: #339933;">&lt;</span>String<span style="color: #339933;">&gt;</span> words <span style="color: #339933;">=</span> sbt.<span style="color: #006633;">getWords</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
		<span style="color: #000066; font-weight: bold;">int</span> wordListSize <span style="color: #339933;">=</span> words.<span style="color: #006633;">size</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
		<span style="color: #666666; font-style: italic;">// Random number between 1/4 and 3/4 through the list.</span>
		<span style="color: #000066; font-weight: bold;">int</span> seedWordIndex <span style="color: #339933;">=</span> <span style="color: #009900;">&#40;</span><span style="color: #003399;">Math</span>.<span style="color: #006633;">abs</span><span style="color: #009900;">&#40;</span>rng.<span style="color: #006633;">nextInt</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span> <span style="color: #339933;">%</span> <span style="color: #009900;">&#40;</span>wordListSize<span style="color: #339933;">/</span><span style="color: #cc66cc;">2</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span> <span style="color: #339933;">+</span> <span style="color: #009900;">&#40;</span>wordListSize <span style="color: #339933;">/</span> <span style="color: #cc66cc;">4</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
		Node<span style="color: #339933;">&lt;</span>String<span style="color: #339933;">&gt;</span> root <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> Node<span style="color: #339933;">&lt;</span>String<span style="color: #339933;">&gt;</span><span style="color: #009900;">&#40;</span>words.<span style="color: #006633;">get</span><span style="color: #009900;">&#40;</span>seedWordIndex<span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
		logger.<span style="color: #006633;">debug</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"Root word: "</span><span style="color: #339933;">+</span>root.<span style="color: #006633;">getValue</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
		<span style="color: #000000; font-weight: bold;">for</span> <span style="color: #009900;">&#40;</span><span style="color: #000066; font-weight: bold;">int</span> i <span style="color: #339933;">=</span> <span style="color: #cc66cc;"></span><span style="color: #339933;">;</span> i <span style="color: #339933;">&lt;</span> size<span style="color: #339933;">;</span> i<span style="color: #339933;">++</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
			<span style="color: #000066; font-weight: bold;">int</span> index <span style="color: #339933;">=</span> <span style="color: #009900;">&#40;</span><span style="color: #003399;">Math</span>.<span style="color: #006633;">abs</span><span style="color: #009900;">&#40;</span>rng.<span style="color: #006633;">nextInt</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span> <span style="color: #339933;">%</span> <span style="color: #009900;">&#40;</span>words.<span style="color: #006633;">size</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
			Node<span style="color: #339933;">&lt;</span>String<span style="color: #339933;">&gt;</span> newNode <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> Node<span style="color: #339933;">&lt;</span>String<span style="color: #339933;">&gt;</span><span style="color: #009900;">&#40;</span>words.<span style="color: #006633;">get</span><span style="color: #009900;">&#40;</span>index<span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
			root.<span style="color: #006633;">addNode</span><span style="color: #009900;">&#40;</span>newNode<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
		<span style="color: #009900;">&#125;</span>
&nbsp;
		logger.<span style="color: #006633;">info</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"Entire tree: "</span><span style="color: #339933;">+</span>root<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
		<span style="color: #003399;">String</span><span style="color: #009900;">&#91;</span><span style="color: #009900;">&#93;</span> searches <span style="color: #339933;">=</span> <span style="color: #009900;">&#123;</span><span style="color: #0000ff;">"andy"</span>, <span style="color: #0000ff;">"joker"</span>, <span style="color: #0000ff;">"kale"</span>, <span style="color: #0000ff;">"moonshine"</span>, <span style="color: #0000ff;">"zebra"</span><span style="color: #009900;">&#125;</span><span style="color: #339933;">;</span>
		<span style="color: #000000; font-weight: bold;">for</span> <span style="color: #009900;">&#40;</span><span style="color: #003399;">String</span> str <span style="color: #339933;">:</span> searches<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
			logger.<span style="color: #006633;">info</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"Search for "</span><span style="color: #339933;">+</span>str<span style="color: #339933;">+</span><span style="color: #0000ff;">": "</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
			<span style="color: #003399;">String</span> s <span style="color: #339933;">=</span> root.<span style="color: #006633;">findMaxStrictlyLessThan</span><span style="color: #009900;">&#40;</span>str<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
			logger.<span style="color: #006633;">info</span><span style="color: #009900;">&#40;</span><span style="color: #0000ff;">"max word: "</span><span style="color: #339933;">+</span>s<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
		<span style="color: #009900;">&#125;</span>
&nbsp;
	<span style="color: #009900;">&#125;</span>
<span style="color: #009900;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>

Output:

<pre lang="" line="1">0 [main] DEBUG com.evanhoffman.bst.StringBstBuilder  - Creating class com.evanhoffman.bst.StringBstBuilder with word list file: /Users/evan/Downloads/scowl-7.1/final/english-words.20 up to 10000 lines
47 [main] DEBUG com.evanhoffman.bst.StringBstBuilder  - Added 7951 words to list
48 [main] DEBUG com.evanhoffman.bst.StringBstBuilder  - Root word: patient's
48 [main] DEBUG com.evanhoffman.bst.Node  - Assigning scroll's to right child of node patient's
48 [main] DEBUG com.evanhoffman.bst.Node  - Assigning dual to left child of node patient's
48 [main] DEBUG com.evanhoffman.bst.Node  - Assigning bat to left child of node dual
48 [main] DEBUG com.evanhoffman.bst.Node  - Assigning moderate to right child of node dual
48 [main] DEBUG com.evanhoffman.bst.Node  - Assigning abort to left child of node bat
48 [main] DEBUG com.evanhoffman.bst.Node  - Assigning migrates to left child of node moderate
48 [main] DEBUG com.evanhoffman.bst.Node  - Assigning terrifying to right child of node scroll's
48 [main] DEBUG com.evanhoffman.bst.Node  - Assigning equilibrium to left child of node migrates
48 [main] DEBUG com.evanhoffman.bst.Node  - Assigning polynomial to left child of node scroll's
48 [main] DEBUG com.evanhoffman.bst.Node  - Assigning tomatoes to right child of node terrifying
48 [main] DEBUG com.evanhoffman.bst.Node  - Assigning unusable to right child of node tomatoes
48 [main] DEBUG com.evanhoffman.bst.Node  - Assigning nailing to right child of node moderate
48 [main] DEBUG com.evanhoffman.bst.Node  - Assigning supporter to left child of node terrifying
49 [main] DEBUG com.evanhoffman.bst.Node  - Assigning prosecution to right child of node polynomial
49 [main] DEBUG com.evanhoffman.bst.Node  - Assigning management to right child of node equilibrium
49 [main] DEBUG com.evanhoffman.bst.Node  - Assigning disabled to right child of node bat
49 [main] DEBUG com.evanhoffman.bst.Node  - Assigning planted to left child of node polynomial
49 [main] DEBUG com.evanhoffman.bst.Node  - Assigning midday to right child of node management
49 [main] DEBUG com.evanhoffman.bst.Node  - Assigning cloth to left child of node disabled
49 [main] DEBUG com.evanhoffman.bst.Node  - Assigning hip to left child of node management
49 [main] DEBUG com.evanhoffman.bst.Node  - Assigning tune's to left child of node unusable
49 [main] DEBUG com.evanhoffman.bst.Node  - Assigning mortal to left child of node nailing
49 [main] DEBUG com.evanhoffman.bst.Node  - Assigning dangers to right child of node cloth
49 [main] DEBUG com.evanhoffman.bst.Node  - Assigning concentrate to left child of node dangers
49 [main] DEBUG com.evanhoffman.bst.Node  - Assigning intimate to right child of node hip
49 [main] DEBUG com.evanhoffman.bst.Node  - Assigning stairs to left child of node supporter
49 [main] DEBUG com.evanhoffman.bst.Node  - Assigning attorney to right child of node abort
49 [main] DEBUG com.evanhoffman.bst.Node  - Assigning globally to left child of node hip
49 [main] DEBUG com.evanhoffman.bst.Node  - Assigning vertical to right child of node unusable
49 [main] DEBUG com.evanhoffman.bst.Node  - Assigning close's to left child of node cloth
49 [main] DEBUG com.evanhoffman.bst.Node  - Assigning debatable to right child of node dangers
50 [main] DEBUG com.evanhoffman.bst.Node  - Assigning guarding to right child of node globally
50 [main] DEBUG com.evanhoffman.bst.Node  - Assigning aided to left child of node attorney
50 [main] DEBUG com.evanhoffman.bst.Node  - Assigning contend to right child of node concentrate
50 [main] DEBUG com.evanhoffman.bst.Node  - Assigning shined to left child of node stairs
50 [main] DEBUG com.evanhoffman.bst.Node  - Assigning journals to right child of node intimate
50 [main] DEBUG com.evanhoffman.bst.Node  - Assigning warmed to right child of node vertical
50 [main] DEBUG com.evanhoffman.bst.Node  - Assigning blaming to left child of node close's
50 [main] DEBUG com.evanhoffman.bst.Node  - Assigning ingredient to left child of node intimate
50 [main] DEBUG com.evanhoffman.bst.Node  - Assigning gleaning to left child of node globally
50 [main] DEBUG com.evanhoffman.bst.Node  - Assigning boys to right child of node blaming
50 [main] DEBUG com.evanhoffman.bst.Node  - Assigning stream's to right child of node stairs
50 [main] DEBUG com.evanhoffman.bst.Node  - Assigning tragedy to left child of node tune's
50 [main] DEBUG com.evanhoffman.bst.Node  - Assigning brands to right child of node boys
50 [main] DEBUG com.evanhoffman.bst.Node  - Assigning revenge to right child of node prosecution
50 [main] DEBUG com.evanhoffman.bst.Node  - Assigning knight to right child of node journals
50 [main] DEBUG com.evanhoffman.bst.Node  - Assigning discard to right child of node disabled
50 [main] DEBUG com.evanhoffman.bst.Node  - Assigning successor to right child of node stream's
51 [main] DEBUG com.evanhoffman.bst.Node  - Assigning synonymous to right child of node supporter
51 [main] DEBUG com.evanhoffman.bst.Node  - Assigning bow to left child of node boys
51 [main] DEBUG com.evanhoffman.bst.Node  - Assigning satisfaction to right child of node revenge
51 [main] DEBUG com.evanhoffman.bst.Node  - Assigning mutters to right child of node mortal
51 [main] DEBUG com.evanhoffman.bst.Node  - Assigning tray to right child of node tragedy
51 [main] DEBUG com.evanhoffman.bst.Node  - Assigning linkage to right child of node knight
51 [main] DEBUG com.evanhoffman.bst.Node  - Assigning face's to left child of node gleaning
51 [main] DEBUG com.evanhoffman.bst.Node  - Assigning decreasing to right child of node debatable
51 [main] DEBUG com.evanhoffman.bst.Node  - Assigning spoiling to right child of node shined
51 [main] DEBUG com.evanhoffman.bst.Node  - Assigning slave's to left child of node spoiling
51 [main] DEBUG com.evanhoffman.bst.Node  - Assigning clarified to right child of node brands
51 [main] DEBUG com.evanhoffman.bst.Node  - Assigning inspection to right child of node ingredient
51 [main] DEBUG com.evanhoffman.bst.Node  - Assigning technically to right child of node synonymous
51 [main] DEBUG com.evanhoffman.bst.Node  - Assigning supervises to right child of node successor
51 [main] DEBUG com.evanhoffman.bst.Node  - Assigning disconnect to right child of node discard
51 [main] DEBUG com.evanhoffman.bst.Node  - Assigning whence to right child of node warmed
51 [main] DEBUG com.evanhoffman.bst.Node  - Assigning creator to right child of node contend
52 [main] DEBUG com.evanhoffman.bst.Node  - Assigning oppress to right child of node nailing
52 [main] DEBUG com.evanhoffman.bst.Node  - Assigning magnificent to right child of node linkage
52 [main] DEBUG com.evanhoffman.bst.Node  - Assigning launch to left child of node linkage
52 [main] DEBUG com.evanhoffman.bst.Node  - Assigning compensation to left child of node concentrate
52 [main] DEBUG com.evanhoffman.bst.Node  - Assigning cave to left child of node clarified
52 [main] DEBUG com.evanhoffman.bst.Node  - Assigning unbearable to right child of node tune's
52 [main] DEBUG com.evanhoffman.bst.Node  - Assigning verifies to left child of node vertical
52 [main] DEBUG com.evanhoffman.bst.Node  - Assigning compact to left child of node compensation
52 [main] DEBUG com.evanhoffman.bst.Node  - Assigning immense to left child of node ingredient
52 [main] DEBUG com.evanhoffman.bst.Node  - Assigning empire to left child of node equilibrium
52 [main] DEBUG com.evanhoffman.bst.Node  - Assigning foreseeable to right child of node face's
52 [main] DEBUG com.evanhoffman.bst.Node  - Assigning joking to left child of node journals
52 [main] DEBUG com.evanhoffman.bst.Node  - Assigning opts to right child of node oppress
52 [main] DEBUG com.evanhoffman.bst.Node  - Assigning convicting to left child of node creator
52 [main] DEBUG com.evanhoffman.bst.Node  - Assigning divisions to right child of node disconnect
52 [main] DEBUG com.evanhoffman.bst.Node  - Assigning fascinate to left child of node foreseeable
52 [main] DEBUG com.evanhoffman.bst.Node  - Assigning reactions to left child of node revenge
52 [main] DEBUG com.evanhoffman.bst.Node  - Assigning discriminated to left child of node divisions
53 [main] DEBUG com.evanhoffman.bst.Node  - Assigning unrealistic to right child of node unbearable
53 [main] DEBUG com.evanhoffman.bst.Node  - Assigning crowds to right child of node creator
53 [main] DEBUG com.evanhoffman.bst.Node  - Assigning marketed to left child of node midday
53 [main] DEBUG com.evanhoffman.bst.Node  - Assigning broad to left child of node cave
53 [main] DEBUG com.evanhoffman.bst.Node  - Assigning socket to right child of node slave's
53 [main] DEBUG com.evanhoffman.bst.Node  - Assigning verbal to left child of node verifies
53 [main] DEBUG com.evanhoffman.bst.Node  - Assigning register's to right child of node reactions
53 [main] DEBUG com.evanhoffman.bst.Node  - Assigning attributes to right child of node attorney
53 [main] DEBUG com.evanhoffman.bst.Node  - Assigning clarity to right child of node clarified
53 [main] DEBUG com.evanhoffman.bst.Node  - Assigning engage to right child of node empire
53 [main] DEBUG com.evanhoffman.bst.Node  - Assigning succeeded to left child of node successor
53 [main] DEBUG com.evanhoffman.bst.Node  - Assigning resembled to right child of node register's
53 [main] DEBUG com.evanhoffman.bst.Node  - Assigning kernel to left child of node knight
53 [main] DEBUG com.evanhoffman.bst.Node  - Assigning dot to right child of node divisions
53 [main] DEBUG com.evanhoffman.bst.Node  - Assigning wrecked to right child of node whence
54 [main] DEBUG com.evanhoffman.bst.Node  - Assigning gratuitously to left child of node guarding
54 [main] DEBUG com.evanhoffman.bst.Node  - Assigning syndicate to left child of node synonymous
54 [main] INFO com.evanhoffman.bst.StringBstBuilder  - Entire tree: {value=patient's, left={value=dual, left={value=bat, left={value=abort, left=null, right={value=attorney, left={value=aided}, right={value=attributes}}}, right={value=disabled, left={value=cloth, left={value=close's, left={value=blaming, left=null, right={value=boys, left={value=bow}, right={value=brands, left=null, right={value=clarified, left={value=cave, left={value=broad}, right=null}, right={value=clarity}}}}}, right=null}, right={value=dangers, left={value=concentrate, left={value=compensation, left={value=compact}, right=null}, right={value=contend, left=null, right={value=creator, left={value=convicting}, right={value=crowds}}}}, right={value=debatable, left=null, right={value=decreasing}}}}, right={value=discard, left=null, right={value=disconnect, left=null, right={value=divisions, left={value=discriminated}, right={value=dot}}}}}}, right={value=moderate, left={value=migrates, left={value=equilibrium, left={value=empire, left=null, right={value=engage}}, right={value=management, left={value=hip, left={value=globally, left={value=gleaning, left={value=face's, left=null, right={value=foreseeable, left={value=fascinate}, right=null}}, right=null}, right={value=guarding, left={value=gratuitously}, right=null}}, right={value=intimate, left={value=ingredient, left={value=immense}, right={value=inspection}}, right={value=journals, left={value=joking}, right={value=knight, left={value=kernel}, right={value=linkage, left={value=launch}, right={value=magnificent}}}}}}, right={value=midday, left={value=marketed}, right=null}}}, right=null}, right={value=nailing, left={value=mortal, left=null, right={value=mutters}}, right={value=oppress, left=null, right={value=opts}}}}}, right={value=scroll's, left={value=polynomial, left={value=planted}, right={value=prosecution, left=null, right={value=revenge, left={value=reactions, left=null, right={value=register's, left=null, right={value=resembled}}}, right={value=satisfaction}}}}, right={value=terrifying, left={value=supporter, left={value=stairs, left={value=shined, left=null, right={value=spoiling, left={value=slave's, left=null, right={value=socket}}, right=null}}, right={value=stream's, left=null, right={value=successor, left={value=succeeded}, right={value=supervises}}}}, right={value=synonymous, left={value=syndicate}, right={value=technically}}}, right={value=tomatoes, left=null, right={value=unusable, left={value=tune's, left={value=tragedy, left=null, right={value=tray}}, right={value=unbearable, left=null, right={value=unrealistic}}}, right={value=vertical, left={value=verifies, left={value=verbal}, right=null}, right={value=warmed, left=null, right={value=whence, left=null, right={value=wrecked}}}}}}}}}
55 [main] INFO com.evanhoffman.bst.StringBstBuilder  - Search for andy: 
55 [main] DEBUG com.evanhoffman.bst.Node  - andy is less than current node patient's, checking left (if it exists)
55 [main] DEBUG com.evanhoffman.bst.Node  - andy is less than current node dual, checking left (if it exists)
55 [main] DEBUG com.evanhoffman.bst.Node  - andy is less than current node bat, checking left (if it exists)
55 [main] DEBUG com.evanhoffman.bst.Node  - andy is >= current node abort, checking right (if exists)
55 [main] DEBUG com.evanhoffman.bst.Node  - andy is less than current node attorney, checking left (if it exists)
55 [main] DEBUG com.evanhoffman.bst.Node  - andy is >= current node aided, checking right (if exists)
55 [main] INFO com.evanhoffman.bst.StringBstBuilder  - max word: aided
56 [main] INFO com.evanhoffman.bst.StringBstBuilder  - Search for joker: 
56 [main] DEBUG com.evanhoffman.bst.Node  - joker is less than current node patient's, checking left (if it exists)
56 [main] DEBUG com.evanhoffman.bst.Node  - joker is >= current node dual, checking right (if exists)
56 [main] DEBUG com.evanhoffman.bst.Node  - joker is less than current node moderate, checking left (if it exists)
56 [main] DEBUG com.evanhoffman.bst.Node  - joker is less than current node migrates, checking left (if it exists)
56 [main] DEBUG com.evanhoffman.bst.Node  - joker is >= current node equilibrium, checking right (if exists)
56 [main] DEBUG com.evanhoffman.bst.Node  - joker is less than current node management, checking left (if it exists)
56 [main] DEBUG com.evanhoffman.bst.Node  - joker is >= current node hip, checking right (if exists)
56 [main] DEBUG com.evanhoffman.bst.Node  - joker is >= current node intimate, checking right (if exists)
56 [main] DEBUG com.evanhoffman.bst.Node  - joker is less than current node journals, checking left (if it exists)
56 [main] DEBUG com.evanhoffman.bst.Node  - joker is less than current node joking, checking left (if it exists)
56 [main] INFO com.evanhoffman.bst.StringBstBuilder  - max word: intimate
56 [main] INFO com.evanhoffman.bst.StringBstBuilder  - Search for kale: 
56 [main] DEBUG com.evanhoffman.bst.Node  - kale is less than current node patient's, checking left (if it exists)
56 [main] DEBUG com.evanhoffman.bst.Node  - kale is >= current node dual, checking right (if exists)
56 [main] DEBUG com.evanhoffman.bst.Node  - kale is less than current node moderate, checking left (if it exists)
56 [main] DEBUG com.evanhoffman.bst.Node  - kale is less than current node migrates, checking left (if it exists)
56 [main] DEBUG com.evanhoffman.bst.Node  - kale is >= current node equilibrium, checking right (if exists)
57 [main] DEBUG com.evanhoffman.bst.Node  - kale is less than current node management, checking left (if it exists)
57 [main] DEBUG com.evanhoffman.bst.Node  - kale is >= current node hip, checking right (if exists)
57 [main] DEBUG com.evanhoffman.bst.Node  - kale is >= current node intimate, checking right (if exists)
57 [main] DEBUG com.evanhoffman.bst.Node  - kale is >= current node journals, checking right (if exists)
57 [main] DEBUG com.evanhoffman.bst.Node  - kale is less than current node knight, checking left (if it exists)
57 [main] DEBUG com.evanhoffman.bst.Node  - kale is less than current node kernel, checking left (if it exists)
57 [main] INFO com.evanhoffman.bst.StringBstBuilder  - max word: journals
57 [main] INFO com.evanhoffman.bst.StringBstBuilder  - Search for moonshine: 
57 [main] DEBUG com.evanhoffman.bst.Node  - moonshine is less than current node patient's, checking left (if it exists)
57 [main] DEBUG com.evanhoffman.bst.Node  - moonshine is >= current node dual, checking right (if exists)
57 [main] DEBUG com.evanhoffman.bst.Node  - moonshine is >= current node moderate, checking right (if exists)
57 [main] DEBUG com.evanhoffman.bst.Node  - moonshine is less than current node nailing, checking left (if it exists)
57 [main] DEBUG com.evanhoffman.bst.Node  - moonshine is less than current node mortal, checking left (if it exists)
57 [main] INFO com.evanhoffman.bst.StringBstBuilder  - max word: moderate
57 [main] INFO com.evanhoffman.bst.StringBstBuilder  - Search for zebra: 
57 [main] DEBUG com.evanhoffman.bst.Node  - zebra is >= current node patient's, checking right (if exists)
57 [main] DEBUG com.evanhoffman.bst.Node  - zebra is >= current node scroll's, checking right (if exists)
57 [main] DEBUG com.evanhoffman.bst.Node  - zebra is >= current node terrifying, checking right (if exists)
58 [main] DEBUG com.evanhoffman.bst.Node  - zebra is >= current node tomatoes, checking right (if exists)
58 [main] DEBUG com.evanhoffman.bst.Node  - zebra is >= current node unusable, checking right (if exists)
58 [main] DEBUG com.evanhoffman.bst.Node  - zebra is >= current node vertical, checking right (if exists)
58 [main] DEBUG com.evanhoffman.bst.Node  - zebra is >= current node warmed, checking right (if exists)
58 [main] DEBUG com.evanhoffman.bst.Node  - zebra is >= current node whence, checking right (if exists)
58 [main] DEBUG com.evanhoffman.bst.Node  - zebra is >= current node wrecked, checking right (if exists)
58 [main] INFO com.evanhoffman.bst.StringBstBuilder  - max word: wrecked

</pre>