# Windows Disk Diagnostics: From Boot Loop to Root Cause

**Goal:** Diagnose disk problems on Windows using PowerShell and SMART data analysis
**Level:** Intermediate
**Language:** English

---

**Table of Contents:** Recognizing the Symptoms | Physical Disk Health | Windows Event Log | I/O Errors per Disk | Storage Spaces Health | SMART Data with CrystalDiskInfo | Volume Health | Complete Diagnostic Script | Resolution Checklist | Quick Reference

---

A practical guide based on a real-world troubleshooting scenario where a system experienced repeated boot loops caused by SATA cable failures.

!!! note "Companion Guide"
    For Linux disk diagnostics using smartmontools and mdadm, see the [Linux Disk Diagnostics](../linux/Linux_Disk_Diagnostics.md) companion guide.

---

## Recognizing the Symptoms

Disk-related boot loops typically present with one or more of these signs:

- System enters a **reboot cycle** — powers on, begins loading Windows, then restarts
- BIOS/UEFI POST shows **disk detection failures** or timeout delays
- Windows briefly displays a **blue screen** referencing storage or NTFS errors
- The system occasionally boots successfully, then **freezes or crashes** during use
- File operations on a specific drive produce **delayed write failures** or I/O errors

These symptoms can originate from the disk itself, the SATA cable, the SATA port on the motherboard, or the power connector. The diagnostic process below separates these causes.

---

## Step 1: Check Physical Disk Health

Once the system boots (even in Safe Mode), start with a high-level health overview.

### Quick Health Check

```powershell
Get-PhysicalDisk | ForEach-Object {
    $sizeGB = [math]::Round($_.Size / 1GB, 1)
    [PSCustomObject]@{
        Name   = $_.FriendlyName
        Media  = $_.MediaType
        Health = $_.HealthStatus
        Status = $_.OperationalStatus
        SizeGB = $sizeGB
        Bus    = $_.BusType
    }
} | Format-Table -AutoSize
```

**What to look for:**

- `HealthStatus` should be `Healthy` — anything else (`Warning`, `Unhealthy`) is an immediate red flag
- `OperationalStatus` should be `OK`
- Note the `BusType` — SATA drives are susceptible to cable issues; NVMe drives are not

### Map Disks to Drive Letters

Knowing which physical disk corresponds to which drive letter is critical for correlating errors.

```powershell
Get-Disk | ForEach-Object {
    $diskNum = $_.Number
    $name = $_.FriendlyName
    $bus = $_.BusType
    $health = $_.HealthStatus
    $sizeGB = [math]::Round($_.Size / 1GB, 1)

    Write-Host "`nDisk $diskNum : $name ($bus) - ${sizeGB}GB - Health: $health"

    $partitions = Get-Partition -DiskNumber $diskNum -ErrorAction SilentlyContinue
    foreach ($part in $partitions) {
        $letter = $part.DriveLetter
        if ($letter) {
            $vol = Get-Volume -DriveLetter $letter -ErrorAction SilentlyContinue
            $label = if ($vol) { $vol.FileSystemLabel } else { "" }
            $partSizeGB = [math]::Round($part.Size / 1GB, 1)
            Write-Host "    -> ${letter}: [$label] ${partSizeGB}GB"
        }
    }
}
```

---

## Step 2: Check the Windows Event Log

The System event log records every disk I/O error, NTFS corruption event, and unexpected shutdown. This is the most important diagnostic data source.

### Disk and Storage Errors (Last 7 Days)

```powershell
$startDate = (Get-Date).AddDays(-7)
$events = Get-WinEvent -FilterHashtable @{
    LogName   = 'System'
    Level     = 1, 2, 3  # Critical, Error, Warning
    StartTime = $startDate
} -ErrorAction SilentlyContinue |
Where-Object {
    $_.Message -match 'disk|ntfs|ata|storage|storahci|ioctl|reset|parity|bad block' -or
    $_.ProviderName -match 'disk|ntfs|ata|stor'
} | Select-Object -First 40

