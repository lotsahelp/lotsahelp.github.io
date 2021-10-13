---
layout: post
title: 'PowerShell Script for Paul''s Survey: how is your tempdb configured?'
date: '2011-02-22 18:30:31 -0600'
categories:
- dba
- powershell
- sql
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
