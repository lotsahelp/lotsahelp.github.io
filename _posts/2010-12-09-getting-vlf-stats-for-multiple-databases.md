---
layout: post
status: publish
published: true
title: Getting VLF stats for multiple databases
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 25
wordpress_url: http://www.erichumphrey.com/2010/12/getting-vlf-stats-for-multiple-databases/
date: '2010-12-09 20:46:00 -0600'
date_gmt: '2010-12-10 02:46:00 -0600'
categories:
- dba
- powershell
- sql
- vlf
tags: []
comments:
- id: 22
  author: retracement
  author_email: ''
  author_url: ''
  date: '2010-12-11 02:10:53 -0600'
  date_gmt: '2010-12-11 08:10:53 -0600'
  content: Good post there Eric. I wrote something similar in C# with GUI front end
    a long time ago now, but I really think the Powershell version is a great example
    of a good reason for DBAs to be learning PS -its simplicity and power.
---
<p>After watching Paul Randal's (<a href="http://sqlskills.com/blogs/paul/">blog</a> | <a href="http://twitter.com/PaulRandal">twitter</a>) MCM <a href="http://technet.microsoft.com/en-us/sqlserver/gg313762.aspx">video on log files</a>, I wanted to find a quick way to get VLF (Virtual Log File) statistics across multiple databases and servers. I went two routes, PowerShell and T-SQL. Our internal monitoring uses T-SQL already, so I wanted to be able to easily work with the existing framework. I wanted to create a PowerShell version, as some others might find it useful and I just like working with PowerShell.</p>
<p><a href="http://db.tt/0Ko0xNO">T-SQL Version</a><br />I wound up making my table structure the same as David Levy in this <a href="http://adventuresinsql.com/2009/12/a-busyaccidental-dbas-guide-to-managing-vlfs/">blog post</a>. Michelle Ufford has a very similar script at her <a href="http://sqlfool.com/2010/06/check-vlf-counts/">blog post</a>.</p>
<p><a href="http://db.tt/kjxfcmm">PowerShell Version</a><br />Thanks to Aaron Nelson (<a href="http://sqlvariant.com/wordpress/">blog</a> | <a href="http://twitter.com/SQLvariant">twitter</a>) for giving me an idea to tweak this.</p>
<p>More on VLFs:</p>
<ul>
<li><span class="Apple-style-span" style="font-family: verdana, sans-serif; font-size: 11px;"><a href="http://www.sqlskills.com/BLOGS/KIMBERLY/post/Transaction-Log-VLFs-too-many-or-too-few.aspx">Transaction Log VLFs - too many or too few?</a></span><span class="Apple-style-span" style="font-family: verdana, sans-serif; font-size: 11px;"><span></span></span></li>
<li><span class="Apple-style-span" style="font-family: verdana, sans-serif;"><span class="Apple-style-span" style="font-size: 11px;"><a href="http://www.sqlskills.com/BLOGS/KIMBERLY/post/8-Steps-to-better-Transaction-Log-throughput.aspx">8 Steps to better Transaction Log throughput</a></span></span></li>
</ul>
<div><span class="Apple-style-span" style="font-family: verdana, sans-serif;"><span class="Apple-style-span" style="font-size: 11px;"><br /></span></span></div>
