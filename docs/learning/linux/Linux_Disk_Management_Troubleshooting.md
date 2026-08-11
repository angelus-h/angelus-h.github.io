# Linux Disk Management & Troubleshooting Quick Reference

**Version:** 1.0
**Last Updated:** 2026-08-12
**Target Audience:** SRE / Sysadmin / DevOps Engineers
**Scope:** Disk identification, partitioning, filesystems, LVM, RAID, SMART, performance, troubleshooting

---

## Table of Contents

1. [Disk Identification & Information](#disk-identification--information)
2. [Partitioning](#partitioning)
3. [Filesystems](#filesystems)
4. [LVM (Logical Volume Manager)](#lvm-logical-volume-manager)
5. [RAID (mdadm)](#raid-mdadm)
6. [SMART Monitoring](#smart-monitoring)
7. [Disk Performance](#disk-performance)
8. [Troubleshooting & Recovery](#troubleshooting--recovery)
9. [SSD-Specific](#ssd-specific)
10. [Best Practices](#best-practices)

---

## Disk Identification & Information

### Device Naming Conventions

| Prefix | Type | Example | Notes |
|--------|------|---------|-------|
| `sd`   | SCSI/SATA/USB | `/dev/sda`, `/dev/sdb1` | Most common for traditional drives |
| `nvme` | NVMe SSD | `/dev/nvme0n1`, `/dev/nvme0n1p1` | `n1` = namespace, `p1` = partition |
| `vd`   | VirtIO (KVM) | `/dev/vda`, `/dev/vda1` | Virtual disks in KVM/QEMU guests |
| `xvd`  | Xen virtual | `/dev/xvda` | AWS EC2 (older instance types) |
| `hd`   | IDE (legacy) | `/dev/hda` | Rare on modern systems |
| `md`   | Software RAID | `/dev/md0` | mdadm arrays |
| `dm`   | Device Mapper | `/dev/dm-0` | LVM, LUKS, multipath |

### Essential Discovery Commands

#### lsblk -- Block Device Tree

```bash
# Basic tree view
lsblk

# Detailed output with filesystem, UUID, mount point
lsblk -f

# Full output including size, type, and model
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINT,MODEL,SERIAL,ROTA,DISC-MAX

# Show only disks (no partitions)
lsblk -d

# JSON output (useful for scripting)
lsblk -J
```

Example output of `lsblk -f`:

```
NAME        FSTYPE      LABEL UUID                                 MOUNTPOINT
sda
+-sda1      ext4              a1b2c3d4-...                         /boot
+-sda2      LVM2_member       e5f6a7b8-...
  +-vg0-root ext4             12345678-...                         /
  +-vg0-swap swap             abcdef01-...                         [SWAP]
nvme0n1
+-nvme0n1p1 vfat        EFI   AAAA-BBBB                           /boot/efi
+-nvme0n1p2 xfs               99887766-...                         /data
```

#### blkid -- Block Device Attributes

```bash
# Show all block devices
sudo blkid

# Query specific device
sudo blkid /dev/sda1

# Output in export format (useful in scripts)
sudo blkid -o export /dev/sda1

# Find device by UUID
sudo blkid -U "a1b2c3d4-e5f6-7890-abcd-ef1234567890"

# Find device by label
sudo blkid -L "DATA"
```

#### fdisk -- Partition Table Info

```bash
# List all disks and partitions
sudo fdisk -l

# List specific disk
sudo fdisk -l /dev/sda

# Show partition sizes in sectors
sudo fdisk -l -o Device,Start,End,Sectors,Size,Type /dev/sda
```

#### parted -- Advanced Partition Info

```bash
# Print partition table
sudo parted /dev/sda print

# Print all disks
sudo parted -l

# Show free space
sudo parted /dev/sda print free
```

#### Identifying Disk Type (HDD vs SSD vs NVMe)

```bash
# Rotational flag: 1 = HDD, 0 = SSD/NVMe
cat /sys/block/sda/queue/rotational

# Using lsblk ROTA column
lsblk -d -o NAME,ROTA,TRAN,SIZE,MODEL
# ROTA=1 -> spinning disk, ROTA=0 -> solid state
# TRAN shows transport: sata, nvme, usb, etc.

# Detailed hardware info (if hwinfo installed)
sudo hwinfo --disk --short

# udevadm for detailed device properties
udevadm info --query=all --name=/dev/sda
udevadm info --query=property --name=/dev/sda | grep -E 'ID_MODEL|ID_SERIAL|ID_BUS|ID_TYPE'

# NVMe device identification
sudo nvme list        # requires nvme-cli
sudo nvme id-ctrl /dev/nvme0 -H
```

### /dev/disk Symlinks

```bash
# Persistent identifiers (survive reboot, useful in fstab/scripts)
ls -l /dev/disk/by-uuid/
ls -l /dev/disk/by-id/
ls -l /dev/disk/by-label/
ls -l /dev/disk/by-path/
```

!!! tip "Always Use Persistent Identifiers"
    In `/etc/fstab`, scripts, and configuration, prefer `UUID=` or `/dev/disk/by-id/` over device names like `/dev/sda1`. Device names can change across reboots when disks are added or removed.

---

## Partitioning

### MBR vs GPT

| Feature | MBR (Master Boot Record) | GPT (GUID Partition Table) |
|---------|--------------------------|---------------------------|
| Max disk size | 2 TiB | 9.4 ZiB (effectively unlimited) |
| Max partitions | 4 primary (or 3 primary + 1 extended) | 128 (default, configurable) |
| Boot mode | BIOS (Legacy) | UEFI (can also work with BIOS via protective MBR) |
| Redundancy | Single copy of partition table | Primary + backup copy at end of disk |
| Data integrity | None | CRC32 checksums |
| OS support | Universal | All modern OS (Linux 2.6.28+, Win Vista+) |

!!! info "When to Use Which"
    Use **GPT** for all new installations. MBR is only needed for legacy BIOS systems or disks under 2 TiB where legacy compatibility is required.

### fdisk (MBR and GPT)

```bash
# Interactive partitioning
sudo fdisk /dev/sdb

# Common fdisk commands (inside interactive mode):
# m  - help menu
# p  - print partition table
# n  - new partition
# d  - delete partition
# t  - change partition type
# w  - write changes and exit
# q  - quit without saving
# g  - create new GPT partition table
# o  - create new MBR partition table
```

Example: Create a single Linux partition on a new disk:

```bash
sudo fdisk /dev/sdb <<EOF
g
n
1


w
EOF
```

### gdisk (GPT-specific)

```bash
# Interactive GPT partitioning
sudo gdisk /dev/sdb

# Common gdisk commands:
# p  - print partition table
# n  - new partition
# d  - delete partition
# i  - detailed partition info
# o  - create new empty GPT
# w  - write and exit
# x  - expert mode (recovery, rebuild)
```

### parted (Non-interactive Capable)

```bash
# Create GPT label
sudo parted /dev/sdb mklabel gpt

# Create partition (entire disk, ext4)
sudo parted /dev/sdb mkpart primary ext4 0% 100%

# Create multiple partitions
sudo parted /dev/sdb mkpart primary ext4 0% 50%
sudo parted /dev/sdb mkpart primary xfs 50% 100%

# Create EFI system partition
sudo parted /dev/sdb mkpart ESP fat32 1MiB 513MiB
sudo parted /dev/sdb set 1 esp on

# Resize partition (grow)
sudo parted /dev/sdb resizepart 2 100%

# Remove partition
sudo parted /dev/sdb rm 2

# Print with free space
sudo parted /dev/sdb print free
```

### Partition Alignment

Modern disks use 4096-byte (4K) physical sectors. Misaligned partitions cause severe performance degradation.

```bash
# Verify alignment
sudo parted /dev/sdb align-check optimal 1

# Check physical/logical sector size
cat /sys/block/sdb/queue/physical_block_size
cat /sys/block/sdb/queue/logical_block_size

# parted automatically aligns when you use percentages (0%, 50%, 100%)
# or MiB units -- avoid specifying sectors directly unless you know what you are doing
```

!!! warning "Alignment Matters"
    Always start partitions on 1 MiB boundaries. Use `parted` with percentage or MiB units. Misaligned partitions on SSDs can reduce performance by 10-30%.

### Inform the Kernel of Partition Changes

```bash
# Re-read partition table (classic, may fail on mounted disks)
sudo partprobe /dev/sdb

# Alternative: inform kernel of specific changes
sudo partx -a /dev/sdb        # add all new partitions
sudo partx -u /dev/sdb        # update all partitions
sudo blockdev --rereadpt /dev/sdb
```

---

## Filesystems

### Filesystem Comparison

| Feature | ext4 | XFS | Btrfs | ZFS |
|---------|------|-----|-------|-----|
| Max file size | 16 TiB | 8 EiB | 16 EiB | 16 EiB |
| Max volume size | 1 EiB | 8 EiB | 16 EiB | 256 ZiB |
| Shrink support | Yes | **No** | Yes | **No** |
| Snapshots | No (use LVM) | No (use LVM) | **Yes (native)** | **Yes (native)** |
| Checksums (data) | No | No | **Yes** | **Yes** |
| Copy-on-Write | No | No | **Yes** | **Yes** |
| Deduplication | No | No | Yes | Yes |
| RAID (built-in) | No | No | **Yes** | **Yes** |
| Maturity | Excellent | Excellent | Good | Excellent |
| Default on | Debian, Ubuntu | RHEL 8+, Fedora | SUSE, Fedora (desktop) | FreeBSD, Proxmox |
| Best use case | General purpose | Large files, databases | Desktops, NAS | Enterprise storage |

### Creating Filesystems (mkfs)

```bash
# ext4 (general purpose)
sudo mkfs.ext4 /dev/sdb1
sudo mkfs.ext4 -L "DATA" /dev/sdb1                 # with label
sudo mkfs.ext4 -m 1 -L "DATA" /dev/sdb1            # 1% reserved (default 5%)

# XFS (high performance)
sudo mkfs.xfs /dev/sdb1
sudo mkfs.xfs -L "FAST" /dev/sdb1
sudo mkfs.xfs -f /dev/sdb1                          # force (overwrite existing)

# Btrfs
sudo mkfs.btrfs /dev/sdb1
sudo mkfs.btrfs -L "POOL" -m dup -d single /dev/sdb1  # dup metadata, single data

# FAT32 (EFI, USB)
sudo mkfs.vfat -F 32 -n "EFI" /dev/sdb1
```

### Mounting Filesystems

```bash
# Basic mount
sudo mount /dev/sdb1 /mnt/data

# Mount with specific options
sudo mount -o noatime,nodiratime /dev/sdb1 /mnt/data

# Mount by UUID
sudo mount UUID="a1b2c3d4-..." /mnt/data

# Mount by label
sudo mount LABEL="DATA" /mnt/data

# Mount read-only
sudo mount -o ro /dev/sdb1 /mnt/data

# Remount read-write (without unmounting)
sudo mount -o remount,rw /mnt/data

# Show all mounts
mount | column -t
findmnt              # tree view
findmnt -t ext4,xfs  # filter by filesystem type

# Unmount
sudo umount /mnt/data
sudo umount -l /mnt/data   # lazy unmount (detach, clean up when idle)
sudo umount -f /mnt/data   # force (NFS stale mounts)
```

### /etc/fstab

```bash
# Format:
# <device>                                <mountpoint>  <type>  <options>        <dump> <fsck>
UUID=a1b2c3d4-e5f6-7890-abcd-ef1234567890 /data         ext4    defaults,noatime  0      2
UUID=99887766-5544-3322-1100-aabbccddeeff /var/log      xfs     defaults          0      2
/dev/mapper/vg0-swap                       none          swap    sw                0      0
LABEL=BACKUP                               /mnt/backup   ext4    defaults,nofail   0      2
```

| fstab Field | Purpose |
|------------|---------|
| `device` | UUID, LABEL, or device path |
| `mountpoint` | Where to mount |
| `type` | Filesystem type (ext4, xfs, swap, etc.) |
| `options` | Mount options (see below) |
| `dump` | 0 = no dump, 1 = dump (rarely used) |
| `fsck` | 0 = skip, 1 = root first, 2 = other partitions |

Common mount options:

| Option | Effect |
|--------|--------|
| `defaults` | rw, suid, dev, exec, auto, nouser, async |
| `noatime` | Do not update access time (improves performance) |
| `nodiratime` | Do not update directory access time |
| `nofail` | Do not fail boot if device is missing |
| `ro` / `rw` | Read-only / read-write |
| `nosuid` | Ignore SUID bits |
| `noexec` | Prevent execution of binaries |
| `discard` | Enable continuous TRIM for SSDs |
| `_netdev` | Wait for network before mounting (NFS, iSCSI) |

!!! danger "Test fstab Before Rebooting"
    A typo in `/etc/fstab` can prevent the system from booting. Always test after editing:
    ```bash
    sudo mount -a
    # If no errors, fstab is valid
    # Also verify with:
    sudo findmnt --verify
    ```

### Filesystem Tuning

#### ext4 Tuning (tune2fs)

```bash
# Show filesystem parameters
sudo tune2fs -l /dev/sdb1

# Reduce reserved space (default 5%, too much for large data volumes)
sudo tune2fs -m 1 /dev/sdb1          # set to 1%
sudo tune2fs -m 0 /dev/sdb1          # set to 0% (data-only volumes)

# Set filesystem label
sudo tune2fs -L "MYDATA" /dev/sdb1

# Set mount count interval for fsck
sudo tune2fs -c 30 /dev/sdb1         # fsck every 30 mounts
sudo tune2fs -c 0 /dev/sdb1          # disable mount-count-based fsck

# Set time-based fsck interval
sudo tune2fs -i 6m /dev/sdb1         # fsck every 6 months
sudo tune2fs -i 0 /dev/sdb1          # disable time-based fsck

# Enable/disable features
sudo tune2fs -O ^has_journal /dev/sdb1  # disable journal (not recommended)
```

#### XFS Tuning (xfs_admin)

```bash
# Show filesystem info
sudo xfs_info /dev/sdb1

# Set label
sudo xfs_admin -L "FAST" /dev/sdb1

# Generate new UUID
sudo xfs_admin -U generate /dev/sdb1

# Show UUID
sudo xfs_admin -u /dev/sdb1
```

### Resizing Filesystems

#### ext4

```bash
# Grow ext4 to fill partition (online, while mounted)
sudo resize2fs /dev/sdb1

# Grow to specific size
sudo resize2fs /dev/sdb1 50G

# Shrink ext4 (MUST unmount first)
sudo umount /mnt/data
sudo e2fsck -f /dev/sdb1             # required before shrinking
sudo resize2fs /dev/sdb1 30G
```

#### XFS

```bash
# Grow XFS (online, must be mounted -- XFS cannot be shrunk)
sudo xfs_growfs /mnt/data

# Grow to specific size (in blocks)
sudo xfs_growfs -D 1048576 /mnt/data
```

!!! warning "XFS Cannot Be Shrunk"
    XFS does not support shrinking. If you need to reduce an XFS volume, back up data, recreate the filesystem at the smaller size, and restore.

---

## LVM (Logical Volume Manager)

### Concepts

```
Physical Disks/Partitions       LVM Layer                  Filesystems
+----------+  +----------+
| /dev/sda1|  | /dev/sdb1|     Physical Volumes (PV)
+----+-----+  +----+-----+
     |             |
     +------+------+
            |
     +------v------+
     |  Volume      |              Volume Group (VG)
     |  Group: vg0  |
     +--+----+---+--+
        |    |   |
     +--v-+ +v--+ +-v---+
     | lv | |lv | | lv  |         Logical Volumes (LV)
     |root| |var| |home |
     +--+-+ +--++ +--+--+
        |     |      |
      ext4   xfs   ext4           Filesystems
```

| Component | Description | Analogy |
|-----------|-------------|---------|
| **PV** (Physical Volume) | Physical disk or partition initialized for LVM | Raw material |
| **VG** (Volume Group) | Pool of one or more PVs | Warehouse |
| **LV** (Logical Volume) | Virtual partition carved from a VG | Shelf in warehouse |
| **PE** (Physical Extent) | Smallest allocation unit (default 4 MiB) | Building block |

### Creating LVM from Scratch

```bash
# Step 1: Create Physical Volumes
sudo pvcreate /dev/sdb1
sudo pvcreate /dev/sdc1

# Step 2: Create Volume Group
sudo vgcreate vg_data /dev/sdb1 /dev/sdc1

# Step 3: Create Logical Volumes
sudo lvcreate -L 50G -n lv_app vg_data        # fixed size
sudo lvcreate -l 100%FREE -n lv_logs vg_data   # all remaining space
sudo lvcreate -l 50%VG -n lv_db vg_data        # 50% of VG

# Step 4: Create filesystem and mount
sudo mkfs.ext4 /dev/vg_data/lv_app
sudo mkdir -p /app
sudo mount /dev/vg_data/lv_app /app

# Step 5: Add to fstab
echo '/dev/vg_data/lv_app /app ext4 defaults 0 2' | sudo tee -a /etc/fstab
```

### Inspecting LVM

```bash
# Physical Volumes
sudo pvs                    # summary
sudo pvdisplay               # detailed
sudo pvdisplay /dev/sdb1     # specific PV

# Volume Groups
sudo vgs                    # summary
sudo vgdisplay               # detailed
sudo vgdisplay vg_data       # specific VG

# Logical Volumes
sudo lvs                    # summary
sudo lvdisplay               # detailed
sudo lvdisplay /dev/vg_data/lv_app  # specific LV

# All at once
sudo lvm fullreport
```

### Extending Volumes

```bash
# --- Extend LV + Filesystem (most common operation) ---

# Extend LV by 10G and resize filesystem in one command
sudo lvextend -L +10G --resizefs /dev/vg_data/lv_app

# Extend LV to 100G total
sudo lvextend -L 100G --resizefs /dev/vg_data/lv_app

# Extend LV to use all free space in VG
sudo lvextend -l +100%FREE --resizefs /dev/vg_data/lv_app

# --- Add new disk to VG ---

# Step 1: Create PV on new disk
sudo pvcreate /dev/sdd1

# Step 2: Extend VG
sudo vgextend vg_data /dev/sdd1

# Step 3: Now extend LV using the new space
sudo lvextend -l +100%FREE --resizefs /dev/vg_data/lv_app
```

!!! tip "Use --resizefs"
    The `--resizefs` flag on `lvextend` automatically grows the filesystem after extending the LV. This works for ext4 and XFS. Without it, you need a separate `resize2fs` or `xfs_growfs` step.

### Shrinking Volumes

!!! danger "Shrinking Requires Care"
    Shrinking must be done in the correct order: filesystem first, then LV. Shrinking the LV before the filesystem causes data loss. XFS cannot be shrunk at all.

```bash
# Shrink ext4 LV (must unmount first)
sudo umount /app
sudo e2fsck -f /dev/vg_data/lv_app
sudo lvreduce -L 30G --resizefs /dev/vg_data/lv_app
sudo mount /app
```

### LVM Snapshots

```bash
# Create snapshot (requires free space in VG)
sudo lvcreate -L 5G -s -n lv_app_snap /dev/vg_data/lv_app

# List snapshots
sudo lvs -o lv_name,lv_size,origin,snap_percent

# Mount snapshot (read-only for backup)
sudo mkdir -p /mnt/snap
sudo mount -o ro /dev/vg_data/lv_app_snap /mnt/snap

# Restore from snapshot (WARNING: destroys current LV data)
sudo umount /app
sudo lvconvert --merge /dev/vg_data/lv_app_snap
# Reboot or reactivate:
sudo vgchange -an vg_data && sudo vgchange -ay vg_data
sudo mount /app

# Remove snapshot
sudo umount /mnt/snap
sudo lvremove /dev/vg_data/lv_app_snap
```

!!! warning "Snapshot Overflow"
    If a snapshot runs out of allocated space, it becomes invalid. Monitor usage with `lvs -o snap_percent`. Size snapshots generously for the expected change rate.

### LVM Thin Provisioning

Thin provisioning allows over-committing storage. Logical volumes draw from a shared thin pool and only consume physical space as data is written.

```bash
# Create thin pool (80% of VG for data, rest for metadata)
sudo lvcreate -L 100G --thinpool thin_pool vg_data

# Create thin volumes (can exceed pool size -- overprovisioned)
sudo lvcreate -V 50G --thin -n thin_vm1 vg_data/thin_pool
sudo lvcreate -V 50G --thin -n thin_vm2 vg_data/thin_pool
sudo lvcreate -V 50G --thin -n thin_vm3 vg_data/thin_pool  # 150G from 100G pool

# Monitor thin pool usage (critical)
sudo lvs -o lv_name,lv_size,data_percent,metadata_percent vg_data/thin_pool

# Extend thin pool when needed
sudo lvextend -L +50G vg_data/thin_pool
```

!!! danger "Monitor Thin Pool Usage"
    If a thin pool fills to 100%, all thin volumes using it become unresponsive. Set up monitoring alerts at 80% usage. Automate extension or use `dmeventd` for automatic pool extension.

### Removing LVM Components

```bash
# Remove LV
sudo umount /app
sudo lvremove /dev/vg_data/lv_app

# Remove VG
sudo vgremove vg_data

# Remove PV label
sudo pvremove /dev/sdb1

# Move data off a PV before removing it from VG
sudo pvmove /dev/sdb1              # move extents to other PVs in the VG
sudo vgreduce vg_data /dev/sdb1    # then remove from VG
```

---

## RAID (mdadm)

### RAID Level Comparison

| Level | Min Disks | Usable Capacity | Fault Tolerance | Read Perf | Write Perf | Use Case |
|-------|-----------|-----------------|-----------------|-----------|------------|----------|
| **RAID 0** | 2 | N x disk | **None** | Excellent | Excellent | Scratch/temp (never for important data) |
| **RAID 1** | 2 | 1 x disk | 1 disk failure | Good | Normal | Boot volumes, OS |
| **RAID 5** | 3 | (N-1) x disk | 1 disk failure | Good | Moderate | General storage |
| **RAID 6** | 4 | (N-2) x disk | 2 disk failures | Good | Slower | Large arrays, archival |
| **RAID 10** | 4 | N/2 x disk | 1 per mirror pair | Excellent | Good | Databases, high IOPS |

!!! warning "RAID 5 on Large Disks"
    With drives larger than 2 TB, the probability of an unrecoverable read error (URE) during rebuild is significant. Prefer RAID 6 or RAID 10 for large drives.

### Creating RAID Arrays

```bash
# RAID 1 (mirror)
sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1

# RAID 5
sudo mdadm --create /dev/md0 --level=5 --raid-devices=3 /dev/sdb1 /dev/sdc1 /dev/sdd1

# RAID 6
sudo mdadm --create /dev/md0 --level=6 --raid-devices=4 \
  /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1

# RAID 10
sudo mdadm --create /dev/md0 --level=10 --raid-devices=4 \
  /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1

# RAID 5 with a hot spare
sudo mdadm --create /dev/md0 --level=5 --raid-devices=3 \
  --spare-devices=1 /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1

# Save configuration
sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf
sudo update-initramfs -u    # Debian/Ubuntu
sudo dracut -f               # RHEL/Fedora
```

### Monitoring RAID

```bash
# Real-time rebuild status
cat /proc/mdstat

# Detailed array info
sudo mdadm --detail /dev/md0

# Check array state
sudo mdadm --detail /dev/md0 | grep -E 'State|Devices|Failed'

# Monitor daemon (sends email on events)
sudo mdadm --monitor --mail=admin@example.com --delay=300 /dev/md0 &

# Watch rebuild progress
watch -n 5 cat /proc/mdstat
```

Example `/proc/mdstat` during rebuild:

```
Personalities : [raid1] [raid5] [raid6]
md0 : active raid5 sdd1[3] sdb1[0] sdc1[1]
      2093056 blocks super 1.2 level 5, 512k chunk, algorithm 2 [3/2] [UU_]
      [==>..................]  recovery = 12.5% (131072/1046528) finish=2.1min speed=65536K/sec
```

### Failed Disk Replacement Procedure

```bash
# Step 1: Identify the failed disk
sudo mdadm --detail /dev/md0
# Look for "faulty" or "removed" in the output

# Step 2: Mark as failed (if not already)
sudo mdadm /dev/md0 --fail /dev/sdc1

# Step 3: Remove from array
sudo mdadm /dev/md0 --remove /dev/sdc1

# Step 4: Physically replace the disk
# (power down if hot-swap not supported)

# Step 5: Partition new disk (match existing partition layout)
sudo sfdisk -d /dev/sdb | sudo sfdisk /dev/sdc

# Step 6: Add new disk to array
sudo mdadm /dev/md0 --add /dev/sdc1

# Step 7: Monitor rebuild
watch -n 5 cat /proc/mdstat

# Step 8: Update config after rebuild completes
sudo mdadm --detail --scan | sudo tee /etc/mdadm/mdadm.conf
sudo update-initramfs -u
```

### Stopping and Removing Arrays

```bash
# Stop array
sudo mdadm --stop /dev/md0

# Remove superblock from member disks
sudo mdadm --zero-superblock /dev/sdb1
sudo mdadm --zero-superblock /dev/sdc1

# Remove from config
sudo vi /etc/mdadm/mdadm.conf   # remove the ARRAY line
sudo update-initramfs -u
```

---

## SMART Monitoring

### smartctl Basics

```bash
# Install
sudo apt install smartmontools    # Debian/Ubuntu
sudo dnf install smartmontools    # RHEL/Fedora

# Check if SMART is supported and enabled
sudo smartctl -i /dev/sda

# Enable SMART on a disk
sudo smartctl -s on /dev/sda

# Full SMART health summary
sudo smartctl -a /dev/sda

# Quick health check (pass/fail)
sudo smartctl -H /dev/sda

# NVMe health
sudo smartctl -a /dev/nvme0
```

### Key SMART Attributes

| ID | Attribute Name | What It Means | Warning Threshold |
|----|---------------|---------------|-------------------|
| 5 | **Reallocated Sector Count** | Bad sectors remapped to spares | Any non-zero and rising |
| 10 | Spin Retry Count | Drive needed multiple attempts to spin up | Any increase |
| 187 | **Reported Uncorrectable Errors** | Errors that ECC could not fix | Any non-zero |
| 188 | Command Timeout | Drive did not respond in time | Increasing trend |
| 197 | **Current Pending Sector Count** | Sectors waiting to be remapped | Any non-zero |
| 198 | **Offline Uncorrectable** | Sectors that failed offline scan | Any non-zero |
| 199 | UDMA CRC Error Count | Cable/interface errors | Any non-zero (check cables first) |
| 200 | Write Error Rate | Errors during write operations | Increasing trend |

!!! danger "Immediate Replacement Indicators"
    If **Reallocated Sector Count (5)**, **Current Pending Sector Count (197)**, or **Offline Uncorrectable (198)** are non-zero and increasing, the disk is failing. Back up data immediately and plan replacement.

### NVMe SMART / Health Attributes

```bash
sudo smartctl -a /dev/nvme0

# Key NVMe health fields:
# - Critical Warning:          should be 0x00
# - Temperature:               check against warranty spec
# - Available Spare:           percentage of spare blocks remaining
# - Available Spare Threshold: when Available Spare drops below this, replace
# - Percentage Used:           estimated life consumed (100% = end of rated life)
# - Media and Data Integrity Errors: should be 0
```

### Running Self-Tests

```bash
# Short self-test (1-2 minutes)
sudo smartctl -t short /dev/sda

# Long self-test (hours, thorough)
sudo smartctl -t long /dev/sda

# Conveyance test (after transport)
sudo smartctl -t conveyance /dev/sda

# Check test results
sudo smartctl -l selftest /dev/sda

# Estimate long test duration
sudo smartctl -c /dev/sda | grep "Extended self-test"
```

### Automated Monitoring with smartd

```bash
# Configuration file
sudo vi /etc/smartd.conf

# Monitor all disks, email on problems
DEVICESCAN -d removable -n standby -m admin@example.com -M exec /usr/share/smartmontools/smartd_warning.sh

# Monitor specific disk with settings
/dev/sda -a -o on -S on -s (S/../.././02|L/../../6/03) -m admin@example.com
# -a         : monitor all attributes
# -o on      : enable offline testing
# -S on      : enable attribute autosave
# -s (...):  : schedule: short test daily at 2AM, long test Saturdays at 3AM
# -m         : email on failure

# Start/enable the service
sudo systemctl enable --now smartd
sudo systemctl status smartd
```

---

## Disk Performance

### Monitoring I/O

#### iostat

```bash
# Install (part of sysstat)
sudo apt install sysstat     # Debian/Ubuntu
sudo dnf install sysstat     # RHEL/Fedora

# Basic I/O stats
iostat

# Extended stats, every 2 seconds, 5 iterations
iostat -xz 2 5

# Key columns to watch:
# %util    - percentage of time device was busy (>80% = saturated)
# await    - average I/O wait time in ms (high = bottleneck)
# r_await  - average read wait time
# w_await  - average write wait time
# avgqu-sz - average queue depth
# r/s, w/s - reads/writes per second
```

#### iotop

```bash
# Install
sudo apt install iotop       # Debian/Ubuntu
sudo dnf install iotop       # RHEL/Fedora

# Show per-process I/O (requires root)
sudo iotop

# Show only processes doing I/O
sudo iotop -o

# Batch mode (for logging)
sudo iotop -b -n 5 -d 2     # 5 iterations, 2-second delay

# Alternative: iotop-c (no Python dependency)
sudo iotop-c
```

#### dstat

```bash
# Combined CPU, disk, net, memory stats
dstat

# Focus on disk I/O
dstat -d

# Detailed disk + CPU
dstat -cdnm --disk-util

# Per-disk stats
dstat -D sda,sdb -d
```

### Benchmarking with fio

```bash
# Install
sudo apt install fio         # Debian/Ubuntu
sudo dnf install fio         # RHEL/Fedora

# --- Sequential read throughput ---
fio --name=seq-read --ioengine=libaio --direct=1 --bs=1M \
    --size=1G --numjobs=1 --rw=read --filename=/tmp/fio-test

# --- Sequential write throughput ---
fio --name=seq-write --ioengine=libaio --direct=1 --bs=1M \
    --size=1G --numjobs=1 --rw=write --filename=/tmp/fio-test

# --- Random read IOPS (4K, typical database workload) ---
fio --name=rand-read --ioengine=libaio --direct=1 --bs=4k \
    --size=1G --numjobs=4 --iodepth=32 --rw=randread --filename=/tmp/fio-test

# --- Random write IOPS ---
fio --name=rand-write --ioengine=libaio --direct=1 --bs=4k \
    --size=1G --numjobs=4 --iodepth=32 --rw=randwrite --filename=/tmp/fio-test

# --- Mixed random read/write (70/30) ---
fio --name=mixed --ioengine=libaio --direct=1 --bs=4k \
    --size=1G --numjobs=4 --iodepth=32 --rw=randrw --rwmixread=70 \
    --filename=/tmp/fio-test

# Clean up
rm -f /tmp/fio-test
```

!!! tip "fio Tips"
    Use `--direct=1` to bypass the page cache and test actual disk performance. Use `--runtime=60 --time_based` for time-limited tests instead of writing a fixed size. Always test on the actual target filesystem/device.

### hdparm

```bash
# Buffered read speed (includes cache)
sudo hdparm -t /dev/sda

# Cached read speed (pure memory)
sudo hdparm -T /dev/sda

# Combined test
sudo hdparm -tT /dev/sda

# Get drive info
sudo hdparm -I /dev/sda

# Check write-caching status
sudo hdparm -W /dev/sda

# Enable write cache (improves performance, risk on power loss)
sudo hdparm -W1 /dev/sda

# Disable write cache (safer)
sudo hdparm -W0 /dev/sda
```

### I/O Scheduler Tuning

```bash
# Check current scheduler
cat /sys/block/sda/queue/scheduler
# Output example: [mq-deadline] bfq none kyber

# Change scheduler (runtime, not persistent)
echo "bfq" | sudo tee /sys/block/sda/queue/scheduler

# Make persistent via udev rule
# /etc/udev/rules.d/60-ioscheduler.rules
# For SSDs/NVMe:
ACTION=="add|change", KERNEL=="sd[a-z]", ATTR{queue/rotational}=="0", ATTR{queue/scheduler}="none"
# For HDDs:
ACTION=="add|change", KERNEL=="sd[a-z]", ATTR{queue/rotational}=="1", ATTR{queue/scheduler}="bfq"
```

| Scheduler | Best For | Description |
|-----------|----------|-------------|
| `none` (noop) | NVMe, fast SSDs | Minimal overhead, lets device handle ordering |
| `mq-deadline` | SSDs, databases | Deadline-based, prevents starvation, low latency |
| `bfq` | HDDs, desktops | Budget Fair Queuing, good for interactive use |
| `kyber` | Fast SSDs | Token-based, good for high-throughput SSDs |

---

## Troubleshooting & Recovery

### Common Error Messages

| Error | Meaning | Action |
|-------|---------|--------|
| `I/O error, dev sda, sector XXXXX` | Hardware read/write failure | Check SMART, check cables, replace disk |
| `EXT4-fs error: remounting filesystem read-only` | Filesystem corruption detected | Run `fsck`, check disk health |
| `Buffer I/O error on device` | Device communication failure | Check connections, SMART, driver issues |
| `No space left on device` | Disk or inodes full | Check `df -h` and `df -i` |
| `Structure needs cleaning` | XFS metadata corruption | Run `xfs_repair` |
| `SQUASHFS error` | Corrupted squashfs image | Re-download/recreate image |
| `md: kicking non-fresh sdX from array` | RAID member has stale data | Re-add or replace disk |
| `EXT4-fs: mounted filesystem with ordered data mode` | Normal informational message | No action needed |
| `ata1: COMRESET failed` | SATA link failure | Check cable, port, disk |

### dmesg / journalctl Disk Error Analysis

```bash
# Recent disk errors in dmesg
dmesg -T | grep -iE 'error|fail|i/o|ata|scsi|sd[a-z]|nvme|blk_update'

# Kernel messages for a specific disk
dmesg -T | grep -i sda

# Journalctl disk errors (persistent across reboots)
journalctl -k -p err                       # kernel errors only
journalctl -k --grep="I/O error"           # specific pattern
journalctl -k -b -1 --grep="sd[a-z]"       # previous boot, disk messages

# Live tail of disk events
dmesg -Tw | grep -iE 'error|sd[a-z]|nvme'

# SCSI error log
journalctl -k --grep="scsi"
```

### Filesystem Check and Repair

#### ext4 (fsck / e2fsck)

```bash
# Check filesystem (must be unmounted or mounted read-only)
sudo umount /dev/sda1
sudo fsck /dev/sda1

# Automatic repair (answer yes to all)
sudo fsck -y /dev/sda1

# Force check even if filesystem appears clean
sudo fsck -f /dev/sda1

# Check without making changes (dry run)
sudo fsck -n /dev/sda1

# Verbose check
sudo e2fsck -fv /dev/sda1

# Check root filesystem (from recovery/live USB):
sudo fsck -y /dev/sda1
```

!!! danger "Never Run fsck on a Mounted Filesystem"
    Running `fsck` on a mounted read-write filesystem will cause data corruption. Always unmount first, or boot from a live USB for root filesystem repairs.

#### XFS (xfs_repair)

```bash
# Unmount first
sudo umount /dev/sda1

# Check (dry run)
sudo xfs_repair -n /dev/sda1

# Repair
sudo xfs_repair /dev/sda1

# If xfs_repair fails with "dirty log" error:
sudo xfs_repair -L /dev/sda1    # zero the log (last resort, may lose recent writes)
```

### badblocks

```bash
# Non-destructive read-only test
sudo badblocks -sv /dev/sdb

# Non-destructive read-write test (preserves data, slow)
sudo badblocks -nsv /dev/sdb

# Destructive write test (DESTROYS ALL DATA)
sudo badblocks -wsv /dev/sdb

# Save bad blocks list to file
sudo badblocks -sv -o /tmp/badblocks.txt /dev/sdb

# Tell ext4 about bad blocks
sudo e2fsck -l /tmp/badblocks.txt /dev/sda1
```

### Data Recovery

#### ddrescue (disk-level recovery)

```bash
# Install
sudo apt install gddrescue    # Debian/Ubuntu (package name differs!)
sudo dnf install ddrescue      # RHEL/Fedora

# Clone failing disk to good disk (most important step)
sudo ddrescue -f -n /dev/sda /dev/sdb rescue.log

# Second pass: retry failed sectors
sudo ddrescue -f -d -r3 /dev/sda /dev/sdb rescue.log

# Clone to image file instead
sudo ddrescue -f -n /dev/sda /mnt/backup/disk.img rescue.log

# -f    : force (overwrite output)
# -n    : skip retries on first pass (rescue good data first)
# -d    : direct access (bypass kernel cache)
# -r3   : retry failed sectors 3 times
```

!!! tip "ddrescue Strategy"
    Always do a fast first pass (`-n`) to rescue all easily readable data. Then do slower retries (`-r3`). The log file tracks progress so you can resume interrupted recoveries.

#### testdisk and photorec

```bash
# Install
sudo apt install testdisk      # includes both testdisk and photorec

# Recover deleted partitions / fix partition table
sudo testdisk /dev/sda

# Recover deleted files (by file type, from any filesystem)
sudo photorec /dev/sda1

# testdisk is interactive -- follow the menus:
# 1. Select disk
# 2. Select partition table type
# 3. Analyse -> Quick Search -> list found partitions
# 4. Write new partition table if correct
```

### Handling Full Disk

```bash
# Check disk usage
df -h
df -h /var

# Check inode usage (can be full even when disk has space)
df -i

# Find largest files
sudo du -ahx / | sort -rh | head -20

# Find large files (>100MB)
sudo find / -xdev -type f -size +100M -exec ls -lh {} \; 2>/dev/null

# Find large directories
sudo du -hx --max-depth=3 / | sort -rh | head -20

# Common culprits:
# Clean old journal logs
sudo journalctl --vacuum-size=100M
sudo journalctl --vacuum-time=7d

# Clean package cache
sudo apt clean                 # Debian/Ubuntu
sudo dnf clean all             # RHEL/Fedora

# Find and remove old kernels (Ubuntu)
sudo apt autoremove --purge

# Truncate large log file (instead of deleting, keeps file handle)
sudo truncate -s 0 /var/log/syslog

# Find deleted but open files still consuming space
sudo lsof +L1 | grep deleted
# Restart the process holding the file to free space
```

### Inode Exhaustion

```bash
# Diagnose: disk shows space available but "No space left on device"
df -i
# If IUsed is near IFree = 0, you have an inode problem

# Find directories with many small files
sudo find / -xdev -printf '%h\n' | sort | uniq -c | sort -rn | head -20

# Common cause: millions of session files, cache files, or mail queue
# Example cleanup:
sudo find /tmp -type f -mtime +7 -delete
sudo find /var/spool/postfix/maildrop -type f -delete
```

### Recovering from Read-Only Filesystem

```bash
# Check why it went read-only
dmesg -T | tail -50
journalctl -k -p err --since "1 hour ago"

# Attempt remount read-write
sudo mount -o remount,rw /

# If remount fails, filesystem likely needs repair:
# 1. If this is root, boot from live USB
# 2. Run fsck
sudo fsck -y /dev/sda1
# 3. Mount and verify
sudo mount /dev/sda1 /mnt
ls -la /mnt

# If hardware error caused the read-only remount, check SMART
sudo smartctl -H /dev/sda
```

---

## SSD-Specific

### TRIM

TRIM tells the SSD which blocks are no longer in use, allowing the drive to optimize its internal garbage collection and maintain performance over time.

```bash
# Check if TRIM is supported
sudo hdparm -I /dev/sda | grep -i trim
# Or for the filesystem:
lsblk -D    # DISC-GRAN and DISC-MAX > 0 means TRIM is supported

# Manual TRIM (recommended approach)
sudo fstrim -v /                      # TRIM root filesystem
sudo fstrim -av                       # TRIM all mounted filesystems

# Scheduled TRIM via systemd timer (recommended)
sudo systemctl enable --now fstrim.timer
sudo systemctl status fstrim.timer
# Runs weekly by default

# Continuous TRIM via mount option (alternative, slight performance cost)
# In /etc/fstab:
UUID=xxxx  /data  ext4  defaults,discard  0 2
```

| TRIM Method | Pros | Cons |
|-------------|------|------|
| `fstrim.timer` (periodic) | No runtime overhead, batch operation | Small delay before blocks are trimmed |
| `discard` mount option (continuous) | Immediate TRIM | Slight I/O overhead on every delete |
| Manual `fstrim` | Full control | Must remember to run it |

!!! tip "Prefer Periodic TRIM"
    Use the `fstrim.timer` systemd service (periodic batch TRIM) over the `discard` mount option. Periodic TRIM has no runtime performance impact and is the recommended approach for most workloads.

### Wear Leveling Awareness

```bash
# Check SSD life remaining (SMART)
sudo smartctl -a /dev/sda | grep -iE 'wear|life|endurance|used'

# For NVMe:
sudo smartctl -a /dev/nvme0 | grep "Percentage Used"

# SSD-friendly practices:
# - Avoid excessive small random writes (use write-back caching)
# - Use noatime mount option (reduces unnecessary writes)
# - Place swap on HDD or use zram instead of SSD swap
# - Avoid running defrag on SSDs (useless and harmful)
```

### NVMe Management (nvme-cli)

```bash
# Install
sudo apt install nvme-cli     # Debian/Ubuntu
sudo dnf install nvme-cli     # RHEL/Fedora

# List NVMe devices
sudo nvme list

# Detailed controller info
sudo nvme id-ctrl /dev/nvme0 -H

# Namespace info
sudo nvme id-ns /dev/nvme0n1 -H

# SMART / health log
sudo nvme smart-log /dev/nvme0

# Error log
sudo nvme error-log /dev/nvme0

# Firmware info
sudo nvme fw-log /dev/nvme0

# Temperature
sudo nvme smart-log /dev/nvme0 | grep -i temperature

# Format NVMe namespace (DESTROYS ALL DATA)
sudo nvme format /dev/nvme0n1 --ses=1  # secure erase

# Firmware update
sudo nvme fw-download /dev/nvme0 --fw=firmware.bin
sudo nvme fw-activate /dev/nvme0 --slot=1 --action=1
```

### SSD Health Monitoring Summary

```bash
# Quick SSD health check script
#!/bin/bash
echo "=== SSD Health Report ==="
for dev in $(lsblk -dn -o NAME,ROTA | awk '$2==0 {print $1}'); do
    echo ""
    echo "--- /dev/$dev ---"
    sudo smartctl -H /dev/$dev 2>/dev/null | grep "SMART overall"
    sudo smartctl -A /dev/$dev 2>/dev/null | grep -iE 'wear|life|reallocated|pending'
    sudo smartctl -a /dev/$dev 2>/dev/null | grep "Percentage Used" 
done
```

---

## Best Practices

### Monitoring Checklist

Set up automated monitoring for these metrics:

| What to Monitor | Tool | Alert Threshold |
|-----------------|------|----------------|
| Disk space usage | `df -h`, monitoring agent | > 80% warning, > 90% critical |
| Inode usage | `df -i` | > 80% |
| SMART health | `smartd`, `smartctl -H` | Any failure |
| Reallocated sectors | `smartctl -A` | Any increase |
| I/O wait | `iostat`, `sar` | %iowait > 20% sustained |
| I/O latency (await) | `iostat -x` | > 20ms for SSD, > 50ms for HDD |
| Device utilization | `iostat -x` | %util > 80% sustained |
| RAID array state | `/proc/mdstat`, `mdadm --detail` | Any degraded state |
| LVM thin pool usage | `lvs` data_percent | > 80% |
| SSD wear level | `smartctl` | Percentage Used > 80% |

### Backup Strategy Reminders

```
Rule of 3-2-1:
  3 copies of data
  2 different storage media
  1 offsite copy

RAID is NOT a backup. RAID protects against hardware failure.
Backups protect against:
  - Accidental deletion
  - Ransomware / malware
  - Software bugs that corrupt data
  - Fire / flood / theft
  - Human error
```

### When to Replace a Disk

Replace immediately if:

- SMART overall health status is **FAILED**
- **Reallocated Sector Count** is non-zero and increasing
- **Current Pending Sector Count** is increasing
- **Offline Uncorrectable** count is increasing
- Multiple I/O errors in dmesg/journal
- SSD **Percentage Used** exceeds 100%
- NVMe **Available Spare** is below threshold

Plan replacement soon if:

- Disk age exceeds 5 years (HDD) or approaches rated TBW (SSD)
- SMART self-tests report errors
- Intermittent timeout errors in logs
- Performance significantly degraded from baseline

### Useful Aliases and Scripts

Add to `~/.bashrc` or `~/.bash_aliases`:

```bash
# Quick disk overview
alias dfs='df -hT -x tmpfs -x devtmpfs -x squashfs | sort -k6 -rh'
alias dfsi='df -iT -x tmpfs -x devtmpfs -x squashfs'

# Disk usage - largest items in current directory
alias duh='du -h --max-depth=1 | sort -rh | head -20'

# I/O monitoring
alias ios='iostat -xz 2'
alias iot='sudo iotop -o'

# SMART quick check
alias smartcheck='sudo smartctl -H'
alias smartfull='sudo smartctl -a'

# LVM overview
alias lvmstatus='sudo pvs && echo "---" && sudo vgs && echo "---" && sudo lvs'

# RAID status
alias raidstatus='cat /proc/mdstat'

# Find large files
alias bigfiles='sudo find / -xdev -type f -size +100M -exec ls -lh {} \; 2>/dev/null | sort -k5 -rh'

# Filesystem usage by directory
alias dirsize='sudo du -hx --max-depth=2 / 2>/dev/null | sort -rh | head -30'
```

Emergency disk space recovery script:

```bash
#!/bin/bash
# emergency-disk-cleanup.sh
# Run when disk is critically full

echo "=== Emergency Disk Cleanup ==="
echo ""
echo "Current usage:"
df -h /

echo ""
echo "=== Step 1: Clean package cache ==="
if command -v apt &>/dev/null; then
    sudo apt clean
    sudo apt autoremove -y --purge
elif command -v dnf &>/dev/null; then
    sudo dnf clean all
    sudo dnf autoremove -y
fi

echo ""
echo "=== Step 2: Clean journal logs ==="
sudo journalctl --vacuum-size=50M

echo ""
echo "=== Step 3: Clean /tmp ==="
sudo find /tmp -type f -atime +3 -delete 2>/dev/null

echo ""
echo "=== Step 4: Find deleted-but-open files ==="
sudo lsof +L1 2>/dev/null | grep deleted | head -20

echo ""
echo "=== Step 5: Largest files ==="
sudo find / -xdev -type f -size +100M -exec ls -lh {} \; 2>/dev/null | sort -k5 -rh | head -15

echo ""
echo "=== Step 6: Largest directories ==="
sudo du -hx --max-depth=2 / 2>/dev/null | sort -rh | head -15

echo ""
echo "Usage after cleanup:"
df -h /
```

### Quick Reference: Common Workflows

#### Add a New Disk (End to End)

```bash
# 1. Identify new disk
lsblk
sudo fdisk -l /dev/sdb

# 2. Create partition
sudo parted /dev/sdb mklabel gpt
sudo parted /dev/sdb mkpart primary ext4 0% 100%

# 3. Create filesystem
sudo mkfs.ext4 -L "DATA" /dev/sdb1

# 4. Create mount point
sudo mkdir -p /data

# 5. Get UUID
sudo blkid /dev/sdb1

# 6. Add to fstab
echo 'UUID=<paste-uuid-here> /data ext4 defaults,noatime,nofail 0 2' | sudo tee -a /etc/fstab

# 7. Mount and verify
sudo mount -a
df -h /data
```

#### Extend a Full LVM Volume

```bash
# 1. Check current state
sudo vgs    # any free space in VG?
sudo lvs    # current LV size
df -h       # current filesystem usage

# 2a. If VG has free space:
sudo lvextend -l +100%FREE --resizefs /dev/vg0/lv_data

# 2b. If VG is full, add a new disk:
sudo pvcreate /dev/sdc1
sudo vgextend vg0 /dev/sdc1
sudo lvextend -l +100%FREE --resizefs /dev/vg0/lv_data

# 3. Verify
df -h /data
```

#### Replace a Failed RAID Disk

```bash
# 1. Identify failure
cat /proc/mdstat
sudo mdadm --detail /dev/md0

# 2. Remove failed disk
sudo mdadm /dev/md0 --fail /dev/sdc1
sudo mdadm /dev/md0 --remove /dev/sdc1

# 3. Replace hardware, partition new disk
sudo sfdisk -d /dev/sdb | sudo sfdisk /dev/sdc

# 4. Add to array and monitor rebuild
sudo mdadm /dev/md0 --add /dev/sdc1
watch cat /proc/mdstat
```

---

*This reference covers the most common disk management and troubleshooting scenarios encountered in production Linux environments. For distribution-specific details, consult the official documentation for RHEL, Ubuntu, or your target platform.*
