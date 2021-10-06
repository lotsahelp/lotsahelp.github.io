---
layout: post
status: publish
published: true
title: Download the MCM Readiness Videos with PowerShell
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 26
wordpress_url: http://www.erichumphrey.com/2010/12/download-the-mcm-readiness-videos-with-powershell/
date: '2010-12-15 19:25:00 -0600'
date_gmt: '2010-12-16 01:25:00 -0600'
categories:
- download script
- learning
- powershell
- sql
tags: []
comments:
- id: 23
  author: learningsql
  author_email: ''
  author_url: ''
  date: '2010-12-15 21:18:44 -0600'
  date_gmt: '2010-12-16 03:18:44 -0600'
  content: I love this script.  Thank you.  I have never run, or created a PowerShell
    script before.  I have wanted to figure it out but have never taken the time.  It
    took me about 15 minutes to get everything setup right and whalah (spellig?) IT
    Ran.  I am so pumped.  Thanks for the script.
- id: 24
  author: Jason
  author_email: ''
  author_url: ''
  date: '2010-12-15 21:50:50 -0600'
  date_gmt: '2010-12-16 03:50:50 -0600'
  content: Excellent script
- id: 25
  author: CFRandall
  author_email: ''
  author_url: ''
  date: '2010-12-16 10:37:12 -0600'
  date_gmt: '2010-12-16 16:37:12 -0600'
  content: Thanks for this.
- id: 26
  author: Murali
  author_email: ''
  author_url: ''
  date: '2010-12-18 11:27:43 -0600'
  date_gmt: '2010-12-18 17:27:43 -0600'
  content: I am not familar with PS can you please send me the complete steps how
    to go about. Your help is much appreciated<br /><br />Regards<br />Murali
- id: 27
  author: Eric H
  author_email: ''
  author_url: ''
  date: '2010-12-18 11:37:39 -0600'
  date_gmt: '2010-12-18 17:37:39 -0600'
  content: Murali, you might want to start here first. <a href="http://technet.microsoft.com/en-us/library/ee177003.aspx"
    rel="nofollow">http://technet.microsoft.com/en-us/library/ee177003.aspx</a>. You&#39;ll
    need to look into Set-ExecutionPolicy
- id: 28
  author: dyfhid
  author_email: ''
  author_url: ''
  date: '2010-12-18 20:01:51 -0600'
  date_gmt: '2010-12-19 02:01:51 -0600'
  content: 'Eric,<br /><br />I get (apparently 53 times in a row :)<br /><br />Exception
    calling "DownloadFile" with "2" argument(s): "An exception occurred during a WebClient
    request."<br />At C:\users\public\documents\powershell\Download MCM Videos.ps1:43
    char:50<br />+             (New-Object System.Net.WebClient).DownloadFile <<<<
    ($fileUrl, $fileName)<br />    + CategoryInfo          : NotSpecified: (:) [],
    MethodInvocationException<br />    + FullyQualifiedErrorId : DotNetMethodException'
- id: 29
  author: Murali
  author_email: ''
  author_url: ''
  date: '2010-12-19 07:14:02 -0600'
  date_gmt: '2010-12-19 13:14:02 -0600'
  content: 'Hi Eric, <br /><br />I get the following Error msg :<br />**************************<br
    />You cannot call a method on a null-valued expression.<br />At C:\Users\Murali\Desktop\Download
    MCM Videos.ps1:36 char:22<br />+     $page = $web.Load <<<< ($_.PSBase.innertext)<br
    />    + CategoryInfo          : InvalidOperation: (Load:String) [], RuntimeException<br
    />    + FullyQualifiedErrorId : InvokeMethodOnNull<br />**************************'
- id: 30
  author: Eric H
  author_email: ''
  author_url: ''
  date: '2010-12-19 11:49:15 -0600'
  date_gmt: '2010-12-19 17:49:15 -0600'
  content: dyfhid, double check your paths. I&#39;m guessing the destination doesn&#39;t
    exist. I&#39;ll try to update the script to add checking if the destination path
    exists.
- id: 31
  author: Eric H
  author_email: ''
  author_url: ''
  date: '2010-12-19 11:51:32 -0600'
  date_gmt: '2010-12-19 17:51:32 -0600'
  content: Murali, are you loading the HtmlAgilityPack dll successfully? Remove the
    | Out-Null from the end of the LoadFile line at the top of the script.
---
<p>Microsoft has changed their SQL MCM program to allow more people access to the course and certification. In doing so, they are recording much of the classroom training that was previously required and <a href="http://technet.microsoft.com/en-us/sqlserver/ff977043.aspx">putting it up on TechNet</a>. We're talking close to 60 videos already. Sure, you could stream them one at a time, but I wanted to be able to download them all and watch them offline without a bunch of clicking. So I built the below PowerShell scripts. They're really simple to use, and only have one external requirement that makes dealing with web pages much easier.</p>
<p>You'll need to download the Html Agility Pack library from <a href="http://htmlagilitypack.codeplex.com/">here</a>.</p>
<p><a href="http://db.tt/DHi8ZPa">Get the script</a></p>
<p>Usage:</p>
<p>Get urls of all WMV files<br />>&amp; '.\Download MCM Videos.ps1'</p>
<p>Download all WMV files<br />>&amp; '.\Download MCM Videos.ps1' -download</p>
<p>Download all iPod files<br />>&amp; '.\Download MCM Videos.ps1' -download -type ipod</p>
