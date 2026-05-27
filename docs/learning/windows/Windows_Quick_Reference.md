# Windows Quick Reference

**Created:** 2026-05-17 
**Version:** 1.0 
**Audience:** IT Administrators, DevOps Engineers

## Table of Contents

1. [Basic System Commands](#basic-system-commands)
2. [Active Directory](#active-directory)
3. [Group Policy](#group-policy)
4. [Networking](#networking)
5. [File & Storage](#file--storage)
6. [Security](#security)
7. [Services & Processes](#services--processes)
8. [Event Logs](#event-logs)
9. [Registry](#registry)
10. [Troubleshooting](#troubleshooting)
11. [Remote Management](#remote-management)
12. [Hyper-V](#hyper-v)

---

## Basic System Commands

### System Information
```powershell
# Full system information
Get-ComputerInfo

# OS version
Get-CimInstance Win32_OperatingSystem | Select-Object Caption, Version, BuildNumber

# Computer name
hostname
$env:COMPUTERNAME

# Uptime
(Get-Date) - (Get-CimInstance Win32_OperatingSystem).LastBootUpTime

# Username
whoami
$env:USERNAME

# Domain or workgroup
(Get-WmiObject Win32_ComputerSystem).Domain
```

### System Administration
```powershell
# Rename computer
Rename-Computer -NewName "SRV01" -Restart

# Join domain
Add-Computer -DomainName "contoso.com" -Credential (Get-Credential) -Restart

# Join workgroup
Add-Computer -WorkgroupName "WORKGROUP"

# Scheduled restart (in 5 minutes)
shutdown /r /t 300 /c "Scheduled reboot for maintenance"

# Cancel restart
shutdown /a

# IP configuration
Get-NetIPConfiguration
ipconfig /all

# Installed Windows Features
Get-WindowsFeature | Where-Object Installed
```

---

## Active Directory

### User Management
```powershell
# Get user information
Get-ADUser -Identity jdoe -Properties *

# Search user (by name)
Get-ADUser -Filter "Name -like '*John*'" | Select-Object Name, SamAccountName

# All enabled users
Get-ADUser -Filter {Enabled -eq $true} | Select-Object Name, SamAccountName

# Create user
New-ADUser -Name "John Doe" -GivenName "John" -Surname "Doe" -SamAccountName "jdoe" -UserPrincipalName "jdoe@contoso.com" -Path "OU=Users,DC=contoso,DC=com" -AccountPassword (Read-Host -AsSecureString "Password") -Enabled $true

# Delete user
Remove-ADUser -Identity jdoe -Confirm:$false

# Disable/enable user
Disable-ADAccount -Identity jdoe
Enable-ADAccount -Identity jdoe

# Reset password
Set-ADAccountPassword -Identity jdoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "NewP@ssw0rd!" -Force)

# Unlock user
Unlock-ADAccount -Identity jdoe

# User group membership
Get-ADPrincipalGroupMembership -Identity jdoe | Select-Object Name

# Add user to group
Add-ADGroupMember -Identity "IT Admins" -Members jdoe
```

### Group Management
```powershell
# Create group
New-ADGroup -Name "IT Admins" -GroupScope Global -GroupCategory Security -Path "OU=Groups,DC=contoso,DC=com"

# List group members
Get-ADGroupMember -Identity "IT Admins" | Select-Object Name, SamAccountName

# Recursive group members (nested groups)
Get-ADGroupMember -Identity "IT Admins" -Recursive

# Delete group
Remove-ADGroup -Identity "IT Admins" -Confirm:$false
```

### Computer Management
```powershell
# Get computer object
Get-ADComputer -Identity WS01 -Properties *

# All computers
Get-ADComputer -Filter * | Select-Object Name, OperatingSystem

# Search computers by OS
Get-ADComputer -Filter "OperatingSystem -like '*Windows 11*'" | Select-Object Name, OperatingSystem

# Disable computer
Disable-ADAccount -Identity WS01

# Delete computer from AD
Remove-ADComputer -Identity WS01 -Confirm:$false
```

### OU Management
```powershell
# Create OU
New-ADOrganizationalUnit -Name "IT Department" -Path "DC=contoso,DC=com"

# List OUs
Get-ADOrganizationalUnit -Filter * | Select-Object Name, DistinguishedName

# Move user to different OU
Move-ADObject -Identity "CN=John Doe,OU=Users,DC=contoso,DC=com" -TargetPath "OU=IT Department,DC=contoso,DC=com"
```

### FSMO Roles
```powershell
# Get FSMO roles
Get-ADDomain | Select-Object PDCEmulator, RIDMaster, InfrastructureMaster
Get-ADForest | Select-Object SchemaMaster, DomainNamingMaster

# Transfer FSMO role
Move-ADDirectoryServerOperationMasterRole -Identity "DC02" -OperationMasterRole PDCEmulator
```

### Recycle Bin
```powershell
# Enable AD Recycle Bin
Enable-ADOptionalFeature -Identity "Recycle Bin Feature" -Scope ForestOrConfigurationSet -Target "contoso.com"

# List deleted objects
Get-ADObject -Filter {IsDeleted -eq $true} -IncludeDeletedObjects | Select-Object Name, ObjectClass, whenChanged

# Restore deleted user
Get-ADObject -Filter {IsDeleted -eq $true -and Name -eq "John Doe"} -IncludeDeletedObjects | Restore-ADObject
```

---

## Group Policy

### GPO Management
```powershell
# List all GPOs
Get-GPO -All | Select-Object DisplayName, GpoStatus, CreationTime

# Create GPO
New-GPO -Name "Desktop Wallpaper Policy" -Comment "Centrally managed wallpaper"

# Link GPO to OU
New-GPLink -Name "Desktop Wallpaper Policy" -Target "OU=Workstations,DC=contoso,DC=com" -LinkEnabled Yes

# Unlink GPO
Remove-GPLink -Name "Desktop Wallpaper Policy" -Target "OU=Workstations,DC=contoso,DC=com"

# Backup GPO
Backup-GPO -Name "Desktop Wallpaper Policy" -Path "C:\GPOBackups"

# Backup all GPOs
Backup-GPO -All -Path "C:\GPOBackups"

# Restore GPO
Restore-GPO -Name "Desktop Wallpaper Policy" -Path "C:\GPOBackups"

# GPO HTML report
Get-GPOReport -Name "Desktop Wallpaper Policy" -ReportType Html -Path "C:\gpo-report.html"

# Delete GPO
Remove-GPO -Name "Old Policy" -Confirm:$false
```

### GPO Troubleshooting
```powershell
# GPResult (local machine)
gpresult /H C:\gpresult.html
gpresult /R

# GPResult (remote machine)
gpresult /S WS01 /U contoso\admin /P * /H C:\gpresult-ws01.html

# Force GP refresh
gpupdate /force

# Force GP refresh on remote machine
Invoke-GPUpdate -Computer "WS01" -Force

# RSoP (Resultant Set of Policy)
Get-GPResultantSetOfPolicy -ReportType Html -Path "C:\rsop.html"
```

---

## Networking

### Network Configuration
```powershell
# Get IP configuration
Get-NetIPConfiguration
Get-NetIPAddress

# Set IP address
New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.1.10 -PrefixLength 24 -DefaultGateway 192.168.1.1

# Set DNS server
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 192.168.1.5,192.168.1.6

# Enable DHCP
Set-NetIPInterface -InterfaceAlias "Ethernet" -Dhcp Enabled

# Hostname resolution
Resolve-DnsName google.com
nslookup google.com

# Network adapter info
Get-NetAdapter
```

### DNS
```powershell
# Create DNS zone
Add-DnsServerPrimaryZone -Name "contoso.com" -ReplicationScope "Forest"

# Add DNS A record
Add-DnsServerResourceRecordA -Name "www" -ZoneName "contoso.com" -IPv4Address "192.168.1.10"

# Add DNS CNAME record
Add-DnsServerResourceRecordCName -Name "intranet" -HostNameAlias "www.contoso.com" -ZoneName "contoso.com"

# List DNS records
Get-DnsServerResourceRecord -ZoneName "contoso.com"

# Flush DNS cache
Clear-DnsClientCache

# Flush DNS server cache
Clear-DnsServerCache
```

### DHCP
```powershell
# Create DHCP scope
Add-DhcpServerv4Scope -Name "LAN Scope" -StartRange 192.168.1.10 -EndRange 192.168.1.200 -SubnetMask 255.255.255.0

# Set DHCP options (Gateway, DNS)
Set-DhcpServerv4OptionValue -ScopeId 192.168.1.0 -Router 192.168.1.1 -DnsServer 192.168.1.5,192.168.1.6

# Add DHCP Reservation
Add-DhcpServerv4Reservation -ScopeId 192.168.1.0 -IPAddress 192.168.1.50 -ClientId "00-15-5D-02-33-04" -Description "File Server"

# List DHCP leases
Get-DhcpServerv4Lease -ScopeId 192.168.1.0
```

### Network Troubleshooting
```powershell
# Ping
Test-Connection -ComputerName google.com -Count 4

# Traceroute
Test-NetConnection -ComputerName google.com -TraceRoute

# Port check
Test-NetConnection -ComputerName server.contoso.com -Port 443

# Routing table
Get-NetRoute
route print

# ARP cache
Get-NetNeighbor
arp -a

# Netstat
Get-NetTCPConnection
netstat -ano
```

---

## File & Storage

### Disk Management
```powershell
# List disks
Get-Disk

# Create partition
New-Partition -DiskNumber 1 -UseMaximumSize -AssignDriveLetter

# Format
Format-Volume -DriveLetter D -FileSystem NTFS -NewFileSystemLabel "Data" -Confirm:$false

# Disk space
Get-PSDrive | Where-Object {$_.Provider -like "*FileSystem*"} | Select-Object Name, @{Name="UsedGB";Expression={[math]::Round($_.Used/1GB,2)}}, @{Name="FreeGB";Expression={[math]::Round($_.Free/1GB,2)}}

# Defragmentation
Optimize-Volume -DriveLetter C -Defrag -Verbose
```

### File Shares
```powershell
# Create SMB Share
New-SmbShare -Name "SharedFolder" -Path "C:\Shares\SharedFolder" -FullAccess "CONTOSO\Domain Admins" -ChangeAccess "CONTOSO\Domain Users"

# List shares
Get-SmbShare

# Share permissions
Get-SmbShareAccess -Name "SharedFolder"
Grant-SmbShareAccess -Name "SharedFolder" -AccountName "CONTOSO\Marketing" -AccessRight Change

# Get NTFS permissions
Get-Acl "C:\Shares\SharedFolder" | Format-List

# Add NTFS permission
$acl = Get-Acl "C:\Shares\SharedFolder"
$permission = "CONTOSO\Marketing","Modify","Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule $permission
$acl.SetAccessRule($accessRule)
Set-Acl "C:\Shares\SharedFolder" $acl
```

### DFS
```powershell
# Create DFS Namespace root
New-DfsnRoot -Path "\\contoso.com\Public" -TargetPath "\\FS01\Public" -Type DomainV2

# Add DFS folder
New-DfsnFolder -Path "\\contoso.com\Public\IT" -TargetPath "\\FS01\IT"

# DFS replication group
New-DfsReplicationGroup -GroupName "Public Replication"
Add-DfsrMember -GroupName "Public Replication" -ComputerName "FS01","FS02"
```

---

## Security

### Firewall
```powershell
# Firewall status
Get-NetFirewallProfile | Select-Object Name, Enabled

# Enable/disable firewall
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True

# Create firewall rule
New-NetFirewallRule -DisplayName "Allow RDP" -Direction Inbound -Protocol TCP -LocalPort 3389 -Action Allow

# List firewall rules
Get-NetFirewallRule | Where-Object {$_.Enabled -eq $true} | Select-Object DisplayName, Direction, Action

# Delete firewall rule
Remove-NetFirewallRule -DisplayName "Allow RDP"
```

### BitLocker
```powershell
# BitLocker status
Get-BitLockerVolume

# Enable BitLocker
Enable-BitLocker -MountPoint "C:" -EncryptionMethod XtsAes256 -UsedSpaceOnly -TpmProtector

# BitLocker recovery key
(Get-BitLockerVolume -MountPoint "C:").KeyProtector

# Backup BitLocker recovery key to AD
Backup-BitLockerKeyProtector -MountPoint "C:" -KeyProtectorId (Get-BitLockerVolume -MountPoint "C:").KeyProtector[1].KeyProtectorId
```

### Windows Defender
```powershell
# Defender status
Get-MpComputerStatus

# Full scan
Start-MpScan -ScanType FullScan

# Quick scan
Start-MpScan -ScanType QuickScan

# Update signatures
Update-MpSignature

# Exclusions
Add-MpPreference -ExclusionPath "C:\Temp"
Add-MpPreference -ExclusionExtension ".log"
```

### User Rights
```powershell
# Local Administrators group members
Get-LocalGroupMember -Group "Administrators"

# Add user to local Administrators
Add-LocalGroupMember -Group "Administrators" -Member "CONTOSO\jdoe"

# Remove user from local Administrators
Remove-LocalGroupMember -Group "Administrators" -Member "CONTOSO\jdoe"
```

---

## Services & Processes

### Services
```powershell
# Service status
Get-Service -Name "Spooler"

# Start service
Start-Service -Name "Spooler"

# Stop service
Stop-Service -Name "Spooler" -Force

# Restart service
Restart-Service -Name "Spooler"

# Set service startup type
Set-Service -Name "Spooler" -StartupType Automatic

# All running services
Get-Service | Where-Object {$_.Status -eq "Running"} | Select-Object Name, DisplayName, StartType

# Create service
New-Service -Name "MyService" -BinaryPathName "C:\MyApp\service.exe" -DisplayName "My Service" -StartupType Automatic
```

### Processes
```powershell
# List processes
Get-Process

# Top CPU consumers
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10 Name, CPU, WS

# Kill process
Stop-Process -Name "notepad" -Force
Stop-Process -Id 1234 -Force

# Start process
Start-Process "notepad.exe"
Start-Process "powershell.exe" -Verb RunAs # Run as Administrator
```

---

## Event Logs

### Event Viewer
```powershell
# Event log types
Get-EventLog -List

# System log last 50 events
Get-EventLog -LogName System -Newest 50

# Application errors (last 24 hours)
Get-EventLog -LogName Application -EntryType Error -After (Get-Date).AddDays(-1)

# Security log: Failed logon attempts
Get-WinEvent -FilterHashtable @{LogName='Security'; ID=4625} -MaxEvents 50

# Clear event log
Clear-EventLog -LogName Application
wevtutil cl Application

# Custom event log query
Get-WinEvent -FilterHashtable @{LogName='System'; Level=2; StartTime=(Get-Date).AddHours(-6)} | Select-Object TimeCreated, Message
```

---

## Registry

### Registry Navigation
```powershell
# Get registry key
Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion"

# Modify registry value
Set-ItemProperty -Path "HKLM:\SOFTWARE\MyApp" -Name "Version" -Value "2.0"

# Create registry key
New-Item -Path "HKLM:\SOFTWARE\MyApp"

# Create registry value
New-ItemProperty -Path "HKLM:\SOFTWARE\MyApp" -Name "InstallPath" -Value "C:\Program Files\MyApp" -PropertyType String

# Delete registry key
Remove-Item -Path "HKLM:\SOFTWARE\MyApp" -Recurse

# Export registry
reg export "HKLM\SOFTWARE\MyApp" "C:\backup.reg"

# Import registry
reg import "C:\backup.reg"
```

### Useful Registry Tweaks
```powershell
# Disable Windows Update automatic restart
Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" -Name "NoAutoRebootWithLoggedOnUsers" -Value 1

# Enable Remote Desktop
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server" -Name "fDenyTSConnections" -Value 0

# Show file extensions
Set-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced" -Name "HideFileExt" -Value 0

# Show hidden files
Set-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced" -Name "Hidden" -Value 1
```

---

## Troubleshooting

### System Health
```powershell
# System File Checker
sfc /scannow

# DISM health check
DISM /Online /Cleanup-Image /CheckHealth
DISM /Online /Cleanup-Image /ScanHealth
DISM /Online /Cleanup-Image /RestoreHealth

# ChkDsk
chkdsk C: /F /R

# Disk cleanup
cleanmgr /sagerun:1
```

### Performance
```powershell
# Performance counters
Get-Counter '\Processor(_Total)\% Processor Time' -SampleInterval 1 -MaxSamples 10
Get-Counter '\Memory\Available MBytes'

# Top memory consumers
Get-Process | Sort-Object WS -Descending | Select-Object -First 10 Name, @{Name="Memory(MB)";Expression={[math]::Round($_.WS/1MB,2)}}

# Boot performance
Get-WinEvent -FilterHashtable @{LogName='System'; ID=12} | Select-Object -First 10 TimeCreated, Message
```

### Network Diagnostics
```powershell
# Reset network stack
netsh winsock reset
netsh int ip reset
ipconfig /flushdns

# TCP/IP diagnostics
Test-NetConnection -ComputerName 8.8.8.8 -DiagnosticsLevel Detailed

# Route troubleshooting
tracert google.com
pathping google.com
```

---

## Remote Management

### Remote Desktop
```powershell
# Enable RDP
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server" -Name "fDenyTSConnections" -Value 0
Enable-NetFirewallRule -DisplayGroup "Remote Desktop"

# RDP connection
mstsc /v:server.contoso.com
```

### PowerShell Remoting
```powershell
# Enable WinRM
Enable-PSRemoting -Force

# Start remote session
Enter-PSSession -ComputerName SRV01 -Credential (Get-Credential)

# Execute remote command
Invoke-Command -ComputerName SRV01,SRV02 -ScriptBlock { Get-Service }

# Execute remote script
Invoke-Command -ComputerName SRV01 -FilePath "C:\Scripts\Update.ps1"

# Exit WinRM session
Exit-PSSession
```

### Remote Server Administration Tools (RSAT)
```powershell
# Install RSAT (Windows 10/11)
Get-WindowsCapability -Name RSAT* -Online | Add-WindowsCapability -Online

# Install RSAT (Windows Server)
Install-WindowsFeature -Name RSAT -IncludeAllSubFeature
```

---

## Hyper-V

### VM Management
```powershell
# List VMs
Get-VM

# Create VM
New-VM -Name "VM01" -MemoryStartupBytes 4GB -Generation 2 -NewVHDPath "C:\VMs\VM01.vhdx" -NewVHDSizeBytes 60GB

# Start VM
Start-VM -Name "VM01"

# Stop VM
Stop-VM -Name "VM01"

# Suspend VM
Suspend-VM -Name "VM01"

# Create VM checkpoint (snapshot)
Checkpoint-VM -Name "VM01" -SnapshotName "Before Updates"

# Restore checkpoint
Restore-VMCheckpoint -Name "Before Updates" -VMName "VM01" -Confirm:$false

# Export VM
Export-VM -Name "VM01" -Path "C:\VMExports"
```

### Virtual Switch
```powershell
# Create virtual switch
New-VMSwitch -Name "External Switch" -NetAdapterName "Ethernet" -AllowManagementOS $true

# List virtual switches
Get-VMSwitch

# Connect VM to virtual switch
Connect-VMNetworkAdapter -VMName "VM01" -SwitchName "External Switch"
```

---

## 🔥 Quick Commands (One-Liners)

### Most Common Tasks

```powershell
# List all domain users
Get-ADUser -Filter * | Select-Object Name, SamAccountName | Export-Csv "C:\users.csv" -NoTypeInformation

# Unlock all locked out users
Search-ADAccount -LockedOut | Unlock-ADAccount

# List disabled users
Get-ADUser -Filter {Enabled -eq $false} | Select-Object Name, SamAccountName

# Users inactive for 90+ days
$date = (Get-Date).AddDays(-90)
Get-ADUser -Filter {LastLogonDate -lt $date} -Properties LastLogonDate | Select-Object Name, LastLogonDate

# Get GPO links for OU
Get-GPInheritance -Target "OU=Workstations,DC=contoso,DC=com"

# Services troubleshooting: Automatic but stopped
Get-Service | Where-Object {$_.Status -eq 'Stopped' -and $_.StartType -eq 'Automatic'}

# Disk space warning (<10% free)
Get-PSDrive | Where-Object {$_.Provider -like "*FileSystem*" -and $_.Free -lt ($_.Used + $_.Free)*0.1}

# Failed logon attempts (last 24h)
Get-WinEvent -FilterHashtable @{LogName='Security'; ID=4625; StartTime=(Get-Date).AddDays(-1)} | Select-Object TimeCreated, Message

# Installed applications
Get-WmiObject -Class Win32_Product | Select-Object Name, Version | Sort-Object Name

# Last reboot time
Get-CimInstance Win32_OperatingSystem | Select-Object @{Name="LastBootTime";Expression={$_.LastBootUpTime}}

# Check pending reboot
if (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate\Auto Update\RebootRequired" -ErrorAction SilentlyContinue) { "Reboot Required" } else { "No Reboot Required" }
```

---

## 📌 Notes

- **PowerShell Execution Policy:** Ensure execution policy allows script execution:
```powershell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```

- **Admin Privileges:** Many commands require elevated privileges (Run as Administrator).

- **Import Modules:** Some commands require module imports:
```powershell
Import-Module ActiveDirectory
Import-Module GroupPolicy
Import-Module DnsServer
Import-Module DhcpServer
```

- **Help:** Detailed help for any command:
```powershell
Get-Help <cmdlet> -Full
Get-Help <cmdlet> -Examples
```

---

**Last Updated:** 2026-05-17
