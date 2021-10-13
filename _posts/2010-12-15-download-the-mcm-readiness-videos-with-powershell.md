---
layout: post
title: Download the MCM Readiness Videos with PowerShell
date: '2010-12-15 19:25:00 -0600'
categories:
- download script
- learning
- powershell
- sql
---
<p>Microsoft has changed their SQL MCM program to allow more people access to the course and certification. In doing so, they are recording much of the classroom training that was previously required and <a href="http://technet.microsoft.com/en-us/sqlserver/ff977043.aspx">putting it up on TechNet</a>. We're talking close to 60 videos already. Sure, you could stream them one at a time, but I wanted to be able to download them all and watch them offline without a bunch of clicking. So I built the below PowerShell scripts. They're really simple to use, and only have one external requirement that makes dealing with web pages much easier.</p>
<p>You'll need to download the Html Agility Pack library from <a href="http://htmlagilitypack.codeplex.com/">here</a>.</p>
<p><a href="http://db.tt/DHi8ZPa">Get the script</a></p>
<p>Usage:</p>
<p>Get urls of all WMV files<br />>&amp; '.\Download MCM Videos.ps1'</p>
<p>Download all WMV files<br />>&amp; '.\Download MCM Videos.ps1' -download</p>
<p>Download all iPod files<br />>&amp; '.\Download MCM Videos.ps1' -download -type ipod</p>
