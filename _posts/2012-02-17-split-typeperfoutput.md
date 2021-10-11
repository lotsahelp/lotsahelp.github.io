---
layout: post
title: Split Typeperf Output in More Managable Chunks
date: '2012-02-17 08:15:22 -0600'
categories:
- dba
- powershell
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
