# Windows System Integrity: SFC, DISM, and Automated Health Checks

**Goal:** Verify and repair Windows system file integrity using built-in tools (SFC, DISM, chkdsk, bootrec)
**Level:** Intermediate → Advanced
**Language:** English

---

**Table of Contents:** How It Works | SFC | DISM | Correct Repair Sequence | Boot Repair | Windows Update Integrity | Driver Integrity | Registry Integrity | Automated Script | Quick Reference

---

A practical guide to verifying and repairing Windows system file integrity using built-in tools. When system files become corrupted — due to disk errors, power loss, failed updates, or malware — these tools detect and restore them from known-good sources.

## How Windows System Integrity Works

Windows maintains integrity through multiple layers:

```
Layer 1: Component Store (WinSxS)
  └── The master copy of all system files, managed by CBS (Component Based Servicing)
  └── Location: C:\Windows\WinSxS
  └── Repaired by: DISM

Layer 2: System Files (System32, etc.)
  └── Active copies deployed from the Component Store
  └── Protected by: Windows Resource Protection (WRP)
  └── Repaired by: SFC

Layer 3: Boot Files (BCD, bootloader)
  └── Boot configuration and loader
  └── Repaired by: bootrec, bcdboot

Layer 4: Filesystem Structure
  └── NTFS metadata, directory entries, MFT
  └── Repaired by: chkdsk
```

!!! tip "Correct repair order"
    Always work **bottom-up**: fix the filesystem first (chkdsk), then the component store (DISM), then the system files (SFC), then boot files if needed (bootrec).

---

## SFC — System File Checker

SFC scans all protected system files and replaces corrupted or modified files with the correct version from the Component Store (`WinSxS`).

### Basic Usage

```powershell
# Run from an elevated (Administrator) PowerShell or Command Prompt

# Full system scan and automatic repair
sfc /scannow

# Verify only (no repair)
sfc /verifyonly

# Scan a specific file
sfc /scanfile=C:\Windows\System32\kernel32.dll

# Verify a specific file
sfc /verifyfile=C:\Windows\System32\kernel32.dll
```

### Understanding SFC Output

| Message | Meaning | Action |
|---------|---------|--------|
| `Windows Resource Protection did not find any integrity violations.` | All system files are intact | No action needed |
| `Windows Resource Protection found corrupt files and successfully repaired them.` | Corruption found and fixed | Check the log for details |
| `Windows Resource Protection found corrupt files but was unable to fix some of them.` | Corruption found, repair failed | Run DISM first, then SFC again |
| `Windows Resource Protection could not perform the requested operation.` | SFC itself cannot run | Boot to Safe Mode or Recovery |

### Reading the SFC Log

SFC writes detailed results to a log file:

```powershell
# View the SFC log (CBS.log filtered for SFC entries)
findstr /c:"[SR]" %windir%\Logs\CBS\CBS.log > "%userprofile%\Desktop\sfclog.txt"

# Or in PowerShell
Select-String -Path "$env:windir\Logs\CBS\CBS.log" -Pattern "\[SR\]" |
    Select-Object -Last 100 |
    ForEach-Object { $_.Line }
```

Log entries to look for:

```
# Healthy file
[SR] Verifying 100 (0x0064) [l:24]"kernel32.dll" ... hash matches

# Corrupted and repaired
[SR] Cannot repair member file [l:24]"example.dll" ... hash mismatch
[SR] Repairing file [l:24]"example.dll" from store

# Cannot repair (Component Store also damaged)
[SR] Cannot repair member file [l:24]"example.dll"
[SR] This component was referenced by [l:...]
```

### Offline SFC (from Recovery Environment)

!!! note "When to use offline SFC"
    When Windows cannot boot, run SFC from the Recovery Environment or a Windows installation USB. The drive letter may differ from the normal boot environment.

```cmd
# Boot from Windows installation media
# Select "Repair your computer" > Command Prompt

# Find the Windows drive letter (may not be C: in recovery)
wmic logicaldisk get caption,volumename

# Run SFC targeting the offline Windows installation
sfc /scannow /offbootdir=D:\ /offwindir=D:\Windows
```

---

## DISM — Deployment Image Servicing and Management

DISM repairs the **Component Store** itself — the source that SFC uses for repairs. If SFC reports it cannot fix files, DISM is the next step.

### Health Check (Read-Only)

