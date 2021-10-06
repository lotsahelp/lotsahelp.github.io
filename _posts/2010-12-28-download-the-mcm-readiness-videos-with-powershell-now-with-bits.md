---
layout: post
status: publish
published: true
title: 'Download the MCM Readiness Videos with PowerShell: Now with BITS'
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 28
wordpress_url: http://www.erichumphrey.com/2010/12/download-the-mcm-readiness-videos-with-powershell-now-with-bits/
date: '2010-12-28 18:50:00 -0600'
date_gmt: '2010-12-29 00:50:00 -0600'
categories:
- download script
- learning
- powershell
- sql
- SQLServerPedia Syndication
tags: []
comments:
- id: 32
  author: Meher
  author_email: mehersql@yahoo.com
  author_url: ''
  date: '2011-01-03 18:14:30 -0600'
  date_gmt: '2011-01-04 00:14:30 -0600'
  content: |-
    Hi Eric,

    Can you please post some instructions on how to run the script. I ran it at the PS Command prompt but every time I run it I get errors and the window disapperas. I have downloaded the Agility pack. I think I must be doing something wrong. If you can post some instructions on how to run the script, that would be great.

    Thank you very much,

    Meher
- id: 33
  author: lotsahelp
  author_email: eric.humphrey@gmail.com
  author_url: http://www.erichumphrey.com
  date: '2011-01-03 19:29:19 -0600'
  date_gmt: '2011-01-04 01:29:19 -0600'
  content: |-
    Meher, you might want to look at <a href="http://bit.ly/dOGA8N" rel="nofollow">my first PowerShell post</a> first. You'll likely have to edit two lines. The first being where the HtmlAgilityPack dll resides. And the second being where to save the videos. $env:public refers to the Public folder in Vista / Win7. Not sure if it works in XP. The $env:userprofile refers to your user profile directory.

    Also, by default, it doesn't download, it only prints the urls that it will download. You'll need to specify the -download argument on the command line.
---
<p>I was playing around with <a href="http://msdn.microsoft.com/en-us/library/bb968799.aspx">BITS</a> transferring backups between servers and discovered that BITS has <a href="http://technet.microsoft.com/en-us/library/dd819413.aspx">PowerShell cmdlets</a>. I was previously using the <a href="http://msdn.microsoft.com/en-us/library/aa362813(v=vs.85).aspx">BITSAdmin Tool</a> which was easy, but I wanted to find out more about the PoSh cmdlets. After reading the docs, I decided to use these cmdlets in my MCM video download script.</p>
<p>BITS stands for Background Intelligent Transfer Service. It is a service built into windows since XP designed to transfer files in the background without interrupting normal user network usage. Windows Update uses BITS to download all the patches that need to be applied to your computer. As stated above, you too can also use BITS to transfer files around. If you regularly transfer large files over the network and get annoyed by how it affects your computer experience, look into replacing the normal copy, xcopy, robocopy commands with it.</p>
<p>My first attempt queued all the files into one BITS transfer. The upside is only one BITS job to manage. The downside is the filenames aren't written until the whole job completes. Long download jobs often get cancelled and the user would want to be able to listen to or watch the files that had already finished. I wanted the opposite, so I went for one job per file.</p>
<p>The BITS cmdlets require PowerShell v2.0</p>
<p><a href="http://db.tt/fAv7KXR">Download the script</a></p>
