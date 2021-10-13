---
layout: post
title: Prevent SQL Logins from using SSMS ... or any other app
date: '2011-06-17 20:20:59 -0500'
categories:
- dba
- sql
- sql triggers
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
