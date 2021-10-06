---
layout: post
status: publish
published: true
title: Automate CPU-Z Capture to Check for Throttled Processors
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 257
wordpress_url: http://www.erichumphrey.com/?p=257
date: '2012-02-16 08:00:34 -0600'
date_gmt: '2012-02-16 14:00:34 -0600'
categories:
- dba
- powershell
tags: []
comments: []
---
<p><a href="http://www.sqlskills.com/blogs/paul/post/are-your-cpus-running-slowly-tool-tip-and-survey.aspx">Several</a> <a href="http://www.brentozar.com/archive/2010/10/sql-server-on-powersaving-cpus-not-so-fast/">professionals</a> <a href="http://colleenmorrow.com/2011/12/29/sql-server-a-to-z-cpu-z/">have</a> <a href="http://sqlserverperformance.wordpress.com/tag/cpu-z/">posted</a> about checking whether or not your processors are running at full speed by using <a href="http://www.cpuid.com/softwares/cpu-z.html">CPU-Z</a>. Some recommendations are to check your servers every couple of months or, if virtual, every time your guest moves. Me being <del>lazy</del> efficient, I'd rather automate having these servers send me their info on a scheduled basis.</p>
<p>First things, first. Be sure to copy CPU-Z out to all your servers, preferably in a consistent folder. Edit the cpuz.ini file to turn off extra scanning (we just need the CPU info).</p>
<pre lang="ini">ACPI=0
PCI=0
DMI=0
Sensor=0
SMBus=0
Display=0</pre>
<p>Save the following as a script and schedule it to run on a monthly or so basis using your favorite scheduler. The email portion was taken from <a href="http://www.techrepublic.com/blog/window-on-windows/send-an-email-with-an-attachment-using-powershell/4969">this post</a>.</p>
<pre lang="powershell">& '.\cpuz64.exe' "-txt=$env:COMPUTERNAME"
Start-Sleep -Seconds 15 #Give CPUZ enough time to generate output

$smtpServer = "127.0.0.1"
$msg = new-object Net.Mail.MailMessage
$smtp = new-object Net.Mail.SmtpClient($smtpServer)
$msg.From = "emailadmin@test.com"
$msg.To.Add("administrator1@test.com")
$msg.To.Add("administrator2@test.com")

$msg.Subject = "[CPU-Z] $env:COMPUTERNAME"
$msg.Body = gc "$env:COMPUTERNAME.txt" | ?{$_ -match "(Specification|Core Speed|Stock frequency)"}

$smtp.Send($msg)</pre>
