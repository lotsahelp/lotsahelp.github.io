---
layout: post
status: publish
published: true
title: What Does Confio's IgniteFree Install on Monitored Instances
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 64
wordpress_url: http://www.erichumphrey.com/?p=64
date: '2011-01-21 13:27:39 -0600'
date_gmt: '2011-01-21 19:27:39 -0600'
categories:
- 3rd party
- dba
tags: []
comments:
- id: 38
  author: Brent Ozar
  author_email: brento@brentozar.com
  author_url: http://www.brentozar.com
  date: '2011-01-22 09:01:08 -0600'
  date_gmt: '2011-01-22 15:01:08 -0600'
  content: |-
    Hmm - did you trace this to see it in action?  Something seems a little off here.  I don't think any product should be granting VIEW SERVER STATE to BUILTIN\Administrators - even if the product is in that group, it shouldn't be granting rights to people outside of itself.  My guess - and this is just a guess - is that you were scripting objects after Ignite ran in order to see what the differences were, perhaps with a data or schema comparison product?

    It might be more accurate to set up a trace, then install Ignite and watch what happens so you can catch the exact commands.
- id: 39
  author: lotsahelp
  author_email: eric.humphrey@gmail.com
  author_url: http://www.erichumphrey.com
  date: '2011-01-22 09:54:04 -0600'
  date_gmt: '2011-01-22 15:54:04 -0600'
  content: "@Brent: I saw this while profiling two different test servers during registering.
    It did a few more things, but this is what I noted as important. If I register
    another instance I'll save the output for verification. So yes, it really does
    grant VIEW SERVER STATE during registration."
- id: 40
  author: Brent Ozar
  author_email: brento@brentozar.com
  author_url: http://www.brentozar.com
  date: '2011-01-22 10:06:16 -0600'
  date_gmt: '2011-01-22 16:06:16 -0600'
  content: If it's granting VIEW SERVER STATE to BUILTIN\Administrators during registration,
    is it running under SA?  What account is it running under?  Is that account part
    of BUILTIN\Administrators?  Are you monitoring SQL Server 2008 or an earlier version?  If
    it's 2008, BUILTIN\Administrators shouldn't even be there by default, so that
    grant statement should error out.  (It shouldn't even be running, but I'm really
    curious now.)
- id: 41
  author: lotsahelp
  author_email: eric.humphrey@gmail.com
  author_url: http://www.erichumphrey.com
  date: '2011-01-22 10:13:50 -0600'
  date_gmt: '2011-01-22 16:13:50 -0600'
  content: "@Brent: Monitoring 2005 SP3. I was running both the registering process
    and monitoring process under my account which is sysadmin. BUILTIN\\Administrators
    is registered on that server as sysadmin as well. I want to do another test using
    differing accounts for registering vs. monitoring and see what it does, then save
    the trace and post it."
- id: 42
  author: SQLRockstar
  author_email: SQLRockstar@thomaslarock.com
  author_url: http://thomaslarock.com
  date: '2011-01-24 20:20:30 -0600'
  date_gmt: '2011-01-25 02:20:30 -0600'
  content: |-
    Eric,

    Thanks for posting this. As I said, we don't require this table. I couldn't say much more because (1) I have a 140-char limit on Twitter and (2) I was in the middle of a board meeting. Still, I wanted to get you an answer. Had I known that you intended to run a trace and blog the results I would have asked for you to include a lot of the information that Brent has already asked about.

    It is my understanding that the table you see is legacy code that simply has not been removed from the install process. I believe that in previous versions of Ignite this table had been used for two purposes:

    1. So we can recognize ourselves as a user that the QuickPoll process should ignore.
    2. To get # CPUs (it executes "master..xp_msver ProcessorCount", and puts the result in ignite_temp which we then query).

    It is not clear to me if those two items are applicable any longer. In fact, I believe the QuickPoll is already filtering for the Ignite user in a different manner. I will ask our engineering team to verify these details once again and also ask they leave a comment on this blog.

    Please keep your trace running and report back anything that seems odd. We'll do our best to clean up whatever we can and as quickly as we can.
- id: 43
  author: lotsahelp
  author_email: eric.humphrey@gmail.com
  author_url: http://www.erichumphrey.com
  date: '2011-01-24 20:31:06 -0600'
  date_gmt: '2011-01-25 02:31:06 -0600'
  content: Tom, thanks for commenting. At the time, I didn't figure on posting this,
    but thought it might be useful for anyone else considering using IgniteFree. I
    haven't had a chance yet to do another install, but will save the results when
    I can.
- id: 44
  author: Dean Richards
  author_email: deanrichards@confio.com
  author_url: ''
  date: '2011-01-24 20:50:34 -0600'
  date_gmt: '2011-01-25 02:50:34 -0600'
  content: Saw your post and I will be looking into this. I am a Sales Eng for Confio
    and have sent this question over to the dev team to determine what is happening.
    I will be back to you as soon as I can. Thx, Dean Richards
- id: 45
  author: SQLRockstar
  author_email: SQLRockstar@thomaslarock.com
  author_url: http://thomaslarock.com
  date: '2011-01-27 16:42:55 -0600'
  date_gmt: '2011-01-27 22:42:55 -0600'
  content: |-
    Eric,

    At this time that table is indeed being used. I believe there are plans to remove it from a future version. Let me know if the presence of this table is causing you any concern.

    Please continue to run additional tests using different accounts. I would be interested to see the results. I believe that the presence of [BUILTIN\Administrators] is due to the fact that DOMAIN\user is a member of that group, but your future tracing should confirm for us one way or the other.

    Thanks again for your help, and for kicking the tires on Ignite.
- id: 46
  author: lotsahelp
  author_email: eric.humphrey@gmail.com
  author_url: http://www.erichumphrey.com
  date: '2011-01-27 16:47:23 -0600'
  date_gmt: '2011-01-27 22:47:23 -0600'
  content: |-
    Tom, Thanks for doing the research. It's not causing concern, as right now we're testing this in development and QA, so we're a little more loose on object creation. If we plan on taking this to production, we just needed an authoritative list of what the footprint will be. It's pretty darn small, so that's great. Having no objects would be even better as the only thing that would need approval would be access for the service account.

    Thanks again for taking the time to reply.
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