foreach ($ev in $events) {
    $msg = $ev.Message
    if ($msg.Length -gt 200) { $msg = $msg.Substring(0, 200) + "..." }
    Write-Host "[$($ev.TimeCreated)] ID:$($ev.Id) Level:$($ev.LevelDisplayName) Source:$($ev.ProviderName)"
    Write-Host "  $msg`n"
}
```

### Key Event IDs

| Event ID | Source | Meaning |
|----------|--------|---------|
| **7** | Ntfs | Write drop detected on a volume — data loss may have occurred |
| **11** | disk | Driver detected a controller error — often a cable/port problem |
| **50** | Ntfs | Delayed write failure — Windows could not flush data to disk |
| **51** | disk | Paging error — disk I/O failed during memory paging |
| **55** | Ntfs | File system structure corruption detected on a volume |
| **153** | disk | I/O retry on a specific logical block address (LBA) |

!!! warning "Event ID 153 in Large Numbers"
    A handful of Event 153 entries can be transient. Dozens or hundreds within a short period indicate a persistent hardware problem — either the disk is failing, or the SATA connection is unreliable.

### Boot and Crash History

```powershell
$startDate = (Get-Date).AddDays(-7)
Get-WinEvent -FilterHashtable @{
    LogName      = 'System'
    ProviderName = 'Microsoft-Windows-Kernel-Boot',
                   'Microsoft-Windows-Kernel-Power',
                   'EventLog'
    StartTime    = $startDate
} -ErrorAction SilentlyContinue |
Where-Object { $_.Id -in @(12, 13, 41, 1001, 1074, 6005, 6006, 6008, 6009, 6013) } |
Select-Object -First 30 |
ForEach-Object {
    $msg = $_.Message
    if ($msg -and $msg.Length -gt 200) { $msg = $msg.Substring(0, 200) + "..." }
    Write-Host "[$($_.TimeCreated)] ID:$($_.Id) Source:$($_.ProviderName)"
    Write-Host "  $msg`n"
}
```

### Key Boot Event IDs

| Event ID | Source | Meaning |
|----------|--------|---------|
| **41** | Kernel-Power | System rebooted without a clean shutdown (bugcheck / power loss) |
| **6005** | EventLog | Event log service started (system booted) |
| **6006** | EventLog | Event log service stopped (clean shutdown) |
| **6008** | EventLog | Previous shutdown was unexpected — includes the timestamp |
| **6009** | EventLog | OS version info logged at boot |
| **6013** | EventLog | System uptime in seconds |

A pattern of **Event 41 + Event 6008** entries occurring close together confirms a boot loop.

---

## Step 3: Count I/O Errors per Disk

When multiple disks are present, determine which disk is generating the errors.

```powershell
$startDate = (Get-Date).AddDays(-7)
$diskEvents = Get-WinEvent -FilterHashtable @{
    LogName      = 'System'
    ProviderName = 'disk'
    StartTime    = $startDate
} -ErrorAction SilentlyContinue

if ($diskEvents) {
    $diskEvents | Group-Object {
        if ($_.Message -match '(\d+)') { "Disk $($matches[1])" } else { "Unknown" }
    } | Sort-Object Count -Descending |
    ForEach-Object {
        Write-Host "  $($_.Name): $($_.Count) I/O events"
    }
} else {
    Write-Host "  No disk I/O events found in the last 7 days."
}
```

!!! example "Real-World Example"
    In the scenario that prompted this guide, one disk accumulated **111 I/O events in 7 days** while the system suffered **5 unexpected shutdowns in 4 days**. Cross-referencing the disk number with the disk-to-drive-letter mapping immediately identified the failing drive.

---

## Step 4: Check Storage Spaces / Drive Pool Health

If the system uses **Windows Storage Spaces** or third-party pooling (StableBit DrivePool), check pool health separately.

```powershell
# Storage Pools
Get-StoragePool | Where-Object { $_.IsPrimordial -eq $false } | ForEach-Object {
    Write-Host "Pool: $($_.FriendlyName) | Health: $($_.HealthStatus) | Status: $($_.OperationalStatus)"
}

# Virtual Disks
Get-VirtualDisk | ForEach-Object {
    Write-Host "VDisk: $($_.FriendlyName) | Health: $($_.HealthStatus) | Resiliency: $($_.ResiliencySettingName)"
}

