---
layout: post
status: publish
published: true
title: NOLOCK for Developers
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 143
wordpress_url: http://www.erichumphrey.com/?p=143
date: '2011-03-25 07:20:47 -0500'
date_gmt: '2011-03-25 13:20:47 -0500'
categories:
- dba
- development
- sql
- SQLServerPedia Syndication
tags: []
comments:
- id: 60
  author: Shannon Lowder
  author_email: slowder@toyboxcreations.net
  author_url: http://shannonlowder.com
  date: '2011-03-25 07:37:45 -0500'
  date_gmt: '2011-03-25 13:37:45 -0500'
  content: |-
    I've also found a situation where a NOLOCK can actually cause blocking on INSERT statements.  I'd have to dig for my exact scenario, but I do remember if you select with NOLOCK on a table that also has an index, the schema lock that NOLOCK holds prevents inserts from occuring until the NOLOCK query has completed.  It was wild when I was able to see it, and even more so when others in the forum could reproduce the scenario too.

    Yet another reason NOLOCKs need to be used less than they are in the development.
- id: 61
  author: NOLOCK Sprinkles | SQLseeker.com
  author_email: ''
  author_url: http://sqlseeker.com/archive/2012/06/nolock-sprinkles/
  date: '2012-06-10 14:18:24 -0500'
  date_gmt: '2012-06-10 20:18:24 -0500'
  content: "[...] http://www.erichumphrey.com/2011/03/nolock-for-developers/ http://michaeljswart.com/tag/nolock-read-uncommitted/
    \  This entry was posted in SQL and tagged [...]"
---
<p><span style="font-size: 20px; font-weight: bold;">What is NOLOCK?</span></p>
<p>NOLOCK is a table hint for SQL Server that tells the engine to not take locks and ignore any locks when querying that table. This is equivalent to the READUNCOMMITTED table hint.</p>
<p>From <a href="http://msdn.microsoft.com/en-us/library/ms187373.aspx">MSDN</a>:</p>
<blockquote><p>Specifies that dirty reads are allowed. No shared locks are issued to prevent other transactions from modifying data read by the current transaction, and exclusive locks set by other transactions do not block the current transaction from reading the locked data. Allowing dirty reads can cause higher concurrency, but at the cost of reading data modifications that then are rolled back by other transactions. This may generate errors for your transaction, present users with data that was never committed, or cause users to see records twice (or not at all). For more information about dirty reads, nonrepeatable reads, and phantom reads, see&nbsp;<a href="http://msdn.microsoft.com/en-us/library/ms190805.aspx">Concurrency Effects</a>.</p></blockquote>
<p>So you might be thinking that this NOLOCK thing is great and will solve all your performance problems...</p>
<h2>There's a Trade-off...</h2>
<p>What's the cost of using the NOLOCK hint or setting the Isolation Level to READ UNCOMMITTED everywhere? Well, you cannot guarantee the accuracy of your queries anymore. If the queries can read data in flight, you might read data that never gets committed. You might read data twice. You might read data that's in the process of being updated or deleted. There's a lot of mights in there. You need to ask yourself and the person that depends on this query, do they value accuracy or speed.</p>
<h2>NOLOCK is a Last Resort</h2>
<p>Many of the problems cause by locking and blocking, which NOLOCK gets around, can be solved by basic performance tuning. Limiting the data you are working with by using appropriate WHERE clauses is a good first step. SQL Server by default tries to lock data at the row level and only&nbsp;escalates&nbsp;to the table level if it determines it would be cheaper to do so. It might escalate <a href="http://sqlskills.com/BLOGS/KIMBERLY/category/The-Tipping-Point.aspx">quicker than you think</a> though. If &nbsp;two queries are operating on different rows in the same table, they will not block each other unless escalation has occurred. Note that I mention rows, not columns. If two queries are making changes to different columns in the same row, one will block the other until done. Adding appropriate indexes or modifying existing indexes would be a second step. These can speed up readers which let writers in quicker.</p>
<p>Another alternative to using NOLOCK is to use some <a href="http://msdn.microsoft.com/en-us/library/cc917674.aspx">level of row versioning</a> within SQL Server such as&nbsp;READ_COMMITTED_SNAPSHOT and / or ALLOW_SNAPSHOT_ISOLATION. This allows more concurrency at the cost of increased tempdb usage. It does this by storing old version(s) of a row in tempdb until it is no longer needed. If you're doing a quick read with no other connection trying to alter that data, then the lifespan in tempdb is very short. If more concurrent operations happen on that row, the lifespan can grow. Before implementing either of these levels, be sure to have a good understanding of the <a href="http://technet.microsoft.com/en-us/library/ms177404.aspx">trade-offs of row versioning</a> as it might cause more problems that it solves.</p>
<h2>The NOLOCK Resort</h2>
<p>Earlier I said to use NOLOCK as a last resort... in transactional workloads. NOLOCK might&nbsp;make a good fit for when you're reporting out of your transactional database. Reporting is better done from a reporting copy of the data. Sometimes we don't have that luxury and reporting must be done against the live data. In most cases, we want the transactional workload to take priority over the reports. Telling our reports not to take locks allows the transactions to continue unhindered by locks, though they still might be fighting for CPU and I/O resources. The same rules apply about speed vs accuracy, but usually reports don't have to be to the second or penny accurate. In this case, NOLOCK may make sense. Just make sure your report consumers are aware that there might be a margin of error.</p>
<h2>Further Reading:</h2>
<p>Kendra Little (<a href="http://littlekendra.com">blog</a> | <a href="http://twitter.com/#!/Kendra_Little">twitter</a>) <a href="http://www.littlekendra.com/2011/02/08/isoposter/">made up a poster</a> that helps visualize the different isolation levels. She also put together a great <a href="http://www.littlekendra.com/resources/isolation/">list of links</a> about NOLOCK and the other isolation levels.</p>
<p>Paul Randal (<a href="http://www.sqlskills.com/blogs/paul">blog</a> | <a href="http://twitter.com/#!/PaulRandal">twitter</a>) has some great <a href="http://www.sqlskills.com/BLOGS/PAUL/category/Locking.aspx">content on locking</a>. He also does a good job of explaining locking &amp; blocking in <a href="http://technet.microsoft.com/en-us/sqlserver/gg508892.aspx">this video</a>.</p>
<p>Kimberly Tripp (<a href="http://www.sqlskills.com/blogs/Kimberly/">blog</a> | <a href="http://twitter.com/#!/KimberlyLTripp">twitter</a>) has more info <a href="http://www.sqlskills.com/BLOGS/KIMBERLY/category/Indexes.aspx">about indexing</a>.</p>
