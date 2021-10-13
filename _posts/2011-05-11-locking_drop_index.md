---
layout: post
title: Locking While Dropping or Altering an Index
date: '2011-05-11 19:19:04 -0500'
categories:
- dba
- observation
- sql
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
