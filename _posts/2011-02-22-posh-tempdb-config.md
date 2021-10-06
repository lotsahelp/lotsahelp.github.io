---
layout: post
status: publish
published: true
title: 'PowerShell Script for Paul''s Survey: how is your tempdb configured?'
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 121
wordpress_url: http://www.erichumphrey.com/?p=121
date: '2011-02-22 18:30:31 -0600'
date_gmt: '2011-02-23 00:30:31 -0600'
categories:
- dba
- powershell
- sql
tags: []
comments:
- id: 55
  author: 'Tweets that mention Eric Humphrey &raquo; PowerShell Script for Paul&rsquo;s
    Survey: how is your tempdb configured? -- Topsy.com'
  author_email: ''
  author_url: http://topsy.com/www.erichumphrey.com/2011/02/posh-tempdb-config/?utm_source=pingback&amp;utm_campaign=L2
  date: '2011-02-22 21:56:12 -0600'
  date_gmt: '2011-02-23 03:56:12 -0600'
  content: "[...] This post was mentioned on Twitter by Eric Humphrey, Sankar Reddy.
    Sankar Reddy said: RT @lotsahelp: [Blog] PowerShell Script for Paul&#039;s Survey:
    how is your tempdb configured? http://bit.ly/i2xfVq #sqlblog [...]"
- id: 56
  author: Jay
  author_email: sqljay.ram@gmail.com
  author_url: ''
  date: '2011-03-11 08:26:27 -0600'
  date_gmt: '2011-03-11 14:26:27 -0600'
  content: |-
    That's a very handy powershell script
    Thanks
---
<p>Paul Randal (<a href="http://sqlskills.com/BLOGS/PAUL/post/Survey-how-is-your-tempdb-configured.aspx">b</a> | <a href="http://twitter.com/paulrandal">t</a>) posted a survey <a href="http://sqlskills.com/BLOGS/PAUL/post/Survey-how-is-your-tempdb-configured.aspx">how is your tempdb configured?</a> Since I just happen to be at a SQLskills event this week I went ahead and created a PowerShell script to get the info across all your servers.</p>
<p>You'll need a list of servers in a text file ($serverfile), one per line. It will output the list to&nbsp;'sqlskills - tempdb proc, file count results.txt' in the directory from which it ran. Send that file, or the results to Paul.</p>
<pre lang="powershell">$serverfile = 'SQL_Servers_PROD.txt'
$query = "SELECT os.Cores, df.Files
FROM
   (SELECT COUNT(*) AS Cores FROM sys.dm_os_schedulers WHERE status = 'VISIBLE ONLINE') AS os,
   (SELECT COUNT(*) AS Files FROM tempdb.sys.database_files WHERE type_desc = 'ROWS') AS df;"

$dt = $null

#Get list of servers from SQL_Servers_PROD.txt
#Exclude any lines that start with # or are blank
gc $serverfile | ?{-not $_.StartsWith("#") -and $_ -ne "" -and $x -lt 2 } | %{
    $tempdbstats = Invoke-Sqlcmd -Query $query -ServerInstance $_ -SuppressProviderContextWarning
    if ($dt -eq $null) {
        $dt = , $tempdbstats
    } else {
        $dt += $tempdbstats
    }
}
$dt | ft -AutoSize | Out-File 'sqlskills - tempdb proc, file count results.txt' -Encoding UTF8</pre>
<p>The output looks like this:</p>
<pre>Cores Files
----- -----
   16     1
   16     8</pre>
