---
layout: post
title: Which Secure Protocols are Allowed on Your Servers
date: '2015-12-15 15:15:29 -0600'
categories:
- powershell
---
A script to have handy to tell you which schannel protocols are enabled / disabled for PCI or certificate compliance.

This one works well for local servers. If you want to query a remote server, look below.

    $path = "HKLM://SYSTEM/CurrentControlSet/Control/SecurityProviders/Schannel/Protocols"

    Push-Location
    Set-Location -Path $path
    Get-ChildItem . |
    Select-Object @{
                    Name="Protocol"
                    Expression={Split-Path $_.PSPath -leaf}
                },@{
                    Name="Client Enabled"
                    Expression={(Get-ItemProperty -Path (Join-Path $_.PSPath 'Client') -ErrorAction silentlycontinue).Enabled}
                },@{
                    Name="Client DisabledByDefault"
                    Expression={(Get-ItemProperty -Path (Join-Path $_.PSPath 'Client') -ErrorAction silentlycontinue).DisabledByDefault}
                },@{
                    Name="Server Enabled"
                    Expression={(Get-ItemProperty -Path (Join-Path $_.PSPath 'Server') -ErrorAction silentlycontinue).Enabled}
                },@{
                    Name="Server DisabledByDefault"
                    Expression={(Get-ItemProperty -Path (Join-Path $_.PSPath 'Server') -ErrorAction silentlycontinue).DisabledByDefault}
                } | ft -autosize
    Pop-Location

This version works against remote servers.

    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$true,Position=1)]
        [string]$ComputerName
    )
    $path = "SYSTEM\\CurrentControlSet\\Control\\SecurityProviders\\Schannel\\Protocols\"

    $Reg = [Microsoft.Win32.RegistryKey]::OpenRemoteBaseKey('LocalMachine', $ComputerName)
    $Reg.OpenSubKey($path).GetSubKeyNames() | ForEach-Object {
        $protocol = $_
        try {
            $cEnabled = $Reg.OpenSubKey("$($path)\\$($_)\\Client").GetValue('Enabled')
            $cDisabledByDefault = $Reg.OpenSubKey("$($path)\\$($_)\\Client").GetValue('DisabledByDefault')
        } catch {}
        try {
            $sEnabled = $Reg.OpenSubKey("$($path)\\$($_)\\Server").GetValue('Enabled')
            $sDisabledByDefault = $Reg.OpenSubKey("$($path)\\$($_)\\Server").GetValue('DisabledByDefault')
        } catch {}
        
    New-Object &ndash;TypeName PSObject &ndash;Prop @{"Protocol"=$protocol; "Client Enabled"=$cEnabled; "Client DisabledByDefault"=$cDisabledByDefault; "Server Enabled"=$sEnabled; "Server DisabledByDefault"=$sDisabledByDefault}
    $cEnabled, $cDisabledByDefault = $null;
    $sEnabled, $sDisabledByDefault = $null;
    } | Format-Table -Property "Protocol", "Client Enabled", "Client DisabledByDefault", "Server Enabled", "Server DisabledByDefault" -AutoSize
    Remove-Variable Reg
