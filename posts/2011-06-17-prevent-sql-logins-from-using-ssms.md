---
layout: post
status: publish
published: true
title: Prevent SQL Logins from using SSMS ... or any other app
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 222
wordpress_url: http://www.erichumphrey.com/?p=222
date: '2011-06-17 20:20:59 -0500'
date_gmt: '2011-06-18 02:20:59 -0500'
categories:
- dba
- sql
- sql triggers
tags: []
comments:
- id: 83
  author: charles
  author_email: csakari@live.com
  author_url: http://www.schoolmaster.co.ke
  date: '2015-01-10 08:23:21 -0600'
  date_gmt: '2015-01-10 14:23:21 -0600'
  content: "Dear Eric,\nI am trying to secure my sql database by preventing loggins
    through SQL management studio and I am trying to run this script  and I am getting
    this error message. \nkindly advise on perquisite and what I am not doing righht\n\nMsg
    2760, Level 16, State 1, Line 2\nThe specified schema name \"SQLTrace\" either
    does not exist or you do not have permission to use it.\nMsg 208, Level 16, State
    1, Procedure loginDataView, Line 18\nInvalid object name 'SQLTrace.loginData'.\nMsg
    4145, Level 15, State 1, Procedure Deny_SQLLogin_SSMS_Trigger, Line 21\nAn expression
    of non-boolean type specified in a context where a condition is expected, near
    ';'."
- id: 84
  author: Killam
  author_email: kande.km@gmail.com
  author_url: ''
  date: '2015-08-17 16:57:59 -0500'
  date_gmt: '2015-08-17 22:57:59 -0500'
  content: nice post. I was exactly in need of this kind of implementation. I will
    use this as a base and develop meaningful script to suit my environment
- id: 85
  author: lotsahelp
  author_email: eric.humphrey@gmail.com
  author_url: http://www.erichumphrey.com
  date: '2015-08-18 12:10:28 -0500'
  date_gmt: '2015-08-18 18:10:28 -0500'
  content: Charles, you'll need to create the SQLTrace schema first. <a href="https://msdn.microsoft.com/en-us/library/ms189462.aspx"
    rel="nofollow">CREATE SCHEMA</a> SQLTrace
---
<p><em>In a continuation of my previous <a title="Have SQL Demand Application Identification" href="http://www.erichumphrey.com/2009/11/have-sql-demand-application-identification/">LOGON TRIGGER post</a>.</em></p>
<p>A downside to SQL logins is that anyone with the username / password combination can login as that user. This prevents us from knowing who truly logged in and performed some action. It is possible to trace and log everything by IP address and correlate back to that.</p>
<p>Another downside is these accounts are usually service accounts, at least in our environment. These SQL logins may have more permissions than they should because they may be part of a vendors product, and the vendor's app requires them to have sysadmin on the server.&nbsp;We have an issue with some of our users logging in as these SQL logins and running queries or manipulating data that they shouldn't be.</p>
<p>So how can we allow the login to still exist, but deny normal users from logging in under those credentials? There's a few different ways, one could be isolating these users to specific endpoints that would allow only that user from a given ip range. Another option, and the one that made sense here, was to use a LOGON TRIGGER. This allows us a great amount of flexibility on deciding when to prevent a connection from happening.</p>
<p>In this case, we wanted any SQL login using Management Studio to be denied access. Any of the other apps that log in with this account will still work. This includes SqlCmd, Access, etc. Our main problem, though, were devs coming in under SSMS. We also wanted to log any login attempts using this method.</p>
<p>This script creates a logging table in our management database (T1001), creates a view to make things nicer then creates the logon trigger to reject any connections that match our criteria.</p>
<pre lang="tsql">--------------------------------------------------------------------------------
-- Create table to hold rejected login attempts
--------------------------------------------------------------------------------

USE T1001;
GO

CREATE TABLE T1001.SQLTrace.loginData (
	id INT IDENTITY PRIMARY KEY,
	data XML,
	program_name sysname
)
GO

--------------------------------------------------------------------------------
-- Create view to make querying the table a little nicer
--------------------------------------------------------------------------------
CREATE VIEW SQLTrace.loginDataView
AS
SELECT id
      ,data.value('(/EVENT_INSTANCE/EventType)[1]', 'sysname') AS EventType
      ,data.value('(/EVENT_INSTANCE/PostTime)[1]', 'datetime') AS PostTime
      ,data.value('(/EVENT_INSTANCE/SPID)[1]', 'int') AS SPID
      ,data.value('(/EVENT_INSTANCE/ServerName)[1]', 'nvarchar(257)') AS ServerName
      ,data.value('(/EVENT_INSTANCE/LoginName)[1]', 'sysname') AS LoginName
      ,data.value('(/EVENT_INSTANCE/LoginType)[1]', 'sysname') AS LoginType
      ,data.value('(/EVENT_INSTANCE/SID)[1]', 'nvarchar(85)') AS SID
      ,data.value('(/EVENT_INSTANCE/ClientHost)[1]', 'sysname') AS ClientHost
      ,data.value('(/EVENT_INSTANCE/IsPooled)[1]', 'bit') AS IsPooled
      ,program_name
FROM SQLTrace.loginData
GO

--------------------------------------------------------------------------------
-- Create logon trigger
--------------------------------------------------------------------------------
USE [master];
GO

/****** Object: DdlTrigger [Deny_SQLLogin_SSMS_Trigger] ******/
IF EXISTS(
	SELECT * FROM master.sys.server_triggers
	WHERE parent_class_desc = 'SERVER'
		AND name = N'Deny_SQLLogin_SSMS_Trigger')
DROP TRIGGER [Deny_SQLLogin_SSMS_Trigger] ON ALL SERVER
GO

CREATE TRIGGER Deny_SQLLogin_SSMS_Trigger
ON ALL SERVER WITH EXECUTE AS 'sa'
FOR LOGON
AS
BEGIN
DECLARE @data XML
SET @data = EVENTDATA()

DECLARE @AppName sysname
       ,@LoginName sysname
       ,@LoginType sysname
SELECT @AppName = [program_name]
FROM sys.dm_exec_sessions
WHERE session_id = @data.value('(/EVENT_INSTANCE/SPID)[1]', 'int')

SELECT @LoginName = @data.value('(/EVENT_INSTANCE/LoginName)[1]', 'sysname')
      ,@LoginType = @data.value('(/EVENT_INSTANCE/LoginType)[1]', 'sysname')

IF @AppName LIKE ('Microsoft SQL Server Management Studio%') --If it's SSMS
   AND @LoginName <> 'sa'
   AND @LoginType = 'SQL Login' --('SQL Login' | 'Windows (NT) Login')
BEGIN
    ROLLBACK; --Disconnect the session

    --Log the exception to our table
    INSERT INTO T1001.SQLTrace.loginData(data, program_name)
	VALUES(@data, @AppName)
END

END;
GO</pre>
<p>Don't forget to create a job to purge historical log data at sufficient intervals for you.</p>
