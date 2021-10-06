---
layout: post
status: publish
published: true
title: Script level upgrade for database 'master' failed
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 248
wordpress_url: http://www.erichumphrey.com/?p=248
date: '2011-11-09 10:17:04 -0600'
date_gmt: '2011-11-09 16:17:04 -0600'
categories:
- clusters
- dba
tags: []
comments: []
---
<p>A few weeks ago one of our clustered server nodes blue-screened and when it came back on SQL refused to start. I started digging in and this is what I found in the Event Log: "Script level upgrade for database 'master' failed because upgrade step 'sqlagent100_msdb_upgrade.sql' encountered error 200, state 7, severity 25." Immediately I started to suspect a corrupt master database. And I found this in the SQL Error Log:</p>
<blockquote><p>Creating procedure sp_sqlagent_get_perf_counters...<br />
Error: 468, Severity: 16, State: 9.<br />
<strong>Cannot resolve the collation conflict between "SQL_Latin1_General_CP1_CI_AS" and "Latin1_General_CI_AS" in the equal to operation.</strong><br />
Error: 912, Severity: 21, State: 2.<br />
Script level upgrade for database 'master' failed because upgrade step 'sqlagent100_msdb_upgrade.sql' encountered error 200, state 7, severity 25. This is a serious error condition which might interfere with regular operation and the database will be taken offline. If the error happened during upgrade of the 'master' database, it will prevent the entire SQL Server instance from starting. Examine the previous errorlog entries for errors, take the appropriate corrective actions and re-start the database so that the script upgrade steps run to completion.<br />
Error: 3417, Severity: 21, State: 3.<br />
Cannot recover the master database. SQL Server is unable to run. Restore master from a full backup, repair it, or rebuild it. For more information about how to rebuild the master database, see SQL Server Books Online.<br />
SQL Trace was stopped due to server shutdown. Trace ID = '1'. This is an informational message only; preser action is required.</p></blockquote>
<p>It turns out that the collation of msdb did not match master. Master was using SQL_Latin1_General_CP1_CI_AS while only msdb and one other database was using&nbsp;&nbsp;Latin1_General_CI_AS. The upgrade script that SQL Server ran at startup performed a join across those two databases resulting in the error. I'm still not sure about why this script got run as no service packs / cumulative updates had been applied to this server recently&nbsp;and it was originally installed as 2008, not an upgrade from 2005. With the upgrade script interrupted, it left the master db unusable.</p>
<p>I started going through the <a href="http://msdn.microsoft.com/en-us/library/ms190679.aspx">steps to restore master</a>.&nbsp;I was able to <a href="http://msdn.microsoft.com/en-us/library/ms188236%28v=sql.100%29.aspx">start the server in single user mode</a>, yet I could never get in as the single user. Something seemed to always be taking the connection before I could get in.</p>
<p>I had a long, long conversation with the #sqlhelp folks on Twitter about this. All of these guys and gals were tossing out help:&nbsp;@sqlinsaneo @kbriankelly @darrelllandrum @rusanu @DBArgenis @Kendra_Little @mrdenny @Bugboi @SQLSoldier. We stopped all services that were known to connect, stopped the browser service, changed the port, only allow IP connections, paused the cluster node I was working on, and still I was getting an error that only one admin was allowed at a time in single user mode.</p>
<p>Since I was getting nowhere fast, I decided on a different tact, namely to <a href="https://blogs.msdn.com/themes/blogs/generic/post.aspx?WeblogApp=psssql&amp;y=2008&amp;m=08&amp;d=29&amp;WeblogPostName=how-to-rebuild-system-databases-in-sql-server-2008&amp;GroupKeys=">rebuild master</a> then recover from backup. Rebuilding master was a fairly quick process. An important note about rebuilding master is that is also rebuilds msdb. Now I had two databases to recover from backup. Once the rebuild was complete, I was successfully able to&nbsp;start the service in single user mode &nbsp;and was able to connect (yay!!!). Now I could restore a backup of master, restart SQL normally and restore msdb. I finally had a running instance 2.5 hours later.</p>
<p>Important lessons learned from this experience:</p>
<ul>
<li>If single user mode isn't working and you have a good backup of msdb, rebuild then recover. Rebuilding might fix some issues and get you on the road to recovery quicker.</li>
<li>Collations of all the system databases really should match. This whole issue was because of a collation conflict between master and msdb.</li>
</ul>
<div>Thanks again to:</div>
<div>
<ul>
<li>Allen Kinsel (<a href="http://www.allenkinsel.com">blog</a> | <a href="http://twitter.com/#!/sqlinsaneo">twitter</a>)</li>
<li>K. Brian Kelley (<a href="http://www.truthsolutions.com">blog</a> | <a href="http://twitter.com/#!/kbriankelley">twitter</a>)</li>
<li>Darrell Landrum (<a href="http://twitter.com/#!/darrelllandrum">twitter</a>)</li>
<li>Remus Rusanu (<a href="http://rusanu.com">blog</a> | <a href="http://twitter.com/#!/rusanu">twitter</a>)</li>
<li>Argenis Fernandez (<a href="http://www.sqlblog.com/blogs/argenis_fernandez">blog</a> | <a href="http://twitter.com/#!/DBArgenis">twitter</a>)</li>
<li>Kendra Little (<a href="http://littlekendra.com">blog</a> | <a href="http://twitter.com/#!/Kendra_Little">twitter</a>)</li>
<li>Denny Cherry (<a href="http://www.mrdenny.com">blog</a> | <a href="http://twitter.com/#!/mrdenny">twitter</a>)</li>
<li>Bugboi (<a href="http://twitter.com/#!/Bugboi">twitter</a>)</li>
<li>Robert Davis (<a href="http://www.sqlsoldier.com">blog</a> | <a href="http://twitter.com/#!/SQLSoldier">twitter</a>)</li>
</ul>
</div>
