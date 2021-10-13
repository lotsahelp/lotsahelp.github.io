---
layout: post
title: What Does Confio's IgniteFree Install on Monitored Instances
date: '2011-01-21 13:27:39 -0600'
categories:
- 3rd party
- dba
---
<p>I was&nbsp;trialling&nbsp;<a href="http://www.confio.com/">Confio's</a> <a href="http://www.ignitefree.com/">IgniteFree</a> software on a development box and wanted to see what exactly gets created when you register an instance for monitoring. Confio states that their installs are "Agentless", which is true.</p>
<p>I asked Thomas LaRock (<a href="http://www.thomaslarock.com/">blog</a> | <a href="http://twitter.com/SQLRockstar">twitter</a>) if IgniteFree creates any objects on the server. Answer:</p>
<blockquote><p>short answer: #ignite does not <em>require</em> creating objects on the monitored instance. --@SQLRockstar (emphasis mine)</p></blockquote>
<p>What I did discover though,&nbsp;<strong>objects do get created on the monitored server</strong>.</p>
<p>So what gets created?</p>
<pre lang="tsql">USE [master]
GO
/****** Object:  User [DOMAIN\user]    Script Date: 01/21/2011 13:15:11 ******/
CREATE USER [DOMAIN\user] FOR LOGIN [DOMAIN\user] WITH DEFAULT_SCHEMA=[null]
GO
/****** Object:  Schema [DOMAIN\user]    Script Date: 01/21/2011 13:15:10 ******/
CREATE SCHEMA [DOMAIN\user] AUTHORIZATION [DOMAIN\user]
GO
/****** Object:  Table [DOMAIN\user].[ignite_temp]    Script Date: 01/21/2011 13:15:11 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [DOMAIN\user].[ignite_temp](
	[index_val] [tinyint] NULL,
	[name] [varchar](50) NULL,
	[internal_value] [int] NULL,
	[char_value] [varchar](255) NULL
) ON [PRIMARY]
GO
SET ANSI_PADDING OFF
GO
GRANT SELECT ON fn_get_sql to [DOMAIN\user]
GO
GRANT VIEW SERVER STATE to [BUILTIN\Administrators]
GO</pre>
