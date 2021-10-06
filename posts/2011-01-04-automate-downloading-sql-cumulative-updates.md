---
layout: post
status: publish
published: true
title: Automate Downloading SQL Cumulative Updates
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 50
wordpress_url: http://www.erichumphrey.com/?p=50
date: '2011-01-04 08:30:26 -0600'
date_gmt: '2011-01-04 14:30:26 -0600'
categories:
- download script
- powershell
- SQLServerPedia Syndication
tags: []
comments:
- id: 35
  author: kiquenet
  author_email: kiquenet+erichumphrey@gmail.com
  author_url: ''
  date: '2013-08-08 03:28:00 -0500'
  date_gmt: '2013-08-08 09:28:00 -0500'
  content: HOw can I get msg file from any message in Outlook using Powershell?
- id: 36
  author: lotsahelp
  author_email: eric.humphrey@gmail.com
  author_url: http://www.erichumphrey.com
  date: '2013-08-13 10:40:39 -0500'
  date_gmt: '2013-08-13 16:40:39 -0500'
  content: You can get the .msg file by saving the email out of Outlook or drag /
    drop the email to your desktop.
---
<p>Those of us admins that download each SQL Cumulative Update when it comes out may find this script handy. When you request a CU from Microsoft, you get this plain text email with links and passwords. Each link is to a different self-extracting zip file with the corresponding password below it.</p>
<p>When a CU has many files, it becomes a long series of point-and-click to download them all. Then you have to manually put in the password to each file in order to extract it. I wanted an easier solution.</p>
<h3><!--more-->Requirements:</h3>
<ul>
<li>PowerShell v2.0 (<a href="http://support.microsoft.com/kb/968930">http://support.microsoft.com/kb/968930</a>)</li>
<li>7-zip (<a href="http://www.7-zip.org/">http://www.7-zip.org/</a>)</li>
<li>CU Email from Microsoft in MSG format (drag out of Outlook onto Desktop)</li>
</ul>
<h3>Usage:</h3>
<pre lang="dos">&amp; '.\Download SQL CU.ps1' "hotfix.msg" "C:\Download\SQL CUs\2008\CU2"</pre>
<p>Be sure to either run the script in the directory you want to download or provide a second argument.</p>
<p><em>Update: I had to make a slight tweak to the argument checking.</em></p>
<h3>Script:</h3>
<pre lang="powershell">[System.Reflection.Assembly]::LoadWithPartialName("Microsoft.Office.Interop.Outlook") | Out-Null
Import-Module BitsTransfer
$7zipPath = "C:\Program Files\7-Zip\7z.exe"

if($args.Length -gt 0) {$file = Resolve-Path $args[0]}
else {Write-Host "Must supply a Cumulative Update email in .MSG format"}

if($args.Length -gt 1) {$destDir = Resolve-Path $args[1]}
else {$destDir = "."}
Write-Host "Downloading to `"$destDir`""

if(-not(Test-Path $7zipPath)) {Write-Host "Path to 7zip must be specified in order to extract"}

$ol = New-Object -comObject Outlook.Application
$mail = $ol.Session.OpenSharedItem($file)
$body = $mail.Body
$mail.Close([Microsoft.Office.Interop.Outlook.OlInspectorClose]::olDiscard)

$filePasses = $b = $null
$body.Split("`r`n") | ?{$_.startswith("Location") -or $_.startswith("Password")} | %{
    if ($_.StartsWith("Location")) {
        $fileUrl = $_.Substring($_.IndexOf("(")+1, $_.LastIndexOf(")")-$_.IndexOf("(")-1)
        $pass = $null
    }
    if ($_.StartsWith("Password")) {
        $pass = $_ -replace "^Password: "
    }
    if ($fileUrl -ne $null -and $pass -ne $null) {
        $fileName = $fileUrl.Substring($fileUrl.LastIndexOf("/")+1)
        $filePasses += @{$fileName = $pass}
		$filePath = Join-Path $destDir $fileName
        if(-not (Test-Path $filePath)) {
            $fileUrlAbbr = $fileUrl.Substring(0, $fileUrl.IndexOf("/", 7)) + "/.../" + $fileName
            Write-Host "Adding `"$fileUrlAbbr`" to BITS"
            #(New-Object System.Net.WebClient).DownloadFile($fileUrl, $fileName)
            if ($b -eq $null) {
                $b = , (Start-BitsTransfer -Source $fileUrl -Destination $destDir -Suspended -Asynchronous -Priority High -DisplayName $fileName)
            } else {
                #Add-BitsFile -BitsJob $b -Source $fileUrl -Destination $destDir | Out-Null
                $b += Start-BitsTransfer -Source $fileUrl -Destination $destDir -Suspended -Asynchronous -Priority High -DisplayName $fileName
            }
        }
        else {Write-Host "`"$fileName`" already exists"}
    }
}
#$b | Get-BitsTransfer | Format-Table -Property DisplayName, JobState
$b | Resume-BitsTransfer -Asynchronous

#loop through transfers and complete any that have finished
#  and resume any that errored
#  recheck every minute
while ($b | Get-BitsTransfer -EA SilentlyContinue) {
    $b | Get-BitsTransfer -EA SilentlyContinue | ?{$_.JobState -eq "Error"} | Resume-BitsTransfer -Asynchronous
    $b | Get-BitsTransfer -EA SilentlyContinue | ?{$_.JobState -eq "Transferred"} | Complete-BitsTransfer
    Start-Sleep -s 60
}

# Do file extraction
if(Test-Path $7zipPath) {
    $filePasses.keys | %{
        if(Test-Path $_) {
            if($_.Contains("i386")){$outDir = "-oi386"}
            elseif($_.Contains("x64")){$outDir = "-ox64"}
            elseif($_.Contains("ia64")){$outDir = "-oia64"}
            else{$outDir = "."}
            $pass = "-p" + $filePasses.$_
            &amp; $7zipPath x $_ $pass $outDir "-aos"
        }
    }
}</pre>
