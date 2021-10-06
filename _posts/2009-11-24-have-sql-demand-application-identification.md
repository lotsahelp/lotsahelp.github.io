---
layout: post
status: publish
published: true
title: Have SQL Demand Application Identification
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 11
wordpress_url: http://www.erichumphrey.com/2009/11/have-sql-demand-application-identification/
date: '2009-11-24 08:27:00 -0600'
date_gmt: '2009-11-24 14:27:00 -0600'
categories:
- sql triggers
tags: []
comments:
- id: 4
  author: Eric Humphrey &raquo; Prevent SQL Logins from using SSMS &#8230; or any
    other app
  author_email: ''
  author_url: http://www.erichumphrey.com/2011/06/prevent-sql-logins-from-using-ssms/
  date: '2011-06-17 20:21:04 -0500'
  date_gmt: '2011-06-18 02:21:04 -0500'
  content: "[...] In a continuation of my previous LOGON TRIGGER post. [...]"
---
<p>How many times have you run Profiler on your SQL Servers to see this?</p>
<p><a style="clear: left; float: left; margin-bottom: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/_OEOq9aEto20/SwvqsplCArI/AAAAAAAADDI/09TjlPbCays/s1600/Profiler+pic+cropped.png"><img src="http://2.bp.blogspot.com/_OEOq9aEto20/SwvqsplCArI/AAAAAAAADDI/09TjlPbCays/s400/Profiler+pic+cropped.png" border="0" alt="" /></a></p>
<p>With all of those apps identifying themselves as .Net SqlClient Data Provider, how can you tell which app is really making those queries?  Unless each app uses a distinct login, you would never know. And many times devs will reuse logins for their own ease.</p>
<p>I ran into this problem while I was doing some migration planning. I was profiling the server to see what apps were making queries to a specific database we were migrating over to another server. I wanted to make sure that anything making a connection was properly configured after the move. I found something similar to the shot above; connections from different machines making different queries at different, albeit regular intervals, all identified as ".Net SqlClient Data Provider." With some other detective work, I was able to figure out where they were coming from.</p>
<p>How can this be avoided? When developers are setting up their connection strings, they need to specify the Application Name parameter. An example is below.</p>
<pre lang="xml">
<add name="LOCAL"
    connectionString="Data Source=(local);Initial Catalog=master;Integrated Security=True;Application Name=LoginTriggerDemoApp"/></pre>
<p>Using <a href="http://msdn.microsoft.com/en-us/library/bb326598.aspx">LOGON TRIGGERS</a> in SQL 2008 and up, you can go one step further and start auditing logons and watching for any connection made that uses a generic Application Name. There is a limited amount of information available in the <a href="http://msdn.microsoft.com/en-us/library/ms173781.aspx">EVENTDATA()</a> from a LOGON EVENT.  You can get more information about the connection by joining to the <a href="http://msdn.microsoft.com/en-us/library/ms176013.aspx">sys.dm_exec_sessions</a> DMV.</p>
<pre lang="tsql">
...
DECLARE @data XML, @ApplicationName NVARCHAR(128)
SET @data = EVENTDATA()
SELECT @ApplicationName = [program_name]
FROM sys.dm_exec_sessions
WHERE session_id = @data.value('(/EVENT_INSTANCE/SPID)[1]', 'int')
...
</pre>
<p>In the code above, I first declare an XML variable to hold the data that comes from the EVENTDATA() function. Then use the SPID to join to the sys.dm_exec_sessions DMV to get the program_name, which becomes our Application Name. From this, you can log information about the connection. You can start researching anything that doesn't have a specific application name and start encouraging your developers to include that portion of the connection string.</p>
<p>You can go even further and block any generic connections. Using the trigger below, any connection that comes in under "<em>.Net SqlClient Data Provider</em>" gets disconnected via ROLLBACK. You can add to or alter the IN clause to prevent generic Java, PHP, etc. connections. You can check for blanks, or even prevent certain apps from logging in all together, regardless of credentials.</p>
<pre lang="tsql">
USE [master];
GO


/****** Object: DdlTrigger [Deny_Generic_ConnString_Trigger] ******/
IF EXISTS(
	SELECT * FROM master.sys.server_triggers
	WHERE parent_class_desc = 'SERVER'
		AND name = N'Deny_Generic_ConnString_Trigger')
DROP TRIGGER [Deny_Generic_ConnString_Trigger] ON ALL SERVER
GO

CREATE TRIGGER Deny_Generic_ConnString_Trigger
ON ALL SERVER
FOR LOGON
AS
BEGIN
DECLARE @AppName nvarchar(128)
SELECT @AppName = [program_name]
FROM sys.dm_exec_sessions
WHERE session_id = EVENTDATA().value(
    '(/EVENT_INSTANCE/SPID)[1]', 'int')


IF @AppName IN ('.Net SqlClient Data Provider')
    ROLLBACK; --Disconnect the session

END;
</pre>
<h2>Notes for Deployment</h2>
<p>If you have dev &amp; QA servers, test these out there first. Give your application developers time to make changes and test against these new triggers. Also, you may not be able to change 3<sup>rd</sup> party apps, so you can make exceptions based on host names or anything else available in the EVENTDATA() or sys.dm_exec_sessions. I did find out that Reporting Services does not like either of these triggers in place. Even the logging trigger will prevent the Report Manager from running.</p>
<p>Blocking generic applications may have consequences on a server that is already serving data to unidentified apps, so be careful. You may want to put logging in for a time and track down any offending applications and give them time to change. For a server coming online though, blocking generic apps may be a good way of preventing a lot of unnecessary detective work, and make profiling or migrating your servers easier.</p>
<p>I have included my scripts as well as sample C# project to demonstrate the technique. <a href="http://sites.google.com/site/erichumphrey/LoginTriggers.zip">Download now</a>.</p>
<p>A special thanks to <a href="http://www.brentozar.com/">Brent Ozar</a> for giving me his opinion on this post and inflating my head somewhat.</p>
