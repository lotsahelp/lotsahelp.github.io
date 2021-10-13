---
layout: post
title: Change SQL Startup Parameters with PowerShell
date: '2011-03-30 19:44:47 -0500'
categories:
- dba
- powershell
- SQLServerPedia Syndication
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
