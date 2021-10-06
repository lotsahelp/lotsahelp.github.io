---
layout: post
status: publish
published: true
title: Extract CLR Assemblies from SQL Server
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 282
wordpress_url: http://www.erichumphrey.com/?p=282
date: '2012-03-02 15:53:03 -0600'
date_gmt: '2012-03-02 21:53:03 -0600'
categories:
- clr
- dba
- powershell
tags: []
comments:
- id: 92
  author: viktor
  author_email: vgurevich@valueline.com
  author_url: ''
  date: '2013-01-07 14:07:15 -0600'
  date_gmt: '2013-01-07 20:07:15 -0600'
  content: |-
    Is it PowerShell?
    Thank you.
- id: 93
  author: lotsahelp
  author_email: eric.humphrey@gmail.com
  author_url: http://www.erichumphrey.com
  date: '2013-01-07 15:04:10 -0600'
  date_gmt: '2013-01-07 21:04:10 -0600'
  content: Yes, this is powershell code.
- id: 94
  author: DJ
  author_email: djoshi911@gmail.com
  author_url: ''
  date: '2013-06-21 04:51:54 -0500'
  date_gmt: '2013-06-21 10:51:54 -0500'
  content: |-
    Hi mate, very useful article, but i need help understanding why the SQLSERVER drive is in the loop syntax?

    is it to iterate through databases held in DB folders on the sql server\instance?

    cheers
    dj
- id: 95
  author: lotsahelp
  author_email: eric.humphrey@gmail.com
  author_url: http://www.erichumphrey.com
  date: '2013-06-21 10:23:21 -0500'
  date_gmt: '2013-06-21 16:23:21 -0500'
  content: This just grabs all assemblies from the passed in $Database parameter.
    The SQLSERVER:\ drive is the PowerShell provider for SQL.
---
<p>I've run into a few situations that required examining the existing CLR assemblies on a server. Whether I needed to do a comparison between two versions to make sure they are the same or confirm something in the assembly itself, this script has come in handy. Point it at a database, give it an output path and it will save all the assemblies in that database to dlls in the given folder. You can then use a .NET disassembler to confirm suspicions or a binary comparison to make sure the dll matches what it should.</p>
<pre lang="powershell"><#
    .SYNOPSIS
        Extracts CLR Assemblies from a SQL 2005+ database.

    .DESCRIPTION
        Extracts CLR Assemblies from a SQL 2005+ database.

    .PARAMETER  ServerInstance
        The Server\Instance to connect to
    .PARAMETER  Database
        The Database to extract assemblies from
    .PARAMETER  OutPath
        The path to output the assemblies

    .EXAMPLE
        PS C:\> .\Get-SqlAssemblies.ps1 -ServerInstance 'MyServer\MyInstance' -Database 'MyDatabase'
        This example shows how to call Get-SqlAssemblies with named parameters.

    .INPUTS
        System.String

    .NOTES
        For more information about advanced functions, call Get-Help with any
        of the topics in the links listed below.

#>
param(
    [string]$ServerInstance = 'LOCALHOST',

    [Parameter(Mandatory=$true)]
    [string]$Database,

    [string]$OutPath = '.'
)

#Correct for variations of incoming ServerInstance names
if(-not $ServerInstance.Contains('\')) {$ServerInstance += '\DEFAULT'}
if($ServerInstance.Contains(',') -and -not $ServerInstance.Contains('`,')) {$ServerInstance = $ServerInstance.Replace(',', '`,')}

dir SQLSERVER:\SQL\$ServerInstance\Databases\$Database\Assemblies | %{
    $_.SqlAssemblyFiles | %{
        $str = $_.name
        $path = Join-Path $OutPath ($str.Substring($str.LastIndexOf('\')+1))
        Set-Content -Path $path -Value $_.GetFileBytes() -Encoding byte;
    }
}
</pre>
