---
layout: post
status: publish
published: true
title: Disable SQL Agent Jobs with PowerShell
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 201
wordpress_url: http://www.erichumphrey.com/?p=201
date: '2011-04-25 12:46:38 -0500'
date_gmt: '2011-04-25 18:46:38 -0500'
categories:
- dba
- powershell
- snippet
tags: []
comments: []
---
<p>I had a need today to disable all of our admin jobs while I moved our admin database to another file location. We didn't want to get a lot of alerts or job failures during the move, even though it took less than 5 minutes. This script runs best in sqlps or by adding the appropriate snap-ins to your regular console.</p>
<pre lang="powershell">cd SQLSERVER:\SQL\[server]\[instance]\JobServer\Jobs
dir | ?{$_.Name.StartsWith('!Admin')} | %{$_.IsEnabled = $false; $_.Alter()} #rerun with $true to enable</pre>
