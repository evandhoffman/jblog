---
title: PostgreSQL query to determine the largest tables in the database
author: Evan Hoffman
excerpt: A PostgreSQL query to see the largest tables on-disk in a given database and how large their indices are.
layout: post
permalink: /2011/05/17/postgresql-query-to-determine-the-largest-tables-in-the-database/
dsq_thread_id:
  - 2960181696
categories:
  - Uncategorized
tags:
  - linux
  - postgresql
  - sql
  - table size
  - work
---
This is just a handy query I use from time to time to see which tables are growing madly, and how much of the growth is index bloat.

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
</pre>
      </td>
      
      <td class="code">
        <pre class="sql" style="font-family:monospace;"><span style="color: #993333; font-weight: bold;">SELECT</span>
schemaname<span style="color: #66cc66;">,</span>
tablename<span style="color: #66cc66;">,</span>
pg_size_pretty<span style="color: #66cc66;">&#40;</span>pg_relation_size<span style="color: #66cc66;">&#40;</span>schemaname <span style="color: #66cc66;">||</span> <span style="color: #ff0000;">'.'</span> <span style="color: #66cc66;">||</span> tablename<span style="color: #66cc66;">&#41;</span><span style="color: #66cc66;">&#41;</span> <span style="color: #993333; font-weight: bold;">AS</span> size_p<span style="color: #66cc66;">,</span>
pg_total_relation_size<span style="color: #66cc66;">&#40;</span>schemaname <span style="color: #66cc66;">||</span> <span style="color: #ff0000;">'.'</span> <span style="color: #66cc66;">||</span> tablename<span style="color: #66cc66;">&#41;</span> <span style="color: #993333; font-weight: bold;">AS</span> siz<span style="color: #66cc66;">,</span>
pg_size_pretty<span style="color: #66cc66;">&#40;</span>pg_total_relation_size<span style="color: #66cc66;">&#40;</span>schemaname <span style="color: #66cc66;">||</span> <span style="color: #ff0000;">'.'</span> <span style="color: #66cc66;">||</span> tablename<span style="color: #66cc66;">&#41;</span><span style="color: #66cc66;">&#41;</span> <span style="color: #993333; font-weight: bold;">AS</span> total_size_p<span style="color: #66cc66;">,</span>
pg_total_relation_size<span style="color: #66cc66;">&#40;</span>schemaname <span style="color: #66cc66;">||</span> <span style="color: #ff0000;">'.'</span> <span style="color: #66cc66;">||</span> tablename<span style="color: #66cc66;">&#41;</span> <span style="color: #66cc66;">-</span> pg_relation_size<span style="color: #66cc66;">&#40;</span>schemaname <span style="color: #66cc66;">||</span> <span style="color: #ff0000;">'.'</span> <span style="color: #66cc66;">||</span> tablename<span style="color: #66cc66;">&#41;</span> <span style="color: #993333; font-weight: bold;">AS</span> index_size<span style="color: #66cc66;">,</span>
<span style="color: #66cc66;">&#40;</span><span style="color: #cc66cc;">100</span><span style="color: #66cc66;">*</span><span style="color: #66cc66;">&#40;</span>pg_total_relation_size<span style="color: #66cc66;">&#40;</span>schemaname <span style="color: #66cc66;">||</span> <span style="color: #ff0000;">'.'</span> <span style="color: #66cc66;">||</span> tablename<span style="color: #66cc66;">&#41;</span> <span style="color: #66cc66;">-</span> pg_relation_size<span style="color: #66cc66;">&#40;</span>schemaname <span style="color: #66cc66;">||</span> <span style="color: #ff0000;">'.'</span> <span style="color: #66cc66;">||</span> tablename<span style="color: #66cc66;">&#41;</span><span style="color: #66cc66;">&#41;</span><span style="color: #66cc66;">&#41;</span><span style="color: #66cc66;">/</span><span style="color: #993333; font-weight: bold;">CASE</span> <span style="color: #993333; font-weight: bold;">WHEN</span> pg_total_relation_size<span style="color: #66cc66;">&#40;</span>schemaname <span style="color: #66cc66;">||</span> <span style="color: #ff0000;">'.'</span> <span style="color: #66cc66;">||</span> tablename<span style="color: #66cc66;">&#41;</span> <span style="color: #66cc66;">=</span> <span style="color: #cc66cc;"></span> <span style="color: #993333; font-weight: bold;">THEN</span> <span style="color: #cc66cc;">1</span> <span style="color: #993333; font-weight: bold;">ELSE</span> pg_total_relation_size<span style="color: #66cc66;">&#40;</span>schemaname <span style="color: #66cc66;">||</span> <span style="color: #ff0000;">'.'</span> <span style="color: #66cc66;">||</span> tablename<span style="color: #66cc66;">&#41;</span> <span style="color: #993333; font-weight: bold;">END</span> <span style="color: #66cc66;">||</span> <span style="color: #ff0000;">'%'</span> <span style="color: #993333; font-weight: bold;">AS</span> index_pct
<span style="color: #993333; font-weight: bold;">FROM</span> pg_tables
<span style="color: #993333; font-weight: bold;">ORDER</span> <span style="color: #993333; font-weight: bold;">BY</span> siz <span style="color: #993333; font-weight: bold;">DESC</span> <span style="color: #993333; font-weight: bold;">LIMIT</span> <span style="color: #cc66cc;">20</span>;</pre>
      </td>
    </tr>
  </table>
