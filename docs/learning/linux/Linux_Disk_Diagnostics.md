# Linux Disk Diagnostics: Tools, Interpretation, and Resolution

**Goal:** Diagnose disk problems on Linux using command-line tools and SMART data analysis
**Level:** Intermediate → Advanced
**Language:** English

---

**Table of Contents:** Essential Tools | SMART Data | Self-Tests | Kernel Messages | Filesystem Integrity | Surface Scan | Software RAID | SATA Link Speed | smartd Monitoring | Diagnostic Script | Cross-Platform Comparison | Quick Reference

---

A practical guide to diagnosing disk problems on Linux using command-line tools. Covers the same diagnostic methodology as the [Windows Disk Diagnostics](../windows/Windows_Disk_Diagnostics.md) guide, using Linux-native tooling.

---

## Essential Tools

| Tool | Package | Purpose |
|------|---------|---------|
| `smartctl` | `smartmontools` | SMART data query and disk self-tests |
| `lsblk` | `util-linux` | Block device layout and mount points |
| `journalctl` | `systemd` | System journal — disk errors, boot events |
| `dmesg` | `util-linux` | Kernel ring buffer — low-level hardware messages |
| `hdparm` | `hdparm` | Disk parameters, interface speed, benchmarks |
| `blkid` | `util-linux` | Filesystem type and UUID information |
| `mdadm` | `mdadm` | Software RAID status and management |
| `fsck` | `e2fsprogs` / `xfsprogs` | Filesystem check and repair |
| `badblocks` | `e2fsprogs` | Surface scan for bad sectors |

### Install smartmontools

```bash
# Debian / Ubuntu
sudo apt install smartmontools

# RHEL / CentOS / Fedora
sudo dnf install smartmontools

# Arch
sudo pacman -S smartmontools
```

---

## Step 1: Identify Block Devices

### List All Disks and Partitions

```bash
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINT,MODEL,SERIAL,ROTA,TRAN
```

- `ROTA` — `1` for spinning disk (HDD), `0` for SSD/NVMe
- `TRAN` — transport type: `sata`, `nvme`, `usb`, etc.

### Detailed Disk Information

```bash
# All SCSI/SATA/NVMe disks
lsblk -d -o NAME,SIZE,MODEL,SERIAL,TRAN,ROTA,STATE

# More detail via hdparm (SATA only)
sudo hdparm -I /dev/sda | head -40
```

The `hdparm -I` output includes the negotiated SATA link speed — useful for detecting cable issues:

```
 *	Gen1 signaling speed (1.5Gb/s)
 *	Gen2 signaling speed (3.0Gb/s)
 *	Gen3 signaling speed (6.0Gb/s)
```

!!! tip "Link Speed Mismatch"
    If the drive supports Gen3 but only negotiates Gen1, suspect a cable or port problem.

---

## Step 2: Query SMART Data

`smartctl` from the `smartmontools` package is the primary tool for reading SMART attributes on Linux. It provides far more detail than any GUI tool.

### Quick Health Check

```bash
# Overall health assessment
sudo smartctl -H /dev/sda

# Result is either PASSED or FAILED
# FAILED means the drive's own firmware considers it end-of-life
```

### Full SMART Attribute Dump

```bash
sudo smartctl -A /dev/sda
```

Example output (SATA HDD):

```
ID# ATTRIBUTE_NAME          FLAG     VALUE WORST THRESH TYPE      UPDATED  RAW_VALUE
  1 Raw_Read_Error_Rate     0x002f   200   200   051    Pre-fail  Always       0
  5 Reallocated_Sector_Ct   0x0033   200   200   140    Pre-fail  Always       0
  7 Seek_Error_Rate         0x002e   200   200   000    Old_age   Always       0
  9 Power_On_Hours          0x0032   082   082   000    Old_age   Always   13156
197 Current_Pending_Sector  0x0012   200   200   000    Old_age   Always       0
198 Offline_Uncorrectable   0x0010   100   253   000    Old_age   Offline      0
199 UDMA_CRC_Error_Count    0x003e   200   200   000    Old_age   Always      62
```

### Complete Drive Information

