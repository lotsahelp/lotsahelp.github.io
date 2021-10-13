---
layout: post
title: Notes for Moving BizTalk's Databases
date: '2011-04-08 09:41:57 -0500'
categories:
- dba
---
<p>Yesterday, I was tasked with helping a team move their BizTalk databases to another server. These are my notes for anyone else in this predicament.</p>
<p>In our case we had 6 databases to move:</p>
<ul>
<li>BizTalkDTADb</li>
<li>BizTalkHwsDb</li>
<li>BizTalkMgmtDb (This is the management database that tells BizTalk where everything else is.)</li>
<li>BizTalkMsgBoxDb</li>
<li>BizTalkRuleEngineDb</li>
<li>SSODB</li>
</ul>
<p>We had originally moved just 5 of these, not realizing that SSODB was part of the pack. Once the dbs were moved, the engineer worked on getting the service talking to the management database. We were still getting errors about connecting. After a little Googling of MSDN, I <a href="http://msdn.microsoft.com/en-us/library/ee267630(v=bts.10).aspx">found there was some changes</a> that needed to be made to the registry and database. The engineers handled the registry, I handled the data changes. Here's a script of all the data changes I had to make.</p>
<pre lang="tsql">USE BizTalkMgmtDb;
GO

UPDATE dbo.adm_Group
SET TrackingDBServerName = 'newServerInstance'
    ,SubscriptionDBServerName = 'newServerInstance'
   --More columns exist in this table for other servers
   --but these are just the two we needed to change

UPDATE dbo.adm_OtherDatabases
SET ServerName = 'newServerInstance'

UPDATE dbo.adm_OtherBackupDatabases
SET ServerName = 'newServerInstance'

UPDATE dbo.adm_MessageBox
SET DBServerName = 'newServerInstance'</pre>
<p>There was a lot more to this move than just what I've put down, but this is the extent of the database changes.</p>
