---
layout: post
title: SQLskills Immersion Event on Internals and Performance - Day 4
date: '2011-02-24 21:55:39 -0600'
categories:
- dba
- learning
---
<h3>Overview</h3>
<p>We just finished the fourth day of SQLskills learning. Today was all on indexes and statistics. We learned about choosing a clustering key, what makes a good one, what makes a bad one. We also have to keep in mind the consequences to non-clustered indexes when choosing the clustered index. We learned about why SQL will choose to use a certain index and why it might use one we wouldn't expect.</p>
<p>Tipping points are interesting. These are the point at which SQL says, it's cheaper to do a scan with sequential IOs instead of a lookup with random IOs. I was amazed at how small a percentage is required before it tips to a scan. The narrower the table, the quicker the tip. This isn't necessarily a bad thing, but you have to be aware of it. Covering indexes aren't affected by tipping points.</p>
<p>Which brings up covering indexes. Be careful of creating too many and too narrow of covering indexes. You really need to consider the entire workload before creating any new indexes. You should also periodically review indexes for consolidation.</p>
<p>There seems to be many misconceptions hanging around or old best practices that have been superseded when 7.0 / 2000 came out. For example, you don't necessarily have to list the most selective column in a NC index. List in order of use frequency.</p>
<p>Tonight Paul &amp; Kimberly opened the floor to anyone wanting to present a 20 minute topic. Kendra Little (<a href="http://littlekendra.com">blog</a> | <a href="http://twitter.com/#!/Kendra_Little">twitter</a>),&nbsp;Brian D. Knight (<a href="http://twitter.com/#!/BrianDKnight">twitter</a>),&nbsp;Trevor Barkhouse (<a href="http://sqlserversleuth.com">blog</a> | <a href="http://twitter.com/#!/SQLServerSleuth">twitter</a>),&nbsp;Martin Hill (<a href="http://martincatherall.com">blog</a> | <a href="http://twitter.com/#!/martinznz">twitter</a>) and myself presented. I know I flubbed my presentation by going down a rabbit hole. Twenty minutes isn't enough to go down those holes. I need better presentation discipline and posture.</p>
<p>Today was a bit more difficult staying focused. I think it's just being in the same environment for almost a week. Hopefully I'll be more focused tomorrow.</p>
<h3>Indexing for Performance (Index Internals)</h3>
<ul>
<li>Cluster on IDENTITY... not so fast (many inserts across many tables in one file), add extra files to help alleviate</li>
<li>If locked to one file group (PRIMARY) you can add files and SHRINK ... EMPTY to balance, but not generally advisable</li>
<li><a href="http://www.sqlskills.com/BLOGS/KIMBERLY/post/Companion-content-for-Chapter-6-(Index-Internals)-of-SQL-Server-2008-Internals.aspx">Companion-content-for-Chapter-6-(Index-Internals)-of-SQL-Server-2008-Internals</a></li>
<li><a href="http://www.sqlskills.com/BLOGS/PAUL/post/TechNet-Magazine-Effective-Database-Maintenance-article-and-August-SQL-QA-column.aspx">TechNet-Magazine-Effective-Database-Maintenance-article-and-August-SQL-QA-column</a></li>
<li><a href="http://sqlskills.com/BLOGS/KIMBERLY/category/sp_helpindex-rewrites.aspx">sp_helpindex-rewrites</a></li>
<li>You can INCLUDE other columns in a UNIQUE NON-CLUSTERED INDEX (<a href="http://sqlskills.com/BLOGS/KIMBERLY/post/Foreign-Keys-can-reference-UNIQUE-indexes-(without-constraints).aspx">Foreign-Keys-can-reference-UNIQUE-indexes-(without-constraints)</a>)</li>
<li>sp_ doesn't mean Stored Procedure... it means SPecial</li>
</ul>
<h3>Indexing for Performance (Internals &amp; Data Access)</h3>
<ul>
<li>Have an idea of each table's tipping points, they can be smaller than you think</li>
<li>Dense tables will have smaller tipping points, possibly < 1%</li>
<li>Forced parameterization could conflict with filtered indexes / stats</li>
<li>Always explicitly&nbsp;specify&nbsp;session options</li>
<li>Filtering indexes / stats can offer widely covering indexes when highly selective</li>
<li>Most selective column shouldn't necessarily be first in index</li>
<li>If equality based, order columns in index by frequency used</li>
</ul>
<h3>Indexing for Performance (Statistics)</h3>
<ul>
<li>Query predicates can be affected by order, for large numbers of predicates</li>
<li>Histograms are usually key</li>
<li>Update stats before rebuilding indexes as it affects&nbsp;parallelization</li>
<li>sp_createstats 'indexonly' to create stats on non-statted columns of indexes</li>
<li>Out of date stats can affect query plan query cost relative to batch</li>
</ul>
