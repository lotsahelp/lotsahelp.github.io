---
layout: post
status: publish
published: true
title: Splitting SQL Server Logs by ProcessInfo
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 27
wordpress_url: http://www.erichumphrey.com/2010/12/splitting-sql-server-logs-by-processinfo/
date: '2010-12-22 19:04:00 -0600'
date_gmt: '2010-12-23 01:04:00 -0600'
categories:
- powershell
- sql
tags: []
comments: []
---
<p>I wanted a way to parse through my SQL logs and be able to look at one particular spid or ProcessInfo class such as Server, Backup, Logon, etc. I came up with the following PowerShell script: *You must have either trace flag 1204 and/or 1222 turned on. (<a href="http://msdn.microsoft.com/en-us/library/ms178104.aspx">More info</a>)</p>
<pre lang="powershell">param($path)
if($path -eq $null) {exit}
if(Test-Path $path) {
    "Parsing $path"
    $spid = $null
    gc $path | %{
        #if ($_.Length -gt 35 -and $_.Substring(24, 12).StartsWith("spid")) {
        if($_ -match "^\d{4}") {
            $spid = $_.Substring(24, 12).Trim().PadRight(12, '_')
        } else {
            if($_.Trim().Length -lt 1 -and $spid -ne $null) {}
            else {
                $spid = "Default".PadRight(12, '_')
            }
        }
        $outfile = $path.Substring(0, $path.LastIndexOf(".")) + [string]::Format(".{0}.txt", $spid)
        $_ | Out-File -FilePath $outfile -Encoding "ASCII" -Append
    }
} else {"Bad path given"}</pre>
<p>If you dump your log file via the following command, this works really well.<br />
<code>sqlcmd -S MyServer\Inst1 -Q "xp_ReadErrorLog" -o myserver.log</code></p>
<p>Once you have it, just call the script and specify the -Path parameter as such:<br />
<code>&amp; '.\Split SQL Log by ProcessInfo.ps1' -Path C:\path\to\myserver.log</code></p>
<p>It looks at each line and figures either the spid or processinfo class and puts the line into a file with that string in its name. If it sees a blank line, it will put it into the previous file. WARNING: This could result any many files.</p>
<p>Yes, you could dump this to SQL or parse this some other way, but I wanted to split it into files so I can run it through another process to get all deadlock information that I'll be posting later.</p>
<p><a href="http://db.tt/sAlgYZ4">Download script</a></p>
