---
layout: post
title: SQLskills Immersion Event on Internals and Performance - Day 2
date: '2011-02-22 21:08:54 -0600'
categories:
- dba
- learning
---
<h3>Overview</h3>
<p>Day two of the SQLskills Immersion Event is over and I have listed the interesting parts below. The food, again, was great, except for the lack of danishes during breakfast (I really like those). Today I got see Kimberly's teaching style. She's a fast talker, so you better keep up. Sadly I was a bit more sleepy in class today, though that's not a reflection on Kimberly. I keep waiting for Kimberly to go into a Joan Cusack type rant, she just has those mannerisms.&nbsp;To tell you how much this couple cares about teaching and the value of whiteboards... <strong>they shipped their own 2 boards</strong> down here from Washington state, even through what seemed like a pretty big hassle.</p>
<p>Tonight also saw SQL Sentry demo their free <a href="http://www.sqlsentry.com/plan-explorer/sql-server-query-view.asp">Plan Explorer</a> tool. A really cool tool that's worth checking out. I learned a few tips for using it that I didn't know before.</p>
<h3>Logging, Recovery, and Log File Maintenance</h3>
<ul>
<li>Updates request update locks on objects, when all locks are granted locks change to eXclusive as they are updated.</li>
<li>There is no such thing as a non-logged operation (in a user database)</li>
<li>fn_dblog()</li>
<li>Crash recovery is logged as well (compensation records)</li>
<li>Fixed width columns are grouped in a log record</li>
<li>Variable width columns get individual log record</li>
<li>*Committed* transaction log records are always written to disk</li>
<li>Uncommitted transaction log records *could* be buffered. Checkpoint causes these to flush to disk.</li>
<li>DBCC LOGINFO</li>
<li>Nested COMMIT TRANSACTION does nothing other than decrement @@TRANCOUNT</li>
<li>Nested ROLLBACK rolls back everything</li>
<li>Don't use nested transactions, use save points instead</li>
<li>BULK_LOGGED recovery model is kind of useless, your save log space, but not log backup space.</li>
<li>If you want to use it, BACKUP LOG, switch to BULK_LOGGED, do op, switch back to FULL, BACKUP &nbsp;LOG.</li>
</ul>
<h3>Locking &amp; Blocking</h3>
<ul>
<li>Fix your code</li>
<li>Optimize your indexes</li>
<li>Watch out for more than one incompatible lock on an object.</li>
<li>UPDATE ... @var = col1 = col1-@debit (added in 6.0) Really neat way of updating a row and getting a value back in one query
<ul>
<li>
<pre lang="tsql">CREATE TABLE testNum (id INT PRIMARY KEY)

INSERT INTO testNum VALUES(5)

DECLARE @id INT

UPDATE testNum
SET @id = id = id + 5
WHERE id = 5

PRINT @id</pre>
</li>
</ul>
</li>
<li>UPDATE ... OUTPUT (to table variables)</li>
<li>No such thing as a nested transaction</li>
<li>KB 271509 - sp_blocker_pss08</li>
</ul>
<p>Nested transactions (no such thing. Though you'll want to learn about save points)</p>
<pre lang="tsql">BEGIN TRAN T1
    BEGIN TRAN T2
        SAVE TRAN T3
            ...
        ROLLBACK TRAN T3
    COMMIT TRAN T2 ==>@@TRANCOUNT = 1
COMMIT TRAN T1 ==>@@TRANCOUNT = 0</pre>
<h3>Snapshot Isolation</h3>
<ul>
<li>If you use row versioning, use a FILLFACTOR to prevent page splits</li>
<li>Versioning will double your writes (once to data file, once to tempdb) and likely increase tempdb space, although not necessarily.</li>
</ul>
<h3>Table Design Strategies &amp; Partitioning</h3>
<ul>
<li>Vertical partitioning could be useful depending on the access patterns and page density</li>
<li>Get your data types right</li>
<li>If LOBS aren't used often, considering pushing them off row</li>
</ul>
