---
layout: post
status: draft
title: CREATE OR ALTER Does Not Publish a Transaction for Replication
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 324
wordpress_url: http://www.erichumphrey.com/?p=324
date: '2017-05-09 20:31:30 -0500'
categories:
- replication
- sql
tags: []
comments: []
---
<pre lang="tsql">
CREATE PROCEDURE ReplicationTest
AS
BEGIN
SELECT 1
END
</pre>
<pre lang="tsql">
CREATE OR ALTER PROCEDURE ReplicationTest
AS
BEGIN
SELECT 2
END
</pre>
<pre lang="tsql">
ALTER PROCEDURE ReplicationTest
AS
BEGIN
SELECT 3
END
</pre>
<p><a href="https://connect.microsoft.com/SQLServer/feedback/details/3133993" target="_blank" rel="noopener noreferrer">Connect Item</a></p>