# Physical disks in each pool
Get-StoragePool | Where-Object { $_.IsPrimordial -eq $false } | ForEach-Object {
    $poolName = $_.FriendlyName
    Get-PhysicalDisk -StoragePool $_ | ForEach-Object {
        $sizeGB = [math]::Round($_.Size / 1TB, 2)
        Write-Host "  [$poolName] $($_.FriendlyName) | Health: $($_.HealthStatus) | ${sizeGB}TB"
    }
}
```

!!! tip "Mirror Resiliency"
    If a Storage Space is configured with **Mirror** resiliency, data is duplicated across multiple physical disks. A single disk failure won't cause data loss, but should still be addressed promptly — the pool runs in a degraded state until the failed disk is replaced.

---

## Step 5: Analyze SMART Data with CrystalDiskInfo

Windows built-in tools provide limited SMART visibility. **CrystalDiskInfo** (free, portable version available) exposes the full SMART attribute table for each disk.

Download: [CrystalDiskInfo](https://crystalmark.info/en/software/crystaldiskinfo/){ target="_blank" }

Use **Edit → Copy** to export a text report for analysis or archival.

### Critical SMART Attributes for Diagnosis

#### Disk Surface Health (Is the Disk Dying?)

| ID (Hex) | Attribute | Healthy Value | Warning Sign |
|----------|-----------|---------------|--------------|
| **05** | Reallocated Sectors Count | 0 | Any value > 0 means the disk has remapped bad sectors to spares |
| **C4** | Reallocation Event Count | 0 | Number of remap operations — rising count means active degradation |
| **C5** | Current Pending Sector | 0 | Sectors waiting to be tested and potentially reallocated |
| **C6** | Offline Uncorrectable | 0 | Sectors that could not be read — data loss has occurred |

#### Interface Health (Is the Cable Bad?)

| ID (Hex) | Attribute | Healthy Value | Warning Sign |
|----------|-----------|---------------|--------------|
| **C7** | UDMA CRC Error Count | 0 | **Any value > 0 indicates data transfer errors** — almost always a cable, connector, or port issue |

#### General Health

| ID (Hex) | Attribute | Notes |
|----------|-----------|-------|
| **01** | Read Error Rate | Vendor-specific formula; raw value interpretation varies by manufacturer |
| **09** | Power-On Hours | Total operating hours — context for wear assessment |
| **0A** | Spin Retry Count | Failed spin-up attempts — can indicate power supply issues |
| **C2** | Temperature | Operating temperature in Celsius |
| **C8** | Write Error Rate | Write operation failures |

### The Diagnostic Decision Tree

```
SMART Data Analysis
│
├── Reallocated Sectors (05) > 0?
│   ├── YES → Disk surface is degrading. Back up immediately.
│   │         Check Current Pending (C5) and Uncorrectable (C6).
│   │         If those are also rising → disk is dying, replace it.
│   │
│   └── NO → Disk surface is healthy.
│            │
│            ├── CRC Error Count (C7) > 0?
│            │   ├── YES → SATA cable or port problem.
│            │   │         Replace cable, try different port.
│            │   │         Monitor CRC count — if it stops rising, fixed.
│            │   │
│            │   └── NO → Disk and interface are both fine.
│            │            Issue may be intermittent connector contact,
│            │            power supply instability, or driver/firmware bug.
│            │
│            └── Transfer Mode shows "----" or negotiated below expected?
│                └── YES → Link not establishing properly.
│                          Strongly suggests cable or port failure.
```

!!! example "Real-World Example"
    In the scenario that prompted this guide, the failing drive showed:

    - Reallocated Sectors: **0** (disk surface healthy)
    - Current Pending: **0**
    - Offline Uncorrectable: **0**
    - **UDMA CRC Error Count: 62** (cable problem confirmed)
    - Transfer Mode: `---- | SATA/150` (link negotiation failing)

    Two other SATA drives in the same system also had elevated CRC counts (136 and 100), suggesting multiple aging SATA cables or a shared power rail issue. The diagnosis: **replace all SATA cables**.

---

## Step 6: Check Volume Health

After identifying the problem, verify file system integrity on affected volumes.

```powershell
Get-Volume | Where-Object { $_.DriveLetter } | ForEach-Object {
    $letter = $_.DriveLetter
    $label = $_.FileSystemLabel
    $health = $_.HealthStatus
    $fs = $_.FileSystem
    $sizeGB = [math]::Round($_.Size / 1GB, 1)
    $freeGB = [math]::Round($_.SizeRemaining / 1GB, 1)
    Write-Host "${letter}: [$label] $fs | Health: $health | ${sizeGB}GB total, ${freeGB}GB free"
}
```

If NTFS corruption was detected in the event log, run a repair:

```powershell
# Check only (no changes):
chkdsk D: /scan

