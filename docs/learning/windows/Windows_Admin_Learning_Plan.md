# Windows Administration Learning Plan

**Created:** 2026-05-17 
**Target Audience:** IT Administrators, DevOps Engineers, Infrastructure Specialists 
**Level:** Beginner to Advanced 
**Duration:** 8-12 weeks (10-15 hours per week)

## Introduction

This learning plan provides a comprehensive guide to Microsoft Windows Server and Desktop environment administration. The plan covers everything from basic system management tasks to enterprise-level solutions, with special emphasis on modern hybrid cloud environments.

---

## Learning Objectives

By the end of this learning plan, you will be able to:

1. **Install and configure Windows Server environments**
2. **Design and operate Active Directory Domain Services**
3. **Implement Group Policy-based configuration management**
4. **Operate network services (DNS, DHCP)**
5. **Design and implement storage solutions**
6. **Manage security policies and updates**
7. **Write PowerShell scripts and automation**
8. **Troubleshoot and optimize performance**
9. **Manage Hyper-V virtualization**
10. **Utilize modern Windows Server features (Containers, Azure Arc)**

---

## Module Overview

### Module 1: Windows Server Basics (1 Week)

**Topics:**
- Windows Server editions comparison (Standard, Datacenter, Essentials)
- Server Core vs. Desktop Experience installations
- Server Manager and role installation
- Windows Admin Center basics
- Initial configuration tasks (Server Configuration)
- Licensing and activation

**Practical Exercises:**
1. Install Windows Server 2022 (Desktop Experience and Server Core)
2. Install Server Manager roles and features
3. Configure Windows Admin Center for remote management
4. Complete Initial Configuration Tasks (ICT)
5. Verify licensing and activation

**Useful PowerShell Commands:**
```powershell
# Get server information
Get-ComputerInfo | Select-Object WindowsProductName, WindowsVersion, OsHardwareAbstractionLayer

# List installed roles and features
Get-WindowsFeature | Where-Object {$_.Installed -eq $true}

# Rename computer
Rename-Computer -NewName "SRV01" -Restart

# Join domain
Add-Computer -DomainName "contoso.com" -Credential (Get-Credential) -Restart

# Convert Server Core to Desktop Experience (not supported reverse)
Install-WindowsFeature Server-Gui-Mgmt-Infra,Server-Gui-Shell -Restart
```