```powershell
# Quick check — is the component store flagged as needing repair?
DISM /Online /Cleanup-Image /CheckHealth

# Deeper scan — actually verify component store integrity
DISM /Online /Cleanup-Image /ScanHealth
```

| Command | Speed | What It Does |
|---------|-------|--------------|
| `/CheckHealth` | Instant | Checks if a corruption flag is set (from a previous scan) |
| `/ScanHealth` | Minutes | Actually scans the component store for corruption |

### Repair

```powershell
# Repair the component store using Windows Update as the source
DISM /Online /Cleanup-Image /RestoreHealth

# Repair using a local Windows image (ISO mounted or install media)
DISM /Online /Cleanup-Image /RestoreHealth /Source:E:\Sources\install.wim

# Repair using a specific image index from the WIM
DISM /Online /Cleanup-Image /RestoreHealth /Source:wim:E:\Sources\install.wim:1

# Prevent DISM from using Windows Update (offline repair only)
DISM /Online /Cleanup-Image /RestoreHealth /Source:E:\Sources\install.wim /LimitAccess
```

### DISM Output

| Message | Meaning |
|---------|---------|
| `The component store is repairable.` | Corruption found, can be fixed |
| `No component store corruption detected.` | Component store is healthy |
| `The restore operation completed successfully.` | Repairs applied successfully |
| `Error: 0x800f081f` | Source files not found — provide a Windows image |
| `Error: 0x800f0906` | Cannot download from Windows Update — use local source |

### Additional DISM Operations

```powershell
# Clean up superseded components (free disk space)
DISM /Online /Cleanup-Image /StartComponentCleanup

# Aggressive cleanup (removes ALL superseded versions — see warning below)
DISM /Online /Cleanup-Image /StartComponentCleanup /ResetBase

# Check installed packages
DISM /Online /Get-Packages

# Check specific feature state
DISM /Online /Get-Features | findstr /i "NetFx"
```

!!! danger "ResetBase is irreversible"
    `/StartComponentCleanup /ResetBase` removes **all** superseded component versions. After running it, you cannot uninstall any previously installed updates. Only use this when you are certain you do not need to roll back.

### DISM Log

DISM logs to a separate file:

```powershell
# View DISM log
notepad "$env:windir\Logs\DISM\dism.log"

# Or search for errors
Select-String -Path "$env:windir\Logs\DISM\dism.log" -Pattern "Error|Warning|Failed" |
    Select-Object -Last 50 |
    ForEach-Object { $_.Line }
```

---

## The Correct Repair Sequence

When dealing with system file corruption, follow this order:

```
Step 1: chkdsk
  └── Fix filesystem structure first
  └── Without a healthy filesystem, file-level repairs are unreliable

Step 2: DISM /RestoreHealth
  └── Repair the Component Store
  └── This restores the "source of truth" that SFC needs

Step 3: sfc /scannow
  └── Repair system files from the (now healthy) Component Store

Step 4: Reboot and verify
  └── Some repairs require a restart to take effect
  └── Run sfc /scannow again after reboot to confirm

Step 5 (if needed): bootrec
  └── Repair boot configuration if the system won't start
```

### Full Repair Sequence Commands

```powershell
# Step 1: Check filesystem (schedule for next boot if on system drive)
chkdsk C: /f /r

# Step 2: Repair Component Store
DISM /Online /Cleanup-Image /RestoreHealth

# Step 3: Repair system files
sfc /scannow

# Step 4: Reboot
Restart-Computer

# Step 5 (after reboot): Verify
sfc /verifyonly
```

---

## Boot Repair Tools

When Windows cannot boot at all, use these from the Recovery Environment:

### bootrec

```cmd
# Rebuild the Boot Configuration Data
bootrec /rebuildbcd

# Fix the Master Boot Record
bootrec /fixmbr

# Fix the boot sector
bootrec /fixboot

# Scan for Windows installations
bootrec /scanos
```

### bcdboot

```cmd
# Rebuild BCD store from a Windows installation
bcdboot C:\Windows /s S: /f UEFI

# For BIOS/MBR systems
bcdboot C:\Windows /s S: /f BIOS
```

---

## Windows Update Integrity

Failed Windows updates can leave the system in an inconsistent state.

### Check Windows Update Health

