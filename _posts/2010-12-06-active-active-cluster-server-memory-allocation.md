---
layout: post
title: Active / Active Cluster Server Memory Allocation
date: '2010-12-06 20:10:00 -0600'
categories:
- clusters
- dba
- powershell
- sql
---
<p>We have several active / active clusters with multiple instances per node in our environment. We want to ensure the instances are spread as evenly as possible across the nodes. I wrote a quick PowerShell script to go through a text file and get the physical node where the instance is running, how much physical memory the node has and how much memory the instance is configured to use.</p>
<p>Just create a text file "servers.txt" with the list of instances on a cluster and run the following.</p>
<div>
<pre lang="powershell">
[System.Reflection.Assembly]::LoadWithPartialName("Microsoft.SqlServer.Smo") | Out-Null
[System.Reflection.Assembly]::LoadWithPartialName("Microsoft.SqlServer.SmoExtended") | Out-Null

$servers = get-content "servers.txt"
foreach($server in $servers)
{
$sqlServer = New-Object Microsoft.SqlServer.Management.Smo.Server $server
[String]::Format("{0} uses {1}MB RAM. Resides on {2}.", $server, $sqlServer.Configuration.MaxServerMemory.ConfigValue, $sqlServer.ComputerNamePhysicalNetBIOS)

$objCS = Get-WmiObject Win32_ComputerSystem -ComputerName $server.Substring(0, $server.IndexOf("\"))
[String]::Format("{0}\{1}: {2}MB", $objCS.Domain, $objCS.Name, [Math]::Round($objCS.TotalPhysicalMemory / 1mb))
}
</pre>
<div>The formatting leaves much to be desired, but it's a quick check and that was what I was interested in. Excel can do all the formatting and calculating later.</div>
</div>