**Resources:**
- [Microsoft Learn: Windows Server Documentation](https://learn.microsoft.com/en-us/windows-server/)
- [Windows Admin Center Overview](https://learn.microsoft.com/en-us/windows-server/manage/windows-admin-center/overview)

---

### Module 2: Active Directory Domain Services (2 Weeks)

**Topics:**
- Active Directory concepts (Forest, Domain, OU, Sites)
- Domain Controller installation and configuration
- User, group, and computer management
- Organizational Units (OU) design
- FSMO roles (Flexible Single Master Operations)
- Trust relationships
- Active Directory Sites and Services
- Global Catalog and Replication
- Read-Only Domain Controllers (RODC)
- Active Directory Recycle Bin

**Practical Exercises:**
1. Create new Active Directory Forest
2. Add additional Domain Controller (redundancy)
3. Design OU structure (Users, Computers, Groups)
4. Bulk create users and groups from CSV
5. Transfer FSMO roles
6. Enable AD Recycle Bin
7. Configure Sites and Services for multi-site environment

**Useful PowerShell Commands:**
```powershell
# Install AD Domain Services
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools

# Create first DC (new forest)
Install-ADDSForest -DomainName "contoso.com" -DomainNetbiosName "CONTOSO" -ForestMode "WinThreshold" -DomainMode "WinThreshold" -InstallDns

# Create user
New-ADUser -Name "John Doe" -GivenName "John" -Surname "Doe" -SamAccountName "jdoe" -UserPrincipalName "jdoe@contoso.com" -Path "OU=Users,DC=contoso,DC=com" -AccountPassword (ConvertTo-SecureString "P@ssw0rd!" -AsPlainText -Force) -Enabled $true

# Bulk user creation from CSV
Import-Csv "C:\users.csv" | ForEach-Object {
New-ADUser -Name $_.Name -GivenName $_.GivenName -Surname $_.Surname -SamAccountName $_.SamAccountName -UserPrincipalName "$($_.SamAccountName)@contoso.com" -Path $_.Path -AccountPassword (ConvertTo-SecureString $_.Password -AsPlainText -Force) -Enabled $true
}

# Create OU
New-ADOrganizationalUnit -Name "IT Department" -Path "DC=contoso,DC=com"

# Get FSMO roles
Get-ADDomain | Select-Object PDCEmulator, RIDMaster, InfrastructureMaster
Get-ADForest | Select-Object SchemaMaster, DomainNamingMaster

# Transfer FSMO role
Move-ADDirectoryServerOperationMasterRole -Identity "DC02" -OperationMasterRole PDCEmulator,RIDMaster

# Enable AD Recycle Bin
Enable-ADOptionalFeature -Identity "Recycle Bin Feature" -Scope ForestOrConfigurationSet -Target "contoso.com"

# Restore deleted objects
Get-ADObject -Filter {IsDeleted -eq $true -and ObjectClass -eq "user"} -IncludeDeletedObjects | Restore-ADObject
```

**Resources:**
- [Microsoft Learn: Active Directory Domain Services Overview](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)
- [FSMO Roles Explained](https://learn.microsoft.com/en-us/troubleshoot/windows-server/identity/fsmo-roles)

---

### Module 3: Group Policy Management (1.5 Weeks)

**Topics:**
- Group Policy principles (GPO, OU links, inheritance)
- Group Policy Management Console (GPMC)
- Policy settings: Computer Configuration vs. User Configuration
- Administrative Templates
- Security Settings
- Scripts (Startup, Shutdown, Logon, Logoff)
- Preferences (Registry, Files, Shortcuts)
- WMI Filters and Item-Level Targeting
- GPO troubleshooting (gpresult, rsop.msc)
- Starter GPOs
- Central Store

**Practical Exercises:**
1. Create GPO and link to OU
2. Configure password policy (Password Policy)
3. Deploy desktop wallpaper centrally
4. Configure drive mapping with Group Policy Preferences
5. Software installation via GPO
6. Run logon script
7. Apply WMI Filter (e.g., laptops only)
8. GPO Backup and Restore
9. Create Group Policy Central Store (ADMX templates)

**Useful PowerShell Commands:**
```powershell
# Import Group Policy module
Import-Module GroupPolicy

# Create GPO
New-GPO -Name "Desktop Wallpaper Policy" -Comment "Sets company wallpaper"

# Link GPO to OU
New-GPLink -Name "Desktop Wallpaper Policy" -Target "OU=Workstations,DC=contoso,DC=com"

# Get GPO settings
Get-GPO -Name "Desktop Wallpaper Policy" | Get-GPOReport -ReportType Html -Path "C:\gpo-report.html"

# List all GPOs
Get-GPO -All | Select-Object DisplayName, GpoStatus, CreationTime

# Backup GPO
Backup-GPO -Name "Desktop Wallpaper Policy" -Path "C:\GPOBackups"

# Restore GPO
Restore-GPO -Name "Desktop Wallpaper Policy" -Path "C:\GPOBackups"

# Run GPResult (local machine)
gpresult /H C:\gpresult.html

# Remote GPResult
Invoke-GPUpdate -Computer "WS01" -Force
```

**Resources:**
- [Microsoft Learn: Group Policy Overview](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831791(v=ws.11))
- [Group Policy Central Store](https://learn.microsoft.com/en-us/troubleshoot/windows-client/group-policy/create-and-manage-central-store)

---

### Module 4: Networking Services (1.5 Weeks)

**Topics:**
- DNS Server role installation and configuration
- DNS zones (Primary, Secondary, Stub, Reverse Lookup)
- DNS Records (A, AAAA, CNAME, MX, SRV, PTR)
- DNS Forwarders and Conditional Forwarders
- DNS Scavenging
- DHCP Server role installation
- DHCP Scopes, Reservations, Exclusions
- DHCP Failover and Load Balancing
- IPAM (IP Address Management)
- TCP/IP configuration and troubleshooting

**Practical Exercises:**
1. Install and configure DNS Server
2. Create Forward Lookup Zone (contoso.com)
3. Create Reverse Lookup Zone (192.168.1.0/24)
4. Add DNS Records (A, CNAME, MX)
5. Install DHCP Server
6. Create DHCP Scope (192.168.1.10-192.168.1.200)
7. Configure DHCP Reservations (fixed IPs for servers)
8. Configure DHCP Failover between two DHCP servers

**Useful PowerShell Commands:**
```powershell
# Install DNS Server
Install-WindowsFeature -Name DNS -IncludeManagementTools

# Create DNS Forward Lookup Zone
Add-DnsServerPrimaryZone -Name "contoso.com" -ReplicationScope "Forest"

# Add DNS A Record
Add-DnsServerResourceRecordA -Name "www" -ZoneName "contoso.com" -IPv4Address "192.168.1.10"

# Add DNS CNAME Record
Add-DnsServerResourceRecordCName -Name "intranet" -HostNameAlias "www.contoso.com" -ZoneName "contoso.com"

# Install DHCP Server
Install-WindowsFeature -Name DHCP -IncludeManagementTools

# Create DHCP Scope
Add-DhcpServerv4Scope -Name "LAN Scope" -StartRange 192.168.1.10 -EndRange 192.168.1.200 -SubnetMask 255.255.255.0 -LeaseDuration 8.00:00:00

# Set DHCP Options (Gateway, DNS)
Set-DhcpServerv4OptionValue -ScopeId 192.168.1.0 -Router 192.168.1.1 -DnsServer 192.168.1.5,192.168.1.6

# Add DHCP Reservation
Add-DhcpServerv4Reservation -ScopeId 192.168.1.0 -IPAddress 192.168.1.50 -ClientId "00-15-5D-02-33-04" -Description "File Server"

# Configure DHCP Failover
Add-DhcpServerv4Failover -Name "DHCP-Failover" -PartnerServer "DHCP02" -ScopeId 192.168.1.0 -LoadBalancePercent 50
```

**Resources:**
- [Microsoft Learn: DNS Server Role](https://learn.microsoft.com/en-us/windows-server/networking/dns/dns-top)
- [Microsoft Learn: DHCP Server Role](https://learn.microsoft.com/en-us/windows-server/networking/technologies/dhcp/dhcp-top)

---

### Module 5: Storage and File Services (1 Week)

**Topics:**
- Disk Management (Basic vs. Dynamic Disks)
- Storage Spaces and Storage Pools
- File Server role
- SMB Shares and NTFS Permissions
- DFS Namespaces and DFS Replication
- iSCSI Target and Initiator
- Storage Replica
- Deduplication
- File Server Resource Manager (FSRM) - Quotas, File Screening
- Shadow Copies (VSS)

**Practical Exercises:**
1. Create Storage Pool from multiple disks
2. Create Virtual Disk with Storage Spaces
3. Create File Share and configure NTFS permissions
4. Configure DFS Namespace
5. Configure DFS Replication between two servers
6. Install File Server Resource Manager and set quotas
7. Enable Shadow Copies (Previous Versions)

**Useful PowerShell Commands:**
```powershell
# List disks
Get-Disk

# Create new partition
New-Partition -DiskNumber 1 -UseMaximumSize -AssignDriveLetter

# Format
Format-Volume -DriveLetter D -FileSystem NTFS -NewFileSystemLabel "Data"

# Create Storage Pool
New-StoragePool -FriendlyName "MyStoragePool" -StorageSubSystemFriendlyName "Windows Storage*" -PhysicalDisks (Get-PhysicalDisk -CanPool $true)

# Create Virtual Disk (mirrored)
New-VirtualDisk -FriendlyName "MirroredDisk" -StoragePoolFriendlyName "MyStoragePool" -ResiliencySettingName Mirror -Size 500GB

# Create SMB Share
New-SmbShare -Name "SharedFolder" -Path "C:\Shares\SharedFolder" -FullAccess "CONTOSO\Domain Admins" -ChangeAccess "CONTOSO\Domain Users"

# Set NTFS permissions
$acl = Get-Acl "C:\Shares\SharedFolder"
$permission = "CONTOSO\Domain Users","Modify","Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule $permission
$acl.SetAccessRule($accessRule)
Set-Acl "C:\Shares\SharedFolder" $acl

# Create DFS Namespace
New-DfsnRoot -Path "\\contoso.com\Public" -TargetPath "\\FS01\Public" -Type DomainV2

# Enable Shadow Copies
vssadmin add shadowstorage /for=C: /on=D: /maxsize=20%
vssadmin create shadow /for=C:
```

**Resources:**
- [Microsoft Learn: Storage Spaces Direct](https://learn.microsoft.com/en-us/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Microsoft Learn: DFS Namespaces and Replication](https://learn.microsoft.com/en-us/windows-server/storage/dfs-namespaces/dfs-overview)

---

### Module 6: Security and Updates (1 Week)

**Topics:**
- Windows Defender and Antivirus
- Windows Firewall Advanced Security
- BitLocker Drive Encryption
- AppLocker and WDAC (Windows Defender Application Control)
- Security Baselines
- Windows Update and WSUS (Windows Server Update Services)
- Microsoft Update Catalog
- Security Compliance Toolkit
- Event Viewer and Audit Policies
- Microsoft Defender for Endpoint (overview)

**Practical Exercises:**
1. Create Windows Firewall inbound/outbound rules
2. Enable BitLocker on system drive
3. Configure AppLocker rules (signed apps only)
4. Install and configure WSUS server
5. Configure Group Policy Update Management
6. Enable Audit Policies (Failed Logon Attempts)
7. Create Event Viewer Custom Views

**Useful PowerShell Commands:**
```powershell
# Windows Defender status
Get-MpComputerStatus

# Create firewall rule
New-NetFirewallRule -DisplayName "Allow RDP" -Direction Inbound -Protocol TCP -LocalPort 3389 -Action Allow

# Enable BitLocker
Enable-BitLocker -MountPoint "C:" -EncryptionMethod XtsAes256 -UsedSpaceOnly -TpmProtector

# Backup BitLocker recovery key to AD
Backup-BitLockerKeyProtector -MountPoint "C:" -KeyProtectorId (Get-BitLockerVolume -MountPoint "C:").KeyProtector[1].KeyProtectorId

# Get AppLocker policy
Get-AppLockerPolicy -Effective -Xml | Out-File "C:\AppLockerPolicy.xml"

# Get Windows Update history
Get-HotFix | Sort-Object InstalledOn -Descending

# Install WSUS
Install-WindowsFeature -Name UpdateServices -IncludeManagementTools

# Get Audit Policy
auditpol /get /category:*

# Get failed logon events
Get-WinEvent -FilterHashtable @{LogName='Security'; ID=4625} -MaxEvents 50
```

**Resources:**
- [Microsoft Learn: Windows Security Baselines](https://learn.microsoft.com/en-us/windows/security/threat-protection/windows-security-configuration-framework/windows-security-baselines)
- [Microsoft Learn: WSUS Deployment Guide](https://learn.microsoft.com/en-us/windows-server/administration/windows-server-update-services/deploy/deploy-windows-server-update-services)

---

### Module 7: Hyper-V Virtualization (1 Week)

**Topics:**
- Hyper-V role installation
- Virtual Machine creation and management
- Virtual Switches (External, Internal, Private)
- Virtual Hard Disks (VHD, VHDX, Differencing Disks)
- Checkpoints (Snapshots)
- Live Migration and Storage Migration
- Hyper-V Replica
- Nested Virtualization
- VM Resource Controls (CPU, Memory, Network)
- Integration Services

**Practical Exercises:**
1. Install Hyper-V role
2. Create Virtual Switch
3. Create VM and install Windows
4. Use Differencing Disk (base image + delta)
5. Create and restore Checkpoint
6. Export and import VM
7. Configure Live Migration between two Hyper-V hosts
8. Configure Hyper-V Replica for DR purposes

**Useful PowerShell Commands:**
```powershell
# Install Hyper-V
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart

# Create Virtual Switch (External)
New-VMSwitch -Name "External Switch" -NetAdapterName "Ethernet" -AllowManagementOS $true

# Create VM
New-VM -Name "VM01" -MemoryStartupBytes 4GB -Generation 2 -NewVHDPath "C:\VMs\VM01\VM01.vhdx" -NewVHDSizeBytes 60GB -SwitchName "External Switch"

# Start VM
Start-VM -Name "VM01"

# Stop VM
Stop-VM -Name "VM01"

# Create Checkpoint
Checkpoint-VM -Name "VM01" -SnapshotName "Before Updates"

# Restore Checkpoint
Restore-VMCheckpoint -Name "Before Updates" -VMName "VM01" -Confirm:$false

# Export VM
Export-VM -Name "VM01" -Path "C:\VMExports"

# Import VM
Import-VM -Path "C:\VMExports\VM01\Virtual Machines\*.vmcx"

# Enable Live Migration
Enable-VMMigration
Set-VMHost -VirtualMachineMigrationAuthenticationType Kerberos
Set-VMHost -UseAnyNetworkForMigration $true

# Migrate VM to another host
Move-VM -Name "VM01" -DestinationHost "HV02"
```

**Resources:**
- [Microsoft Learn: Hyper-V Overview](https://learn.microsoft.com/en-us/windows-server/virtualization/hyper-v/hyper-v-on-windows-server)
- [Microsoft Learn: Live Migration Overview](https://learn.microsoft.com/en-us/windows-server/virtualization/hyper-v/manage/live-migration-overview)

---

### Module 8: PowerShell Automation (1.5 Weeks)

**Topics:**
- PowerShell Core vs. Windows PowerShell
- Cmdlets, Aliases, Functions
- Pipeline and Filtering
- Variables and Data Types
- Conditional Logic (If, Switch)
- Loops (ForEach, While, Do-While)
- Functions and Modules
- Error Handling (Try/Catch/Finally)
- Remoting (Enter-PSSession, Invoke-Command)
- Scheduled Tasks
- Desired State Configuration (DSC) basics

**Practical Exercises:**
1. Write script for AD user creation from CSV
2. Write function for disk space monitoring
3. Execute remote commands on multiple servers
4. Create Scheduled Task for daily backup script
5. Write DSC configuration for IIS installation
6. Create Module from custom functions
7. Implement error handling in script

**Useful patterns and examples:**
```powershell
# Function for disk space check
function Get-DiskSpace {
param(
[string[]]$ComputerName = $env:COMPUTERNAME
)

foreach ($Computer in $ComputerName) {
try {
Get-CimInstance -ClassName Win32_LogicalDisk -ComputerName $Computer -Filter "DriveType=3" -ErrorAction Stop |
Select-Object @{Name='ComputerName';Expression={$Computer}}, DeviceID, 
@{Name='Size(GB)';Expression={[math]::Round($_.Size/1GB,2)}},
@{Name='FreeSpace(GB)';Expression={[math]::Round($_.FreeSpace/1GB,2)}},
@{Name='%Free';Expression={[math]::Round(($_.FreeSpace/$_.Size)*100,2)}}
}
catch {
Write-Warning "Failed to query $Computer : $_"
}
}
}

# Remote command on multiple servers
$servers = "SRV01", "SRV02", "SRV03"
Invoke-Command -ComputerName $servers -ScriptBlock {
Get-Service | Where-Object {$_.Status -eq 'Running'} | Select-Object Name, DisplayName
}

# Create Scheduled Task
$action = New-ScheduledTaskAction -Execute "PowerShell.exe" -Argument "-File C:\Scripts\Backup.ps1"
$trigger = New-ScheduledTaskTrigger -Daily -At 2AM
Register-ScheduledTask -TaskName "Daily Backup" -Action $action -Trigger $trigger -User "SYSTEM"

# DSC configuration (IIS installation)
Configuration WebServer {
Node "WEB01" {
WindowsFeature IIS {
Ensure = "Present"
Name = "Web-Server"
}

WindowsFeature ASP {
Ensure = "Present"
Name = "Web-Asp-Net45"
}
}
}

WebServer -OutputPath "C:\DSC"
Start-DscConfiguration -Path "C:\DSC" -Wait -Verbose
```

**Resources:**
- [Microsoft Learn: PowerShell Documentation](https://learn.microsoft.com/en-us/powershell/)
- [PowerShell Gallery](https://www.powershellgallery.com/)

---

### Module 9: Troubleshooting and Performance (1 Week)

**Topics:**
- Event Viewer and Event Log Analysis
- Performance Monitor (Perfmon) and Data Collector Sets
- Resource Monitor
- Task Manager Advanced Features
- Network Troubleshooting (ping, tracert, nslookup, Test-NetConnection)
- Windows Admin Center Troubleshooting tools
- Sysinternals Suite (Process Explorer, Process Monitor, Autoruns)
- Best Practices Analyzer
- System File Checker (SFC) and DISM
- Memory Dump Analysis (WinDbg basics)

**Practical Exercises:**
1. Create performance baseline with Data Collector Set
2. Identify and diagnose high CPU process
3. Detect memory leak with Process Monitor
4. Network connectivity troubleshooting with Test-NetConnection
5. Run System File Checker to fix corrupted files
6. Run Best Practices Analyzer for AD DS
7. Analyze boot performance (xbootmgr)

**Useful PowerShell Commands:**
```powershell
# Query event log
Get-EventLog -LogName System -Newest 100 -EntryType Error
Get-WinEvent -FilterHashtable @{LogName='System'; Level=2; StartTime=(Get-Date).AddDays(-7)}

# Get Performance Counter
Get-Counter '\Processor(_Total)\% Processor Time' -SampleInterval 1 -MaxSamples 10

# Top CPU consuming processes
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10 Name, CPU, WS

# Network connectivity test
Test-NetConnection -ComputerName "google.com" -Port 443 -InformationLevel Detailed

# DNS resolution test
Resolve-DnsName "contoso.com" -Type A

# System File Checker
sfc /scannow

# DISM health check
DISM /Online /Cleanup-Image /CheckHealth
DISM /Online /Cleanup-Image /ScanHealth
DISM /Online /Cleanup-Image /RestoreHealth

# Services troubleshooting
Get-Service | Where-Object {$_.Status -eq 'Stopped' -and $_.StartType -eq 'Automatic'}
```

**Resources:**
- [Microsoft Learn: Windows Server Troubleshooting](https://learn.microsoft.com/en-us/troubleshoot/windows-server/)
- [Sysinternals Suite](https://learn.microsoft.com/en-us/sysinternals/)

---

### Module 10: Modern Windows Server Features (1 Week)

**Topics:**
- Windows Server Containers (Docker)
- Windows Subsystem for Linux (WSL) on Server
- Azure Arc Hybrid Management
- Windows Admin Center Extensions
- Storage Migration Service
- Server Core App Compatibility Feature on Demand (FOD)
- System Insights (predictive analytics)
- Software Defined Networking (SDN) basics
- Azure Hybrid Benefit
- Windows Server on Azure

**Practical Exercises:**
1. Install Windows Container and run first container
2. Install Azure Arc agent on Windows Server
3. Use Storage Migration Service to migrate legacy server
4. Install Windows Admin Center Extension
5. Enable System Insights and capacity forecasting
6. Configure hybrid domain join with Azure AD

**Useful PowerShell Commands:**
```powershell
# Install Docker on Windows Server
Install-Module -Name DockerMsftProvider -Repository PSGallery -Force
Install-Package -Name docker -ProviderName DockerMsftProvider
Restart-Computer -Force

# Run container
docker run -it mcr.microsoft.com/windows/servercore:ltsc2022 cmd

# Install Azure Arc agent
$serviceAccountCreds = Get-Credential
Connect-AzAccount
./OnboardingScript.ps1 -ServiceAccountCreds $serviceAccountCreds

# Check System Insights capability
Get-InsightsCapability

# Enable System Insights volume capacity forecasting
Enable-InsightsCapability -Name "Volume capacity forecasting"

# Install App Compatibility FOD (on Server Core)
Add-WindowsCapability -Online -Name ServerCore.AppCompatibility~~~~0.0.1.0
```

**Resources:**
- [Microsoft Learn: Windows Containers](https://learn.microsoft.com/en-us/virtualization/windowscontainers/)
- [Microsoft Learn: Azure Arc Overview](https://learn.microsoft.com/en-us/azure/azure-arc/servers/overview)

---

## 🛠️ Practical Projects

### Project 1: Enterprise Active Directory Lab (Week 3-4)

**Objective:** Build complete AD environment in lab.

**Requirements:**
1. 2 Domain Controllers (DC01, DC02) with redundancy
2. FSMO roles distribution
3. AD Sites configuration (two sites: HQ, Branch)
4. OU structure: Departments (IT, HR, Finance), Workstations, Servers
5. Import 50+ test users from CSV
6. Create 10+ Security Groups with different permissions
7. Group Policy Objects:
- Password Policy (complexity, history, lockout)
- Desktop wallpaper deployment
- Mapped drives (H: drive home folder)
- Software installation (deploy 7-Zip via GPO)
- Logon script (map printers, show welcome message)

**Evaluation Criteria:**
- AD replication working between both DCs
- FSMO roles properly distributed
- GPOs applied to correct OUs
- Users can login and receive correct policies

---

### Project 2: File Server with DFS and Quotas (Week 6)

**Objective:** Centralized file server solution with DFS namespace and quota management.

**Requirements:**
1. 2 File Servers (FS01, FS02)
2. DFS Namespace: \\contoso.com\Shares
3. DFS Replication: Shares/Departments automatically replicated between FS01 and FS02
4. NTFS Permissions:
- HR folder: HR group only
- Finance folder: Finance group + Auditors read-only
5. FSRM Quotas: 5GB limit per user home folder
6. FSRM File Screening: block .exe, .mp3, .avi files
7. Shadow Copies enabled (users can restore deleted files)

**Evaluation Criteria:**
- DFS namespace working, users access shares via \\contoso.com\Shares path
- Replication working (file created on FS01 appears on FS02)
- Permissions correctly configured
- Quota enforced (user cannot write beyond 5GB)

---

### Project 3: WSUS and Patch Management (Week 7)

**Objective:** Centralized Windows Update management with WSUS.

**Requirements:**
1. Install and configure WSUS Server
2. Create Computer Groups: Servers, Workstations, Test Group
3. Automatic Approvals: Security updates auto-approve for Test Group
4. Manual Approvals: Critical updates manually approved for Production
5. GPO configuration: clients use WSUS server (not Microsoft Update)
6. Reporting: Generate weekly compliance report

**Evaluation Criteria:**
- Clients connect to WSUS
- Updates download and install on test group
- Reporting dashboard functional

---

### Project 4: Hyper-V Lab with Live Migration (Week 8)

**Objective:** Multi-host Hyper-V environment with live migration.

**Requirements:**
1. 2 Hyper-V Hosts (HV01, HV02)
2. Shared Storage: iSCSI target or SMB 3.0 share
3. Create 4 VMs (2 per host):
- DC01, FS01 on HV01
- DC02, FS02 on HV02
4. Configure Live Migration
5. Migrate VM from HV01 to HV02 without downtime
6. Configure Hyper-V Replica: FS01 replicate to HV02

**Evaluation Criteria:**
- Live Migration working, VM moves to another host while running
- Replica working, FS01 can failover to HV02
- No downtime migration

---

## Quiz Questions and Tests

### Active Directory
1. What's the difference between Security Group and Distribution Group?
2. What are FSMO roles and what happens if PDC Emulator becomes unavailable?
3. How do you restore an accidentally deleted AD user?

### Group Policy
4. What's the difference between Computer Configuration and User Configuration?
5. How do you debug when a GPO doesn't apply to a user?
6. What is loopback processing and when do you use it?

### Networking
7. What's the difference between DNS Forwarder and Conditional Forwarder?
8. How does DHCP Failover work in load balancing mode?
9. What is DNS Scavenging and why is it important?

### Storage
10. What's the difference between NTFS permissions and Share permissions?
11. How does DFS Replication work and when do you use it?
12. What is Storage Spaces Direct and how does it differ from traditional RAID?

### Security
13. How do you configure BitLocker without TPM?
14. What's the difference between AppLocker and WDAC?
15. How do you monitor failed logon attempts?

---

## Certifications and Learning Paths

### Microsoft Official Certifications

1. **AZ-800: Administering Windows Server Hybrid Core Infrastructure**
- Topics: Hybrid identity, compute, storage, networking
- Duration: ~40 hours preparation
- [Official Learning Path](https://learn.microsoft.com/en-us/certifications/exams/az-800/)

2. **AZ-801: Configuring Windows Server Hybrid Advanced Services**
- Topics: Secure Windows Server, implement disaster recovery, monitor and troubleshoot
- Prerequisite: AZ-800
- [Official Learning Path](https://learn.microsoft.com/en-us/certifications/exams/az-801/)

3. **Microsoft Certified: Windows Server Hybrid Administrator Associate**
- Combines AZ-800 and AZ-801 exams
- Validity: 1 year (renewal required)

### Recommended Learning Path

**3-month plan:**
- **Month 1:** Basics (Module 1-4) + AZ-800 materials
- **Month 2:** Advanced (Module 5-7) + Lab exercises
- **Month 3:** Modern features (Module 8-10) + AZ-801 materials + exam prep

---

## Additional Resources

### Documentation
- [Microsoft Learn: Windows Server](https://learn.microsoft.com/en-us/windows-server/)
- [Windows Server TechNet Library](https://learn.microsoft.com/en-us/previous-versions/windows/)
- [PowerShell Documentation](https://learn.microsoft.com/en-us/powershell/)

### Books
- **"Windows Server 2022 Administration Fundamentals"** - Bekim Dauti
- **"Learn PowerShell in a Month of Lunches"** - Don Jones, Jeffrey Hicks
- **"Active Directory: Designing, Deploying, and Running Active Directory"** - Brian Desmond

### YouTube Channels
- **Microsoft Mechanics** - Official Microsoft channel
- **John Savill's Technical Training** - AD, Azure, Hyper-V deep dives
- **PowerShell.org** - PowerShell tutorials

### Community
- [r/sysadmin](https://www.reddit.com/r/sysadmin/) - Reddit community
- [Spiceworks Community](https://community.spiceworks.com/) - IT pro forums
- [TechNet Forums](https://social.technet.microsoft.com/) - Microsoft official forums

### Lab Environment
- **Microsoft Evaluation Center:** 180-day trial Windows Server ISOs
- **Microsoft Learn Sandbox:** Free Azure lab environment
- **VirtualBox/Hyper-V:** Local lab setup on own machine

---

## Success Criteria

By the end of this learning plan, you should achieve:

1. **Independent AD environment setup** (forest, domain, DCs)
2. **GPO design and implementation** with 10+ policies
3. **DNS and DHCP configuration** at production level
4. **File Server operation** with DFS and permissions
5. **PowerShell scripting** for automation tasks
6. **Hyper-V virtualization** with live migration
7. **Troubleshooting skills** with event logs, perfmon, sysinternals
8. **Modern hybrid scenarios** Azure Arc, containers
9. **Security best practices** BitLocker, firewalls, WSUS
10. **Production-ready skillset** for enterprise Windows environments

---

## Next Steps

1. **Lab environment setup** (VirtualBox/Hyper-V + Windows Server trial)
2. **Daily 1-2 hours practice** with hands-on labs
3. **Weekly 1 project** from practical projects
4. **PowerShell script writing** every day (even 5-10 lines)
5. **Microsoft Learn modules** follow official materials
6. **Community engagement** (forums, Reddit, Stack Overflow)
7. **AZ-800 exam preparation** (after 3rd month)

---

**Happy Learning! **

*Last Updated: 2026-05-17*
