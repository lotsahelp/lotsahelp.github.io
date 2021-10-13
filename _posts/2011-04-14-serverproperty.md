---
layout: post
title: Get all SERVERPROPERTY values for SQL Server
date: '2011-04-14 08:13:03 -0500'
categories:
- dba
- sql
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
