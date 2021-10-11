---
layout: post
title: Sync SSMS Tools Pack Across Computers Using SyncToy
date: '2011-10-06 12:44:06 -0500'
categories:
- dba
---
<p>My favorite and most used feature of the <a href="http://www.ssmstoolspack.com/">SSMS Tools Pack</a> is the query history. This feature has saved me a lot of time and effort. DBAs where I work are assigned both a desktop for normal day to day operations and a laptop for when we're at home, meetings, travel, etc. Today I ran into a scenario that made me want to keep the query history of both machines in sync. I asked&nbsp;<a href="http://twitter.com/#!/MladenPrajdic">Mladen Prajdic</a>, the creator, about syncing across computers and he directed me to a <a href="http://www.real-sql-guy.com/2011/10/wonder-twin-powers-activate.html">blog post</a> written by <a href="https://twitter.com/#!/RealSQLGuy">REAL SQL Guy</a> that does just that. Unfortunately his method uses Dropbox which is blocked at work, so I needed an alternative. I use <a href="https://www.microsoft.com/download/en/details.aspx?id=15155">SyncToy</a> from Microsoft regularly to keep other folders in sync, so I decided to do the same for this project. Using SyncToy manually means that both machines need to be powered on and can see each other on the network since there is no intermediary.</p>
<p>The first thing I did was to share the C:\SSMSTools folder on each machine so I can trigger the sync from whichever computer I'm sitting at. I then created a folder pair on each machine specifying the "Left" folder as the remote shared folder and the "Right" folder as the local one. I made sure to use the "Synchronize" setting so that changes can go both ways. Give it a name, click Preview and Run. Now I have my entire history from both machines available from either one.</p>
<p>Link Recap:</p>
<ul>
<li><a href="http://www.ssmstoolspack.com/">SSMS Tools Pack</a></li>
<li><a href="http://www.real-sql-guy.com/2011/10/wonder-twin-powers-activate.html">Real SQL Guy</a></li>
<li><a href="https://www.microsoft.com/download/en/details.aspx?id=15155">SyncToy</a></li>
</ul>