```bash
# Everything: identity, SMART attributes, error log, self-test log
sudo smartctl -a /dev/sda

# For NVMe drives
sudo smartctl -a /dev/nvme0
```

### Interpreting SMART Attributes

#### Disk Surface Health

| ID | Attribute | Healthy | Critical |
|----|-----------|---------|----------|
| **5** | `Reallocated_Sector_Ct` | 0 | > 0 means bad sectors exist; rising count = active degradation |
| **196** | `Reallocated_Event_Count` | 0 | Number of remap operations performed |
| **197** | `Current_Pending_Sector` | 0 | Sectors queued for reallocation test |
| **198** | `Offline_Uncorrectable` | 0 | Unreadable sectors — data has been lost |

#### Interface Health (SATA Cable / Port)

| ID | Attribute | Healthy | Critical |
|----|-----------|---------|----------|
| **199** | `UDMA_CRC_Error_Count` | 0 | **Any value > 0 = data transfer error.** Almost always a cable, connector, or port issue — not the disk itself. |

#### General Indicators

| ID | Attribute | Notes |
|----|-----------|-------|
| **1** | `Raw_Read_Error_Rate` | Vendor-specific. WD uses a complex formula — a nonzero raw value is normal. |
| **9** | `Power_On_Hours` | Total runtime. HDDs: 30,000–50,000 hours typical lifespan. |
| **10** | `Spin_Retry_Count` | Failed spin-up attempts. May indicate PSU or mechanical issues. |
| **194** | `Temperature_Celsius` | Operating temp. HDDs: keep below 45°C; SSDs: below 70°C. |

### The Decision Tree

```
SMART Analysis
│
├── smartctl -H says FAILED?
│   └── YES → Drive firmware declares end-of-life. Replace immediately.
│
├── Reallocated_Sector_Ct (5) > 0?
│   ├── YES + rising → Disk is dying. Back up and replace.
│   └── YES + stable → Old damage, currently stable. Monitor closely.
│
├── Current_Pending_Sector (197) > 0?
│   └── YES → Sectors pending reallocation. May clear after rewrite,
│             or may grow. Run self-test: smartctl -t long /dev/sdX
│
├── UDMA_CRC_Error_Count (199) > 0?
│   └── YES → Cable / port / connector problem.
│             Replace cable, try different port, check power.
│
└── All zeros but I/O errors in dmesg?
    └── Intermittent connection. Reseat cables, check PSU.
```

---

## Step 3: Run SMART Self-Tests

`smartctl` can instruct the drive to run internal diagnostics.

```bash
# Short self-test (~2 minutes)
sudo smartctl -t short /dev/sda

# Extended self-test (can take hours on large drives)
sudo smartctl -t long /dev/sda

# Check self-test results
sudo smartctl -l selftest /dev/sda
```

Example result:

```
Num  Test_Description  Status                  Remaining  LifeTime  LBA_of_first_error
# 1  Extended offline  Completed without error       00%     13158  -
# 2  Short offline     Completed without error       00%     13156  -
```

!!! warning "Self-Test Failure"
    If a test shows `Completed: read failure` with an LBA address, that's a confirmed bad sector.

---

## Step 4: Check Kernel Messages

### Recent Disk Errors from dmesg

```bash
# Filter for disk-related messages
sudo dmesg -T | grep -iE 'ata[0-9]|sd[a-z]|error|i/o|reset|failed|exception|timeout|sector'
```

### Key dmesg Patterns

| Pattern | Meaning |
|---------|---------|
| `ata1.00: exception Emask` | ATA command failed — cable, port, or disk issue |
| `ata1.00: failed command: READ FPDMA` | Read operation failed (NCQ) |
| `ata1: SATA link up at 1.5 Gbps` | Link negotiated below capability — cable problem |
| `ata1: SATA link down` | Drive disconnected or cable completely failed |
| `ata1.00: status: { DRDY ERR }` | Drive reported an error |
| `ata1.00: error: { UNC }` | Uncorrectable read error — bad sector |
| `ata1.00: error: { ICRC ABRT }` | **CRC error — SATA cable problem** |
| `sd 0:0:0:0: [sda] tag#X CRC error` | Data transfer corruption |
| `I/O error, dev sda, sector XXXXX` | Kernel could not read/write a specific sector |
| `Buffer I/O error on dev sda` | Higher-level I/O failure |
| `EXT4-fs error: ... I/O failure` | Filesystem detected the I/O error |
| `ata1.00: revalidation failed` | Drive re-identification failed after error |
| `ata1: hard reset` / `soft reset` | Controller reset the link to recover from errors |