```powershell
# Check for pending updates and their state
Get-WindowsUpdate -ErrorAction SilentlyContinue

# View Windows Update log (Windows 10/11)
Get-WindowsUpdateLog

# Check update history
Get-HotFix | Sort-Object InstalledOn -Descending | Select-Object -First 20

# Check for failed updates in Event Log
Get-WinEvent -FilterHashtable @{
    LogName = 'System'
    ProviderName = 'Microsoft-Windows-WindowsUpdateClient'
} -MaxEvents 20 -ErrorAction SilentlyContinue |
ForEach-Object {
    Write-Host "[$($_.TimeCreated)] ID:$($_.Id) $($_.Message.Substring(0, [Math]::Min($_.Message.Length, 150)))"
}
```

### Reset Windows Update Components

!!! warning "Use as a last resort"
    This procedure stops core update services and renames their cache folders. Only use it when Windows Update is completely broken and normal troubleshooting has failed.

```powershell
# Stop update services
Stop-Service -Name wuauserv, cryptSvc, bits, msiserver -Force

# Rename cache folders (backup)
Rename-Item "$env:windir\SoftwareDistribution" "SoftwareDistribution.old"
Rename-Item "$env:windir\System32\catroot2" "catroot2.old"

# Restart services
Start-Service -Name wuauserv, cryptSvc, bits, msiserver

# Trigger a fresh update scan
Start-Process "USOClient.exe" "StartScan"
```

---

## Driver Integrity

Corrupted drivers can cause BSODs and hardware malfunctions.

### Check Driver Signing

```powershell
# List all drivers and their signing status
driverquery /v | findstr /i "not signed"

# Or use PowerShell for more detail
Get-WmiObject Win32_PnPSignedDriver |
    Where-Object { $_.IsSigned -eq $false } |
    Select-Object DeviceName, DriverVersion, Manufacturer |
    Format-Table -AutoSize
```

### Verify System Driver Integrity

```powershell
# sigverif — GUI tool for driver signature verification
sigverif

# Or use the System Information tool
msinfo32
# Navigate to: Software Environment > Signed Drivers
```

---

## Registry Integrity

!!! warning "Registry is a critical system database"
    Corruption in the Windows Registry can prevent boot or cause erratic behavior. Always create a backup before making manual registry changes.

### Built-in Registry Backup

!!! note "Disabled by default since Windows 10 1803"
    Automatic registry backup to `RegBack` is disabled by default in Windows 10 version 1803 and later. You must re-enable it via Task Scheduler (see command below).

```powershell
# Windows automatically backs up the registry to:
# C:\Windows\System32\config\RegBack\

# Check if RegBack contains recent files
Get-ChildItem "$env:windir\System32\config\RegBack" | 
    Select-Object Name, Length, LastWriteTime |
    Format-Table -AutoSize

# Re-enable automatic registry backup via Task Scheduler
# Task: \Microsoft\Windows\Registry\RegIdleBackup
schtasks /Change /TN "Microsoft\Windows\Registry\RegIdleBackup" /Enable
```

### Manual Registry Backup

```powershell
# Export entire registry (large file)
reg export HKLM "$env:userprofile\Desktop\HKLM_backup.reg"
reg export HKCU "$env:userprofile\Desktop\HKCU_backup.reg"

# Export specific key
reg export "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion" "$env:userprofile\Desktop\CurrentVersion_backup.reg"
```

---

## The Complete System Integrity Script

