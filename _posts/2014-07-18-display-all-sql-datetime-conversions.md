---
layout: post
title: Display All SQL DateTime Conversions
date: '2014-07-18 14:30:18 -0500'
categories:
- powershell
- sql
---
If you want a quick way to determine which format, or style, to specify when converting a datetime value in SQL to a character data type, you can use this simple PowerShell script. Or you can look [at the MSDN page](http://msdn.microsoft.com/en-us/library/ms187928.aspx "CAST and CONVERT (Transact-SQL)").

    0..200 | %{
        Invoke-Sqlcmd -ServerInstance . -Query ("SELECT num = {0}, CONVERT(varchar(100), GETDATE(), {0})" -f $_)
    } | ft -AutoSize
