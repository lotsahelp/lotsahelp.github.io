---
layout: post
status: publish
published: true
title: Locking While Dropping or Altering an Index
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 208
wordpress_url: http://www.erichumphrey.com/?p=208
date: '2011-05-11 19:19:04 -0500'
date_gmt: '2011-05-12 01:19:04 -0500'
categories:
- dba
- observation
- sql
tags: []
comments:
- id: 80
  author: Tony Lucero
  author_email: tony_c_lucero@yahoo.com
  author_url: ''
  date: '2013-01-28 12:21:11 -0600'
  date_gmt: '2013-01-28 18:21:11 -0600'
  content: |-
    "hypothetical index since neither are used by active queries?"

    I wouldn't be quite sure about that.  I seen hypothetical indexes referenced in execution plans.  I believe that internally the optimizer must divert back to the PK but have no evidence to backup.  However whats worse is proper stats arn't kept on the tables and furthermore the optimizer can't offer proper index choices to help you index correctly.  This is junk the last "DBA" left me from a failed index tuning job across of course all the busiest environments in my data center.  I came here in the situation your in and like you a downtime is in the near future....
- id: 81
  author: kb
  author_email: bhkhx91@gmail.com
  author_url: ''
  date: '2014-10-20 16:05:44 -0500'
  date_gmt: '2014-10-20 22:05:44 -0500'
  content: If I have a script that checks through many tables and does the Reindex
    only if the table meets certain criteria, would all the tables in that criteria
    be locked or just the current/active table that is being be re-indexed
- id: 82
  author: lotsahelp
  author_email: eric.humphrey@gmail.com
  author_url: http://www.erichumphrey.com
  date: '2014-10-20 16:27:41 -0500'
  date_gmt: '2014-10-20 22:27:41 -0500'
  content: Just the current table.
---
<p>Yesterday I was trying to drop some hypothetical indexes in production against a fairly active table. I started causing blocking so I had the bright idea of disabling the indexes first, then dropping. Well, that didn't help, even when setting DEADLOCK_PRIORITY to LOW. I ended up waiting until early morning to remove those indexes to prevent from blocking other users.</p>
<p>Finding no info on the web about the locks taken during the process of dropping or disabling an index, I set about doing this small bit of research.</p>
<p>The queries I used:<br />
In Window 1 (Execute first)</p>
<pre lang="tsql">BEGIN TRAN
SELECT TOP 100 * FROM myTable WITH (TABLOCKX)</pre>
<p>* I use TABLOCKX to simulate many updates going to this table.</p>
<p>In Window 2</p>
<pre lang="tsql">DROP INDEX [ix_myTable_testIndex] ON myTable</pre>
<p>Using Adam Machanic's (<a href="http://sqlblog.com/blogs/adam_machanic/">blog</a> | <a href="http://twitter.com/#!/AdamMachanic">twitter</a>) <a href="http://sqlblog.com/blogs/adam_machanic/archive/tags/who+is+active/">sp_WhoIsActive</a> I was able to get the lock details easily.</p>
<pre lang="tsql">sp_whoisactive @get_locks = 1</pre>
<p>When you disable an index, you end up trying to acquire a Sch-M or Schema Modification lock. </p>
<pre lang="xml"><Database name="Sandbox">
  <Locks>
    <Lock request_mode="S" request_status="GRANT" request_count="1" />
  </Locks>
  <Objects>
    <Object name="myTable" schema_name="dbo">
      <Locks>
        <Lock resource_type="OBJECT" request_mode="Sch-M" request_status="WAIT" request_count="1" />
      </Locks>
    </Object>
  </Objects>
</Database></pre>
<p>Then the next select query comes along and trys to grab an IS or Intent Shared lock</p>
<pre lang="xml"><Database name="Sandbox">
  <Locks>
    <Lock request_mode="S" request_status="GRANT" request_count="1" />
  </Locks>
  <Objects>
    <Object name="myTable" schema_name="dbo">
      <Locks>
        <Lock resource_type="OBJECT" request_mode="IS" request_status="WAIT" request_count="1" />
      </Locks>
    </Object>
  </Objects>
</Database></pre>
<p>So at this point yesterday, I was thinking, maybe I can disable the index and then drop it. Well, ALTER INDEX ... DISABLE also waits on a Sch-M lock before it can proceed. I'm not any better off. Even after the disabling the index, SQL Server still tries to grab a Sch-M lock to drop the disabled index. All these rules also seem to apply to hypothetical indexes. I'm sure there's some internals reason why it does this, but why worry about locks when dropping a disabled or hypothetical index since neither are used by active queries?</p>
<p>Lesson learned, drop indexes when the table usage is low.</p>
<p><strong>*Update</strong><br />
[qtweet 68491945105821696]</p>