??? note "system_integrity_check.ps1 - Click to expand"

    ```powershell
    # Windows System Integrity Check
    # Run from an elevated (Administrator) PowerShell session
    # Usage: .\system_integrity_check.ps1 | Tee-Object -FilePath integrity_report.txt

    param(
        [switch]$Repair,
        [switch]$Full
    )

    $ErrorActionPreference = "Continue"

    Write-Host "============================================"
    Write-Host "  SYSTEM INTEGRITY CHECK"
    Write-Host "  $(Get-Date)"
    if ($Repair) { Write-Host "  MODE: SCAN + REPAIR" -ForegroundColor Yellow }
    else { Write-Host "  MODE: SCAN ONLY (use -Repair to fix)" }
    Write-Host "============================================"

    # 1. OS Information
    Write-Host "`n=== 1. SYSTEM INFORMATION ==="
    $os = Get-WmiObject Win32_OperatingSystem
    Write-Host "  OS:       $($os.Caption) $($os.Version)"
    Write-Host "  Build:    $($os.BuildNumber)"
    Write-Host "  Install:  $($os.ConvertToDateTime($os.InstallDate))"
    Write-Host "  Uptime:   $(((Get-Date) - $os.ConvertToDateTime($os.LastBootUpTime)).ToString('d\.hh\:mm\:ss'))"

    # 2. Disk Health (prerequisite for integrity checks)
    Write-Host "`n=== 2. VOLUME HEALTH ==="
    Get-Volume | Where-Object { $_.DriveLetter -and $_.FileSystem } |
        Sort-Object DriveLetter | ForEach-Object {
        $letter = $_.DriveLetter
        $health = $_.HealthStatus
        $fs = $_.FileSystem
        $sizeGB = [math]::Round($_.Size / 1GB, 1)
        $freeGB = [math]::Round($_.SizeRemaining / 1GB, 1)
        $status = if ($health -ne "Healthy") { " !! UNHEALTHY !!" } else { "" }
        Write-Host "  ${letter}: $fs - Health: ${health} - ${freeGB}/${sizeGB}GB free${status}"
    }

    # 3. DISM Component Store Health
    Write-Host "`n=== 3. COMPONENT STORE (DISM) ==="
    if ($Full) {
        Write-Host "  Running DISM ScanHealth (deep scan, may take several minutes)..."
        if ($Repair) {
            $dismResult = & DISM /Online /Cleanup-Image /RestoreHealth 2>&1
        } else {
            $dismResult = & DISM /Online /Cleanup-Image /ScanHealth 2>&1
        }
    } else {
        Write-Host "  Running DISM CheckHealth (quick check)..."
        $dismResult = & DISM /Online /Cleanup-Image /CheckHealth 2>&1
    }
    $dismOutput = $dismResult | Out-String
    if ($dismOutput -match "No component store corruption") {
        Write-Host "  Result: HEALTHY" -ForegroundColor Green
    } elseif ($dismOutput -match "repairable") {
        Write-Host "  Result: CORRUPTION DETECTED (repairable)" -ForegroundColor Red
        if (-not $Repair) {
            Write-Host "  Run with -Repair flag to fix."
        }
    } elseif ($dismOutput -match "restore operation completed successfully") {
        Write-Host "  Result: REPAIRED SUCCESSFULLY" -ForegroundColor Yellow
    } else {
        Write-Host "  Result:"
        $dismResult | ForEach-Object { Write-Host "    $_" }
    }

    # 4. SFC System File Check
    Write-Host "`n=== 4. SYSTEM FILE CHECKER (SFC) ==="
    if ($Repair) {
        Write-Host "  Running sfc /scannow (scan + repair, may take several minutes)..."
        $sfcResult = & sfc /scannow 2>&1
    } else {
        Write-Host "  Running sfc /verifyonly (scan only, may take several minutes)..."
        $sfcResult = & sfc /verifyonly 2>&1
    }
    $sfcOutput = $sfcResult | Out-String
    if ($sfcOutput -match "did not find any integrity violations") {
        Write-Host "  Result: ALL FILES INTACT" -ForegroundColor Green
    } elseif ($sfcOutput -match "successfully repaired") {
        Write-Host "  Result: CORRUPTION FOUND AND REPAIRED" -ForegroundColor Yellow
    } elseif ($sfcOutput -match "unable to fix") {
        Write-Host "  Result: CORRUPTION FOUND - REPAIR FAILED" -ForegroundColor Red
        Write-Host "  Run DISM /Online /Cleanup-Image /RestoreHealth first, then retry."
    } else {
        Write-Host "  Result:"
        $sfcResult | ForEach-Object { Write-Host "    $_" }
    }

    # 5. Recent SFC Log Entries
    Write-Host "`n=== 5. RECENT SFC LOG ==="
    $cbsLog = "$env:windir\Logs\CBS\CBS.log"
    if (Test-Path $cbsLog) {
        $sfcEntries = Select-String -Path $cbsLog -Pattern "\[SR\]" |
            Select-Object -Last 20
        if ($sfcEntries) {
            $corrupted = $sfcEntries | Where-Object { $_.Line -match "Cannot repair|hash mismatch|corrupt" }
            $repaired = $sfcEntries | Where-Object { $_.Line -match "Repairing|repaired|Successfully" }
            Write-Host "  Last 20 SFC log entries:"
            Write-Host "    Corruption entries: $(($corrupted | Measure-Object).Count)"
            Write-Host "    Repair entries:     $(($repaired | Measure-Object).Count)"
            if ($corrupted) {
                Write-Host "  Corrupted files found:" -ForegroundColor Red
                $corrupted | ForEach-Object {
                    $line = $_.Line
                    if ($line.Length -gt 150) { $line = $line.Substring(0, 150) + "..." }
                    Write-Host "    $line"
                }
            }
        } else {
            Write-Host "  No SFC entries in CBS.log."
        }
    } else {
        Write-Host "  CBS.log not found."
    }

    # 6. DISM Log Errors
    Write-Host "`n=== 6. RECENT DISM ERRORS ==="
    $dismLog = "$env:windir\Logs\DISM\dism.log"
    if (Test-Path $dismLog) {
        $dismErrors = Select-String -Path $dismLog -Pattern "Error|HRESULT" |
            Select-Object -Last 10
        if ($dismErrors) {
            Write-Host "  Last 10 DISM error entries:"
            $dismErrors | ForEach-Object {
                $line = $_.Line.Trim()
                if ($line.Length -gt 150) { $line = $line.Substring(0, 150) + "..." }
                Write-Host "    $line"
            }
        } else {
            Write-Host "  No errors in DISM log." -ForegroundColor Green
        }
    } else {
        Write-Host "  DISM log not found."
    }

    # 7. Windows Update Status
    Write-Host "`n=== 7. WINDOWS UPDATE STATUS ==="
    $lastUpdates = Get-HotFix -ErrorAction SilentlyContinue |
        Sort-Object InstalledOn -Descending -ErrorAction SilentlyContinue |
        Select-Object -First 5
    if ($lastUpdates) {
        Write-Host "  Last 5 installed updates:"
        foreach ($upd in $lastUpdates) {
            Write-Host "    $($upd.HotFixID) - $($upd.InstalledOn) - $($upd.Description)"
        }
    }

    # Failed update events
    $updateErrors = Get-WinEvent -FilterHashtable @{
        LogName = 'System'
        ProviderName = 'Microsoft-Windows-WindowsUpdateClient'
        Level = 2
    } -MaxEvents 5 -ErrorAction SilentlyContinue

    if ($updateErrors) {
        Write-Host "`n  Recent Windows Update errors:" -ForegroundColor Red
        foreach ($ev in $updateErrors) {
            $msg = $ev.Message
            if ($msg.Length -gt 150) { $msg = $msg.Substring(0, 150) + "..." }
            Write-Host "    [$($ev.TimeCreated)] $msg"
        }
    } else {
        Write-Host "  No recent Windows Update errors." -ForegroundColor Green
    }

    # 8. Unsigned Drivers
    Write-Host "`n=== 8. DRIVER INTEGRITY ==="
    $unsignedDrivers = Get-WmiObject Win32_PnPSignedDriver -ErrorAction SilentlyContinue |
        Where-Object { $_.IsSigned -eq $false -and $_.DeviceName }

    if ($unsignedDrivers) {
        Write-Host "  Unsigned drivers found:" -ForegroundColor Yellow
        foreach ($drv in $unsignedDrivers) {
            Write-Host "    $($drv.DeviceName) - $($drv.Manufacturer) - v$($drv.DriverVersion)"
        }
    } else {
        Write-Host "  All loaded drivers are signed." -ForegroundColor Green
    }

    # 9. Secure Boot Status
    Write-Host "`n=== 9. SECURE BOOT ==="
    try {
        $secureBoot = Confirm-SecureBootUEFI -ErrorAction Stop
        if ($secureBoot) {
            Write-Host "  Secure Boot: ENABLED" -ForegroundColor Green
        } else {
            Write-Host "  Secure Boot: DISABLED" -ForegroundColor Yellow
        }
    } catch {
        Write-Host "  Secure Boot: NOT SUPPORTED or BIOS mode"
    }

    # 10. Pending Reboot Check
    Write-Host "`n=== 10. PENDING OPERATIONS ==="
    $pendingReboot = $false

    # Check Component Based Servicing
    $cbsKey = "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\RebootPending"
    if (Test-Path $cbsKey) {
        Write-Host "  Component Based Servicing: REBOOT PENDING" -ForegroundColor Yellow
        $pendingReboot = $true
    }

    # Check Windows Update
    $wuKey = "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate\Auto Update\RebootRequired"
    if (Test-Path $wuKey) {
        Write-Host "  Windows Update: REBOOT REQUIRED" -ForegroundColor Yellow
        $pendingReboot = $true
    }

    # Check pending file rename operations
    $pfro = Get-ItemProperty "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager" -Name PendingFileRenameOperations -ErrorAction SilentlyContinue
    if ($pfro.PendingFileRenameOperations) {
        $count = ($pfro.PendingFileRenameOperations | Where-Object { $_ }).Count
        Write-Host "  Pending file renames: $count operations" -ForegroundColor Yellow
        $pendingReboot = $true
    }

    if (-not $pendingReboot) {
        Write-Host "  No pending reboots or operations." -ForegroundColor Green
    }

    # 11. Registry Backup Status
    Write-Host "`n=== 11. REGISTRY BACKUP ==="
    $regBack = "$env:windir\System32\config\RegBack"
    if (Test-Path $regBack) {
        $regFiles = Get-ChildItem $regBack -ErrorAction SilentlyContinue
        $nonEmpty = $regFiles | Where-Object { $_.Length -gt 0 }
        if ($nonEmpty) {
            Write-Host "  RegBack files:"
            foreach ($rf in $nonEmpty) {
                $ageD = [math]::Round(((Get-Date) - $rf.LastWriteTime).TotalDays, 1)
                $sizeKB = [math]::Round($rf.Length / 1KB, 0)
                $fresh = if ($ageD -gt 30) { " [STALE]" } else { "" }
                Write-Host "    $($rf.Name) - ${sizeKB}KB - ${ageD} days old${fresh}"
            }
        } else {
            Write-Host "  RegBack exists but files are 0 bytes (disabled since Win10 1803)." -ForegroundColor Yellow
            Write-Host "  To enable: schtasks /Change /TN '\Microsoft\Windows\Registry\RegIdleBackup' /Enable"
        }
    } else {
        Write-Host "  RegBack directory not found." -ForegroundColor Yellow
    }

    # 12. Summary
    Write-Host "`n=== SUMMARY ==="
    Write-Host ""
    Write-Host "  Checks completed. Review any RED or YELLOW items above."
    if (-not $Repair) {
        Write-Host ""
        Write-Host "  To repair issues, run:"
        Write-Host "    .\system_integrity_check.ps1 -Repair       (quick DISM + SFC)"
        Write-Host "    .\system_integrity_check.ps1 -Repair -Full (deep DISM scan + SFC)"
    }
    if ($pendingReboot) {
        Write-Host ""
        Write-Host "  !! A reboot is pending. Restart the system before running repairs. !!" -ForegroundColor Yellow
    }

    Write-Host "`n============================================"
    Write-Host "  DONE"
    Write-Host "============================================"
    ```

---

## Quick Reference

### Repair Commands (Correct Order)

| Step | Command | Duration | Requires Reboot |
|------|---------|----------|-----------------|
| 1 | `chkdsk C: /f /r` | 30min - 4hrs | Yes (scheduled) |
| 2 | `DISM /Online /Cleanup-Image /RestoreHealth` | 5-30 min | No |
| 3 | `sfc /scannow` | 5-20 min | Sometimes |
| 4 | `sfc /verifyonly` (confirm) | 5-20 min | No |

### Diagnostic Commands (Read-Only)

| Task | Command |
|------|---------|
| Quick component store check | `DISM /Online /Cleanup-Image /CheckHealth` |
| Deep component store scan | `DISM /Online /Cleanup-Image /ScanHealth` |
| Verify system files (no repair) | `sfc /verifyonly` |
| View SFC results | `findstr /c:"[SR]" %windir%\Logs\CBS\CBS.log` |
| View DISM log | `notepad %windir%\Logs\DISM\dism.log` |
| Check pending reboot | `Get-ItemProperty "HKLM:\SOFTWARE\...\RebootPending"` |
| Recent updates | `Get-HotFix \| Sort-Object InstalledOn -Descending` |
| Unsigned drivers | `driverquery /v \| findstr "not signed"` |
| Secure Boot status | `Confirm-SecureBootUEFI` |

### Recovery Environment Commands

| Task | Command |
|------|---------|
| Offline SFC | `sfc /scannow /offbootdir=D:\ /offwindir=D:\Windows` |
| Rebuild BCD | `bootrec /rebuildbcd` |
| Fix MBR | `bootrec /fixmbr` |
| Fix boot sector | `bootrec /fixboot` |
| Rebuild bootloader (UEFI) | `bcdboot C:\Windows /s S: /f UEFI` |