</div>

This returns schema, table, size on disk (in human-readable and byte format &#8211; for sorting), and total size on disk including indices, and the percentage of the total size that comprises the indexes.

Sample result from our <a href="http://www.igniterealtime.org/downloads/index.jsp" onclick="_gaq.push(['_trackEvent', 'outbound-article', 'http://www.igniterealtime.org/downloads/index.jsp', 'OpenFire']);" >OpenFire</a> Jabber server:

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
</pre>
      </td>
      
      <td class="code">
        <pre class="bash" style="font-family:monospace;"> schemaname <span style="color: #000000; font-weight: bold;">|</span>      tablename       <span style="color: #000000; font-weight: bold;">|</span>   size_p   <span style="color: #000000; font-weight: bold;">|</span>    siz    <span style="color: #000000; font-weight: bold;">|</span> total_size_p <span style="color: #000000; font-weight: bold;">|</span> index_size <span style="color: #000000; font-weight: bold;">|</span> index_pct 
------------+----------------------+------------+-----------+--------------+------------+-----------
 public     <span style="color: #000000; font-weight: bold;">|</span> ofconversation       <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">71</span> MB      <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">159236096</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">152</span> MB       <span style="color: #000000; font-weight: bold;">|</span>   <span style="color: #000000;">84623360</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">53</span><span style="color: #000000; font-weight: bold;">%</span>
 public     <span style="color: #000000; font-weight: bold;">|</span> ofconparticipant     <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">38</span> MB      <span style="color: #000000; font-weight: bold;">|</span>  <span style="color: #000000;">95395840</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">91</span> MB        <span style="color: #000000; font-weight: bold;">|</span>   <span style="color: #000000;">55394304</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">58</span><span style="color: #000000; font-weight: bold;">%</span>
 public     <span style="color: #000000; font-weight: bold;">|</span> ofpresence           <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">10184</span> kB   <span style="color: #000000; font-weight: bold;">|</span>  <span style="color: #000000;">10452992</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">10208</span> kB     <span style="color: #000000; font-weight: bold;">|</span>      <span style="color: #000000;">24576</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;"></span><span style="color: #000000; font-weight: bold;">%</span>
 public     <span style="color: #000000; font-weight: bold;">|</span> ofpubsubitem         <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">5336</span> kB    <span style="color: #000000; font-weight: bold;">|</span>   <span style="color: #000000;">7733248</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">7552</span> kB      <span style="color: #000000; font-weight: bold;">|</span>    <span style="color: #000000;">2269184</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">29</span><span style="color: #000000; font-weight: bold;">%</span>
 public     <span style="color: #000000; font-weight: bold;">|</span> ofid                 <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">4008</span> kB    <span style="color: #000000; font-weight: bold;">|</span>   <span style="color: #000000;">4120576</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">4024</span> kB      <span style="color: #000000; font-weight: bold;">|</span>      <span style="color: #000000;">16384</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;"></span><span style="color: #000000; font-weight: bold;">%</span>
 public     <span style="color: #000000; font-weight: bold;">|</span> ofpubsubsubscription <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">912</span> kB     <span style="color: #000000; font-weight: bold;">|</span>   <span style="color: #000000;">1458176</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">1424</span> kB      <span style="color: #000000; font-weight: bold;">|</span>     <span style="color: #000000;">524288</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">35</span><span style="color: #000000; font-weight: bold;">%</span>
 public     <span style="color: #000000; font-weight: bold;">|</span> ofpubsubaffiliation  <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">728</span> kB     <span style="color: #000000; font-weight: bold;">|</span>   <span style="color: #000000;">1196032</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">1168</span> kB      <span style="color: #000000; font-weight: bold;">|</span>     <span style="color: #000000;">450560</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">37</span><span style="color: #000000; font-weight: bold;">%</span>
 public     <span style="color: #000000; font-weight: bold;">|</span> ofoffline            <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">832</span> kB     <span style="color: #000000; font-weight: bold;">|</span>    <span style="color: #000000;">942080</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">920</span> kB       <span style="color: #000000; font-weight: bold;">|</span>      <span style="color: #000000;">90112</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">9</span><span style="color: #000000; font-weight: bold;">%</span>
 pg_catalog <span style="color: #000000; font-weight: bold;">|</span> pg_depend            <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">320</span> kB     <span style="color: #000000; font-weight: bold;">|</span>    <span style="color: #000000;">794624</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">776</span> kB       <span style="color: #000000; font-weight: bold;">|</span>     <span style="color: #000000;">466944</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">58</span><span style="color: #000000; font-weight: bold;">%</span>
 pg_catalog <span style="color: #000000; font-weight: bold;">|</span> pg_attribute         <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">384</span> kB     <span style="color: #000000; font-weight: bold;">|</span>    <span style="color: #000000;">761856</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">744</span> kB       <span style="color: #000000; font-weight: bold;">|</span>     <span style="color: #000000;">368640</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">48</span><span style="color: #000000; font-weight: bold;">%</span>
 pg_catalog <span style="color: #000000; font-weight: bold;">|</span> pg_proc              <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">376</span> kB     <span style="color: #000000; font-weight: bold;">|</span>    <span style="color: #000000;">753664</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">736</span> kB       <span style="color: #000000; font-weight: bold;">|</span>     <span style="color: #000000;">368640</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">48</span><span style="color: #000000; font-weight: bold;">%</span>
 pg_catalog <span style="color: #000000; font-weight: bold;">|</span> pg_rewrite           <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">72</span> kB      <span style="color: #000000; font-weight: bold;">|</span>    <span style="color: #000000;">270336</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">264</span> kB       <span style="color: #000000; font-weight: bold;">|</span>     <span style="color: #000000;">196608</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">72</span><span style="color: #000000; font-weight: bold;">%</span>
 pg_catalog <span style="color: #000000; font-weight: bold;">|</span> pg_description       <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">136</span> kB     <span style="color: #000000; font-weight: bold;">|</span>    <span style="color: #000000;">245760</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">240</span> kB       <span style="color: #000000; font-weight: bold;">|</span>     <span style="color: #000000;">106496</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">43</span><span style="color: #000000; font-weight: bold;">%</span>
 pg_catalog <span style="color: #000000; font-weight: bold;">|</span> pg_operator          <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">112</span> kB     <span style="color: #000000; font-weight: bold;">|</span>    <span style="color: #000000;">237568</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">232</span> kB       <span style="color: #000000; font-weight: bold;">|</span>     <span style="color: #000000;">122880</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">51</span><span style="color: #000000; font-weight: bold;">%</span>
 pg_catalog <span style="color: #000000; font-weight: bold;">|</span> pg_class             <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">88</span> kB      <span style="color: #000000; font-weight: bold;">|</span>    <span style="color: #000000;">212992</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">208</span> kB       <span style="color: #000000; font-weight: bold;">|</span>     <span style="color: #000000;">122880</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">57</span><span style="color: #000000; font-weight: bold;">%</span>
 pg_catalog <span style="color: #000000; font-weight: bold;">|</span> pg_type              <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">64</span> kB      <span style="color: #000000; font-weight: bold;">|</span>    <span style="color: #000000;">155648</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">152</span> kB       <span style="color: #000000; font-weight: bold;">|</span>      <span style="color: #000000;">90112</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">57</span><span style="color: #000000; font-weight: bold;">%</span>
 pg_catalog <span style="color: #000000; font-weight: bold;">|</span> pg_statistic         <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">72</span> kB      <span style="color: #000000; font-weight: bold;">|</span>     <span style="color: #000000;">98304</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">96</span> kB        <span style="color: #000000; font-weight: bold;">|</span>      <span style="color: #000000;">24576</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">25</span><span style="color: #000000; font-weight: bold;">%</span>
 pg_catalog <span style="color: #000000; font-weight: bold;">|</span> pg_amop              <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">24</span> kB      <span style="color: #000000; font-weight: bold;">|</span>     <span style="color: #000000;">90112</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">88</span> kB        <span style="color: #000000; font-weight: bold;">|</span>      <span style="color: #000000;">65536</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">72</span><span style="color: #000000; font-weight: bold;">%</span>
 pg_catalog <span style="color: #000000; font-weight: bold;">|</span> pg_conversion        <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">16</span> kB      <span style="color: #000000; font-weight: bold;">|</span>     <span style="color: #000000;">90112</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">88</span> kB        <span style="color: #000000; font-weight: bold;">|</span>      <span style="color: #000000;">73728</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">81</span><span style="color: #000000; font-weight: bold;">%</span>
 pg_catalog <span style="color: #000000; font-weight: bold;">|</span> pg_constraint        <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">8192</span> bytes <span style="color: #000000; font-weight: bold;">|</span>     <span style="color: #000000;">81920</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">80</span> kB        <span style="color: #000000; font-weight: bold;">|</span>      <span style="color: #000000;">73728</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #000000;">90</span><span style="color: #000000; font-weight: bold;">%</span>
<span style="color: #7a0874; font-weight: bold;">&#40;</span><span style="color: #000000;">20</span> rows<span style="color: #7a0874; font-weight: bold;">&#41;</span>
&nbsp;
Time: <span style="color: #000000;">8.917</span> ms</pre>
      </td>
    </tr>
  </table>
</div>