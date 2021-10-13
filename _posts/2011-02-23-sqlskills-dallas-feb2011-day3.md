---
layout: post
title: SQLskills Immersion Event on Internals and Performance - Day 3
date: '2011-02-23 19:52:28 -0600'
categories:
- dba
- learning
---
<h3>Overview</h3>
<p>Today was mostly about Table Design, Partitioning, with a little of Indexing. Partitioning is a big, complex topics with a lot of intricate details and interactions. If you are thinking about implementing partitioning using either partitioned tables or partitioned views, do your homework. Don't just implement either without knowing the background of both. This is not a simple subject.</p>
<p>Another key take away is, be smart when choosing between heaps and clustered indexes. Heaps have their place, but good CIs are the better choice in most cases. Also understand the effect that choosing certain CIs will have on your non-clustered indexes. Beware choosing a bad CI.</p>
<p>After hours, SQL Sentry did a demo on their Event Manager &amp; Performance Adviser products. Practical experience behind the products. The disk info they give is amazing. Tracking down blocking and deadlocks look really easy using their tool.</p>
<h3>Table Design Strategies &amp; Partitioning</h3>
<div id="_mcePaste">
<ul>
<li>Partitioned views more likely than partitioned tables</li>
<li>Neither solves all problems by itself</li>
<li>A single large table toes NOT have to be one table</li>
<li>Partitioned tables - EE only</li>
<li>Partitioned views - Any edition</li>
<li>http://sqlskills.com/BLOGS/KIMBERLY/post/Little-Bobby-Tables-SQL-Injection-and-EXECUTE-AS.aspx</li>
<li>Staging filegroups, once clean, Rebuild Clustered Index on destination FG</li>
<li>PV: check constraints after load</li>
<li>PV: nothing protects you from creating different indexes on participating tables</li>
<li>PV: make sure indexes match</li>
<li>Re-enabling&nbsp;constraints should be done WITH CHECK</li>
<li>PT Function = Logical, Scheme = Physical</li>
<li>Do Partition Lab, then read Kim's 2005 WP, then read Ron's 2008 WP using DATE, then Online Ops lab</li>
<li>Using functions in defining the partition function is a one time calculation upon creation</li>
<li>Switching out tables is easier than switching in</li>
<li>Requirements are checked when switching in
<ul>
<li>Set next used file group</li>
<li>Constraint</li>
<li>SPLIT Function</li>
<li>SWITCH</li>
</ul>
</li>
</ul>
</div>
<h3>Indexing for Performance</h3>
<div id="_mcePaste">
<ul>
<li>SQL Server has an error 666, if you exhaust a uniquifier (INT) on a poorly chosen clustered index</li>
</ul>
</div>
