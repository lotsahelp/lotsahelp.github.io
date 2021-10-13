---
layout: post
title: Get All SESSIONPROPERTY Values for Your Session
date: '2011-08-23 14:26:45 -0500'
categories:
- sql
---
<p>As a follow-up to my <a title="Get all SERVERPROPERTY values for SQL Server" href="http://www.erichumphrey.com/2011/04/serverproperty/">SERVERPROPERTY() post</a>, this is a quick script to grab all the SESSIONPROPERTY() values from your current session in a table. Useful when you forget what the propertyname options are. Properties obtained from&nbsp;<a href="http://msdn.microsoft.com/en-us/library/ms175001.aspx">http://msdn.microsoft.com/en-us/library/ms175001.aspx</a>.</p>
<pre lang="tsql">DECLARE @props TABLE (propertyname sysname PRIMARY KEY)
INSERT INTO @props(propertyname)
SELECT 'ANSI_NULLS'
UNION ALL
SELECT 'ANSI_PADDING'
UNION ALL
SELECT 'ANSI_WARNINGS'
UNION ALL
SELECT 'ARITHABORT'
UNION ALL
SELECT 'CONCAT_NULL_YIELDS_ NULL'
UNION ALL
SELECT 'NUMERIC_ROUNDABORT'
UNION ALL
SELECT 'QUOTED_IDENTIFIER'

SELECT propertyname, SESSIONPROPERTY(propertyname) FROM @props</pre>