!!! warning "ICRC Errors"
    The pattern `error: { ICRC ABRT }` in dmesg is the Linux equivalent of SMART attribute 199 (UDMA CRC Error Count). It confirms a **SATA interface problem** — the data arriving over the cable has CRC mismatches. Replace the cable.

### Using journalctl for Historical Analysis

```bash
# All disk-related messages from the last 7 days
journalctl --since "7 days ago" -k | grep -iE 'ata[0-9]|sd[a-z]|error|i/o|reset|sector'

# Boot-related messages
journalctl --list-boots

# Messages from a specific boot (e.g., the failed one)
journalctl -b -1 -k | grep -iE 'ata|disk|error|i/o'

# Count ATA errors per device
journalctl --since "7 days ago" -k | grep -c 'ata1'
journalctl --since "7 days ago" -k | grep -c 'ata2'
```

---

## Step 5: Check Filesystem Integrity

### ext4

```bash
# Check (read-only, can run on mounted filesystem)
sudo e2fsck -n /dev/sda1

# Repair (filesystem must be unmounted!)
sudo umount /dev/sda1
sudo e2fsck -f -y /dev/sda1
```

### XFS

```bash
# Check
sudo xfs_repair -n /dev/sda1

# Repair (filesystem must be unmounted!)
sudo umount /dev/sda1
sudo xfs_repair /dev/sda1
```

### Btrfs

```bash
# Check (can run on mounted filesystem in read-only mode)
sudo btrfs check /dev/sda1

# Scrub (online verification — preferred method)
sudo btrfs scrub start /mountpoint
sudo btrfs scrub status /mountpoint
```

!!! danger "Never Run fsck on a Mounted Filesystem"
    Running `e2fsck` or `xfs_repair` on a mounted filesystem can cause catastrophic data corruption. Always unmount first. For the root filesystem, boot from a live USB.

---

## Step 6: Surface Scan with badblocks

For a thorough sector-by-sector scan when SMART data is inconclusive:

```bash
# Read-only test (safe, non-destructive)
sudo badblocks -sv /dev/sda

# Read-write test (DESTROYS ALL DATA — use only on empty disks)
sudo badblocks -wsv /dev/sda
```

- `-s` shows progress
- `-v` verbose output
- `-w` write mode — writes patterns, reads back, compares

!!! info "Performance Note"
    This takes a long time on large drives. Use SMART self-tests first; reserve `badblocks` for cases where SMART data is unavailable or suspect.

---

## Step 7: Check Software RAID (mdadm)

If the system uses Linux software RAID:

```bash
# RAID array status
cat /proc/mdstat

# Detailed status for a specific array
sudo mdadm --detail /dev/md0

# Check for failed/degraded members
sudo mdadm --detail /dev/md0 | grep -E 'State|Failed|Active|Spare'
```

### Key States

| State | Meaning |
|-------|---------|
| `active` | Array is healthy |
| `degraded` | One or more members failed — data is at risk |
| `rebuilding` | Array is reconstructing after a disk replacement |
| `failed` | Array has lost redundancy or is non-functional |

---

## Step 8: Monitor SATA Link Speed

```bash
# Check negotiated vs. capable link speed
sudo hdparm -I /dev/sda | grep -A5 "SATA Version"

# Or via sysfs
cat /sys/class/ata_link/link*/sata_spd
cat /sys/class/ata_link/link*/sata_spd_limit
```

A mismatch between the negotiated speed and the drive's capability is a strong indicator of cable problems:

| Drive Capability | Negotiated | Interpretation |
|-----------------|------------|----------------|
| 6.0 Gb/s | 6.0 Gb/s | Normal |
| 6.0 Gb/s | 3.0 Gb/s | Cable or port limiting speed |
| 6.0 Gb/s | 1.5 Gb/s | Bad cable — fallback to minimum |
| Any | Link down | Cable disconnected or dead |

---

## Automated Monitoring with smartd

The `smartd` daemon from `smartmontools` continuously monitors SMART data and sends alerts.

### Configuration

```bash
sudo vim /etc/smartd.conf
```

Basic configuration — monitor all drives, email on problems:

```
# Monitor all drives, check every 12 hours, email on issues
DEVICESCAN -a -o on -S on -n standby,q -s (S/../.././02|L/../../6/03) -W 4,45,55 -m admin@example.com
```

Flags explained:

| Flag | Meaning |
|------|---------|
| `-a` | Monitor all SMART attributes |
| `-o on` | Enable automatic offline tests |
| `-S on` | Enable automatic attribute autosave |
| `-n standby,q` | Don't wake sleeping drives |
| `-s (S/../.././02\|L/../../6/03)` | Short test daily at 2am, long test Saturdays at 3am |
| `-W 4,45,55` | Warn at 45°C, critical at 55°C, report if temp rises 4°C between checks |
| `-m email` | Send alerts to this address |

### Enable and Start

```bash
sudo systemctl enable smartd
sudo systemctl start smartd

# Check status
sudo systemctl status smartd
```

---

## The Complete Diagnostic Script

Save as `disk_diag.sh` and run with `sudo`:

??? note "disk_diag.sh — Click to expand"

    ```bash
    #!/bin/bash
    # Linux Disk Diagnostics Script
    # Usage: sudo ./disk_diag.sh | tee disk_report.txt

    set -euo pipefail

    echo "============================================"
    echo "  DISK DIAGNOSTICS REPORT"
    echo "  $(date)"
    echo "============================================"

    # 1. Block Device Layout
    echo ""
    echo "--- BLOCK DEVICES ---"
    lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINT,MODEL,SERIAL,ROTA,TRAN

    # 2. SMART Health for Each Disk
    echo ""
    echo "--- SMART HEALTH STATUS ---"
    for disk in /dev/sd? /dev/nvme?n1; do
        [ -b "$disk" ] || continue
        echo ""
        echo "  Disk: $disk"
        model=$(smartctl -i "$disk" 2>/dev/null | grep "Device Model\|Model Number" | head -1 | sed 's/.*: *//')
        serial=$(smartctl -i "$disk" 2>/dev/null | grep "Serial Number" | head -1 | sed 's/.*: *//')
        echo "  Model:  $model"
        echo "  Serial: $serial"

        health=$(smartctl -H "$disk" 2>/dev/null | grep -i "result\|status" | head -1)
        echo "  Health: $health"

        # Key SMART attributes (SATA)
        smartctl -A "$disk" 2>/dev/null | awk '
            $1 == 5   { printf "    Reallocated Sectors:  %s\n", $NF }
            $1 == 196 { printf "    Reallocation Events: %s\n", $NF }
            $1 == 197 { printf "    Current Pending:     %s\n", $NF }
            $1 == 198 { printf "    Offline Uncorrect:   %s\n", $NF }
            $1 == 199 { printf "    CRC Error Count:     %s%s\n", $NF, ($NF > 0 ? " <<<< CABLE ISSUE!" : "") }
            $1 == 9   { printf "    Power-On Hours:      %s\n", $NF }
            $1 == 194 { printf "    Temperature (C):     %s\n", $NF }
        '

        # NVMe attributes
        smartctl -A "$disk" 2>/dev/null | awk '
            /Percentage Used/    { printf "    Percentage Used:     %s\n", $NF }
            /Media.*Integrity/   { printf "    Media Errors:        %s\n", $NF }
            /Power On Hours/     { printf "    Power-On Hours:      %s\n", $NF }
            /Temperature:/       { printf "    Temperature:         %s\n", $NF }
        '
    done

    # 3. SATA Link Speeds
    echo ""
    echo "--- SATA LINK SPEEDS ---"
    for link in /sys/class/ata_link/link*; do
        [ -d "$link" ] || continue
        spd=$(cat "$link/sata_spd" 2>/dev/null || echo "N/A")
        limit=$(cat "$link/sata_spd_limit" 2>/dev/null || echo "N/A")
        name=$(basename "$link")
        echo "  $name: negotiated=$spd limit=$limit"
    done

    # 4. Kernel Disk Errors
    echo ""
    echo "--- KERNEL DISK ERRORS (recent) ---"
    dmesg -T 2>/dev/null | grep -iE 'ata[0-9].*error|ata[0-9].*exception|ata[0-9].*reset|sd[a-z].*error|i/o error|ICRC|CRC|bad sector|medium error' | tail -30 || echo "  No disk errors in kernel ring buffer."

    # 5. Journal Disk Errors (last 7 days)
    echo ""
    echo "--- JOURNALCTL DISK ERRORS (last 7 days) ---"
    error_count=$(journalctl --since "7 days ago" -k 2>/dev/null | grep -ciE 'ata.*error|i/o error|reset|ICRC' || true)
    echo "  Total disk-related error messages: $error_count"

    if [ "$error_count" -gt 0 ]; then
        echo ""
        journalctl --since "7 days ago" -k 2>/dev/null | grep -iE 'ata.*error|ata.*exception|i/o error|ICRC|reset' | tail -20
    fi

    # 6. Boot History
    echo ""
    echo "--- RECENT BOOTS ---"
    journalctl --list-boots 2>/dev/null | tail -10

    # 7. Software RAID Status
    echo ""
    echo "--- SOFTWARE RAID STATUS ---"
    if [ -f /proc/mdstat ]; then
        cat /proc/mdstat
    else
        echo "  No software RAID configured."
    fi

    # 8. Filesystem Mount Health
    echo ""
    echo "--- MOUNTED FILESYSTEMS ---"
    df -hT | grep -v tmpfs | grep -v devtmpfs

    echo ""
    echo "--- DONE ---"
    echo "For full SMART details on a specific disk: sudo smartctl -a /dev/sdX"
    ```

