# Windows Disk Management & Troubleshooting Quick Reference

**Created:** 2026-08-12
**Version:** 1.0
**Audience:** IT Administrators, SRE Engineers, Windows System Administrators

## Table of Contents

1. [Disk Identification & Information](#1-disk-identification--information)
2. [Partitioning](#2-partitioning)
3. [Filesystems](#3-filesystems)
4. [Storage Spaces](#4-storage-spaces)
5. [SMART & Health Monitoring](#5-smart--health-monitoring)
6. [Disk Performance](#6-disk-performance)
7. [Troubleshooting & Recovery](#7-troubleshooting--recovery)
8. [SSD-Specific](#8-ssd-specific)
9. [BitLocker](#9-bitlocker)
10. [Best Practices](#10-best-practices)

---

## 1. Disk Identification & Information

### Disk Management GUI

Open via `diskmgmt.msc` or **Win+X > Disk Management**. Provides a visual layout of all disks, partitions, volumes, and their statuses (Healthy, Failed, Online, Offline).

### diskpart

```cmd
REM List all disks
diskpart
DISKPART> list disk

REM Select a disk and show details
DISKPART> select disk 0
DISKPART> detail disk

REM List all volumes
DISKPART> list volume

REM List partitions on selected disk
DISKPART> select disk 0
DISKPART> list partition
```

### PowerShell Cmdlets

```powershell
# All physical disks (model, media type, health, size)
Get-PhysicalDisk | Format-Table DeviceId, FriendlyName, MediaType, Size, HealthStatus

# Logical disks (partitions, style, operational status)
Get-Disk | Format-Table Number, FriendlyName, PartitionStyle, OperationalStatus, @{N='SizeGB';E={[math]::Round($_.Size/1GB,2)}}

# All partitions
Get-Partition | Format-Table DiskNumber, PartitionNumber, DriveLetter, @{N='SizeGB';E={[math]::Round($_.Size/1GB,2)}}, Type

# All volumes (drive letter, filesystem, size, free space)
Get-Volume | Format-Table DriveLetter, FileSystemLabel, FileSystem, @{N='SizeGB';E={[math]::Round($_.Size/1GB,2)}}, @{N='FreeGB';E={[math]::Round($_.SizeRemaining/1GB,2)}}, HealthStatus

# Disk to partition to volume mapping
Get-Disk | ForEach-Object {
    $disk = $_
    Get-Partition -DiskNumber $disk.Number | ForEach-Object {
        [PSCustomObject]@{
            Disk       = $disk.Number
            Model      = $disk.FriendlyName
            Partition  = $_.PartitionNumber
            Letter     = $_.DriveLetter
            SizeGB     = [math]::Round($_.Size/1GB,2)
            Type       = $_.Type
        }
    }
} | Format-Table -AutoSize
```

### WMI / CIM Queries

```powershell
# Disk drives (model, serial, interface, firmware)
Get-CimInstance Win32_DiskDrive | Select-Object DeviceID, Model, SerialNumber, InterfaceType, FirmwareRevision, @{N='SizeGB';E={[math]::Round($_.Size/1GB,2)}}

# Logical disks (classic)
Get-CimInstance Win32_LogicalDisk | Select-Object DeviceID, VolumeName, FileSystem, @{N='SizeGB';E={[math]::Round($_.Size/1GB,2)}}, @{N='FreeGB';E={[math]::Round($_.FreeSpace/1GB,2)}}

# Disk partitions
Get-CimInstance Win32_DiskPartition | Select-Object Name, Type, @{N='SizeGB';E={[math]::Round($_.Size/1GB,2)}}
```

```cmd
REM CMD equivalent (legacy)
wmic diskdrive get model,serialNumber,interfaceType,size,status
wmic logicaldisk get caption,description,filesystem,size,freespace
```

!!! tip "Device Manager"
    Open `devmgmt.msc` and expand **Disk drives** to see physical disk hardware, driver info, and hardware IDs. Right-click a disk > **Properties > Details > Hardware Ids** to identify the exact model and firmware revision. The **Events** tab shows recent disk-related events.

---

## 2. Partitioning

### MBR vs GPT

| Feature | MBR | GPT |
|---|---|---|
| Max disk size | 2 TB | 18 EB (exabytes) |
| Max primary partitions | 4 (or 3 + extended) | 128 |
| Boot mode | BIOS/Legacy | UEFI |
| Partition table backup | None | Secondary GPT header at end of disk |
| Data integrity | None | CRC32 checksums |
| OS support | All Windows | Windows 7+ (data), Windows 8+ (boot) |

!!! warning "UEFI Boot Requirement"
    Windows can only boot from a GPT disk in UEFI mode. For BIOS/Legacy mode, the boot disk must be MBR. Data disks can use GPT regardless of boot mode.

### diskpart: Partition Operations

```cmd
REM === Create a primary partition ===
diskpart
DISKPART> select disk 1
DISKPART> create partition primary size=51200
DISKPART> format fs=ntfs label="Data" quick
DISKPART> assign letter=D

REM === Extend a partition (unallocated space must be adjacent and to the right) ===
DISKPART> select volume D
DISKPART> extend size=10240

REM === Shrink a partition ===
DISKPART> select volume C
DISKPART> shrink desired=20480 minimum=10240

REM Query how much can be shrunk
DISKPART> shrink querymax

REM === Delete a partition ===
DISKPART> select disk 1
DISKPART> select partition 2
DISKPART> delete partition

REM Force-delete a protected partition (OEM, recovery)
DISKPART> delete partition override

REM === Initialize a new disk ===
DISKPART> select disk 2
DISKPART> clean
DISKPART> convert gpt
DISKPART> create partition primary
DISKPART> format fs=ntfs label="NewDisk" quick
DISKPART> assign letter=E
```

### PowerShell: Partition Operations

```powershell
# Initialize a new raw disk as GPT
Initialize-Disk -Number 2 -PartitionStyle GPT

# Create a new partition using all available space
New-Partition -DiskNumber 2 -UseMaximumSize -AssignDriveLetter

# Create a partition with a specific size and drive letter
New-Partition -DiskNumber 2 -Size 50GB -DriveLetter E

# Format the new partition
Format-Volume -DriveLetter E -FileSystem NTFS -NewFileSystemLabel "Data" -Confirm:$false

# Resize (extend) a partition
Resize-Partition -DriveLetter D -Size (Get-PartitionSupportedSize -DriveLetter D).SizeMax

# Resize (shrink) a partition — set target size, not shrink amount
$current = Get-Partition -DriveLetter C
Resize-Partition -DriveLetter C -Size ($current.Size - 20GB)

# Remove a partition
Remove-Partition -DiskNumber 1 -PartitionNumber 3 -Confirm:$false

# One-liner: initialize, partition, and format a new disk
Initialize-Disk 2 -PartitionStyle GPT -PassThru |
    New-Partition -UseMaximumSize -AssignDriveLetter |
    Format-Volume -FileSystem NTFS -NewFileSystemLabel "Backup"
```

### Special Partitions

| Partition | Purpose | Typical Size | Notes |
|---|---|---|---|
| EFI System Partition (ESP) | UEFI boot loader files | 100-500 MB | FAT32, mounted at `\EFI` |
| Microsoft Reserved (MSR) | GPT management area | 16-128 MB | No filesystem, invisible in Disk Management |
| Recovery Partition | Windows RE (WinRE) | 500-1000 MB | NTFS, hidden, no drive letter |
| OEM Partition | Manufacturer recovery tools | Varies | Vendor-specific, can be deleted with `override` |

```powershell
# View all partitions including hidden ones
Get-Partition -DiskNumber 0 | Select-Object PartitionNumber, GptType, @{N='SizeGB';E={[math]::Round($_.Size/1GB,2)}}, Type, IsHidden

# Common GPT type GUIDs
# EFI System:  {c12a7328-f81f-11d2-ba4b-00a0c93ec93b}
# MSR:         {e3c9e316-0b5c-4db8-817d-f92df00215ae}
# Basic Data:  {ebd0a0a2-b9e5-4433-87c0-68b6b72699c7}
# Recovery:    {de94bba4-06d1-4d40-a16a-bfd50179d6ac}
```

### Converting MBR to GPT

```cmd
REM In-place conversion for Windows 10 1703+ (non-destructive for boot disks)
REM Run from Windows PE or Recovery Environment
mbr2gpt /validate /disk:0
mbr2gpt /convert /disk:0

REM Allow conversion while OS is running (risky, use /allowFullOS)
mbr2gpt /convert /disk:0 /allowFullOS
```

!!! danger "Before MBR to GPT Conversion"
    1. Back up all data on the disk.
    2. Ensure firmware supports UEFI boot.
    3. Change BIOS settings from Legacy to UEFI after conversion.
    4. `mbr2gpt` only works on boot disks with at most 3 primary partitions.
    5. For data-only disks, use diskpart `clean` + `convert gpt` (destroys data) or third-party tools for non-destructive conversion.

---

## 3. Filesystems

### Filesystem Comparison

| Feature | NTFS | ReFS | exFAT | FAT32 |
|---|---|---|---|---|
| Max volume size | 256 TB (practical) | 35 PB | 128 PB | 2 TB |
| Max file size | 256 TB | 35 PB | 128 PB | 4 GB |
| Journaling | Yes | Yes (integrity streams) | No | No |
| Permissions (ACL) | Yes | Yes | No | No |
| Compression | Yes | No | No | No |
| Encryption (EFS) | Yes | No | No | No |
| Deduplication | Yes (Server) | Yes (Server) | No | No |
| BitLocker | Yes | No | Yes (Win 10+) | Yes |
| Cross-platform | Linux (rw), macOS (ro) | Windows only | Universal | Universal |
| Best for | OS drives, general use | Storage Spaces, backups | USB drives, SD cards | Legacy devices |

### Format Commands

```cmd
REM CMD: format a volume
format D: /FS:NTFS /Q /V:DataDrive
format E: /FS:exFAT /Q /V:USBDrive
format F: /FS:FAT32 /Q /V:Legacy
```

```powershell
# PowerShell: format a volume
Format-Volume -DriveLetter D -FileSystem NTFS -NewFileSystemLabel "DataDrive" -Confirm:$false
Format-Volume -DriveLetter E -FileSystem exFAT -NewFileSystemLabel "USBDrive" -Confirm:$false

# Format with specific allocation unit size (cluster size)
Format-Volume -DriveLetter D -FileSystem NTFS -AllocationUnitSize 65536 -NewFileSystemLabel "LargeFiles"
```

### Drive Letters and Mount Points

```powershell
# Assign a drive letter
Set-Partition -DiskNumber 1 -PartitionNumber 2 -NewDriveLetter G

# Remove a drive letter (make the volume accessible only via mount point)
Remove-PartitionAccessPath -DiskNumber 1 -PartitionNumber 2 -AccessPath "G:\"

# Mount a volume to a folder (NTFS mount point)
Add-PartitionAccessPath -DiskNumber 1 -PartitionNumber 2 -AccessPath "C:\Mounts\DataDrive"

# List all mount points
Get-Volume | Where-Object { $_.DriveLetter -eq $null } | ForEach-Object {
    $vol = $_
    Get-Partition | Where-Object { $_.AccessPaths -match $vol.UniqueId } |
        Select-Object DiskNumber, PartitionNumber, AccessPaths
}
```

```cmd
REM diskpart: change drive letter
diskpart
DISKPART> select volume 3
DISKPART> assign letter=G

REM diskpart: remove drive letter
DISKPART> select volume 3
DISKPART> remove letter=G

REM mountvol: create a mount point
mountvol C:\Mounts\DataDrive \\?\Volume{GUID}\

REM mountvol: list volume GUIDs
mountvol
```

!!! tip "Drive Letter Persistence"
    Drive letter assignments are stored in the registry at `HKLM\SYSTEM\MountedDevices`. If a drive letter is not sticking after reboot, check that no other device is claiming the same letter and that the `MountedDevices` entries are correct.

---

## 4. Storage Spaces

### Overview

Storage Spaces pools multiple physical disks into a single logical storage pool. Virtual disks (storage spaces) are created from the pool with configurable resiliency.

| Layout | Min Disks | Fault Tolerance | Space Efficiency | Best For |
|---|---|---|---|---|
| Simple | 1 | None | 100% | Temporary data, performance |
| Two-way Mirror | 2 | 1 disk failure | 50% | OS volumes, critical data |
| Three-way Mirror | 5 | 2 disk failures | 33% | High availability |
| Parity | 3 | 1 disk failure | 67% (3 disks) | Archive, sequential workloads |
| Dual Parity | 7 | 2 disk failures | 71%+ (7 disks) | Large archive pools |

### Creating Storage Spaces via PowerShell

```powershell
# Step 1: Identify available physical disks (must show CanPool = True)
Get-PhysicalDisk -CanPool $true | Format-Table DeviceId, FriendlyName, MediaType, @{N='SizeGB';E={[math]::Round($_.Size/1GB,2)}}

# Step 2: Create a storage pool
$disks = Get-PhysicalDisk -CanPool $true
New-StoragePool -FriendlyName "DataPool" -StorageSubSystemFriendlyName "Windows Storage*" -PhysicalDisks $disks

# Step 3: Create a virtual disk (two-way mirror, thin provisioning)
New-VirtualDisk -StoragePoolFriendlyName "DataPool" -FriendlyName "MirrorDisk" -ResiliencySettingName Mirror -UseMaximumSize

# With specific size and provisioning type
New-VirtualDisk -StoragePoolFriendlyName "DataPool" -FriendlyName "MirrorDisk" `
    -ResiliencySettingName Mirror `
    -Size 500GB `
    -ProvisioningType Thin `
    -NumberOfColumns 2

# Step 4: Initialize, partition, and format
Get-VirtualDisk -FriendlyName "MirrorDisk" | Get-Disk | Initialize-Disk -PartitionStyle GPT -PassThru |
    New-Partition -UseMaximumSize -AssignDriveLetter |
    Format-Volume -FileSystem ReFS -NewFileSystemLabel "MirrorData"
```

### Managing Storage Spaces

```powershell
# List all storage pools
Get-StoragePool | Where-Object IsPrimordial -eq $false | Format-Table FriendlyName, HealthStatus, OperationalStatus, @{N='SizeGB';E={[math]::Round($_.Size/1GB,2)}}

# List virtual disks in a pool
Get-VirtualDisk | Format-Table FriendlyName, ResiliencySettingName, OperationalStatus, HealthStatus, @{N='SizeGB';E={[math]::Round($_.Size/1GB,2)}}

# Check physical disk assignment in a pool
Get-StoragePool -FriendlyName "DataPool" | Get-PhysicalDisk | Format-Table DeviceId, FriendlyName, MediaType, HealthStatus, Usage

# Add a new physical disk to an existing pool
$newDisk = Get-PhysicalDisk -CanPool $true | Where-Object DeviceId -eq 3
Add-PhysicalDisk -StoragePoolFriendlyName "DataPool" -PhysicalDisks $newDisk

# Remove a virtual disk
Remove-VirtualDisk -FriendlyName "MirrorDisk" -Confirm:$false

# Remove a storage pool (remove all virtual disks first)
Remove-StoragePool -FriendlyName "DataPool" -Confirm:$false

# Repair a degraded virtual disk (after replacing a failed disk)
Repair-VirtualDisk -FriendlyName "MirrorDisk"
```

### Storage Tiers (SSD + HDD)

```powershell
# Create storage tiers in a pool (Server OS or Windows 10/11 with Storage Spaces)
$pool = Get-StoragePool -FriendlyName "DataPool"

# Define SSD tier
New-StorageTier -StoragePoolFriendlyName "DataPool" -FriendlyName "SSD_Tier" -MediaType SSD -ResiliencySettingName Mirror

# Define HDD tier
New-StorageTier -StoragePoolFriendlyName "DataPool" -FriendlyName "HDD_Tier" -MediaType HDD -ResiliencySettingName Parity

# Create a tiered virtual disk
$ssdTier = Get-StorageTier -FriendlyName "SSD_Tier"
$hddTier = Get-StorageTier -FriendlyName "HDD_Tier"

New-VirtualDisk -StoragePoolFriendlyName "DataPool" -FriendlyName "TieredDisk" `
    -StorageTiers $ssdTier, $hddTier `
    -StorageTierSizes 50GB, 200GB `
    -WriteCacheSize 5GB
```

---

## 5. SMART & Health Monitoring

### PowerShell Health Checks

```powershell
# Physical disk health overview
Get-PhysicalDisk | Select-Object DeviceId, FriendlyName, MediaType, BusType, HealthStatus, OperationalStatus, @{N='SizeGB';E={[math]::Round($_.Size/1GB,2)}}

# Detailed SMART-like reliability counters
Get-PhysicalDisk | Get-StorageReliabilityCounter | Format-List *

# Key counters to watch
Get-PhysicalDisk | ForEach-Object {
    $disk = $_
    $counter = $_ | Get-StorageReliabilityCounter
    [PSCustomObject]@{
        Disk              = $disk.FriendlyName
        Temperature       = $counter.Temperature
        Wear              = $counter.Wear
        ReadErrors        = $counter.ReadErrorsTotal
        WriteErrors       = $counter.WriteErrorsTotal
        PowerOnHours      = $counter.PowerOnHours
        StartStopCycles   = $counter.StartStopCycleCount
        ReadLatencyMax    = $counter.ReadLatencyMax
        WriteLatencyMax   = $counter.WriteLatencyMax
    }
}

# Check NVMe-specific health (Windows 10 1903+)
Get-PhysicalDisk | Where-Object BusType -eq NVMe | Get-StorageReliabilityCounter |
    Select-Object DeviceId, Temperature, Wear, ReadErrorsTotal, WriteErrorsTotal
```

### WMI Disk Status

```cmd
REM Quick SMART status check (returns "OK" or "Pred Fail")
wmic diskdrive get model,status,serialNumber
```

```powershell
# CIM equivalent
Get-CimInstance Win32_DiskDrive | Select-Object Model, SerialNumber, Status
```

### CrystalDiskInfo

CrystalDiskInfo is a free tool that reads raw SMART attributes.

- Download: [https://crystalmark.info/en/software/crystaldiskinfo/](https://crystalmark.info/en/software/crystaldiskinfo/)
- Portable version available (no install needed)
- Monitors: Reallocated Sectors, Pending Sectors, Temperature, Power-On Hours, Wear Leveling Count
- Can run as a service for alerting (System > Resident / Startup)

!!! warning "SMART Limitations"
    SMART status "OK" does not guarantee the disk is healthy. A disk can fail without SMART warning. Use SMART as one signal among many (performance metrics, error logs, age). SMART is particularly unreliable for predicting sudden failures.

### Event Viewer: Disk-Related Events

Open `eventvwr.msc` and check **Windows Logs > System** and **Applications and Services Logs > Microsoft > Windows > StorDiag**.

| Event ID | Source | Severity | Meaning |
|---|---|---|---|
| 7 | Disk | Error | Bad block detected on disk |
| 9 | Disk | Error | Controller not responding; device timeout |
| 11 | Disk | Error | Controller error (I/O failed) |
| 15 | Disk | Error | Disk not ready for access |
| 51 | Disk | Warning | Paging error during I/O operation |
| 52 | Disk | Warning | Disk driver detected a firmware bug |
| 129 | storahci | Warning | Reset to device (NVMe/AHCI timeout) |
| 153 | Disk | Warning | Disk retry on I/O operation |
| 154 | Disk | Warning | Disk surprise removal |
| 157 | Disk | Error | Disk has been surprise removed |

```powershell
# Query disk error events from the last 7 days
Get-WinEvent -FilterHashtable @{
    LogName   = 'System'
    ProviderName = 'disk','storahci','stornvme','partmgr','ntfs'
    Level     = 1,2,3   # Critical, Error, Warning
    StartTime = (Get-Date).AddDays(-7)
} | Format-Table TimeCreated, Id, LevelDisplayName, Message -Wrap

# Count disk errors per Event ID
Get-WinEvent -FilterHashtable @{LogName='System'; ProviderName='disk'; Level=1,2,3; StartTime=(Get-Date).AddDays(-30)} |
    Group-Object Id | Select-Object Count, Name | Sort-Object Count -Descending
```

---

## 6. Disk Performance

### Performance Monitor (perfmon)

Key disk counters to monitor in `perfmon.msc`:

| Counter | Object | Warning Threshold | Description |
|---|---|---|---|
| `% Disk Time` | PhysicalDisk | >80% sustained | Time disk is busy |
| `Avg. Disk Queue Length` | PhysicalDisk | >2 per spindle | Pending I/O requests |
| `Avg. Disk sec/Read` | PhysicalDisk | >20ms (HDD), >10ms (SSD) | Read latency |
| `Avg. Disk sec/Write` | PhysicalDisk | >20ms (HDD), >10ms (SSD) | Write latency |
| `Disk Reads/sec` | PhysicalDisk | Baseline-dependent | IOPS (read) |
| `Disk Writes/sec` | PhysicalDisk | Baseline-dependent | IOPS (write) |
| `Disk Bytes/sec` | PhysicalDisk | Baseline-dependent | Throughput |
| `Current Disk Queue Length` | PhysicalDisk | >2 | Instantaneous queue depth |

```cmd
REM Collect disk performance data for 60 seconds (1-second interval)
typeperf "\PhysicalDisk(*)\Avg. Disk sec/Read" "\PhysicalDisk(*)\Avg. Disk sec/Write" "\PhysicalDisk(*)\Disk Reads/sec" "\PhysicalDisk(*)\Disk Writes/sec" -si 1 -sc 60 -o disk_perf.csv
```

### PowerShell Performance Queries

```powershell
# Real-time disk I/O per process (top 10)
Get-Process | Sort-Object -Property @{E={$_.IO_DATA_BYTES_PER_SEC}} -Descending |
    Select-Object -First 10 Name, Id,
        @{N='ReadMB';E={[math]::Round(($_.ReadTransferCount)/1MB,2)}},
        @{N='WriteMB';E={[math]::Round(($_.WriteTransferCount)/1MB,2)}}

# Performance counter snapshot
Get-Counter '\PhysicalDisk(*)\Avg. Disk sec/Read','\PhysicalDisk(*)\Avg. Disk sec/Write','\PhysicalDisk(*)\Current Disk Queue Length' |
    ForEach-Object { $_.CounterSamples } | Format-Table Path, CookedValue

# Continuous monitoring (5-second intervals, 12 samples)
Get-Counter '\PhysicalDisk(_Total)\% Disk Time','\PhysicalDisk(_Total)\Avg. Disk Queue Length' -SampleInterval 5 -MaxSamples 12
```

### Resource Monitor

Open `resmon.exe` and go to the **Disk** tab for real-time per-process I/O activity with read/write speeds and response times.

### Windows System Assessment Tool (winsat)

```cmd
REM Run disk assessment
winsat disk -drive C

REM Sequential read/write test
winsat disk -seq -read -drive C
winsat disk -seq -write -drive C

REM Random read/write test
winsat disk -ran -read -drive C
winsat disk -ran -write -drive C
```

### DiskSpd Benchmarking

DiskSpd is Microsoft's recommended storage benchmarking tool (successor to SQLIO).

Download: [https://github.com/microsoft/diskspd](https://github.com/microsoft/diskspd)

```cmd
REM Sequential read test: 8 threads, 64KB blocks, 30 seconds, 10GB file
diskspd -b64K -d30 -o8 -t8 -h -r -w0 -L -Z1M -c10G C:\diskspd_test.dat

REM Random 4K read/write mix (70/30): simulates OLTP workload
diskspd -b4K -d60 -o32 -t4 -r -w30 -L -Z1M -c2G C:\diskspd_test.dat

REM Sequential write test
diskspd -b64K -d30 -o8 -t8 -h -r -w100 -L -Z1M -c10G C:\diskspd_test.dat
```

**DiskSpd Key Parameters:**

| Parameter | Meaning |
|---|---|
| `-b<size>` | Block size (4K, 64K, 1M) |
| `-d<seconds>` | Test duration |
| `-o<count>` | Outstanding I/O per thread (queue depth) |
| `-t<count>` | Threads per target file |
| `-r` | Random I/O (omit for sequential) |
| `-w<percent>` | Write percentage (0 = all reads, 100 = all writes) |
| `-L` | Capture latency statistics |
| `-h` | Disable OS caching (hardware caching only) |
| `-Z<size>` | Entropy seed size for write buffer |
| `-c<size>` | Create file of specified size |

!!! tip "DiskSpd Best Practices"
    Always use `-h` to bypass the OS cache for realistic results. Run tests multiple times and average the results. Use `-Z1M` or larger to avoid compression effects on SSDs with hardware compression. Delete the test file after benchmarking.

---

## 7. Troubleshooting & Recovery

### chkdsk (Check Disk)

```cmd
REM Check for errors (read-only, no fix)
chkdsk D:

REM Fix filesystem errors
chkdsk D: /f

REM Fix errors + scan for bad sectors and recover readable data
chkdsk D: /r

REM Force dismount before checking (use on locked volumes)
chkdsk D: /x

REM Re-evaluate bad clusters (NTFS only, implies /r)
chkdsk D: /b

REM Scan online (NTFS, Windows 8+, minimal downtime)
chkdsk D: /scan

REM Schedule chkdsk on boot for system volume
chkdsk C: /f
REM (Responds "yes" when prompted to schedule on next restart)
```

```powershell
# PowerShell equivalent
Repair-Volume -DriveLetter D -Scan                 # Read-only scan
Repair-Volume -DriveLetter D -OfflineScanAndFix     # Offline fix
Repair-Volume -DriveLetter D -SpotFix               # Online spot fix (Server 2012+/Win 8+)
```

!!! warning "chkdsk /r Runtime"
    `chkdsk /r` can take hours on large volumes because it scans every sector. For SSDs, bad sector recovery is less relevant; use `/f` or `/scan` instead. Never run `/r` on an SSD unless you suspect controller-level issues.

### SFC and DISM

```cmd
REM System File Checker - scan and repair protected system files
sfc /scannow

REM If SFC fails, repair the Windows image first with DISM
DISM /Online /Cleanup-Image /CheckHealth
DISM /Online /Cleanup-Image /ScanHealth
DISM /Online /Cleanup-Image /RestoreHealth

REM Then re-run SFC
sfc /scannow

REM DISM with a known-good source (mounted ISO or WIM)
DISM /Online /Cleanup-Image /RestoreHealth /Source:D:\sources\install.wim
```

### Common Disk Error States

| Disk State | Cause | Resolution |
|---|---|---|
| **Offline** | Disk policy, name collision, or disk error | Right-click > Online in Disk Management, or `Set-Disk -Number X -IsOffline $false` |
| **Missing** | Disk was removed or failed | Check physical connection; rescan: `Update-StorageProviderCache` |
| **Foreign** | Disk was moved from another system (dynamic disks) | Right-click > Import Foreign Disks in Disk Management |
| **Not Initialized** | New disk, no partition table | Initialize via Disk Management or `Initialize-Disk -Number X -PartitionStyle GPT` |
| **Read Only** | Disk policy or hardware write-protect | `Set-Disk -Number X -IsReadOnly $false` or check physical switch |
| **No Media** | Card reader with no card inserted | Normal; insert media |

```powershell
# Bring an offline disk online
Set-Disk -Number 2 -IsOffline $false

# Clear read-only flag
Set-Disk -Number 2 -IsReadOnly $false

# Set SAN policy to online all new disks (useful for VMs and SANs)
# Run in elevated PowerShell:
Set-StorageSetting -NewDiskPolicy OnlineAll
```

### Disk Signature Collision

When two disks have the same MBR signature (common when cloning), one goes offline.

```cmd
REM Fix via diskpart: assign a new signature
diskpart
DISKPART> select disk 1
DISKPART> uniqueid disk
DISKPART> uniqueid disk id=AABBCCDD
```

```powershell
# View disk signatures
Get-Disk | Select-Object Number, FriendlyName, Signature, Guid, OperationalStatus
```

### Handling Full Disk

```powershell
# Find largest files on a volume
Get-ChildItem -Path C:\ -Recurse -File -ErrorAction SilentlyContinue |
    Sort-Object Length -Descending |
    Select-Object -First 20 @{N='SizeMB';E={[math]::Round($_.Length/1MB,2)}}, FullName

# Find largest folders (top-level)
Get-ChildItem -Path C:\ -Directory -ErrorAction SilentlyContinue | ForEach-Object {
    [PSCustomObject]@{
        Folder = $_.FullName
        SizeGB = [math]::Round((Get-ChildItem $_.FullName -Recurse -File -ErrorAction SilentlyContinue |
            Measure-Object Length -Sum).Sum / 1GB, 2)
    }
} | Sort-Object SizeGB -Descending | Select-Object -First 15

# Windows Disk Cleanup (interactive)
cleanmgr /d C:

# Disk Cleanup with all options pre-selected (sageset creates profile, sagerun executes it)
cleanmgr /sageset:1
cleanmgr /sagerun:1

# Clear Windows Update cache
Stop-Service wuauserv
Remove-Item -Path "C:\Windows\SoftwareDistribution\Download\*" -Recurse -Force
Start-Service wuauserv

# Clear temp files
Remove-Item -Path "$env:TEMP\*" -Recurse -Force -ErrorAction SilentlyContinue
Remove-Item -Path "C:\Windows\Temp\*" -Recurse -Force -ErrorAction SilentlyContinue

# DISM: clean up old component store versions
DISM /Online /Cleanup-Image /StartComponentCleanup /ResetBase
```

**GUI Tools for disk space analysis:**

- **WinDirStat** - classic treemap visualization ([https://windirstat.net](https://windirstat.net))
- **TreeSize Free** - fast, Explorer-integrated ([https://www.jam-software.com/treesize_free](https://www.jam-software.com/treesize_free))
- **WizTree** - fastest scanner (reads MFT directly) ([https://wiztree.com](https://wiztree.com))

### Windows Recovery Environment (WinRE)

Access via **Settings > System > Recovery > Advanced startup**, or boot from installation media and choose **Repair your computer**.

Available tools in WinRE:

- **Command Prompt** - run `chkdsk`, `sfc`, `diskpart`, `bcdedit`, `bootrec`
- **Startup Repair** - automatic boot issue fix
- **System Restore** - revert to a restore point
- **System Image Recovery** - restore from a full backup image
- **UEFI Firmware Settings** - direct access to BIOS/UEFI

```cmd
REM Common WinRE commands

REM Fix boot records
bootrec /fixmbr
bootrec /fixboot
bootrec /rebuildbcd

REM Repair EFI boot (when bootrec /fixboot fails with "access denied")
diskpart
DISKPART> select disk 0
DISKPART> list volume
REM Identify the EFI partition (FAT32, ~100MB)
DISKPART> select volume 2
DISKPART> assign letter=S
DISKPART> exit

cd /d S:\EFI\Microsoft\Boot
ren BCD BCD.bak
bcdboot C:\Windows /s S: /f UEFI
```

---

## 8. SSD-Specific

### TRIM

TRIM tells the SSD which blocks are no longer in use so the controller can manage wear leveling and garbage collection efficiently.

```powershell
# Check if TRIM is enabled (RetrimEnabled should be True)
fsutil behavior query DisableDeleteNotify
# 0 = TRIM enabled, 1 = TRIM disabled

# Verify optimization settings
Get-Volume | Select-Object DriveLetter, FileSystem, @{N='MediaType';E={(Get-PhysicalDisk | Where-Object DeviceId -eq (Get-Partition -DriveLetter $_.DriveLetter -ErrorAction SilentlyContinue | Select-Object -ExpandProperty DiskNumber -ErrorAction SilentlyContinue)).MediaType}}

# Manually trigger TRIM/optimization
Optimize-Volume -DriveLetter C -ReTrim -Verbose
```

```cmd
REM Optimize (TRIM for SSD, defrag for HDD)
defrag C: /O

REM Force retrim
defrag C: /L

REM Check optimization status
dfrgui
```

!!! warning "Do Not Defragment SSDs"
    Windows automatically runs TRIM on SSDs via the scheduled "Optimize Drives" task. Traditional defragmentation is unnecessary and harmful to SSDs. The `defrag /O` command is safe because it detects the media type and runs TRIM instead of defragmentation.

### NVMe Management

```powershell
# Identify NVMe drives
Get-PhysicalDisk | Where-Object BusType -eq NVMe |
    Select-Object DeviceId, FriendlyName, @{N='SizeGB';E={[math]::Round($_.Size/1GB,2)}}, HealthStatus, OperationalStatus

# NVMe-specific health data
Get-PhysicalDisk | Where-Object BusType -eq NVMe | Get-StorageReliabilityCounter |
    Select-Object DeviceId, Temperature, Wear, ReadErrorsTotal, WriteErrorsTotal, PowerOnHours

# NVMe namespace information (Windows 11 / Server 2022+)
Get-Disk | Where-Object BusType -eq NVMe | Select-Object Number, FriendlyName, FirmwareVersion, AdapterSerialNumber
```

```cmd
REM List NVMe controllers (requires elevated CMD)
wmic path Win32_NVMeController get DeviceId,Name,DriverVersion
```

### SSD Firmware Updates

1. Identify the SSD model: `Get-PhysicalDisk | Select-Object FriendlyName, FirmwareVersion`
2. Download the firmware update tool from the manufacturer's website
3. Common manufacturer tools:
   - **Samsung:** Samsung Magician
   - **Crucial/Micron:** Crucial Storage Executive
   - **Western Digital / SanDisk:** WD Dashboard
   - **Intel:** Intel Memory and Storage Tool (MAS CLI)
   - **Kingston:** Kingston SSD Manager
   - **Seagate:** SeaTools

!!! danger "Firmware Update Precautions"
    Always back up data before firmware updates. Some updates require a power cycle; never interrupt the update process. Use a UPS or ensure stable power. Some NVMe firmware updates require a bootable ISO.

### Over-Provisioning

Over-provisioning reserves a portion of SSD capacity for the controller to use for wear leveling, garbage collection, and bad block replacement.

- Most consumer SSDs have 7-10% built-in over-provisioning
- Enterprise SSDs typically have 20-28%
- You can manually increase OP by leaving unpartitioned space at the end of the drive
- Some manufacturer tools (Samsung Magician, Crucial Storage Executive) offer a dedicated OP setting

```powershell
# Check current partitioned vs total capacity
$disk = Get-Disk -Number 0
$partitioned = (Get-Partition -DiskNumber 0 | Measure-Object Size -Sum).Sum
$total = $disk.Size
$opPercent = [math]::Round((($total - $partitioned) / $total) * 100, 1)
Write-Host "Total: $([math]::Round($total/1GB,2)) GB, Partitioned: $([math]::Round($partitioned/1GB,2)) GB, Unallocated (OP): $opPercent%"
```

---

## 9. BitLocker

### Status and Information

```powershell
# Check BitLocker status for all volumes
Get-BitLockerVolume | Format-Table MountPoint, VolumeStatus, EncryptionMethod, EncryptionPercentage, ProtectionStatus, LockStatus, KeyProtector

# Detailed status for a specific volume
Get-BitLockerVolume -MountPoint "C:" | Format-List *
```

```cmd
REM manage-bde: check status
manage-bde -status
manage-bde -status C:
```

### Enable BitLocker

```powershell
# Enable BitLocker with TPM protector (typical for OS drives)
Enable-BitLocker -MountPoint "C:" -EncryptionMethod XtsAes256 -TpmProtector

# Enable with TPM + PIN
$pin = ConvertTo-SecureString "123456" -AsPlainText -Force
Enable-BitLocker -MountPoint "C:" -EncryptionMethod XtsAes256 -TpmAndPinProtector -Pin $pin

# Enable BitLocker on a data drive with password
Enable-BitLocker -MountPoint "D:" -EncryptionMethod XtsAes256 -PasswordProtector -Password (Read-Host -AsSecureString "Enter password")

# Add a recovery password protector (numerical recovery key)
Add-BitLockerKeyProtector -MountPoint "C:" -RecoveryPasswordProtector
```

```cmd
REM manage-bde: enable with TPM
manage-bde -on C: -EncryptionMethod XtsAes256

REM manage-bde: enable with password (data drives)
manage-bde -on D: -EncryptionMethod XtsAes256 -Password

REM manage-bde: add recovery key protector
manage-bde -protectors -add C: -RecoveryPassword
```

### Recovery Key Management

```powershell
# View recovery keys
(Get-BitLockerVolume -MountPoint "C:").KeyProtector | Where-Object KeyProtectorType -eq 'RecoveryPassword'

# Back up recovery key to Active Directory
Backup-BitLockerKeyProtector -MountPoint "C:" -KeyProtectorId "{GUID-HERE}"

# Back up to file
(Get-BitLockerVolume -MountPoint "C:").KeyProtector |
    Where-Object KeyProtectorType -eq 'RecoveryPassword' |
    ForEach-Object { $_.RecoveryPassword | Out-File "\\server\share\BitLocker_$env:COMPUTERNAME.txt" }
```

```cmd
REM manage-bde: view protectors
manage-bde -protectors -get C:

REM Unlock a drive with recovery password
manage-bde -unlock D: -RecoveryPassword 123456-123456-123456-123456-123456-123456-123456-123456

REM Unlock with recovery key file
manage-bde -unlock D: -RecoveryKey F:\RecoveryKey.bek
```

### Disable / Suspend / Resume BitLocker

```powershell
# Suspend protection (for maintenance, firmware updates, etc.)
Suspend-BitLocker -MountPoint "C:" -RebootCount 1   # Auto-resumes after 1 reboot

# Resume protection
Resume-BitLocker -MountPoint "C:"

# Disable BitLocker (decrypts the volume)
Disable-BitLocker -MountPoint "D:"
```

```cmd
REM manage-bde equivalents
manage-bde -pause C:         &REM Pause encryption/decryption
manage-bde -resume C:        &REM Resume encryption/decryption
manage-bde -protectors -disable C:   &REM Suspend protection
manage-bde -protectors -enable C:    &REM Resume protection
manage-bde -off C:           &REM Decrypt and disable
```

!!! tip "Suspend Before BIOS Updates"
    Always run `Suspend-BitLocker -MountPoint "C:" -RebootCount 1` before BIOS/UEFI firmware updates or boot configuration changes. Failure to suspend can trigger BitLocker recovery mode on next boot, requiring the recovery key.

---

## 10. Best Practices

### Monitoring Checklist

Run these checks regularly (weekly or via scheduled tasks):

```powershell
# --- Comprehensive Disk Health Report ---

Write-Host "=== Physical Disk Health ===" -ForegroundColor Cyan
Get-PhysicalDisk | Format-Table DeviceId, FriendlyName, MediaType, HealthStatus, OperationalStatus, @{N='SizeGB';E={[math]::Round($_.Size/1GB,2)}}

Write-Host "`n=== Volume Free Space ===" -ForegroundColor Cyan
Get-Volume | Where-Object DriveLetter | Format-Table DriveLetter, FileSystemLabel, @{N='SizeGB';E={[math]::Round($_.Size/1GB,2)}}, @{N='FreeGB';E={[math]::Round($_.SizeRemaining/1GB,2)}}, @{N='Free%';E={[math]::Round(($_.SizeRemaining/$_.Size)*100,1)}}, HealthStatus

Write-Host "`n=== SMART Reliability ===" -ForegroundColor Cyan
Get-PhysicalDisk | Get-StorageReliabilityCounter |
    Select-Object DeviceId, Temperature, Wear, ReadErrorsTotal, WriteErrorsTotal, PowerOnHours |
    Format-Table

Write-Host "`n=== Recent Disk Errors (7 days) ===" -ForegroundColor Cyan
Get-WinEvent -FilterHashtable @{LogName='System'; ProviderName='disk','storahci','stornvme'; Level=1,2,3; StartTime=(Get-Date).AddDays(-7)} -ErrorAction SilentlyContinue |
    Format-Table TimeCreated, Id, LevelDisplayName, Message -Wrap
```

### Disk Maintenance Schedule

| Task | Frequency | Command / Tool |
|---|---|---|
| Check disk health | Weekly | `Get-PhysicalDisk`, `Get-StorageReliabilityCounter` |
| Review Event Viewer disk errors | Weekly | Event IDs 7, 9, 11, 51, 129, 153 |
| SSD TRIM/Optimize | Automatic (weekly) | Scheduled task: "Optimize Drives" |
| Check free space | Daily (alert-based) | `Get-Volume`, monitoring agent |
| chkdsk scan | Monthly | `chkdsk /scan` or `Repair-Volume -Scan` |
| Firmware update check | Quarterly | Manufacturer tool / website |
| Backup verification | Monthly | Test restore from backup |
| BitLocker key backup | On change | `Backup-BitLockerKeyProtector` |
| Storage Spaces health | Weekly | `Get-VirtualDisk`, `Get-StoragePool` |

### When to Replace a Disk

| Indicator | Action |
|---|---|
| SMART `HealthStatus` = **Warning** or **Unhealthy** | Replace immediately |
| Reallocated sector count growing | Plan replacement within days |
| `Wear` indicator approaching 100% (SSD) | Plan replacement |
| Recurring Event ID 7, 11, or 153 | Investigate; likely replace |
| Read/write errors increasing | Replace proactively |
| `PowerOnHours` > 40,000 (HDD) or > 30,000 (SSD) | Monitor closely, plan replacement |
| Audible clicking or grinding (HDD) | Replace immediately, back up first |
| Multiple chkdsk repairs needed | Replace soon |
| Performance degradation not explained by workload | Investigate; may need replacement |

### Useful PowerShell One-Liners

```powershell
# All disks, partitions, and volumes in one view
Get-Disk | ForEach-Object { $d=$_; Get-Partition -DiskNumber $d.Number -ErrorAction SilentlyContinue | ForEach-Object { [PSCustomObject]@{Disk=$d.Number; Model=$d.FriendlyName; Part=$_.PartitionNumber; Letter=$_.DriveLetter; SizeGB=[math]::Round($_.Size/1GB,2); Type=$_.Type} } } | Format-Table

# Find volumes with less than 10% free space
Get-Volume | Where-Object { $_.DriveLetter -and $_.Size -gt 0 -and ($_.SizeRemaining / $_.Size) -lt 0.10 } | Select-Object DriveLetter, @{N='Free%';E={[math]::Round(($_.SizeRemaining/$_.Size)*100,1)}}

# Export full disk report to CSV
Get-PhysicalDisk | Select-Object DeviceId, FriendlyName, MediaType, BusType, HealthStatus, OperationalStatus, @{N='SizeGB';E={[math]::Round($_.Size/1GB,2)}}, FirmwareVersion, SerialNumber | Export-Csv -Path "$env:USERPROFILE\Desktop\DiskReport.csv" -NoTypeInformation

# List all drives with their serial numbers and firmware
Get-CimInstance Win32_DiskDrive | Select-Object Index, Model, SerialNumber, FirmwareRevision, InterfaceType, @{N='SizeGB';E={[math]::Round($_.Size/1GB,2)}}

# Quick disk benchmark (simple sequential read test)
$testFile = "C:\diskspd_test.dat"; [System.IO.File]::WriteAllBytes($testFile, (New-Object byte[] 104857600)); $sw=[Diagnostics.Stopwatch]::StartNew(); [System.IO.File]::ReadAllBytes($testFile)|Out-Null; $sw.Stop(); Write-Host "Sequential read: $([math]::Round(100/$sw.Elapsed.TotalSeconds,0)) MB/s"; Remove-Item $testFile

# Identify which process has a file locked (requires handle.exe from Sysinternals)
# handle.exe "C:\path\to\locked\file"

# Monitor disk queue length in real time
while($true){ Get-Counter '\PhysicalDisk(_Total)\Current Disk Queue Length' | ForEach-Object { "$((Get-Date).ToString('HH:mm:ss')) Queue: $($_.CounterSamples[0].CookedValue)" }; Start-Sleep 2 }

# Generate disk health summary email-friendly output
Get-PhysicalDisk | ForEach-Object { $c = $_ | Get-StorageReliabilityCounter; "$($_.FriendlyName): Health=$($_.HealthStatus), Temp=$($c.Temperature)C, Wear=$($c.Wear)%, Hours=$($c.PowerOnHours)" }
```

---

*This reference covers Windows 10, Windows 11, and Windows Server 2016/2019/2022. Some features (Storage Spaces Direct, Storage Tiers, ReFS) are available only on Server editions. Always test commands in a non-production environment first.*