# Full repair (requires volume lock or reboot for system drive):
chkdsk D: /f /r
```

!!! warning
    `chkdsk /f /r` on a large drive can take hours. Run it during planned downtime. The `/r` flag scans for bad sectors and recovers readable data — essential after I/O errors.

---

## The Complete Diagnostic Script

Save this as a `.ps1` file and run it from an **elevated (Administrator) PowerShell** session whenever disk problems are suspected.

??? note "disk_diag.ps1 — Click to expand"

    ```powershell
    # Disk Diagnostics Script
    # Run from an elevated (Administrator) PowerShell session
    # Save output: .\disk_diag.ps1 | Tee-Object -FilePath disk_report.txt

    Write-Host "============================================"
    Write-Host "  DISK DIAGNOSTICS REPORT"
    Write-Host "  $(Get-Date)"
    Write-Host "============================================"

    # 1. Physical Disk Health
    Write-Host "`n--- PHYSICAL DISK HEALTH ---"
    Get-PhysicalDisk | ForEach-Object {
        $sizeGB = [math]::Round($_.Size / 1GB, 1)
        Write-Host "  $($_.FriendlyName) | $($_.MediaType) | Health: $($_.HealthStatus) | Status: $($_.OperationalStatus) | Size: ${sizeGB}GB"
    }

    # 2. Disk to Drive Letter Mapping
    Write-Host "`n--- DISK TO DRIVE LETTER MAPPING ---"
    Get-Disk | ForEach-Object {
        $diskNum = $_.Number
        $name = $_.FriendlyName
        $bus = $_.BusType
        $health = $_.HealthStatus
        $sizeGB = [math]::Round($_.Size / 1GB, 1)

        Write-Host "`n  Disk $diskNum : $name ($bus) - ${sizeGB}GB - Health: $health"

        Get-Partition -DiskNumber $diskNum -ErrorAction SilentlyContinue | ForEach-Object {
            $letter = $_.DriveLetter
            if ($letter) {
                $vol = Get-Volume -DriveLetter $letter -ErrorAction SilentlyContinue
                $label = if ($vol) { $vol.FileSystemLabel } else { "" }
                $partSizeGB = [math]::Round($_.Size / 1GB, 1)
                Write-Host "    -> ${letter}: [$label] ${partSizeGB}GB"
            }
        }
    }

    # 3. Storage Spaces / Pool Status
    Write-Host "`n--- STORAGE POOL STATUS ---"
    $pools = Get-StoragePool | Where-Object { $_.IsPrimordial -eq $false }
    if ($pools) {
        foreach ($pool in $pools) {
            Write-Host "  Pool: $($pool.FriendlyName) | Health: $($pool.HealthStatus) | Status: $($pool.OperationalStatus)"
            Get-PhysicalDisk -StoragePool $pool | ForEach-Object {
                Write-Host "    - $($_.FriendlyName) | Health: $($_.HealthStatus) | $([math]::Round($_.Size/1TB, 2))TB"
            }
        }
        Get-VirtualDisk | ForEach-Object {
            Write-Host "  VDisk: $($_.FriendlyName) | Resiliency: $($_.ResiliencySettingName) | Health: $($_.HealthStatus)"
        }
    } else {
        Write-Host "  No Storage Spaces configured."
    }

    # 4. Disk/Storage Errors from Event Log
    Write-Host "`n--- DISK/STORAGE ERRORS (last 7 days) ---"
    $startDate = (Get-Date).AddDays(-7)
    $events = Get-WinEvent -FilterHashtable @{
        LogName   = 'System'
        Level     = 1, 2, 3
        StartTime = $startDate
    } -ErrorAction SilentlyContinue |
    Where-Object {
        $_.Message -match 'disk|ntfs|ata|storage|storahci|ioctl|reset|parity|bad block|boot' -or
        $_.ProviderName -match 'disk|ntfs|ata|stor'
    } | Select-Object -First 40

    if ($events) {
        foreach ($ev in $events) {
            $msg = $ev.Message
            if ($msg.Length -gt 250) { $msg = $msg.Substring(0, 250) + "..." }
            Write-Host "`n  [$($ev.TimeCreated)] ID:$($ev.Id) Level:$($ev.LevelDisplayName) Source:$($ev.ProviderName)"
            Write-Host "  $msg"
        }
    } else {
        Write-Host "  No disk/storage errors found."
    }

    # 5. I/O Error Count per Disk
    Write-Host "`n--- I/O ERRORS PER DISK (last 7 days) ---"
    $diskEvents = Get-WinEvent -FilterHashtable @{
        LogName      = 'System'
        ProviderName = 'disk'
        StartTime    = $startDate
    } -ErrorAction SilentlyContinue

    if ($diskEvents) {
        $diskEvents | Group-Object {
            if ($_.Message -match '(\d+)') { "Disk $($matches[1])" } else { "Unknown" }
        } | Sort-Object Count -Descending |
        ForEach-Object { Write-Host "  $($_.Name): $($_.Count) events" }
    } else {
        Write-Host "  No disk I/O events found."
    }

    # 6. Boot/Crash Events
    Write-Host "`n--- BOOT / CRASH EVENTS (last 7 days) ---"
    $bootEvents = Get-WinEvent -FilterHashtable @{
        LogName      = 'System'
        ProviderName = 'Microsoft-Windows-Kernel-Boot',
                       'Microsoft-Windows-Kernel-Power',
                       'EventLog'
        StartTime    = $startDate
    } -ErrorAction SilentlyContinue |
    Where-Object { $_.Id -in @(12, 13, 41, 1001, 1074, 6005, 6006, 6008, 6009, 6013) } |
    Select-Object -First 30

    if ($bootEvents) {
        foreach ($ev in $bootEvents) {
            $msg = $ev.Message
            if ($msg -and $msg.Length -gt 250) { $msg = $msg.Substring(0, 250) + "..." }
            Write-Host "  [$($ev.TimeCreated)] ID:$($ev.Id) Source:$($ev.ProviderName)"
            Write-Host "  $msg"
        }
    } else {
        Write-Host "  No notable boot events found."
    }

    # 7. Volume Health
    Write-Host "`n--- VOLUME HEALTH ---"
    Get-Volume | Where-Object { $_.DriveLetter } | ForEach-Object {
        $letter = $_.DriveLetter
        $label = $_.FileSystemLabel
        $health = $_.HealthStatus
        $fs = $_.FileSystem
        $sizeGB = [math]::Round($_.Size / 1GB, 1)
        $freeGB = [math]::Round($_.SizeRemaining / 1GB, 1)
        Write-Host "  ${letter}: [$label] $fs | Health: $health | ${sizeGB}GB total, ${freeGB}GB free"
    }

    Write-Host "`n--- DONE ---"
    Write-Host "Next step: Run CrystalDiskInfo for full SMART attribute analysis."
    ```

---

## Resolution Checklist

After identifying the root cause, follow the appropriate resolution path:

### SATA Cable Issue (CRC Errors, No Bad Sectors)

- [ ] Replace the SATA data cable on the affected drive
- [ ] Try a different SATA port on the motherboard
- [ ] Check the SATA power connector for a firm connection
- [ ] After cable replacement, run `chkdsk /f /r` on affected volumes
- [ ] Monitor CRC error count in CrystalDiskInfo — it should stop increasing
- [ ] If multiple drives show CRC errors, consider replacing all SATA cables or investigating the power supply

### Failing Disk (Reallocated / Pending / Uncorrectable Sectors)

- [ ] **Back up all data immediately** — the disk may become unreadable at any time
- [ ] Order a replacement drive
- [ ] Clone the disk using a tool like Clonezilla or Macrium Reflect while it's still readable
- [ ] If the disk is part of a Storage Space with Mirror resiliency, replace it through the Storage Spaces management UI

### Intermittent Issues (No SMART Warnings, But I/O Errors Persist)

- [ ] Check the power supply — use a PSU tester or try a different SATA power rail
- [ ] Update the SATA/AHCI controller driver and motherboard firmware (BIOS/UEFI)
- [ ] Test with a known-good SATA cable
- [ ] Run a manufacturer-specific diagnostic tool (e.g., WD Data Lifeguard, Seagate SeaTools)
- [ ] If the drive is very old (10+ years), consider preventive replacement regardless of SMART status

---

## Quick Reference: Diagnostic Commands

| Task | Command |
|------|---------|
| List physical disks | `Get-PhysicalDisk` |
| Map disks to drive letters | `Get-Disk`, `Get-Partition`, `Get-Volume` |
| Check Storage Spaces | `Get-StoragePool`, `Get-VirtualDisk` |
| View disk errors (Event Log) | `Get-WinEvent` with `ProviderName = 'disk'` |
| View NTFS errors | `Get-WinEvent` with `ProviderName = 'Ntfs'` |
| View crash/boot events | Event IDs 41, 6008 from Kernel-Power / EventLog |
| Check file system | `chkdsk X: /scan` (read-only) or `chkdsk X: /f /r` (repair) |
| SMART data | CrystalDiskInfo (GUI) or `Get-StorageReliabilityCounter` (limited) |
| WMI disk info | `Get-WmiObject Win32_DiskDrive` |
