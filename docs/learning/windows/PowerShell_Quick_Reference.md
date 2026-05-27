# PowerShell Quick Reference

**Created:** 2026-05-17 
**Version:** 1.0 
**PowerShell Version:** 5.1 / 7.x 
**Audience:** IT Administrators, DevOps Engineers, Scripters

## Table of Contents

1. [Basics](#basics)
2. [Pipeline and Filtering](#pipeline-and-filtering)
3. [Variables and Data Types](#variables-and-data-types)
4. [Conditional Logic](#conditional-logic)
5. [Loops](#loops)
6. [Functions](#functions)
7. [File Operations](#file-operations)
8. [Registry Operations](#registry-operations)
9. [Networking](#networking)
10. [Active Directory](#active-directory)
11. [Remote Management](#remote-management)
12. [Error Handling](#error-handling)
13. [Modules and Snap-ins](#modules-and-snap-ins)
14. [Useful One-Liners](#useful-one-liners)

---

## Basics

### Cmdlet Structure
```powershell
# General form: Verb-Noun
Get-Process
Get-Service
Set-Location
New-Item
Remove-Item

# Parameters
Get-Service -Name "Spooler"
Get-Process -Name "notepad" -ComputerName "SRV01"
```

### Help System
```powershell
# Update help (only once)
Update-Help

# Get help
Get-Help Get-Process
Get-Help Get-Process -Full
Get-Help Get-Process -Examples
Get-Help Get-Process -Online

# Find commands
Get-Command *service*
Get-Command -Verb Get -Noun *AD*

# Aliases
Get-Alias
Get-Alias -Name ls
Get-Alias -Definition Get-ChildItem
```

### Basic Navigation
```powershell
# Current directory
Get-Location
pwd

# Change directory
Set-Location C:\Temp
cd C:\Temp

# List files
Get-ChildItem
ls
dir

# Recursive listing
Get-ChildItem -Recurse

# Include hidden files
Get-ChildItem -Force

# Filter by extension
Get-ChildItem -Filter *.txt
Get-ChildItem -Path C:\Logs -Filter *.log -Recurse
```

---

## Pipeline and Filtering

### Pipeline Basics
```powershell
# Pipeline: left side output passes to right side
Get-Process | Sort-Object CPU -Descending
Get-Service | Where-Object {$_.Status -eq "Running"}
Get-ChildItem | Select-Object Name, Length

# Multi-stage pipeline
Get-Process | Where-Object {$_.CPU -gt 10} | Sort-Object CPU -Descending | Select-Object -First 10
```

### Where-Object (Filtering)
```powershell
# Basic syntax
Get-Service | Where-Object {$_.Status -eq "Running"}
Get-Process | Where-Object {$_.WorkingSet -gt 100MB}

# Simplified syntax (PSv3+)
Get-Service | Where Status -eq "Running"
Get-Process | Where CPU -gt 10

# Multiple conditions (AND)
Get-Process | Where-Object {$_.CPU -gt 10 -and $_.WS -gt 100MB}

# Multiple conditions (OR)
Get-Service | Where-Object {$_.Status -eq "Running" -or $_.StartType -eq "Automatic"}

# Like operator
Get-Process | Where-Object {$_.Name -like "*chrome*"}

# Match operator (regex)
Get-Process | Where-Object {$_.Name -match "^c.*e$"}
```

### Select-Object (Field Selection)
```powershell
# Select specific fields
Get-Process | Select-Object Name, CPU, WS

# First/Last N items
Get-Process | Select-Object -First 10
Get-Process | Select-Object -Last 5

# Unique values
Get-Process | Select-Object -Property ProcessName -Unique

# Calculated fields
Get-Process | Select-Object Name, @{Name="Memory(MB)";Expression={[math]::Round($_.WS/1MB,2)}}
```

### Sort-Object (Sorting)
```powershell
# Ascending order (default)
Get-Process | Sort-Object CPU

# Descending order
Get-Process | Sort-Object CPU -Descending

# Multiple fields
Get-Process | Sort-Object CPU,WS -Descending

# Unique values
Get-Process | Select-Object ProcessName | Sort-Object ProcessName -Unique
```

### Measure-Object (Statistics)
```powershell
# Aggregation
Get-ChildItem | Measure-Object -Property Length -Sum
Get-Process | Measure-Object -Property WS -Average -Maximum -Minimum

# Line count
Get-Content C:\file.txt | Measure-Object -Line -Word -Character
```

### ForEach-Object (Iteration)
```powershell
# Execute action on each item
Get-Service | ForEach-Object { $_.Name.ToUpper() }

# Shorthand: %
Get-Process | % { $_.Name }

# Property output
Get-Process | ForEach-Object -MemberName Name
```

---

## Variables and Data Types

### Variables
```powershell
# Create variable
$name = "John"
$age = 30
$isActive = $true

# Output variable
Write-Host $name
$name

# Get type
$age.GetType()

# Environment variables
$env:COMPUTERNAME
$env:USERNAME
$env:PATH
```

### Data Types
```powershell
# String
$text = "Hello World"
$multiline = @"
Line 1
Line 2
Line 3
"@

# Integer
$number = 42

# Double
$decimal = 3.14

# Boolean
$isTrue = $true
$isFalse = $false

# Array
$colors = @("Red", "Green", "Blue")
$colors[0] # Red
$colors.Count

# Hash Table (Dictionary)
$person = @{
Name = "John"
Age = 30
City = "Budapest"
}
$person.Name
$person["Age"]
```

### String Operations
```powershell
# Concatenation
$firstName = "John"
$lastName = "Doe"
$fullName = $firstName + " " + $lastName
$fullName = "$firstName $lastName"

# String interpolation
$message = "Hello, $firstName!"

# String length
$text = "PowerShell"
$text.Length

# Substring
$text.Substring(0, 5) # Power

# Replace
$text.Replace("Shell", "Core") # PowerCore

# Split
$csv = "Apple,Banana,Orange"
$fruits = $csv.Split(",")

# ToUpper / ToLower
$text.ToUpper()
$text.ToLower()

# Contains
$text.Contains("Shell") # True
```

### Array Operations
```powershell
# Create array
$numbers = @(1, 2, 3, 4, 5)

# Add element
$numbers += 6

# Access element
$numbers[0] # 1
$numbers[-1] # last element

# Array length
$numbers.Count

# Iteration
foreach ($num in $numbers) {
Write-Host $num
}

# Contains value
$numbers -contains 3 # True

# Join
$numbers -join ", " # "1, 2, 3, 4, 5"
```

---

## Conditional Logic

### If-ElseIf-Else
```powershell
# Simple if
if ($age -gt 18) {
Write-Host "Adult"
}

# If-Else
if ($age -ge 18) {
Write-Host "Adult"
} else {
Write-Host "Child"
}

# If-ElseIf-Else
if ($age -lt 13) {
Write-Host "Child"
} elseif ($age -lt 18) {
Write-Host "Teenager"
} else {
Write-Host "Adult"
}

# Logical operators
if ($age -ge 18 -and $isActive -eq $true) {
Write-Host "Active adult"
}

if ($status -eq "Running" -or $status -eq "Starting") {
Write-Host "Service running or starting"
}
```

### Switch
```powershell
# Basic syntax
$day = "Monday"
switch ($day) {
"Monday"  { "Monday" }
"Tuesday"  { "Tuesday" }
"Wednesday" { "Wednesday" }
Default   { "Unknown day" }
}

# Numbers
$number = 2
switch ($number) {
1 { "One" }
2 { "Two" }
3 { "Three" }
Default { "Other" }
}

# Regex matching
$text = "PowerShell"
switch -Regex ($text) {
"^Power" { "Starts with Power" }
"Shell$" { "Ends with Shell" }
}

# Wildcard matching
switch -Wildcard ($text) {
"Power*" { "Starts with Power" }
"*Shell" { "Ends with Shell" }
}
```

---

## Loops

### For Loop
```powershell
# Basic syntax
for ($i = 0; $i -lt 10; $i++) {
Write-Host "Iteration: $i"
}

# Descending
for ($i = 10; $i -gt 0; $i--) {
Write-Host $i
}
```

### ForEach Loop
```powershell
# Iterate array
$colors = @("Red", "Green", "Blue")
foreach ($color in $colors) {
Write-Host $color
}

# Iterate files
$files = Get-ChildItem C:\Temp -Filter *.txt
foreach ($file in $files) {
Write-Host $file.Name
}

# Iterate hash table
$person = @{Name="John"; Age=30; City="Budapest"}
foreach ($key in $person.Keys) {
Write-Host "$key : $($person[$key])"
}
```

### While Loop
```powershell
# Basic syntax
$count = 0
while ($count -lt 5) {
Write-Host "Count: $count"
$count++
}

# User input until valid
$input = ""
while ($input -ne "quit") {
$input = Read-Host "Enter command (or 'quit' to exit)"
Write-Host "You entered: $input"
}
```

### Do-While Loop
```powershell
# Execute at least once
$count = 0
do {
Write-Host "Count: $count"
$count++
} while ($count -lt 5)
```

### Do-Until Loop
```powershell
# Run until condition is met
$count = 0
do {
Write-Host "Count: $count"
$count++
} until ($count -ge 5)
```

### Break and Continue
```powershell
# Break - exit loop
for ($i = 0; $i -lt 10; $i++) {
if ($i -eq 5) { break }
Write-Host $i
}

# Continue - skip to next iteration
for ($i = 0; $i -lt 10; $i++) {
if ($i % 2 -eq 0) { continue }
Write-Host $i # Only odd numbers
}
```

---

## Functions

### Basic Function
```powershell
# Simple function
function Say-Hello {
Write-Host "Hello, World!"
}
Say-Hello

# With parameters
function Say-Greeting {
param(
[string]$Name
)
Write-Host "Hello, $Name!"
}
Say-Greeting -Name "John"

# Return value
function Get-Square {
param([int]$Number)
return $Number * $Number
}
$result = Get-Square -Number 5
```

### Advanced Function
```powershell
function Get-DiskSpace {
[CmdletBinding()]
param(
[Parameter(Mandatory=$true)]
[string[]]$ComputerName,

[Parameter(Mandatory=$false)]
[int]$ThresholdGB = 10
)

foreach ($Computer in $ComputerName) {
try {
$disks = Get-CimInstance -ClassName Win32_LogicalDisk -ComputerName $Computer -Filter "DriveType=3" -ErrorAction Stop

foreach ($disk in $disks) {
$freeGB = [math]::Round($disk.FreeSpace/1GB, 2)
$totalGB = [math]::Round($disk.Size/1GB, 2)
$percentFree = [math]::Round(($disk.FreeSpace/$disk.Size)*100, 2)

[PSCustomObject]@{
ComputerName = $Computer
Drive = $disk.DeviceID
TotalGB = $totalGB
FreeGB = $freeGB
PercentFree = $percentFree
Warning = if ($freeGB -lt $ThresholdGB) { "Low Space" } else { "OK" }
}
}
}
catch {
Write-Warning "Failed to query $Computer : $_"
}
}
}

# Usage
Get-DiskSpace -ComputerName "SRV01","SRV02" -ThresholdGB 20
```

### Parameter Validation
```powershell
function Set-ServiceState {
param(
[Parameter(Mandatory=$true)]
[ValidateSet("Running", "Stopped", "Paused")]
[string]$DesiredState,

[Parameter(Mandatory=$true)]
[ValidateNotNullOrEmpty()]
[string]$ServiceName,

[ValidateRange(1,999)]
[int]$TimeoutSeconds = 30
)

# Implementation...
}
```

---

## File Operations

### File and Folder Operations
```powershell
# Create file
New-Item -Path "C:\Temp\test.txt" -ItemType File
"Hello" | Out-File "C:\Temp\test.txt"

# Create folder
New-Item -Path "C:\Temp\NewFolder" -ItemType Directory

# Copy file
Copy-Item -Path "C:\Temp\test.txt" -Destination "C:\Backup\test.txt"

# Copy folder recursively
Copy-Item -Path "C:\Temp\MyFolder" -Destination "C:\Backup\MyFolder" -Recurse

# Move file
Move-Item -Path "C:\Temp\test.txt" -Destination "C:\Archive\test.txt"

# Rename file
Rename-Item -Path "C:\Temp\test.txt" -NewName "test_old.txt"

# Delete file
Remove-Item -Path "C:\Temp\test.txt"

# Delete folder recursively
Remove-Item -Path "C:\Temp\OldFolder" -Recurse -Force

# Test file existence
Test-Path "C:\Temp\test.txt"
```

### File Content Read/Write
```powershell
# Read entire file
$content = Get-Content "C:\Temp\test.txt"

# Read line by line
Get-Content "C:\Temp\test.txt" | ForEach-Object {
Write-Host $_
}

# First/Last N lines
Get-Content "C:\Temp\test.txt" -First 10
Get-Content "C:\Temp\test.txt" -Last 5

# Real-time file monitoring (like tail -f)
Get-Content "C:\Logs\app.log" -Wait -Tail 10

# Write to file (overwrite)
"New content" | Out-File "C:\Temp\test.txt"
Set-Content "C:\Temp\test.txt" "New content"

# Append
"Additional line" | Add-Content "C:\Temp\test.txt"
"More data" | Out-File "C:\Temp\test.txt" -Append

# Read CSV
$data = Import-Csv "C:\Data\users.csv"
$data | ForEach-Object {
Write-Host "$($_.Name) - $($_.Email)"
}

# Write CSV
$users = @(
[PSCustomObject]@{Name="John"; Email="john@example.com"}
[PSCustomObject]@{Name="Jane"; Email="jane@example.com"}
)
$users | Export-Csv "C:\Data\users.csv" -NoTypeInformation

# Read JSON
$json = Get-Content "C:\Data\config.json" | ConvertFrom-Json

# Write JSON
$config = @{Server="SRV01"; Port=8080}
$config | ConvertTo-Json | Out-File "C:\Data\config.json"
```

### File Search
```powershell
# Search files by extension
Get-ChildItem -Path C:\ -Filter *.log -Recurse -ErrorAction SilentlyContinue

# Search files by size (>100MB)
Get-ChildItem -Path C:\Temp -Recurse | Where-Object {$_.Length -gt 100MB}

# Search by modification date (last 7 days)
$date = (Get-Date).AddDays(-7)
Get-ChildItem -Path C:\Temp -Recurse | Where-Object {$_.LastWriteTime -gt $date}

# Regex pattern in content
Get-ChildItem -Path C:\Logs -Filter *.log | Select-String -Pattern "ERROR"
```

---

## Registry Operations

### Registry Read
```powershell
# Get registry key
Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion"

# Specific value
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion").ProgramFilesDir

# Navigate registry (like cd)
Set-Location HKLM:\SOFTWARE
Get-ChildItem
```

### Registry Modify
```powershell
# Create registry key
New-Item -Path "HKLM:\SOFTWARE\MyCompany"

# Create registry value
New-ItemProperty -Path "HKLM:\SOFTWARE\MyCompany" -Name "AppVersion" -Value "1.0" -PropertyType String

# Modify registry value
Set-ItemProperty -Path "HKLM:\SOFTWARE\MyCompany" -Name "AppVersion" -Value "2.0"

# Delete registry value
Remove-ItemProperty -Path "HKLM:\SOFTWARE\MyCompany" -Name "AppVersion"

# Delete registry key
Remove-Item -Path "HKLM:\SOFTWARE\MyCompany" -Recurse
```

### Registry Backup/Restore
```powershell
# Export registry
reg export "HKLM\SOFTWARE\MyCompany" "C:\Backup\mycompany.reg"

# Import registry
reg import "C:\Backup\mycompany.reg"
```

---

## Networking

### Network Information
```powershell
# IP configuration
Get-NetIPConfiguration
Get-NetIPAddress

# Network adapters
Get-NetAdapter

# MAC address
Get-NetAdapter | Select-Object Name, MacAddress

# Routing table
Get-NetRoute

# DNS settings
Get-DnsClientServerAddress

# ARP cache
Get-NetNeighbor
```

### Network Testing
```powershell
# Ping
Test-Connection -ComputerName google.com -Count 4

# Ping multiple computers
Test-Connection -ComputerName "SRV01","SRV02","SRV03" -Count 2

# Port availability
Test-NetConnection -ComputerName server.contoso.com -Port 443

# Traceroute
Test-NetConnection -ComputerName google.com -TraceRoute

# DNS resolution
Resolve-DnsName google.com
Resolve-DnsName google.com -Type MX

# HTTP/HTTPS request
Invoke-WebRequest -Uri "https://google.com"
(Invoke-WebRequest -Uri "https://api.example.com/data").Content | ConvertFrom-Json
```

### Network Configuration
```powershell
# Set IP address
New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.1.10 -PrefixLength 24 -DefaultGateway 192.168.1.1

# Set DNS server
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 8.8.8.8,8.8.4.4

# Enable DHCP
Set-NetIPInterface -InterfaceAlias "Ethernet" -Dhcp Enabled

# Flush DNS cache
Clear-DnsClientCache
```

---

## Active Directory

### User Management
```powershell
# Import module
Import-Module ActiveDirectory

# Get user
Get-ADUser -Identity jdoe -Properties *

# Search user
Get-ADUser -Filter "Name -like '*John*'" | Select-Object Name, SamAccountName

# Create user
New-ADUser -Name "John Doe" -GivenName "John" -Surname "Doe" -SamAccountName "jdoe" -UserPrincipalName "jdoe@contoso.com" -Path "OU=Users,DC=contoso,DC=com" -AccountPassword (Read-Host -AsSecureString "Password") -Enabled $true

# Bulk user creation from CSV
Import-Csv "C:\users.csv" | ForEach-Object {
New-ADUser -Name $_.Name -GivenName $_.GivenName -Surname $_.Surname -SamAccountName $_.SamAccountName -UserPrincipalName "$($_.SamAccountName)@contoso.com" -Path $_.Path -AccountPassword (ConvertTo-SecureString $_.Password -AsPlainText -Force) -Enabled $true
}

# Modify user
Set-ADUser -Identity jdoe -Department "IT" -Title "System Administrator"

# Disable/enable user
Disable-ADAccount -Identity jdoe
Enable-ADAccount -Identity jdoe

# Reset password
Set-ADAccountPassword -Identity jdoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "NewP@ssw0rd!" -Force)

# Unlock user
Unlock-ADAccount -Identity jdoe

# Delete user
Remove-ADUser -Identity jdoe -Confirm:$false
```

### Group Management
```powershell
# Create group
New-ADGroup -Name "IT Admins" -GroupScope Global -GroupCategory Security -Path "OU=Groups,DC=contoso,DC=com"

# List group members
Get-ADGroupMember -Identity "IT Admins" | Select-Object Name, SamAccountName

# Add user to group
Add-ADGroupMember -Identity "IT Admins" -Members jdoe,jsmith

# Remove user from group
Remove-ADGroupMember -Identity "IT Admins" -Members jdoe -Confirm:$false

# User group membership
Get-ADPrincipalGroupMembership -Identity jdoe | Select-Object Name
```

### Computer Management
```powershell
# Get computer
Get-ADComputer -Identity WS01 -Properties *

# Computers by OS
Get-ADComputer -Filter "OperatingSystem -like '*Windows 11*'" | Select-Object Name, OperatingSystem

# Disable computer
Disable-ADAccount -Identity WS01

# Delete computer
Remove-ADComputer -Identity WS01 -Confirm:$false
```

---

## Remote Management

### PowerShell Remoting
```powershell
# Enable WinRM
Enable-PSRemoting -Force

# Start remote session
Enter-PSSession -ComputerName SRV01
Enter-PSSession -ComputerName SRV01 -Credential (Get-Credential)

# Execute remote command (one server)
Invoke-Command -ComputerName SRV01 -ScriptBlock { Get-Service }

# Execute remote command (multiple servers)
Invoke-Command -ComputerName SRV01,SRV02,SRV03 -ScriptBlock {
Get-Service | Where-Object {$_.Status -eq "Running"}
}

# Execute remote script
Invoke-Command -ComputerName SRV01 -FilePath "C:\Scripts\Update.ps1"

# Persistent session
$session = New-PSSession -ComputerName SRV01
Invoke-Command -Session $session -ScriptBlock { Get-Process }
Remove-PSSession -Session $session

# Exit remote session
Exit-PSSession
```

### Copy Files to Remote
```powershell
# Session-based file copy
$session = New-PSSession -ComputerName SRV01
Copy-Item -Path "C:\Scripts\script.ps1" -Destination "C:\Temp\" -ToSession $session
Remove-PSSession -Session $session

# Copy file from remote
Copy-Item -Path "C:\Logs\app.log" -Destination "C:\LocalLogs\" -FromSession $session
```

---

## Error Handling

### Try-Catch-Finally
```powershell
# Basic syntax
try {
Get-Content "C:\nonexistent.txt" -ErrorAction Stop
Write-Host "File read successfully"
}
catch {
Write-Host "Error occurred: $_"
}

# Finally block (always executes)
try {
$file = Get-Content "C:\file.txt" -ErrorAction Stop
}
catch {
Write-Warning "Failed to read file: $_"
}
finally {
Write-Host "Cleanup completed"
}

# Multiple catch blocks
try {
Get-Item "C:\nonexistent.txt" -ErrorAction Stop
}
catch [System.IO.FileNotFoundException] {
Write-Host "File not found"
}
catch {
Write-Host "Other error: $_"
}
```

### Error Action Preference
```powershell
# SilentlyContinue - suppress error, continue
Get-Content "C:\nonexistent.txt" -ErrorAction SilentlyContinue

# Stop - throw exception, try-catch-able
Get-Content "C:\nonexistent.txt" -ErrorAction Stop

# Inquire - prompt for action
Get-Content "C:\nonexistent.txt" -ErrorAction Inquire

# Continue - display warning, continue (default)
Get-Content "C:\nonexistent.txt" -ErrorAction Continue

# Global ErrorActionPreference
$ErrorActionPreference = "Stop"
```

### Error Logging
```powershell
# Error variable
Get-Content "C:\nonexistent.txt" -ErrorAction SilentlyContinue -ErrorVariable myError
$myError

# $Error automatic variable (contains all errors)
$Error[0] # Most recent error
$Error.Clear() # Clear
```

---

## Modules and Snap-ins

### Modules
```powershell
# Available modules
Get-Module -ListAvailable

# Import module
Import-Module ActiveDirectory
Import-Module DnsServer

# Module commands
Get-Command -Module ActiveDirectory

# Module information
Get-Module ActiveDirectory

# Remove module
Remove-Module ActiveDirectory

# Install module from PowerShell Gallery
Install-Module -Name Az -Scope CurrentUser

# Update module
Update-Module -Name Az
```

### Custom Module
```powershell
# MyModule.psm1
function Get-Greeting {
param([string]$Name)
return "Hello, $Name!"
}

Export-ModuleMember -Function Get-Greeting

# Import
Import-Module "C:\Modules\MyModule.psm1"
Get-Greeting -Name "John"
```

---

## Useful One-Liners

### System Administration
```powershell
# All running services
Get-Service | Where-Object {$_.Status -eq "Running"} | Select-Object Name, DisplayName

# Automatic but not running services
Get-Service | Where-Object {$_.Status -eq 'Stopped' -and $_.StartType -eq 'Automatic'}

# Top 10 CPU consuming processes
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10 Name, CPU, WS

# Disk space check (<10% free)
Get-PSDrive | Where-Object {$_.Provider -like "*FileSystem*" -and $_.Free -lt ($_.Used + $_.Free)*0.1}

# Installed applications
Get-WmiObject -Class Win32_Product | Select-Object Name, Version | Sort-Object Name

# Last reboot
(Get-CimInstance Win32_OperatingSystem).LastBootUpTime

# Uptime
(Get-Date) - (Get-CimInstance Win32_OperatingSystem).LastBootUpTime
```

### Active Directory
```powershell
# All enabled users
Get-ADUser -Filter {Enabled -eq $true} | Select-Object Name, SamAccountName

# Locked out users
Search-ADAccount -LockedOut | Select-Object Name, SamAccountName

# Inactive users (90+ days)
$date = (Get-Date).AddDays(-90)
Get-ADUser -Filter {LastLogonDate -lt $date} -Properties LastLogonDate | Select-Object Name, LastLogonDate

# Password never expires users
Get-ADUser -Filter {PasswordNeverExpires -eq $true} | Select-Object Name, SamAccountName

# Empty groups
Get-ADGroup -Filter * | Where-Object {-not (Get-ADGroupMember -Identity $_)} | Select-Object Name

# Users in multiple groups
Get-ADUser -Filter * -Properties MemberOf | Select-Object Name, @{Name="GroupCount";Expression={$_.MemberOf.Count}} | Sort-Object GroupCount -Descending
```

### File System
```powershell
# Largest files (Top 10)
Get-ChildItem -Path C:\ -Recurse -File -ErrorAction SilentlyContinue | Sort-Object Length -Descending | Select-Object -First 10 FullName, @{Name="SizeMB";Expression={[math]::Round($_.Length/1MB,2)}}

# Files modified in last 24 hours
Get-ChildItem -Path C:\Temp -Recurse | Where-Object {$_.LastWriteTime -gt (Get-Date).AddDays(-1)}

# Duplicate files (by name)
Get-ChildItem -Path C:\Temp -Recurse | Group-Object Name | Where-Object {$_.Count -gt 1} | Select-Object Name, Count

# Delete old log files (30+ days)
Get-ChildItem -Path C:\Logs -Filter *.log | Where-Object {$_.LastWriteTime -lt (Get-Date).AddDays(-30)} | Remove-Item -Force
```

### Networking
```powershell
# Get public IP
(Invoke-WebRequest -Uri "https://ifconfig.me/ip").Content.Trim()

# Open ports (listening)
Get-NetTCPConnection -State Listen | Select-Object LocalAddress, LocalPort, OwningProcess | Sort-Object LocalPort

# ARP table
Get-NetNeighbor | Where-Object {$_.State -eq "Reachable"} | Select-Object IPAddress, LinkLayerAddress

# DNS cache
Get-DnsClientCache | Select-Object Entry, Data
```

### Event Logs
```powershell
# Failed logon attempts (last 24h)
Get-WinEvent -FilterHashtable @{LogName='Security'; ID=4625; StartTime=(Get-Date).AddDays(-1)} | Select-Object TimeCreated, Message

# System errors (last 7 days)
Get-WinEvent -FilterHashtable @{LogName='System'; Level=2; StartTime=(Get-Date).AddDays(-7)} | Select-Object TimeCreated, Message

# Application crashes
Get-WinEvent -FilterHashtable @{LogName='Application'; Level=2} -MaxEvents 50 | Select-Object TimeCreated, ProviderName, Message
```

---

## 🔥 Pro Tips

### Execution Policy
```powershell
# Check current policy
Get-ExecutionPolicy

# Set policy (CurrentUser scope)
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

# Bypass policy for single script
PowerShell.exe -ExecutionPolicy Bypass -File "C:\Scripts\script.ps1"
```

### Transcription (Session Logging)
```powershell
# Start transcript
Start-Transcript -Path "C:\Logs\session.log"

# Commands executed...

# Stop transcript
Stop-Transcript
```

### Profile Customization
```powershell
# Check if profile exists
Test-Path $PROFILE

# Create profile
New-Item -Path $PROFILE -ItemType File -Force

# Edit profile
notepad $PROFILE

# Example profile content:
# Set-Location C:\Work
# Import-Module ActiveDirectory
# function prompt { "PS [$env:USERNAME@$env:COMPUTERNAME] $pwd> " }
```

### Aliases
```powershell
# Create temporary alias
Set-Alias -Name ll -Value Get-ChildItem

# Create persistent alias (add to profile)
Set-Alias -Name np -Value notepad

# Remove alias
Remove-Alias -Name ll
```

### History
```powershell
# View command history
Get-History

# Invoke previous command
Invoke-History -Id 5

# Clear history
Clear-History
```

---

**Last Updated:** 2026-05-17
