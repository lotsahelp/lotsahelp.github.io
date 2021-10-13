---
layout: post
title: 'Download the MCM Readiness Videos with PowerShell: Now with BITS'
date: '2010-12-28 18:50:00 -0600'
categories:
- download script
- learning
- powershell
- sql
- SQLServerPedia Syndication
---
<p>I was playing around with <a href="http://msdn.microsoft.com/en-us/library/bb968799.aspx">BITS</a> transferring backups between servers and discovered that BITS has <a href="http://technet.microsoft.com/en-us/library/dd819413.aspx">PowerShell cmdlets</a>. I was previously using the <a href="http://msdn.microsoft.com/en-us/library/aa362813(v=vs.85).aspx">BITSAdmin Tool</a> which was easy, but I wanted to find out more about the PoSh cmdlets. After reading the docs, I decided to use these cmdlets in my MCM video download script.</p>
<p>BITS stands for Background Intelligent Transfer Service. It is a service built into windows since XP designed to transfer files in the background without interrupting normal user network usage. Windows Update uses BITS to download all the patches that need to be applied to your computer. As stated above, you too can also use BITS to transfer files around. If you regularly transfer large files over the network and get annoyed by how it affects your computer experience, look into replacing the normal copy, xcopy, robocopy commands with it.</p>
<p>My first attempt queued all the files into one BITS transfer. The upside is only one BITS job to manage. The downside is the filenames aren't written until the whole job completes. Long download jobs often get cancelled and the user would want to be able to listen to or watch the files that had already finished. I wanted the opposite, so I went for one job per file.</p>
<p>The BITS cmdlets require PowerShell v2.0</p>
<p><a href="http://db.tt/fAv7KXR">Download the script</a></p>