---

## Windows vs. Linux: Tool Comparison

| Task | Windows | Linux |
|------|---------|-------|
| List disks | `Get-PhysicalDisk` | `lsblk -d` |
| Disk-to-mount mapping | `Get-Disk` + `Get-Partition` | `lsblk` |
| SMART health check | CrystalDiskInfo | `smartctl -H /dev/sdX` |
| SMART attributes | CrystalDiskInfo (GUI) | `smartctl -A /dev/sdX` |
| SMART self-test | CrystalDiskInfo | `smartctl -t long /dev/sdX` |
| Disk errors (log) | `Get-WinEvent` (Event Log) | `journalctl -k`, `dmesg` |
| Boot history | Event IDs 41, 6008 | `journalctl --list-boots` |
| Filesystem check | `chkdsk X: /f /r` | `e2fsck`, `xfs_repair` |
| Surface scan | `chkdsk X: /r` | `badblocks -sv` |
| SATA link speed | CrystalDiskInfo Transfer Mode | `hdparm -I`, sysfs |
| Software RAID | Storage Spaces (`Get-StoragePool`) | `mdadm --detail`, `/proc/mdstat` |
| Continuous monitoring | — | `smartd` daemon |
| SATA cable diagnosis | SMART ID 199 (CRC Error Count) | SMART ID 199 + dmesg `ICRC` errors |

---

## Quick Reference: One-Liners

```bash
# Am I about to lose data?
sudo smartctl -H /dev/sda

# Is my SATA cable bad?
sudo smartctl -A /dev/sda | grep CRC

# What happened during the last crash?
journalctl -b -1 -k | grep -i error

# How many disk errors this week?
journalctl --since "7 days ago" -k | grep -c 'I/O error'

# What speed is my SATA link running at?
sudo hdparm -I /dev/sda | grep -A3 "SATA Version"

# Is my RAID healthy?
cat /proc/mdstat

# Start a long SMART self-test
sudo smartctl -t long /dev/sda

# Watch self-test progress
sudo smartctl -l selftest /dev/sda
```
