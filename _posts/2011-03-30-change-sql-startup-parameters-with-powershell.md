---
layout: post
status: publish
published: true
title: Change SQL Startup Parameters with PowerShell
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 152
wordpress_url: http://www.erichumphrey.com/?p=152
date: '2011-03-30 19:44:47 -0500'
date_gmt: '2011-03-31 01:44:47 -0500'
categories:
- dba
- powershell
- SQLServerPedia Syndication
tags: []
comments:
- id: 62
  author: Adrienne
  author_email: adrienne.lore@gmail.com
  author_url: ''
  date: '2012-03-02 14:56:12 -0600'
  date_gmt: '2012-03-02 20:56:12 -0600'
  content: This was a godsend, thank you for posting!  I am using it for post-unattended
    install configuration.
- id: 63
  author: John
  author_email: johnedwardhall@gmail.com
  author_url: ''
  date: '2013-01-09 13:59:20 -0600'
  date_gmt: '2013-01-09 19:59:20 -0600'
  content: Does this also work for multiple trace like -T1117 and -T1118 at the same
    time or would i need to run it twice ?
- id: 64
  author: lotsahelp
  author_email: eric.humphrey@gmail.com
  author_url: http://www.erichumphrey.com
  date: '2013-01-09 14:01:46 -0600'
  date_gmt: '2013-01-09 20:01:46 -0600'
  content: It's currently coded to do one at a time. I might update it to accept more
    when I have spare time.
- id: 65
  author: Derek
  author_email: derek.wharton@conocophillips.com
  author_url: ''
  date: '2013-02-08 10:03:14 -0600'
  date_gmt: '2013-02-08 16:03:14 -0600'
  content: What about adding it remotely?  I would like to use this to add the flag
    to multiple servers.
- id: 66
  author: lotsahelp
  author_email: eric.humphrey@gmail.com
  author_url: http://www.erichumphrey.com
  date: '2013-02-08 21:14:39 -0600'
  date_gmt: '2013-02-09 03:14:39 -0600'
  content: It's possible. This script just isn't written for it properly.
- id: 67
  author: Stephen Archbold
  author_email: stephen_archbold@msn.com
  author_url: http://simplesql.blogspot.com
  date: '2014-08-14 10:43:15 -0500'
  date_gmt: '2014-08-14 16:43:15 -0500'
  content: Just found this now, and it got me out of such a bind I had to say thanks
    even though it's 2 years later!
- id: 68
  author: How to set SQL Server trace flag 610 on | Big Data
  author_email: ''
  author_url: http://sabigdata.wordpress.com/2014/09/10/how-to-set-sql-server-trace-flag-610-on/
  date: '2014-09-10 03:27:04 -0500'
  date_gmt: '2014-09-10 09:27:04 -0500'
  content: "[&#8230;] Humphrey has a nice Powershell script to do the task for you
    without all the clicking. Thanks [&#8230;]"
- id: 69
  author: Thomas Rushton
  author_email: ThomasRushton@gmail.com
  author_url: http://thelonedba.wordpress.com
  date: '2015-11-25 08:08:49 -0600'
  date_gmt: '2015-11-25 14:08:49 -0600'
  content: Thanks for this - looks to be just the thing I need to roll out T3226 &amp;
    T1222 across my estate.
- id: 70
  author: OJ
  author_email: omar_jaber@Hotmail.com
  author_url: ''
  date: '2015-12-09 16:10:33 -0600'
  date_gmt: '2015-12-09 22:10:33 -0600'
  content: I ran this script against SQL14 and it did not work. Not sure what editor
    used to create the script but it ISE indentation all screwed up.
- id: 71
  author: Ademir Passos
  author_email: ademirop@hotmail.com
  author_url: ''
  date: '2016-08-26 06:53:00 -0500'
  date_gmt: '2016-08-26 12:53:00 -0500'
  content: Hi! Thanks a lot for great script! Did you already updated it to be executed
    for multiple servers or to be executed remotely? Thanks!!
- id: 72
  author: lotsahelp
  author_email: eric.humphrey@gmail.com
  author_url: http://www.erichumphrey.com
  date: '2016-09-07 22:48:49 -0500'
  date_gmt: '2016-09-08 04:48:49 -0500'
  content: I have not. I've taken some time off from being database focused. I might
    get back around to it eventually.
---
<p>[caption id="attachment_154" align="alignright" width="272" caption="Backups Abound"]<a href="/wp-content/uploads/2011/03/Backups-Abound.png"><img class="size-medium wp-image-154" title="Backups Abound" src="/wp-content/uploads/2011/03/Backups-Abound-272x300.png" alt="" width="272" height="300" /></a>[/caption]</p>
<h2>The Annoyance</h2>
<p>Sometimes when we're trying to track down a problem and looking through SQL's Logs we have to dig through hundreds of backup successful events just to find what we're looking for. Unfortunately, SSMS doesn't have an exclusion filter when viewing the logs, so I can't filter them out that way.&nbsp;We also don't care about having successful backups in these logs as the info is available elsewhere, including our management database.</p>
<h2>The Solution</h2>
<p><a href="http://msdn.microsoft.com/en-us/library/ms188396.aspx">Trace flag</a> 3226 will turn off logging of successful backups to the SQL Server error log, in addition to the Windows system event log. There are two ways to turn this on. Using the T-SQL command DBCC TRACEON(3226,-1), you immediately enable it, but only until SQL restarts as flags set this way do not survive a service restart. The other way to set it is by using startup parameters for the SQL service.</p>
<p>[caption id="attachment_155" align="alignleft" width="300" caption="Don&#39;t Forget the Semicolons"]<a href="/wp-content/uploads/2011/03/SQL-Startup-Parameters.png"><img class="size-medium wp-image-155" title="SQL Startup Parameters" src="/wp-content/uploads/2011/03/SQL-Startup-Parameters-300x224.png" alt="" width="300" height="224" /></a>[/caption]</p>
<p>In order to change SQL's startup parameters, you need to:</p>
<ol>
<li>Load SQL Server Configuration Manager</li>
<li>Right-click and bring up the Properties for the service you want to edit</li>
<li>Click the Advanced tab</li>
<li>Edit the Startup Parameters property by appending -T3226, remembering to separate with semicolons</li>
<li>Click OK</li>
</ol>
<h2>The Better Solution</h2>
<p>I wanted to do this quick and easy without all the clicks. I knew I wouldn't just do this on one server, but maybe tens if not a hundred. So I opened up PowerShell ISE and started cranking out a script to this for me. My first hurdle was how are these startup parameters stored and how can I actually get to them to edit them. It turns out, they are just stored as registry keys. PowerShell works with the registry real well. After finding the key locations, doing some logic to make sure I change all instances on that server, I had my script. I made it generic enough so you could add any startup parameter to SQL, not just trace flags.</p>
<p>Calling the script to update all instances on the current computer is as easy as</p>
<pre lang="powershell">PS> .\Add-SqlServerStartupParameter.ps1 '-T3226'</pre>
<h3>Caveats</h3>
<p>The current version does not support SQL 2000, though I plan to release an update that will. I also plan to release a version that supports remote servers. If you are changing a clustered instance, make sure to change it on node the instance is running on as clustered instances overwrite registry settings with whatever their last values were when the instance was running. It's best to change all nodes in a cluster at once, to be on the safe side.</p>
<p>Download the script:&nbsp;<a href="http://www.erichumphrey.com/wp-content/uploads/2011/03/Add-SqlServerStartupParameter.ps1">Add-SqlServerStartupParameter</a></p>
