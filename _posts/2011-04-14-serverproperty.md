---
layout: post
status: publish
published: true
title: Get all SERVERPROPERTY values for SQL Server
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 196
wordpress_url: http://www.erichumphrey.com/?p=196
date: '2011-04-14 08:13:03 -0500'
date_gmt: '2011-04-14 14:13:03 -0500'
categories:
- dba
- sql
tags: []
comments:
- id: 75
  author: Norman Kelm
  author_email: norman@gerasus.com
  author_url: http://www.gerasus.com/
  date: '2011-04-15 10:39:34 -0500'
  date_gmt: '2011-04-15 16:39:34 -0500'
  content: |-
    Hi Eric,

    Nice script, but since I'm learning PoSH I had to ask myself how to do this in PoSH.

    Here it is:
    Get-Item SQLSERVER:\SQL\\ | %{ $_.Properties} | Sort-Object Name | Select Name, Value

    Norman
- id: 76
  author: Norman Kelm
  author_email: norman@gerasus.com
  author_url: http://www.gerasus.com/
  date: '2011-04-15 10:43:17 -0500'
  date_gmt: '2011-04-15 16:43:17 -0500'
  content: |-
    Ooops, some of my code got removed since I used lt and gt symbols.

    Get-Item SQLSERVER:\SQL\server\instance | %{ $_.Properties} | Sort-Object Name | Select Name, Value
- id: 77
  author: Ryan Adams
  author_email: ryan@ryanjadams.com
  author_url: http://www.ryanjadams.com
  date: '2011-04-17 10:06:29 -0500'
  date_gmt: '2011-04-17 16:06:29 -0500'
  content: Good post.  I can never remember all those and always have to look them
    up.  This will come in very handy and surprised I didn't think to do this myself.
- id: 78
  author: Eric Humphrey &raquo; Get All SESSIONPROPERTY Values for Your Session
  author_email: ''
  author_url: http://www.erichumphrey.com/2011/08/sessionproperty/
  date: '2011-08-23 14:26:49 -0500'
  date_gmt: '2011-08-23 20:26:49 -0500'
  content: "[...] a follow-up to my SERVERPROPERTY() post, this is a quick script
    to grab all the SESSIONPROPERTY() values from your current session in a [...]"
- id: 79
  author: Get All SESSIONPROPERTY Values for Your Session - SQL Server - SQL Server
    - Toad World
  author_email: ''
  author_url: http://www.toadworld.com/platforms/sql-server/b/weblog/archive/2011/08/23/get-all-sessionproperty-values-for-your-session
  date: '2015-08-24 17:37:30 -0500'
  date_gmt: '2015-08-24 23:37:30 -0500'
  content: "[&#8230;] a follow-up to my SERVERPROPERTY() post, this is a quick script
    to grab all the SESSIONPROPERTY() values from your current session in a [&#8230;]"
---
<p>A quick script to grab all the SERVERPROPERTY() values from a SQL instance in a table. Useful when you forget what the propertyname options are. Properties obtained from&nbsp;<a href="http://msdn.microsoft.com/en-us/library/ms174396.aspx">http://msdn.microsoft.com/en-us/library/ms174396.aspx</a>.</p>
<pre lang="tsql">DECLARE @props TABLE (propertyname sysname PRIMARY KEY)
INSERT INTO @props(propertyname)
SELECT 'BuildClrVersion'
UNION
SELECT 'Collation'
UNION
SELECT 'CollationID'
UNION
SELECT 'ComparisonStyle'
UNION
SELECT 'ComputerNamePhysicalNetBIOS'
UNION
SELECT 'Edition'
UNION
SELECT 'EditionID'
UNION
SELECT 'EngineEdition'
UNION
SELECT 'InstanceName'
UNION
SELECT 'IsClustered'
UNION
SELECT 'IsFullTextInstalled'
UNION
SELECT 'IsIntegratedSecurityOnly'
UNION
SELECT 'IsSingleUser'
UNION
SELECT 'LCID'
UNION
SELECT 'LicenseType'
UNION
SELECT 'MachineName'
UNION
SELECT 'NumLicenses'
UNION
SELECT 'ProcessID'
UNION
SELECT 'ProductVersion'
UNION
SELECT 'ProductLevel'
UNION
SELECT 'ResourceLastUpdateDateTime'
UNION
SELECT 'ResourceVersion'
UNION
SELECT 'ServerName'
UNION
SELECT 'SqlCharSet'
UNION
SELECT 'SqlCharSetName'
UNION
SELECT 'SqlSortOrder'
UNION
SELECT 'SqlSortOrderName'
UNION
SELECT 'FilestreamShareName'
UNION
SELECT 'FilestreamConfiguredLevel'
UNION
SELECT 'FilestreamEffectiveLevel'

SELECT propertyname, SERVERPROPERTY(propertyname) FROM @props</pre>
