---
layout: post
status: publish
published: true
title: PowerPointShell
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 106
wordpress_url: http://www.erichumphrey.com/?p=106
date: '2011-02-18 08:30:21 -0600'
date_gmt: '2011-02-18 14:30:21 -0600'
categories:
- powershell
tags: []
comments: []
---
<p>I wanted to add a neat demo to my <a href="/2011/02/presenting-for-the-pass-powershell-virtual-chapter/">.NET Powers Activate! Form of PowerShell!</a> presentation that would really demonstrate the use of .NET in PowerShell. I decided to have PowerShell create a PowerPoint presentation from code for my opening demo.</p>
<p>It uses New-Object -ComObject to instantiate a PowerPoint.Application COMobject. The code then proceeds to create a presentation and add two slides, with content, to the presentation. Finally it applies a theme to the presentation. I obfuscated the slide text just for fun by encoding to Base64 and storing the encoded string in the variables at the top. The text gets decoded when getting posted to the slides. The code to encode the text is commented at the bottom for completeness.</p>
<pre lang="powershell">#Script my PowerPoint slides
#Theme requires PowerPoint 2007 (v12)

$title = 'Lk5FVCBQb3dlcnMgQWN0aXZhdGUhIEZvcm0gb2YgUG93ZXJTaGVsbCE='
$subtitle = 'RXJpYyBIdW1waHJleQ=='
$contactInfo = 'd3d3LmVyaWNodW1waHJleS5jb20KQGxvdHNhaGVscAp3d3cubGlua2VkaW4uY29tL2luL2xvdHNhaGVscAplcmljLmh1bXBocmV5QGdtYWlsLmNvbQ=='

$app = New-Object -ComObject PowerPoint.Application -strict -property @{visible=$true}
$ppt = $app.Presentations.Add($true)
$slide = $ppt.Slides.Add(1, 1)
$slide.Shapes.Title.TextFrame.TextRange = [System.Text.Encoding]::ASCII.GetString([Convert]::FromBase64String($title))
$slide.Shapes | ?{$_.Name -eq 'Subtitle 2'} | %{$_.TextFrame.TextRange = [System.Text.Encoding]::ASCII.GetString([Convert]::FromBase64String($subtitle))}

$slide2 = $ppt.Slides.Add(2, 2)
$slide2.Shapes.Title.TextFrame.TextRange = 'Contact Me'
$slide2.Shapes |
    ?{$_.Name -eq 'Text Placeholder 2'} |
    %{$_.TextFrame.TextRange = [System.Text.Encoding]::ASCII.GetString([Convert]::FromBase64String($contactInfo))}

if(Test-Path 'C:\Program Files (x86)' -PathType Container) {
    $ppt.ApplyTheme("C:\Program Files (x86)\Microsoft Office\Document Themes 12\Module.thmx")
} else {
    $ppt.ApplyTheme("C:\Program Files\Microsoft Office\Document Themes 12\Module.thmx")
}

#Command used to encode strings to Base64
#[System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes(''))</pre>
