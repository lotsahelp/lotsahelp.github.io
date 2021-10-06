---
layout: post
status: publish
published: true
title: Split Typeperf Output in More Managable Chunks
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 262
wordpress_url: http://www.erichumphrey.com/?p=262
date: '2012-02-17 08:15:22 -0600'
date_gmt: '2012-02-17 14:15:22 -0600'
categories:
- dba
- powershell
tags: []
comments:
- id: 88
  author: Jeffery Hicks
  author_email: jhicks@jdhitsolutions.com
  author_url: http://jdhitsolutions.com/blog
  date: '2012-02-17 08:31:08 -0600'
  date_gmt: '2012-02-17 14:31:08 -0600'
  content: |-
    This is more complicated than it needs to be. I'm assuming you must have a developer background, but for an IT Pro looking at this it might be a bit daunting. For example, it seems the only reason you are defining $inFile is to to verify $filepath exists. If so, why not just use Test-Path?

    if (Test-Path $filepath) {
      $data=get-content $filepath
    }
    else {
      Write-warning "Failed to find $filepath"
    }

    The addition of some comments and using full cmdlet names instead of aliases would also help. You have something useful but it is difficult to fully comprehend.  An example of the data you are importing and the final result would also help.
- id: 89
  author: lotsahelp
  author_email: eric.humphrey@gmail.com
  author_url: http://www.erichumphrey.com
  date: '2012-02-17 08:36:06 -0600'
  date_gmt: '2012-02-17 14:36:06 -0600'
  content: "<a href=\"#comment-197\" rel=\"nofollow\">@Jeffery Hicks </a> \nJeff,
    thanks for the comment. I'm using $inFile to also get the parent directory of
    the incoming file so new files can be written to the same place. I'll work on
    expanding aliases and providing examples."
- id: 90
  author: Jeffery Hicks
  author_email: jhicks@jdhitsolutions.com
  author_url: http://jdhitsolutions.com/blog
  date: '2012-02-17 09:24:12 -0600'
  date_gmt: '2012-02-17 15:24:12 -0600'
  content: |-
    Is this what you are after?

    $parent=(Get-Item $filepath).Directory

    There's nothing wrong with using .NET classes and methods, but personally I think that should only be if there isn't a cmdlet approach. Otherwise the script starts looking more like C# source code. But that's because I'm coming at PowerShell from the IT Pro side and I'm trying to make it easier for people to adopt it and not be scared off.
- id: 91
  author: Jeffery Hicks
  author_email: jhicks@jdhitsolutions.com
  author_url: http://jdhitsolutions.com/blog
  date: '2012-02-17 10:02:08 -0600'
  date_gmt: '2012-02-17 16:02:08 -0600'
  content: Nice changes. Can you post a sample typeperf command that creates the file
    you are processing with this script? If it is a CSV file you might be able to
    use Import-CSV and make this even easier.
---
<p>I really like using <a href="http://technet.microsoft.com/en-us/library/bb490960.aspx">typeperf</a> for perfmon counter collection. It allows me to save a collection of counters to monitor and store those readings in a csv file for later analysis. Sometimes I end up running the output through the <a href="http://pal.codeplex.com/">PAL tool</a>. Unfortunately, the PAL tool generates graphs that are fairly narrow. Monitoring sessions of long duration causes these graphs to be really cramped. I wanted a way to split the typeperf output to get a reasonable amount of data points in these graphs. A side benefit is the processing per file is a lot quicker.</p>
<p>The script takes a filepath as its only argument. It splits by the hour and copies the header row to each new file.</p>
<pre lang="powershell">param (
	[string]$filepath #incoming file
)

#Does the file exist
if (Test-Path $filepath) {
	$infile = Get-Item $filepath
	$data = Get-Content $infile
}
#if not, exit the script
else {
	Write-Warning "Failed to find $filepath"
	exit
}

#Get the header to be able to repeat it in each file
$header = $data | Select-Object -First 1
$lastHour = $null
$outFile = $null

#Loop through the data, skipping the header line.
$data | Select-Object -Skip 1 | ForEach-Object {
	$date = [DateTime]::Parse([string]$_.Substring(1, [string]$_.IndexOf('"',1)-1))
	if($lastHour -eq $null -or $date.Hour -ne $lastHour.Hour -or $outFile -eq $null) {
		$lastHour = $date.AddMinutes(-$date.Minute).AddSeconds(-$date.Second)
		$outFile = Join-Path $infile.Directory ("{0}_{1}{2}" -f $infile.BaseName, $lastHour.ToString('yyyyMMdd_HHmmss'), $infile.extension)
		$header | Out-File $outFile -Encoding UTF8
	}
	$_ | Out-File $outFile -Encoding UTF8 -Append
}</pre>
<p><strong>Update:</strong> I cleaned up the script a little based on Jeff's comments below.</p>
