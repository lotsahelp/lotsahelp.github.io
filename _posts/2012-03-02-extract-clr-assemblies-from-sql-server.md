---
layout: post
title: Extract CLR Assemblies from SQL Server
date: '2012-03-02 15:53:03 -0600'
categories:
- clr
- dba
- powershell
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
