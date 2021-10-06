---
layout: post
status: publish
published: true
title: Display All SQL DateTime Conversions
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 302
wordpress_url: http://www.erichumphrey.com/?p=302
date: '2014-07-18 14:30:18 -0500'
date_gmt: '2014-07-18 20:30:18 -0500'
categories:
- powershell
- sql
tags: []
comments: []
---
<p>If you want a quick way to determine which format, or style, to specify when converting a datetime value in SQL to a character data type, you can use this simple PowerShell script. Or you can look <a href="http://msdn.microsoft.com/en-us/library/ms187928.aspx">at the MSDN page</a>.</p>
<pre lang="powershell">0..200 | %{
    Invoke-Sqlcmd -ServerInstance . -Query ("SELECT num = {0}, CONVERT(varchar(100), GETDATE(), {0})" -f $_)
} | ft -AutoSize
</pre>
