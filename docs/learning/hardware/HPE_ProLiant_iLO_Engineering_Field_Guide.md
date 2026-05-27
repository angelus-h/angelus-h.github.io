# HPE ProLiant + iLO Engineering Field Guide

**A Production-Grade Technical Reference for Linux System Engineers**

**Target Audience:** Linux system administrators, enterprise infrastructure engineers, platform engineers, homelab builders, refurbished server operators

**Last Updated:** 2026-05-25

---

## Table of Contents

1. [ProLiant Platform Overview](#1-proliant-platform-overview)
2. [iLO Architecture and Evolution](#2-ilo-architecture-and-evolution)
3. [Firmware and Lifecycle Management](#3-firmware-and-lifecycle-management)
4. [Smart Array Storage Controllers](#4-smart-array-storage-controllers)
5. [Linux Tooling and Hardware Monitoring](#5-linux-tooling-and-hardware-monitoring)
6. [Remote Management](#6-remote-management)
7. [Thermal Management](#7-thermal-management)
8. [Power Management](#8-power-management)
9. [Linux Distribution Compatibility](#9-linux-distribution-compatibility)
10. [Security Features](#10-security-features)
11. [Homelab and Refurbished Server Guide](#11-homelab-and-refurbished-server-guide)
12. [Troubleshooting Cookbook](#12-troubleshooting-cookbook)
13. [Automation with Redfish API](#13-automation-with-redfish-api)
14. [Appendices](#14-appendices)

---

## 1. ProLiant Platform Overview

### Platform Evolution: G6 Through Gen11

HPE ProLiant servers have evolved through multiple generations, each bringing significant improvements in performance, memory bandwidth, security, and I/O capabilities.

#### G6 Generation (2009-2010)

**Processor Platform:**
- Intel Xeon 5500/5600 series processors
- Intel 5520 chipset
- Support for up to 18 DIMM slots on DL360 G6
- Maximum memory: 192GB DDR3 registered memory ([HP ProLiant DL360 G6](https://support.hpe.com/hpesc/public/docDisplay?docId=c01710737&docLocale=en_US))

**Key Characteristics:**
- DDR3 memory technology
- PCIe Gen2 I/O
- iLO2 remote management

#### G7 Generation (2010-2012)

**Processor Platform:**
- Intel Xeon 5600 series processors
- Intel 5520 chipset (same as G6)
- 12 DDR3 memory modules running at speeds up to 1333MHz
- Maximum memory: 384GB in dual-processor configuration ([HPE ProLiant DL380 G7](https://support.hpe.com/hpesc/public/docDisplay?docId=c02215285&docLocale=en_US))

**Improvements over G6:**
- Enhanced thermal management
- Hyper-Threading technology support
- iLO3 remote management with improved remote console

#### G8 Generation (2012-2014)

**Processor Platform:**
- Intel Xeon E5-2600 series processors
- SmartMemory supporting DDR3-1600 at 1600MHz
- Up to 24 cores (upgradable from 2 processors)

**Major Enhancements:**
- Thermal Discovery Services (10% energy reduction) ([HPE Gen9 vs Gen8](https://www.curvature.com/resources/blog/comparing-hpe-proliant-gen9-vs-gen10-servers/))
- Redesigned drive carriers
- iLO4 with RESTful API support
- **Silicon Root of Trust** introduced (firmware defense mechanism)

#### G9 Generation (2014-2017)

**Processor Platform:**
- Intel Xeon E5-2600 v3/v4 series processors
- 26 CPU selections, 50% more L3 cache than Gen8
- Up to 22 cores standard, upgradable to 36 cores ([HPE ProLiant Generations Comparison](https://www.summit360.com/comparing-hpe-proliant-server-generations/))

**Key Features:**
- Enhanced DDR4 memory support
- iLO4 with advanced remote management
- Improved Smart Array controller options

#### Gen10 Generation (2017-2021)

**Processor Platform:**
- Intel Xeon Scalable processors (Skylake/Cascade Lake)
- 71% performance increase vs Gen9
- 27% increase in core count
- 66% more memory bandwidth ([HPE Gen10 vs Gen11](https://www.globalonetechnology.com/blog/hpe-gen10-vs-gen11-proliant-servers-key-differences/))

**Revolutionary Features:**
- **DDR4 memory** with significantly higher bandwidth
- **PCIe Gen3** I/O capabilities
- **iLO5** with Silicon Root of Trust validation
- Up to 28 cores standard, upgradable to 36+ cores
- Maximum internal storage: 459TB
- Secure Compute Lifecycle (firmware protection, malware detection, firmware recovery)

#### Gen10 Plus (2021-2022)

**Processor Platform:**
- 3rd generation Intel Xeon Scalable processors
- PCIe Gen4 capabilities ([HPE ProLiant Generations](https://www.summit360.com/comparing-hpe-proliant-server-generations/))

**Enhancements:**
- Doubled PCIe bandwidth over Gen10
- Enhanced iLO5 features
- Improved memory performance

#### Gen11 Generation (2022-Present)

**Processor Platform:**
- 4th generation Intel Xeon Scalable processors (Sapphire Rapids) - up to 60 cores
- 4th and 5th generation AMD EPYC 9004/9005 series - up to 160 cores ([HPE Gen11 Servers](https://www.servethehome.com/hpe-proliant-gen11-servers-with-4th-gen-intel-xeon-scalable-launched-sapphire-rapids/))
- **DDR5 memory** (up to 4800 MT/s, maximum 8TB SmartMemory)
- **PCIe Gen5** (double the I/O bandwidth of Gen4)

**Security Enhancements:**
- **iLO6** remote management
- Advanced Silicon Root of Trust
- Quantum-resistant cryptography (Gen12 with iLO7 meets NIST SP800-208 standards) ([What's different about iLO 7](https://www.techfinitive.com/features/whats-different-about-ilo-7/))

### Platform Comparison Table

| Generation | Year | CPU Platform | Memory Type | Max Memory | PCIe Gen | iLO Version | Key Innovation |
|------------|------|--------------|-------------|------------|----------|-------------|----------------|
| G6 | 2009 | Xeon 5500/5600 | DDR3 | 192GB | Gen2 | iLO2 | First modern platform |
| G7 | 2010 | Xeon 5600 | DDR3-1333 | 384GB | Gen2 | iLO3 | Improved thermal mgmt |
| G8 | 2012 | Xeon E5-2600 | DDR3-1600 | 768GB | Gen3 | iLO4 | Silicon Root of Trust |
| G9 | 2014 | Xeon E5-2600 v3/v4 | DDR4 | 1.5TB | Gen3 | iLO4 | 36-core support |
| Gen10 | 2017 | Xeon Scalable | DDR4 | 3TB | Gen3 | iLO5 | Secure Compute Lifecycle |
| Gen10+ | 2021 | Xeon Scalable Gen3 | DDR4 | 4TB | Gen4 | iLO5 | PCIe Gen4 |
| Gen11 | 2022 | Xeon Scalable Gen4/AMD EPYC 9004+ | DDR5 | 8TB | Gen5 | iLO6 | DDR5, PCIe Gen5, 160-core AMD |

### NUMA Architecture Considerations

Modern ProLiant servers use **Non-Uniform Memory Access (NUMA)** architecture where each processor has local memory with faster access than remote memory attached to other sockets.

**NUMA Optimization:**
- Best throughput when workload is NUMA-aware ([NUMA Settings](https://techlibrary.hpe.com/docs/iss/proliant_uefi/UEFI_TM_030617/s_set_NUMA_group.html))
- Node interleaving can be enabled/disabled in RBSU (BIOS)
- When **node interleaving is disabled** (default): processor accesses local memory first
- When **enabled**: processor reads/writes pages equally on local and remote memory
- **All nodes must be equal memory size** when node interleaving is enabled ([Node Interleaving](https://techlibrary.hpe.com/docs/iss/proliant_uefi/UEFI_Gen9_121417/s_enable_node_interleaving.html))

### Rack vs Tower Models

**Rack-Mount Series (DL):**
- **DL360**: 1U density, 2-socket, high-performance compute
- **DL380**: 2U density, 2-socket, balanced compute/storage
- **DL385**: 2U AMD EPYC variant
- **DL580**: 4U 4-socket, mission-critical workloads

**Tower Series (ML):**
- **ML110**: Entry-level tower, single socket
- **ML350**: Mid-range tower, dual socket
- **MicroServer**: Compact homelab/SMB server

---

## 2. iLO Architecture and Evolution

### iLO Overview

**HPE Integrated Lights-Out (iLO)** is a dedicated management processor (ASIC) embedded in HPE ProLiant servers that provides out-of-band remote management capabilities independent of the host operating system.

**Core Functions:**
- Remote console access (KVM)
- Virtual media mounting
- Power control (on/off/reset)
- Hardware monitoring (temperatures, fans, power supplies)
- Firmware updates
- Active Health System log collection
- RESTful API (Redfish) for automation

### iLO Architecture

iLO runs on a **dedicated ASIC** with its own:
- ARM-based processor
- Dedicated memory (256MB-1GB depending on generation)
- Dedicated network interface (shared or dedicated NIC port)
- NAND flash storage for firmware and logs
- Independent power supply (draws from server standby power)

**Management Pipeline:**
```
User/Automation
↓
iLO Network Interface (HTTPS/SSH/IPMI)
↓
iLO ASIC (ARM processor running Linux-based OS)
↓
System Management Bus (SMBus/I2C)
↓
Server Hardware (sensors, controllers, power)
```

### iLO Version Evolution

#### iLO2 (G6-G7 Servers)

**Key Features:**
- Java-based remote console
- Virtual media support (1.44MB floppy, CD-ROM, DVD)
- Basic remote management ([iLO2 Remote Console](https://support.hpe.com/hpesc/public/docDisplay?docId=a00043545en_us&docLocale=en_US))
- Terminal Services Pass-Through (deprecated in later versions)

**Limitations:**
- Java dependency for remote console
- Limited API capabilities
- No HTML5 console support

#### iLO3 (G7 Servers)

**Improvements over iLO2:**
- Enhanced Integrated Remote Console
- Session leader can grant full console control to participants ([iLO Comparison](https://en.wikipedia.org/wiki/HP_Integrated_Lights-Out))
- Improved virtual media performance
- Better thermal monitoring

#### iLO4 (G8-G9 Servers)

**Major Architectural Change:**
- **RESTful API** introduced (foundation for Redfish) ([iLO RESTful API](https://developer.hpe.com/platform/ilo-restful-api/home/))
- Silicon Root of Trust firmware defense (2012) ([Wikipedia - iLO](https://en.wikipedia.org/wiki/HP_Integrated_Lights-Out))
- HTML5 remote console support (firmware 2.50+)
- Agentless Management Service (AMS) integration

**Security Enhancement:**
- Firmware integrity checking at boot
- Digital signature validation
- Protection against firmware tampering

#### iLO5 (Gen10-Gen10+ Servers)

**Revolutionary Security Features:**
- **Silicon Root of Trust** hardware validation ([Silicon Root of Trust](https://support.hpe.com/hpesc/public/docDisplay?docId=a00018320en_us&page=GUID-0CA2D59D-C6A4-45D8-85AA-5CA6F97A449A.html&docLocale=en_US))
- iLO firmware validates System ROM **before execution**
- Digital fingerprint embedded in silicon (immutable)
- Runtime Firmware Validation (with iLO Advanced Premium Security License)

**Operational Improvements:**
- iLO Service Port (USB port on front panel for direct management) ([iLO5 Features](https://support.hpe.com/hpesc/public/docDisplay?docId=a00105236en_us&docLocale=en_US))
- Faster HTML5 console
- Enhanced Redfish API conformance

**Validation Process:**
```
Power On
↓
iLO5 chipset reads digital fingerprint from silicon
↓
Validates iLO firmware hash matches silicon signature
↓
If valid: Execute iLO firmware
If invalid: Refuse to boot (ROM not executed)
↓
iLO firmware validates System ROM
↓
System ROM validates Option ROMs via UEFI Secure Boot
↓
Boot OS
```

#### iLO6 (Gen11-Gen12 Servers)

**Enhancements over iLO5:**
- Faster web UI (improved responsiveness) ([iLO5 vs iLO6](https://icd3s.com/blog/hpe-ilo-5-vs-ilo-6-gen10-gen11-remote-management/))
- Improved security features
- Enhanced Redfish API
- Better HTML5 console performance

#### iLO7 (Gen12 Servers, 2021+)

**Performance and Security:**
- **30% decrease in OS boot time**
- **3x faster operations** ([What's different about iLO 7](https://www.techfinitive.com/features/whats-different-about-ilo-7/))
- **Quantum-resistant cryptography** (NIST SP800-208 compliance)
- AI-driven insights for predictive maintenance

### iLO Server Generation Mapping

| Server Generation | iLO Version | Year Introduced | Key Feature |
|-------------------|-------------|-----------------|-------------|
| G6 | iLO2 | 2009 | Java remote console |
| G7 | iLO2/iLO3 | 2010 | Enhanced remote console |
| G8 | iLO4 | 2012 | Silicon Root of Trust, RESTful API |
| G9 | iLO4 | 2014 | Mature RESTful API |
| Gen10 | iLO5 | 2017 | Hardware-validated Silicon Root of Trust |
| Gen10+ | iLO5 | 2021 | Enhanced security |
| Gen11 | iLO6 | 2022 | Faster UI, improved Redfish |
| Gen12 | iLO7 | 2024 | Quantum-resistant crypto, AI insights |

### Active Health System (AHS)

**Active Health System** continuously monitors **over 1600 system parameters** and logs hardware health data to assist with troubleshooting and support cases ([Active Health System](https://buy.hpe.com/us/en/software/server-management-software/server-ilo-management/ilo-management-engine/hpe-active-health-system/p/5219983)).

**Monitored Parameters:**
- CPU temperatures and utilization
- Memory errors (correctable/uncorrectable)
- Storage controller events
- Power supply status
- Fan speeds and failures
- PCIe device errors
- BIOS POST codes

**AHS Log Collection:**

1. **Via iLO Web Interface:**
- Navigate to **Information > Active Health System log**
- Select time range (default: 7 days)
- Click **Download Entire Log** ([How to Generate AHS Log](https://support.hpe.com/hpesc/public/docDisplay?docId=sf000046561en_us&docLocale=en_US))

2. **Via CLI:**
```bash
# Using hponcfg utility
hponcfg -f get_ahs_log.xml
```

**File Format:**
- `.ahs` file (compressed binary format)
- Can be uploaded to HPE support for analysis
- Enables faster problem analysis without reproducing errors

---

## 3. Firmware and Lifecycle Management

### Service Pack for ProLiant (SPP)

**HPE Service Pack for ProLiant (SPP)** is an ISO image containing pretested firmware, drivers, and software packages for Linux and Windows ([SPP Documentation](https://support.hpe.com/docs/display/public/a00sppdocen_US/spp/)).

**SPP Contents:**
- System ROM (BIOS)
- iLO firmware
- Smart Array controller firmware
- NIC firmware
- Power management firmware
- Innovation Engine firmware (Gen10+)
- Linux/Windows drivers

**Release Schedule:**
- **Major releases**: Twice per year
- **Hot fixes**: Released as patch bundles throughout the year ([SPP Releases](https://techlibrary.hpe.com/docs/iss/DL380_Gen10/setup_install/c_sppug_about_spp.html))

### SPP Deployment Methods

#### 1. Offline Update (Bootable ISO)

**Process:**
1. Download SPP ISO from [HPE Support](https://www.hpe.com/us/en/servers/hpe-service-pack-for-proliant.html)
2. Mount ISO via iLO Virtual Media or burn to USB/DVD
3. Boot server from SPP ISO
4. Follow interactive firmware update wizard

**Advantages:**
- No OS required
- Clean environment for firmware updates
- Reliable for complex updates

**Disadvantages:**
- **Requires downtime** (minimum 45 minutes per server)
- Manual intervention needed
- Not suitable for large-scale deployments

#### 2. Online Update (Smart Update Tools)

**Linux Installation:**
```bash
# Add HPE SPP repository (RHEL/Rocky/CentOS)
echo "[spp]
name=Service Pack for ProLiant
baseurl=https://downloads.linux.hpe.com/SDR/repo/spp/redhat/8/x86_64/current
enabled=1
gpgcheck=1
gpgkey=https://downloads.linux.hpe.com/SDR/hpPublicKey2048_key1.pub" | sudo tee /etc/yum.repos.d/spp.repo

# Install Smart Update Manager
sudo dnf install sut
```

**Update Process:**
```bash
# Launch Smart Update Manager
sudo sut

# Or use command-line mode
sudo sut -i -f /path/to/spp.iso
```

**Advantages:**
- No downtime (hot updates for most components)
- Automated dependency resolution
- Suitable for production systems

**Disadvantages:**
- OS-dependent
- Some firmware requires reboot
- Not available for all operating systems

#### 3. Intelligent Provisioning

**Intelligent Provisioning** is a UEFI-based deployment environment embedded in server firmware.

**Access:**
- Press **F10** during POST
- Navigate to **Perform Maintenance > Update Firmware**

**Update Methods:**
- Mount SPP ISO via iLO Virtual Media
- HTTP/FTP repository
- Local USB media

**Use Cases:**
- New server deployment
- OS installation with firmware updates
- Rescue/recovery operations

### Firmware Dependency Chain

HPE firmware components have **strict dependency requirements**. Incorrect update order can cause boot failures.

**Recommended Update Order:**
1. **iLO firmware** (establishes management baseline)
2. **System ROM (BIOS)** (depends on iLO version)
3. **Innovation Engine** (Gen10+, depends on System ROM)
4. **Smart Array controller firmware** (depends on System ROM)
5. **NIC firmware** (depends on System ROM)
6. **Power management firmware** (depends on System ROM)

**SPP handles dependencies automatically** when using guided update mode.

### Firmware Rollback and Downgrade

**CRITICAL:** Firmware downgrades can cause instability or hardware failure. Only downgrade when absolutely necessary.

#### Downgrade Procedure

**Method 1: SPP Interactive Mode**
```bash
# Boot from SPP ISO
# Select "Interactive Mode"
# Enable "Force" option to allow downgrades
# Select components to downgrade
```

**Method 2: Smart Update Manager with Force Flag**
```bash
sudo sut -i -f /path/to/older-spp.iso --force
```

**Gen10+ Downgrade Protection:**
- User-controlled option in RBSU: **System Configuration > BIOS/Platform Configuration (RBSU) > Server Security > Firmware Downgrade Prevention**
- **Disabled by default** ([Firmware Downgrade](https://support.hpe.com/hpesc/public/docDisplay?docId=sd00005729en_us&page=GUID-2BDC3F13-4815-4D42-A86D-ACA71B1539E5.html&docLocale=en_US))
- When enabled, blocks all firmware downgrades

**Downgrade Risks:**
- Server may become unstable
- Incompatibility with newer hardware
- Loss of security features
- Potential boot failure

**Best Practice:**
- **Always backup AHS logs before downgrade**
- Document current firmware versions
- Have recovery media ready (Intelligent Provisioning)
- Test on non-production system first

### Firmware Corruption Recovery

If firmware becomes corrupted (e.g., failed update, power loss during flash), HPE provides recovery mechanisms.

#### iLO Firmware Recovery

**Flash Recovery via Network:**
1. Power off server completely (remove AC power for 30 seconds)
2. Reconnect power (do not boot OS)
3. iLO detects corrupted firmware
4. iLO automatically attempts network flash recovery if DHCP is available ([iLO Flash Recovery](https://support.hpe.com/hpesc/public/docDisplay?docId=a00048142en_us&page=GUID-355C5AE4-6D7B-4973-B1C9-26ECAD6B7FEC.html&docLocale=en_US))

**Manual Recovery (Gen8-Gen9 with iLO4 v2.61+):**
1. Access iLO web interface
2. Navigate to **Administration > Firmware**
3. Select **Update Firmware**
4. Choose **iLO firmware file** (`.bin` format)
5. Upload and flash

#### System ROM (BIOS) Recovery

**Intelligent Provisioning Recovery:**
1. Boot server
2. Press **F10** at POST
3. Select **System Recovery**
4. Follow guided recovery process

**Manual NVRAM Clear (if POST fails):**
1. Power down server, remove AC power
2. Open server chassis
3. Locate **System Maintenance Switch** (usually near CPU1)
4. Set switch **position 6 to ON**
5. Power on server
6. Wait for POST message: "NVRAM defaults loaded"
7. Power off, set switch **position 6 to OFF**
8. Boot normally ([POST Troubleshooting](https://support.hpe.com/hpesc/public/docDisplay?docId=sd00002310en_us&page=GUID-05CCF6BB-87DB-44CB-923C-410D7136ADA5.html&docLocale=en_US))

---

## 4. Smart Array Storage Controllers

### Controller Generations and Linux Support

HPE Smart Array RAID controllers have evolved through multiple generations, with varying levels of Linux kernel support.

#### P410 Controller (Gen6-Gen7)

**Specifications:**
- 512MB BBWC (Battery-Backed Write Cache)
- 6Gb/s SAS interface
- **Does NOT support HBA mode** ([Smart Array Linux Support](https://sumguy.com/linux-hp-smart-array-raid-controller/))
- Linux driver: **hpsa** (HP Smart Array SCSI driver)

**Linux Kernel Support:**
- Supported since kernel 2.6.30+
- Part of mainline kernel ([hpsa driver](https://manpages.ubuntu.com/manpages/bionic/man4/hpsa.4.html))

#### P420 Controller (Gen8)

**Specifications:**
- 1GB or 2GB FBWC (Flash-Backed Write Cache)
- 6Gb/s SAS interface
- **Supports HBA mode** (requires firmware 6.60+)
- Linux driver: **hpsa**

**HBA Mode Enable:**
```bash
# WARNING: Requires firmware 6.60 or higher
hpssacli controller slot=0 modify hbamode=on forced
```

**IMPORTANT:** Controllers in HBA mode **cannot boot the OS**. Install OS in RAID mode or use separate boot device.

#### P440ar Controller (Gen9)

**Specifications:**
- Embedded controller (not removable)
- 2GB FBWC cache
- 12Gb/s SAS interface
- Supports HBA mode
- Linux driver: **hpsa**

**Gen9 Battery Sharing:**
- Single Smart Storage Battery can be shared across multiple controllers on Gen9 servers ([FBWC Battery Failure](https://community.hpe.com/t5/proliant-servers-ml-dl-sl/hpe-smart-storage-battery-for-hpe-smart-array-p440-4g-controller/td-p/6898218))

#### P840 Controller (Gen9)

**Specifications:**
- PCIe 3.0 x8 interface
- 12Gb/s SAS
- 4GB FBWC cache
- High-performance controller for storage-intensive workloads
- Linux driver: **hpsa** ([P840 Specifications](https://support.hpe.com/hpesc/public/docDisplay?docId=emr_na-c04495034))

### Linux Driver Architecture

**Two Driver Families:**

1. **cciss** (legacy, block driver)
- Used for older controllers (P400, P800)
- Block-level interface
- Being phased out

2. **hpsa** (modern, SCSI driver)
- **Recommended for all 'P' series controllers**
- SCSI-level interface
- Part of mainline kernel
- Supports all P410/P420/P440/P840 controllers ([hpsa driver documentation](https://docs.kernel.org/scsi/hpsa.html))

**Driver Detection:**
```bash
# Check loaded driver
lsmod | grep -i hpsa

# Check driver version
modinfo hpsa

# Verify controller detection
dmesg | grep -i hpsa
```

### HBA Mode and ZFS Compatibility

**HBA Mode Benefits:**
- Direct disk access (no RAID abstraction)
- Required for ZFS, Ceph, software RAID
- Better SMART monitoring
- No RAID overhead

**Limitations:**
- **Cannot boot from HBA mode** ([HBA Mode Guide](https://forum.proxmox.com/threads/hp-smart-array-p420i-hba-mode-complete-guide.142350/))
- Gen8 P410i does **NOT support HBA mode**
- P420/P440 require firmware 6.60+ for HBA mode
- Stability concerns compared to LSI controllers ([P420i HBA Mode Issues](https://forum.proxmox.com/threads/issue-with-zfs-on-hp-smart-arraw-p420i.55368/))

**ZFS Workaround (RAID-0 per disk):**
```bash
# Create individual RAID-0 arrays for each disk
# This allows ZFS to see individual disks while maintaining boot capability

# Example: Create RAID-0 for disk 1I:1:1
ssacli controller slot=0 create type=ld drives=1I:1:1 raid=0

# Repeat for each disk
# ZFS will see each RAID-0 array as a separate disk
```

**Important:** This workaround **loses hardware RAID acceleration** but enables ZFS pools.

### Cache Modules and Battery Backup

#### FBWC vs BBWC

**Flash-Backed Write Cache (FBWC):**
- Uses NAND flash to preserve cache during power loss
- Faster failover than battery-based cache
- No battery aging concerns
- Gen8+ controllers

**Battery-Backed Write Cache (BBWC):**
- Uses rechargeable battery (typically NiMH)
- Battery lifespan: 3-5 years
- Requires periodic battery replacement
- G6-G7 controllers

#### Write Cache States

```bash
# Check cache status
ssacli controller slot=0 show status
```

**Possible States:**
- **Enabled (OK)**: Write cache active, battery/capacitor healthy
- **Temporarily Disabled**: Battery charging, insufficient voltage
- **Permanently Disabled**: Battery/capacitor failed, cache module error

**CRITICAL:** When write cache is disabled, **write performance degrades significantly** (up to 70% slower for write-heavy workloads).

### ssacli Command Reference

**Installation:**
```bash
# RHEL/Rocky/CentOS
sudo dnf install ssacli

# Debian/Ubuntu
wget https://downloads.linux.hpe.com/SDR/repo/hpssacli/debian/pool/non-free/ssacli-<version>.deb
sudo dpkg -i ssacli-<version>.deb
```

**Common Commands:**

```bash
# Show all controllers
ssacli controller all show config

# Show controller status
ssacli controller slot=0 show status

# Show physical drives
ssacli controller slot=0 physicaldrive all show

# Show logical drives
ssacli controller slot=0 logicaldrive all show

# Show detailed controller info
ssacli controller slot=0 show detail

# Create RAID-1 array
ssacli controller slot=0 create type=ld drives=1I:1:1,1I:1:2 raid=1

# Create RAID-10 array
ssacli controller slot=0 create type=ld drives=1I:1:1,1I:1:2,1I:1:3,1I:1:4 raid=1+0

# Delete logical drive
ssacli controller slot=0 logicaldrive 1 delete

# Set rebuild priority
ssacli controller slot=0 modify rebuildpriority=high

# Add all unassigned disks as spares
ssacli controller slot=0 array A add spares=allunassigned

# Enable HBA mode (P420/P440 only, firmware 6.60+)
ssacli controller slot=0 modify hbamode=on forced
```

**Advanced Diagnostics:**

```bash
# Show detailed physical drive info (including SSD wear)
ssacli controller slot=0 physicaldrive 1I:1:1 show detail

# Show array configuration
ssacli controller slot=0 array all show

# Identify drive LED (blink for 15 seconds)
ssacli controller slot=0 physicaldrive 1I:1:1 modify led=on

# Check cache module status
ssacli controller slot=0 modify cacheratio=?
```

**Monitoring Scripts:**

```bash
#!/bin/bash
# Smart Array health check script

SLOT=0

# Check controller status
CONTROLLER_STATUS=$(ssacli controller slot=${SLOT} show status | grep -i status | awk '{print $2}')

if [ "$CONTROLLER_STATUS" != "OK" ]; then
echo "WARNING: Controller status: $CONTROLLER_STATUS"
fi

# Check logical drives
ssacli controller slot=${SLOT} logicaldrive all show | grep -i failed && echo "CRITICAL: Failed logical drive detected"

# Check physical drives
ssacli controller slot=${SLOT} physicaldrive all show | grep -i failed && echo "CRITICAL: Failed physical drive detected"

# Check cache status
CACHE_STATUS=$(ssacli controller slot=${SLOT} show detail | grep -i "cache status" | awk -F: '{print $2}' | xargs)

if [ "$CACHE_STATUS" != "OK" ]; then
echo "WARNING: Cache status: $CACHE_STATUS"
fi
```

---

## 5. Linux Tooling and Hardware Monitoring

### HPE Management Utilities

HPE provides several command-line tools for hardware monitoring and management on Linux.

#### hpasmcli (HP Advanced Server Management CLI)

**Purpose:** Read hardware sensors, fan speeds, temperatures, and power supply status.

**Installation:**
```bash
# RHEL/Rocky/CentOS
sudo dnf install hp-health

# Debian/Ubuntu
sudo apt install hp-health
```

**Common Commands:**

```bash
# Enter interactive mode
hpasmcli

# Show temperature sensors
hpasmcli -s "SHOW TEMP"

# Show fan status
hpasmcli -s "SHOW FAN"

# Show power supply status
hpasmcli -s "SHOW POWERSUPPLY"

# Show server information
hpasmcli -s "SHOW SERVER"

# Show all hardware status
hpasmcli -s "SHOW TEMP; SHOW FAN; SHOW POWERSUPPLY"
```

**Example Output:**
```
# hpasmcli -s "SHOW TEMP"

Sensor  Location       Temp    Threshold
------  --------       ----    ---------
#1    AMBIENT       22C/71F  42C/107F
#2    PROCESSOR_ZONE    40C/104F  70C/158F
#3    MEMORY_BD      35C/95F  87C/188F
#4    POWER_SUPPLY     45C/113F  75C/167F
#5    CHIPSET       50C/122F  95C/203F
```

**Monitoring Script:**

```bash
#!/bin/bash
# HPE hardware health monitoring

TEMP_THRESHOLD=60
FAN_SPEED_MIN=20

# Check temperatures
TEMPS=$(hpasmcli -s "SHOW TEMP" | grep -v "Sensor" | awk '{print $4}' | sed 's/C.*//')

for temp in $TEMPS; do
if [ "$temp" -gt "$TEMP_THRESHOLD" ]; then
echo "WARNING: High temperature detected: ${temp}°C"
fi
done

# Check fan speeds
FAN_STATUS=$(hpasmcli -s "SHOW FAN" | grep -i failed)

if [ -n "$FAN_STATUS" ]; then
echo "CRITICAL: Fan failure detected"
fi

# Check power supplies
PSU_STATUS=$(hpasmcli -s "SHOW POWERSUPPLY" | grep -i failed)

if [ -n "$PSU_STATUS" ]; then
echo "CRITICAL: Power supply failure detected"
fi
```

#### hponcfg (HP Online Configuration Utility)

**Purpose:** Configure iLO settings without server downtime or web interface access.

**Installation:**
```bash
# RHEL/Rocky/CentOS
sudo dnf install hponcfg

# Debian/Ubuntu
sudo apt install hponcfg
```

**Common Operations:**

```bash
# Get iLO network configuration
hponcfg -g

# Apply XML configuration file
sudo hponcfg -f /path/to/config.xml

# Reset iLO (requires server reboot to take effect)
sudo hponcfg -r

# Get iLO firmware version
sudo hponcfg -g | grep -i firmware
```

**Example XML Configuration (set iLO hostname):**

```xml
<!-- set_ilo_hostname.xml -->
<RIBCL VERSION="2.0">
<LOGIN USER_LOGIN="Administrator" PASSWORD="password">
<RIB_INFO MODE="write">
<MOD_NETWORK_SETTINGS>
<DNS_NAME value="ilo-server01"/>
</MOD_NETWORK_SETTINGS>
</RIB_INFO>
</LOGIN>
</RIBCL>
```

```bash
# Apply configuration
sudo hponcfg -f set_ilo_hostname.xml
```

**Export iLO Configuration:**
```bash
# Export current iLO config to file
sudo hponcfg -w /tmp/ilo_config.xml
```

**Use Cases:**
- Automated iLO provisioning
- Configuration backup/restore
- Scripted iLO setup for large deployments

#### Agentless Management Service (AMS)

**Purpose:** Enable hardware monitoring for Gen10+ servers without OS-level agents.

**CRITICAL for Gen11 Servers:**
- Gen11 servers running Linux/Proxmox **require AMS** to prevent fan runaway ([Gen11 Fan Noise](https://community.hpe.com/t5/proliant-servers-ml-dl-sl/ml110-gen11-fan-noise/td-p/7203781?nobounce))
- Without AMS, fans may run at 100% speed continuously

**Installation (RHEL/Rocky):**
```bash
# Add HPE repository
curl https://downloads.linux.hpe.com/SDR/hpPublicKey2048_key1.pub | sudo tee /etc/pki/rpm-gpg/GPG-KEY-HPE
sudo dnf config-manager --add-repo https://downloads.linux.hpe.com/SDR/repo/mcp/centos/8/x86_64/current

# Install AMS
sudo dnf install amsd
sudo systemctl enable --now amsd
```

**Verification:**
```bash
# Check AMS status
sudo systemctl status amsd

# Verify iLO communication
sudo tail -f /var/log/amsd.log
```

### Kernel Driver Support

**hpsa driver (Smart Array):**
```bash
# Verify driver loaded
lsmod | grep hpsa

# Check supported controllers
modinfo hpsa | grep alias

# Driver source location in kernel
# drivers/scsi/hpsa.c
```

**Supported Controllers in hpsa driver:**
- P410, P410i, P411
- P420, P420i, P421
- P430, P430i, P431
- P440, P440ar
- P840, P840ar
- H240, H240ar
- And many more ([hpsa kernel documentation](https://docs.kernel.org/scsi/hpsa.html))

**Legacy cciss driver:**
```bash
# Check if legacy driver loaded (should NOT be used for P-series)
lsmod | grep cciss
```

**Driver Conflicts:**
- If both hpsa and cciss are loaded, **blacklist cciss**:

```bash
# /etc/modprobe.d/blacklist-cciss.conf
blacklist cciss
```

---

## 6. Remote Management

### iLO Remote Console Options

HPE iLO provides three remote console technologies with varying requirements and capabilities.

#### HTML5 Integrated Remote Console

**Requirements:**
- iLO 4 firmware 2.50+ (Gen8-Gen9)
- iLO 5 firmware 1.20+ (Gen10+) ([HTML5 IRC](https://support.hpe.com/hpesc/public/docDisplay?docId=a00099683en_us&docLocale=en_US))
- Modern web browser (Chrome, Firefox, Edge)
- **No plugins required**

**Access:**
1. Log into iLO web interface
2. Navigate to **Remote Console > HTML5 Console**
3. Click **Launch**

**Features:**
- Full keyboard/mouse control
- Virtual media mounting
- Power control
- Video recording capability (iLO Advanced license)

**Firewall Requirements:**
- Port **443** (HTTPS) must be open

**Advantages:**
- No Java dependency
- Works on any OS
- Fast and responsive
- Modern security

**Limitations:**
- Requires iLO Advanced license for virtual media on iLO4
- Limited to single session on Standard license

#### Java Integrated Remote Console (IRC)

**Requirements:**
- Java Runtime Environment (JRE) 8+
- iLO Advanced license for full features

**Access:**
1. Log into iLO web interface
2. Navigate to **Remote Console > Java Integrated Remote Console**
3. Download `.jnlp` file
4. Launch with `javaws` or configure browser association

**Launch Command:**
```bash
# Install Java (if not present)
sudo dnf install java-1.8.0-openjdk

# Launch JNLP file
javaws /path/to/irc.jnlp
```

**Advantages:**
- Cross-platform (Linux, Windows, macOS)
- Reliable for older server generations
- Better compatibility with legacy systems

**Disadvantages:**
- Requires Java installation
- Java security warnings
- Deprecated in newer browsers

#### .NET Integrated Remote Console (Windows Only)

**Requirements:**
- Windows host
- .NET Framework 4.5+

**Access:**
1. Log into iLO web interface (from Windows PC)
2. Navigate to **Remote Console > .NET IRC**
3. Download and launch `.NET` application

**Features:**
- Native Windows application
- Best performance on Windows
- No browser required after launch

**Firewall Requirements:**
- Port **17990** (for .NET IRC) ([Virtual Media Considerations](https://support.hpe.com/hpesc/public/docDisplay?docId=a00105236en_us&page=GUID-D7147C7F-2016-0901-06D0-0000000020DB.html&docLocale=en_US))

### Virtual Media

**Virtual Media** allows mounting ISO images, floppy images, or USB drives from a remote client to the server as if they were physically connected.

**Supported Media Types:**
- Floppy diskette images (1.44MB)
- CD-ROM / DVD ISO images
- USB flash drives (on client PC)

**Virtual Media Options:**

1. **Local Image File (Client PC)**
- Mount ISO from your workstation
- Bandwidth-dependent performance
- Requires stable network connection

2. **URL-Based Image**
- iLO fetches image from HTTP/HTTPS URL
- Faster than client-based mounting
- Requires iLO network access to URL

**Mounting Virtual Media (HTML5 Console):**

1. Launch HTML5 remote console
2. Click **Virtual Media** icon
3. Select **Virtual Drives**
4. Choose **CD/DVD-ROM** or **USB**
5. Browse to ISO file or enter URL
6. Click **Mount**
7. Boot server from virtual media (configure boot order in BIOS if needed)

**Mounting via iLO CLI:**

```bash
# Using hponcfg utility
# mount_cdrom.xml
<RIBCL VERSION="2.0">
<LOGIN USER_LOGIN="Administrator" PASSWORD="password">
<RIB_INFO MODE="write">
<INSERT_VIRTUAL_MEDIA DEVICE="CDROM" IMAGE_URL="http://fileserver/images/ubuntu-22.04.iso"/>
</RIB_INFO>
</LOGIN>
</RIBCL>
```

```bash
sudo hponcfg -f mount_cdrom.xml
```

**Virtual Media Performance:**
- **Local image**: 5-15 MB/s (network-dependent)
- **URL-based**: 10-40 MB/s (depends on iLO network speed)
- **Direct iLO connection**: Fastest (dedicated management NIC)

**Use Cases:**
- OS installation
- Firmware updates (SPP ISO)
- Rescue/recovery operations
- Diagnostic tools

### iLO Network Configuration

**Shared Network Port:**
- iLO shares a physical NIC port with host OS
- Cost-effective for small deployments
- Potential network contention during heavy management tasks

**Dedicated Management Port:**
- Dedicated physical NIC for iLO (Gen8+)
- Isolated management network
- **Recommended for production environments**
- No bandwidth contention with host traffic

**Configuration Options:**

```bash
# Via RBSU during boot:
# Press F9 > System Configuration > iLO Network Configuration
```

**Static IP Configuration (hponcfg):**

```xml
<!-- set_ilo_static_ip.xml -->
<RIBCL VERSION="2.0">
<LOGIN USER_LOGIN="Administrator" PASSWORD="password">
<RIB_INFO MODE="write">
<MOD_NETWORK_SETTINGS>
<ENABLE_NIC value="Yes"/>
<REG_DDNS_SERVER value="No"/>
<DHCP_ENABLE value="No"/>
<IP_ADDRESS value="192.168.1.100"/>
<SUBNET_MASK value="255.255.255.0"/>
<GATEWAY_IP_ADDRESS value="192.168.1.1"/>
</MOD_NETWORK_SETTINGS>
</RIB_INFO>
</LOGIN>
</RIBCL>
```

**Security Best Practices:**
- Use **dedicated management network** (separate VLAN)
- Enable **TLS 1.2+ only** (disable older protocols)
- Disable **IPMI over LAN** (use Redfish instead)
- Configure **firewall rules** to restrict iLO access
- Use **strong passwords** (minimum 12 characters, mixed case, numbers, symbols)
- Enable **two-factor authentication** (iLO 5+)

---

## 7. Thermal Management

### Fan Control Architecture

HPE ProLiant servers use **iLO's thermal management engine** to dynamically adjust fan speeds based on temperature sensors throughout the system.

**Thermal Zones:**
- **Ambient zone**: Front intake area
- **Processor zone**: CPU heatsink area
- **Memory zone**: DIMM slots
- **Storage zone**: Drive bays
- **Power supply zone**: PSU intake/exhaust
- **PCIe zone**: Expansion cards

**Normal Operation:**
- iLO reads temperatures from all zones
- Calculates required airflow
- Adjusts fan speeds (typically 11-40% at idle)

### Aggressive Fan Policy

**Trigger Conditions:**
- Unsupported PCIe cards detected
- Non-HPE hard drives installed
- Thermal sensor failure
- High ambient temperature
- Firmware version incompatibility ([Fan Noise Issues](https://www.globalonetechnology.com/blog/how-to-fix-excessive-fan-noise-in-hpe-proliant-servers/))

**Symptoms:**
- Fans immediately ramp to 50-100% speed
- Persistent high RPM even when temperatures are normal
- Noise level increases from ~40dB to 70+ dB

**Common Causes in Homelab Environments:**

1. **Third-Party Hard Drives:**
- Non-HPE drives lack OEM firmware signatures
- iLO assumes "unsupported configuration"
- Fans run at maximum RPM "for safety"

2. **Unsupported PCIe Cards:**
- Consumer GPUs (NVIDIA GeForce, AMD Radeon)
- Non-HPE NICs
- Custom RAID controllers

3. **iLO Firmware Bugs:**
- iLO 5 v3.04 caused excessive fan noise on DL380 Gen10 ([Gen10 Fan Noise](https://community.hpe.com/t5/proliant-servers-ml-dl-sl/hp-proliant-dl380-gen-10-excessive-fan-noise-after-ilo5-update/td-p/7215850))
- Fixed in v3.05+

4. **Missing AMS (Gen11):**
- Gen11 servers require **Agentless Management Service** daemon
- Without AMS, fans run at 100% continuously ([Gen11 Fan Control](https://xcp-ng.org/forum/topic/9517/hpe-ml350-g11-fan-at-high-speed-agentless-management-ams))

### Thermal Tuning Solutions

#### Solution 1: Update iLO Firmware

```bash
# Check current iLO firmware version
curl -k -u admin:password https://ilo-ip/redfish/v1/Managers/1/ | jq '.FirmwareVersion'

# Download latest iLO firmware from HPE support
# Flash via iLO web interface: Administration > Firmware
```

#### Solution 2: Configure Thermal Profile

**Access RBSU:**
1. Reboot server
2. Press **F9** during POST
3. Navigate to **System Configuration > BIOS/Platform Configuration (RBSU)**
4. Go to **Advanced Options > Fan and Thermal Options > Thermal Configuration**

**Thermal Profiles:**
- **Optimal Cooling** (default): Balance performance and acoustics
- **Increased Cooling**: Higher fan speeds for hot environments
- **Max Cooling**: Maximum fan speeds (loudest)

**Workaround for Stuck Fans:**
1. Change to **Max Cooling**
2. Reboot server
3. Change back to **Optimal Cooling** ([Fan Noise Fix](https://community.hpe.com/t5/proliant-servers-ml-dl-sl/hp-proliant-dl380-gen-10-excessive-fan-noise-after-ilo5-update/td-p/7215850))
4. Reboot again

This forces iLO to re-detect thermal configuration.

#### Solution 3: Redfish API Fan Control

**Manual Fan Speed Adjustment (iLO 5+):**

```bash
# Set fan speed to 50% (USE WITH CAUTION)
curl -k -u admin:password -X PATCH https://ilo-ip/redfish/v1/Chassis/1/ThermalSubsystem/FanPercentMinimum \
-H "Content-Type: application/json" \
-d '{"FanPercentAdjust": 50}'
```

**CRITICAL WARNING:**
- Manual fan control **disables automatic thermal management**
- Risk of **component overheating**
- Monitor temperatures continuously with `hpasmcli`
- **Use only in well-ventilated environments**

#### Solution 4: Install AMS (Gen11 Required)

```bash
# RHEL/Rocky/CentOS
sudo dnf install amsd
sudo systemctl enable --now amsd

# Verify iLO communication
sudo journalctl -u amsd -f
```

### Homelab Acoustic Optimization

**Target Noise Levels:**
- Office-friendly: **< 35 dB** (quiet conversation level)
- Homelab acceptable: **35-45 dB** (background hum)
- Datacenter typical: **65-75 dB** (requires hearing protection)

**Noise Reduction Strategies:**

1. **Use Gen10+ Servers:**
- Gen10/Gen11 have better thermal efficiency
- Lower idle power = lower cooling requirements

2. **Minimize Drive Count:**
- Each drive adds thermal load
- Use larger-capacity drives to reduce drive count

3. **Avoid Unsupported Hardware:**
- Use HPE-certified drives when possible
- Remove unnecessary PCIe cards

4. **Improve Ambient Cooling:**
- Rack in air-conditioned room
- Maintain front-to-back airflow
- Keep ambient temperature below 25°C

5. **Acoustic Enclosures:**
- Soundproof server rack enclosures
- Ensure adequate ventilation
- Monitor internal temperatures

6. **Power Capping:**
```bash
# Limit server power consumption via iLO
curl -k -u admin:password -X PATCH https://ilo-ip/redfish/v1/Chassis/1/Power \
-H "Content-Type: application/json" \
-d '{"PowerControl": [{"PowerCapacityWatts": 300}]}'
```
Lower power = lower thermal load = lower fan speeds

---

## 8. Power Management

### Power Supply Architecture

**Redundancy Options:**
- **Non-redundant**: Single PSU (entry-level models)
- **Redundant (N+1)**: Two PSUs, each capable of powering entire server
- **Redundant (N+N)**: Multiple PSUs sharing load, failure of one PSU does not impact operation

**PSU Efficiency Ratings:**
- **80 PLUS Platinum**: 92% efficiency at 50% load
- **80 PLUS Titanium**: 94% efficiency at 50% load (Gen10+)

**Example (DL380 Gen10):**
- Two 800W Platinum PSUs
- Peak load: 600W
- Each PSU provides 400W (50% load = maximum efficiency)
- If one PSU fails, remaining PSU can deliver 800W (sufficient for 600W load)

### Power Consumption

**Idle Power Consumption:**

| Server Model | Generation | Idle Power | Load Power | Notes |
|--------------|------------|------------|------------|-------|
| DL380e | Gen8 | 90W | 400W+ | Low-power CPU, 16GB RAM, 11 HDDs ([Power Consumption](http://thehomeserverblog.com/hp-server-power-calculator/)) |
| DL380 Gen9 | Gen9 | 39W | 81W | Specific low-power config |
| DL360 G5 | Gen5 | ~100W | 400W | 75% utilization, low-wattage Intel CPUs |
| DL380 Gen10 | Gen10 | 90-100W | 500W+ | Dual Xeon, 128GB RAM |

**Power Calculation Formula:**

```
Total Facility Power = Server Power × (1 + PUE)

Where PUE (Power Usage Effectiveness) = Total Facility Power / IT Equipment Power

Typical PUE:
- Modern datacenter: 1.2-1.4
- Legacy datacenter: 1.8-2.0
- Homelab (no dedicated cooling): ~1.1-1.2
```

**Example Calculation:**
- DL380 Gen10: 100W idle, 500W load
- Homelab PUE: 1.2
- Annual electricity cost (24/7 idle): 100W × 1.2 × 8760 hours × $0.12/kWh = **$126/year**
- Annual cost (50% load): 300W × 1.2 × 8760 hours × $0.12/kWh = **$379/year**

### HPE Power Advisor

**HPE Power Advisor** is a web-based tool for estimating power consumption and planning datacenter power requirements ([HPE Power Advisor](https://paonline56.itcs.hpe.com/?Page=Index)).

**Features:**
- Configure server specifications (CPU, RAM, drives, cards)
- Estimate idle and load power
- Calculate cooling requirements
- Plan rack-level and datacenter-level power

**Typical Usage:**
1. Select server model (e.g., DL380 Gen10)
2. Configure components:
- Processor count and model
- Memory capacity
- Number of drives
- PCIe cards
3. View power estimates:
- Idle power
- Typical load
- Maximum power
4. Export report

**Cooling Factor:**
- Power Advisor assumes **1 watt of cooling per 1 watt of server power** (effectively doubles facility power) ([Power Calculation](https://community.hpe.com/hpeb/attachments/hpeb/itrc-264/80662/1/308229.pdf))

### Power Capping

**Dynamic Power Capping** allows limiting maximum server power consumption via iLO.

**Use Cases:**
- Prevent circuit breaker trips
- Reduce datacenter cooling load
- Comply with power budgets
- Lower homelab electricity costs

**Configuration via Redfish API:**

```bash
# Set power cap to 300W
curl -k -u admin:password -X PATCH https://ilo-ip/redfish/v1/Chassis/1/Power \
-H "Content-Type: application/json" \
-d '{
"PowerControl": [{
"PowerCapacityWatts": 300
}]
}'

# Verify current power consumption
curl -k -u admin:password https://ilo-ip/redfish/v1/Chassis/1/Power | jq '.PowerControl[0].PowerConsumedWatts'
```

**Configuration via iLO Web Interface:**
1. Navigate to **Power & Thermal > Power Regulation**
2. Select **Dynamic Power Capping Mode**
3. Enter **Power Cap Value** in watts
4. Click **Submit**

**Effects of Power Capping:**
- CPU performance throttled if cap is too low
- Fan speeds may increase (to maintain cooling with reduced power)
- Monitor with `hpasmcli -s "SHOW SERVER"` to ensure stability

### BIOS Power Management Settings

**Access RBSU:**
- Reboot server
- Press **F9**
- Navigate to **Power and Performance Options**

**Key Settings:**

**1. Power Regulator Mode:**
- **Dynamic Power Savings Mode** (default): Balance performance and power
- **Static Low Power Mode**: Minimize power consumption (reduced performance)
- **Static High Performance Mode**: Maximum performance (highest power)
- **OS Control Mode**: OS manages power states (Linux cpufreq, Windows power plans)

**2. Collaborative Power Control:**
- Allows OS and BIOS to collaborate on power management
- Recommended for Linux with `tuned` or `cpupower`

**3. Minimum Processor Idle Power:**
- **C-states**: Deep CPU sleep states
- **C6**: Deepest idle state (lowest power)
- Enable for maximum power savings in idle/low-load scenarios

**4. Energy/Performance Bias:**
- **Maximum Performance**: Favor performance over power
- **Balanced Performance**: Default
- **Balanced Power**: Favor power savings
- **Power Savings Mode**: Minimum power consumption

**Example Configuration (Homelab Idle Optimization):**
- Power Regulator Mode: **Dynamic Power Savings**
- Minimum Processor Idle Power: **C6 State**
- Energy/Performance Bias: **Balanced Power**

**Result:** 20-30% reduction in idle power consumption.

---

## 9. Linux Distribution Compatibility

### Official HPE Support

HPE provides official support and certification for specific Linux distributions through **HPE Support and Certification Matrices** ([HPE Support Matrix](https://www.hpe.com/us/en/collaterals/collateral.a50010841enw.html)).

**Officially Supported Distributions:**
- **Red Hat Enterprise Linux (RHEL)** 7, 8, 9
- **SUSE Linux Enterprise Server (SLES)** 12, 15
- **Ubuntu Server LTS** 18.04, 20.04, 22.04, 24.04 ([Ubuntu Certified - DL380 Gen10](https://ubuntu.com/certified/201904-26935))
- **VMware ESXi** 6.7, 7.0, 8.0

**Certification Details:**
- DL380 Gen10 Skylake: **Ubuntu certified** ([Ubuntu Certification](https://ubuntu.com/certified/201904-26935))
- DL380 Gen11: **Ubuntu certified**

### RHEL and Rocky Linux

**Driver Support:**
- HPE provides **hpsa** driver for RHEL
- Driver included in RHEL kernel since RHEL 7
- **Rocky Linux** and **AlmaLinux** are RHEL-compatible (binary-compatible rebuilds)

**HPE Management Tools for RHEL/Rocky:**

```bash
# Add HPE SDR repository
sudo curl https://downloads.linux.hpe.com/SDR/repo/mcp/rhel/9/x86_64/current/mcp.repo \
-o /etc/yum.repos.d/mcp.repo

# Install management utilities
sudo dnf install hp-health hponcfg ssacli amsd

# Enable services
sudo systemctl enable --now hp-health
sudo systemctl enable --now amsd
```

**Gen9 Limitation:**
- HPE only provides **Agent monitoring service for RHEL 8** on Gen9 servers ([Rocky Linux Gen9 Support](https://forums.rockylinux.org/t/hpe-proliant-gen9-rocky-linux-version-9/7632))
- Gen10+ has broader support

### Ubuntu Server

**Driver Support:**
- **hpsa driver** included in Ubuntu kernel
- Ubuntu-certified hardware list includes many ProLiant models

**Installation:**

```bash
# Add HPE repository
sudo add-apt-repository "deb http://downloads.linux.hpe.com/SDR/repo/mcp/ $(lsb_release -cs)/current non-free"

# Import HPE GPG key
wget https://downloads.linux.hpe.com/SDR/hpPublicKey2048_key1.pub
sudo apt-key add hpPublicKey2048_key1.pub

# Install utilities
sudo apt update
sudo apt install hp-health hponcfg ssacli

# Enable services
sudo systemctl enable --now hp-health
```

### Proxmox VE

**Compatibility:**
- Proxmox VE kernel derived from **Ubuntu LTS kernel** ([Proxmox Compatibility](https://forum.proxmox.com/threads/compatibility-matrix.168096/))
- Proxmox VE 8.x uses kernel based on Ubuntu 24.04
- Check Ubuntu certified hardware list for compatibility indication

**Known Working Configurations:**
- DL380 G6, G7, Gen8, Gen9, Gen10, Gen11 ([Proxmox on DL380](https://forum.proxmox.com/threads/hardware-compatibility-hpe-proliant-dl380.172935/))
- DL360 Gen10+
- ML350 Gen11

**HPE Tools Installation on Proxmox:**

```bash
# Proxmox is Debian-based, use Debian repositories
echo "deb http://downloads.linux.hpe.com/SDR/repo/mcp/debian $(lsb_release -cs)/current non-free" | \
sudo tee /etc/apt/sources.list.d/mcp.list

wget https://downloads.linux.hpe.com/SDR/hpPublicKey2048_key1.pub
sudo apt-key add hpPublicKey2048_key1.pub

sudo apt update
sudo apt install hp-health ssacli amsd

sudo systemctl enable --now hp-health amsd
```

**ZFS on Proxmox with Smart Array:**
- Use **HBA mode** (P420/P440ar with firmware 6.60+)
- Or use **RAID-0 per disk workaround** ([ZFS on Smart Array](https://gist.github.com/mrpeardotnet/a9ce41da99936c0175600f484fa20d03))

### Kernel Driver Matrix

| Distribution | Kernel Version | hpsa Driver | ssacli Support | Notes |
|--------------|----------------|-------------|----------------|-------|
| RHEL 7 | 3.10+ | Yes | Yes | Mature support |
| RHEL 8 | 4.18+ | Yes | Yes | Full Gen10 support |
| RHEL 9 | 5.14+ | Yes | Yes | Gen11 support |
| Rocky Linux 8 | 4.18+ | Yes | Yes | RHEL clone |
| Rocky Linux 9 | 5.14+ | Yes | Yes | RHEL clone |
| Ubuntu 20.04 | 5.4+ | Yes | Yes | LTS, certified |
| Ubuntu 22.04 | 5.15+ | Yes | Yes | LTS, certified |
| Ubuntu 24.04 | 6.8+ | Yes | Yes | Latest LTS |
| Proxmox VE 8 | 6.8+ | Yes | Yes | Debian-based |
| Debian 12 | 6.1+ | Yes | Yes | Stable support |

**Driver Location in Kernel:**
- Source: `drivers/scsi/hpsa.c`
- Module: `hpsa.ko`
- Load order: Loaded automatically via udev

---

## 10. Security Features

### Silicon Root of Trust

**Silicon Root of Trust** is HPE's firmware security architecture introduced with Gen10 servers.

**Architecture Overview:**

```
Power On
↓
[1] iLO Silicon (Immutable Firmware in ASIC)
↓ Validates digital fingerprint
[2] iLO Firmware
↓ Validates System ROM signature
[3] System ROM (BIOS/UEFI)
↓ Validates Option ROM signatures
[4] Option ROMs (NIC, Storage Controllers)
↓ UEFI Secure Boot validates OS bootloader
[5] OS Bootloader
↓
[6] Operating System Kernel
```

**Validation Process:**

1. **Boot-time Validation:**
- iLO chipset reads **immutable digital fingerprint** from silicon
- Fingerprint acts as root of trust (cannot be modified)
- iLO firmware hash must match silicon signature
- If mismatch: **firmware not executed**, boot halted ([Silicon Root of Trust](https://support.hpe.com/hpesc/public/docDisplay?docId=a00018320en_us&page=GUID-0CA2D59D-C6A4-45D8-85AA-5CA6F97A449A.html&docLocale=en_US))

2. **Chain of Trust:**
- iLO firmware validates System ROM digital signature
- System ROM validates Option ROMs (NIC, RAID controllers)
- UEFI Secure Boot validates OS bootloader
- Each layer validates the next before execution ([What is Silicon Root of Trust](https://www.wwt.com/article/what-is-hpe-silicon-root-of-trust))

3. **Runtime Firmware Validation (iLO Advanced Premium):**
- Continuous background verification (up to once per day)
- Detects firmware tampering during operation
- Alerts via iLO if integrity check fails

**Protection Against:**
- Firmware rootkits
- Firmware tampering via malware
- Supply chain firmware attacks
- Unauthorized firmware modifications

**Gen10 vs Legacy:**

| Feature | Legacy (G6-G9) | Gen10+ Silicon Root of Trust |
|---------|----------------|------------------------------|
| Firmware validation | Software-based (iLO checks integrity) | **Hardware-based (silicon fingerprint)** |
| Validation frequency | At boot only | Boot + runtime (with license) |
| Root of trust | iLO firmware (can be modified) | **Silicon (immutable)** |
| Compromised iLO | Can execute malicious firmware | **Cannot execute** (hash mismatch) |

### Secure Boot

**UEFI Secure Boot** prevents execution of unsigned operating system bootloaders and kernel modules.

**Configuration:**

1. **Enable Secure Boot in RBSU:**
- Reboot server, press **F9**
- Navigate to **System Configuration > BIOS/Platform Configuration (RBSU) > Server Security > Secure Boot Settings**
- Set **Secure Boot Enforcement** to **Enabled**
- Select **Secure Boot Mode**: **Standard** or **Custom**

2. **Install OS in UEFI Mode:**
- Ensure installation media boots in UEFI mode (not legacy BIOS)
- Modern Linux distributions (RHEL 8+, Ubuntu 20.04+) support Secure Boot by default

3. **Verify Secure Boot Status:**
```bash
# Check if Secure Boot is enabled
mokutil --sb-state
# Output: SecureBoot enabled

# Alternative method
dmesg | grep -i secure
# Output: secureboot: Secure boot enabled
```

**Key Databases:**

- **db (Signature Database)**: Contains authorized signing keys
- **dbx (Forbidden Signature Database)**: Contains revoked keys
- **KEK (Key Exchange Key)**: Used to update db/dbx
- **PK (Platform Key)**: Top-level key, controls KEK

**Custom Key Enrollment (Advanced):**

```bash
# Generate custom keys (for signing custom kernels/modules)
openssl req -new -x509 -newkey rsa:2048 -keyout PK.key -out PK.crt -days 3650 -nodes -subj "/CN=Platform Key/"
openssl req -new -x509 -newkey rsa:2048 -keyout KEK.key -out KEK.crt -days 3650 -nodes -subj "/CN=Key Exchange Key/"
openssl req -new -x509 -newkey rsa:2048 -keyout db.key -out db.crt -days 3650 -nodes -subj "/CN=Signature Database/"

# Convert to EFI signature list format
cert-to-efi-sig-list -g "$(uuidgen)" PK.crt PK.esl
cert-to-efi-sig-list -g "$(uuidgen)" KEK.crt KEK.esl
cert-to-efi-sig-list -g "$(uuidgen)" db.crt db.esl

# Sign with PK
sign-efi-sig-list -k PK.key -c PK.crt PK PK.esl PK.auth
sign-efi-sig-list -k PK.key -c PK.crt KEK KEK.esl KEK.auth
sign-efi-sig-list -k KEK.key -c KEK.crt db db.esl db.auth

# Enroll via efi-updatevar or RBSU Custom Key Management
```

**IMPORTANT:** Custom key enrollment **disables Microsoft/Red Hat signatures**. Only use if you understand implications.

### iLO Security Best Practices

**Network Isolation:**
- Place iLO on **dedicated management VLAN**
- Separate from production networks
- Firewall rules restricting access to management workstations

**Authentication:**
- **Disable default accounts** (if present)
- Create user accounts with **strong passwords** (16+ characters)
- Enable **two-factor authentication** (iLO 5+)
- Use **directory services** (LDAP/Active Directory) for centralized authentication

**Access Control:**
- Restrict **iLO Administrator** privilege to minimal users
- Use **Read-Only** or **Operator** privileges for monitoring-only accounts
- Review iLO user list regularly

**Protocol Security:**
- Disable **SSLv3, TLS 1.0, TLS 1.1** (use TLS 1.2+ only)
- Disable **IPMI over LAN** (use Redfish API instead)
- Enable **Require Login for iLO RBSU** (prevent BIOS-level access without authentication)

**Firmware Security:**
- Enable **Firmware Downgrade Prevention** (Gen10+)
- Verify firmware signatures before flashing
- Only download firmware from **official HPE sources**

**Audit Logging:**
- Enable **iLO Event Log**
- Forward logs to **syslog server** for centralized monitoring
- Monitor for failed login attempts, configuration changes

**Example syslog forwarding configuration:**

```xml
<!-- syslog_config.xml -->
<RIBCL VERSION="2.0">
<LOGIN USER_LOGIN="Administrator" PASSWORD="password">
<RIB_INFO MODE="write">
<MOD_SNMP_IM_SETTINGS>
<SNMP_ADDRESS_1 value="192.168.1.10"/>
<SNMP_ADDRESS_1_ROCOMMUNITY value="public"/>
</MOD_SNMP_IM_SETTINGS>
</RIB_INFO>
</LOGIN>
</RIBCL>
```

```bash
sudo hponcfg -f syslog_config.xml
```

---

## 11. Homelab and Refurbished Server Guide

### Popular Homelab Models

#### DL360 G7/Gen8

**Pros:**
- 1U rack form factor (space-efficient)
- Abundant on refurbished market
- Low cost ($100-$300 USD used)
- Good performance for hypervisor (ESXi, Proxmox)

**Cons:**
- **HIGH NOISE** (1U fans = 60+ dB at load)
- Limited drive bays (8x 2.5" SFF)
- Higher power consumption than Gen10+

**Ideal Use Cases:**
- Proxmox cluster nodes
- Kubernetes worker nodes
- High-density compute (not suitable for office environments)

#### DL380 G7/Gen8/Gen9

**Pros:**
- 2U form factor (quieter than DL360)
- 12-25 drive bays (excellent for storage)
- Good expansion (8x PCIe slots on Gen9)
- Moderate power consumption at idle

**Cons:**
- Larger/heavier than DL360
- Still noisy with third-party drives
- G7 lacks modern security features

**Ideal Use Cases:**
- NAS/SAN storage (TrueNAS, Ceph)
- Virtualization with local storage
- Homelab "all-in-one" server

**Recommended Configuration (DL380 Gen9 for Homelab):**
- Dual Intel Xeon E5-2680 v4 (28 cores, 56 threads)
- 128GB DDR4 ECC RAM
- P440ar controller in HBA mode (for ZFS)
- 8x 4TB SATA drives (ZFS RAID-Z2)
- Dedicated management NIC for iLO
- **Estimated Cost (used, 2026):** $800-$1200

#### MicroServer Gen8/Gen10/Gen11

**Pros:**
- **Very quiet** (designed for office environments)
- Low power consumption (40-60W idle)
- Compact tower form factor
- Affordable ($300-$600 new/used)

**Cons:**
- Limited expansion (1-2 PCIe slots)
- Single-socket only (lower performance)
- Fewer drive bays (4-8 drives)

**Ideal Use Cases:**
- Home NAS
- Small business file server
- Development/testing lab
- Quiet homelab in residential space

**Recommended Configuration (MicroServer Gen10+):**
- Intel Xeon E-2236 or AMD EPYC Embedded
- 32-64GB ECC RAM
- 4x 8TB SATA drives (ZFS mirror or RAID-Z1)
- **Estimated Cost (new, 2026):** $1200-$1800

### Power Consumption and Electricity Costs

**Annual Electricity Cost Formula:**

```
Annual Cost = (Idle Power in kW) × (24 hours/day) × (365 days/year) × (Electricity Rate in $/kWh)
```

**Example (DL380 Gen9, 100W idle, $0.12/kWh):**
```
Annual Cost = (0.1 kW) × (24) × (365) × ($0.12) = $105.12/year
```

**Power Consumption Comparison (24/7 operation):**

| Model | Idle Power | Annual Cost @ $0.12/kWh | Annual Cost @ $0.15/kWh |
|-------|------------|-------------------------|-------------------------|
| DL360 G7 | 100W | $105 | $131 |
| DL380 G7 | 120W | $126 | $158 |
| DL380 Gen9 | 90W | $95 | $118 |
| DL380 Gen10 | 85W | $89 | $112 |
| MicroServer Gen10 | 50W | $53 | $66 |
| DL380 Gen11 | 80W | $84 | $105 |

**Power Optimization Tips:**
1. Enable **C6 CPU idle states** in BIOS
2. Use **Dynamic Power Savings Mode**
3. Remove unnecessary PCIe cards
4. Reduce drive count (use higher-capacity drives)
5. Configure power capping via iLO
6. Shut down servers during off-hours (if not 24/7 services)

### Noise Management

**Noise Levels (Reference):**
- **35 dB**: Quiet office
- **45 dB**: Normal conversation
- **60 dB**: Vacuum cleaner
- **70 dB**: Hairdryer

**Server Noise Levels:**

| Model | Idle Noise | Load Noise | Acceptable Environment |
|-------|------------|------------|------------------------|
| DL360 1U | 55-60 dB | 70+ dB | Datacenter only |
| DL380 2U | 40-45 dB | 60 dB | Basement, garage |
| MicroServer | 25-30 dB | 35 dB | **Office, bedroom** |

**Noise Reduction Strategies:**

1. **Server Selection:**
- Prefer 2U over 1U
- Prefer tower (ML) over rack (DL)
- Gen10+ quieter than G6-G9

2. **Firmware Updates:**
- Update iLO firmware to latest
- Install AMS (Gen11 requirement)

3. **Hardware Choices:**
- Use HPE-certified drives when possible
- Remove unsupported PCIe cards

4. **Physical Isolation:**
- Place servers in dedicated room
- Use soundproof rack enclosures
- Maintain proper ventilation

5. **Software Tuning:**
- Enable thermal optimization in BIOS
- Use power capping to reduce heat generation

### Rack Infrastructure for Homelab

**Standard Rack Sizes:**
- **42U**: Full-height rack (2 meters)
- **27U**: Half-height rack (1.4 meters)
- **12U**: Wall-mount rack

**Homelab Rack Recommendations:**
- **Enclosed rack** (better noise isolation)
- **Ventilated front/rear doors** (adequate airflow)
- **Wheels/casters** (mobility for cleaning/maintenance)
- **PDU with monitoring** (power metering)

**Example Homelab Rack (12U):**
- 2x DL380 Gen9 (2U each) = 4U
- 1x Network switch (1U) = 1U
- 1x Patch panel (1U) = 1U
- 1x UPS (2U) = 2U
- 1x PDU (mounted vertically)
- **Total:** 8U used, 4U spare

**PDU Selection:**
- **Metered PDU** (shows total power consumption)
- **Switched PDU** (remote on/off per outlet)
- **Minimum 15A circuit** for 2-3 servers
- **Dedicated 20A circuit recommended** for 4+ servers

**UPS Sizing:**

```
UPS Capacity (VA) ≥ Total Server Load (W) / Power Factor (0.8)
```

**Example:**
- 2x DL380 Gen9 @ 150W each = 300W
- 1x Switch @ 50W = 50W
- Total: 350W
- Required UPS: 350W / 0.8 = **437.5 VA** (choose 500-750VA UPS)

**Runtime Target:** 10-15 minutes (sufficient for graceful shutdown)

### Refurbished Server Checklist

**Pre-Purchase Inspection:**

1. **iLO Access:**
- Request iLO credentials
- Verify iLO firmware version
- Check for iLO Advanced license

2. **Hardware Health:**
- Review IML (Integrated Management Log) for errors
- Check AHS log for recurring issues
- Verify SMART status of all drives

3. **Firmware Versions:**
- System ROM (BIOS) version
- iLO firmware version
- Smart Array firmware version

4. **Included Components:**
- Rail kit (if rack-mount)
- Power cables (C13/C14 or C19/C20)
- iLO license key
- Original HPE bezel (optional but nice)

5. **Warranty Status:**
- Check HPE warranty lookup: https://support.hpe.com/hpesc/public/home/warrantyCheck
- Verify if transferable
- Consider third-party warranty extension

**Post-Purchase Setup:**

1. **Firmware Updates:**
- Download latest SPP ISO
- Update all firmware (iLO, BIOS, controllers)

2. **BIOS Configuration:**
- Enable virtualization (VT-x/AMD-V)
- Configure boot order
- Enable UEFI boot mode
- Disable legacy BIOS (if not needed)

3. **iLO Configuration:**
- Change default password
- Configure static IP or DHCP reservation
- Enable SSH access
- Configure SNMP/syslog if needed

4. **OS Installation:**
- Boot from virtual media (SPP ISO or OS installer)
- Partition layout planning
- Install management utilities (hp-health, ssacli, amsd)

5. **Monitoring Setup:**
- Configure health monitoring (Nagios, Zabbix, Prometheus)
- Monitor IML for hardware errors
- Set up AHS log collection

---

## 12. Troubleshooting Cookbook

### iLO NAND Corruption

**Symptoms:**
- iLO web interface inaccessible
- Error messages:
- "Embedded media initialization failed due to media write-verify test failure"
- "Partition Table Read Error"
- "NAND read failure" ([iLO NAND Error](https://gbeifuss.github.io/p/resolving-hp-ilo-nand-error/))

**Root Cause:**
- NAND flash memory wear-out (excessive write cycles)
- Gen8-Gen9 servers particularly affected
- Faulty SD flash chip on mainboard ([NAND Wearout](https://community.hpe.com/t5/proliant-servers-netservers/ilo-integrated-flash-card-failure-on-g8-and-g9-servers-why-did/td-p/7123691))

**Recovery Procedure (Gen8-Gen9 with iLO4 v2.61+):**

**Step 1: NAND Format via iLO Web Interface (if accessible)**

1. Log into iLO
2. Navigate to **Administration > Diagnostics**
3. Select **Embedded Flash/SD Card**
4. Click **Format NAND**
5. Confirm warning (all data erased)
6. Wait for format completion (5-10 minutes)
7. Reboot server

**Step 2: NAND Format via RBSU (if iLO web inaccessible)**

1. Boot server
2. Press **F8** during POST (iLO RBSU access)
3. Navigate to **Embedded Flash/SD-CARD**
4. Select **Format**
5. Confirm operation
6. Reboot

**Step 3: Cold Boot (if format fails)**

1. Shut down server completely
2. Remove **all power cords** for 30 seconds
3. Reconnect power
4. Boot server ([NAND Reset](https://community.hpe.com/t5/proliant-servers-ml-dl-sl/nand-reset-and-system-board-replacement/td-p/7202485?nobounce))

**Limitations:**
- Formatting **erases all iLO configuration**
- AHS logs lost
- License keys lost (must re-enter)
- If NAND is physically dead, **motherboard replacement required** ([NAND Failure Recovery](https://www.ibm.com/support/pages/how-format-nand-hpe-proliant-ilo-4))

**Prevention:**
- Update iLO firmware to latest (includes NAND wear-leveling improvements)
- Avoid excessive AHS log generation
- Use external log collection instead of storing on iLO

### POST Hang

**Symptoms:**
- Server powers on, fans spin, but no POST screen
- System hangs at "Initializing memory" or "Detecting controllers"
- No BIOS splash screen

**Common Causes:**
1. Memory configuration error
2. Failed memory module
3. RAID controller initialization failure
4. NVRAM corruption
5. CPU seating issue

**Troubleshooting Steps:**

**Step 1: Check iLO IML (Integrated Management Log)**

```bash
# Access iLO web interface
# Navigate to: Information > Integrated Management Log
# Look for:
# - Memory errors (correctable/uncorrectable)
# - POST errors
# - Controller timeout errors
```

**Step 2: Minimum Boot Configuration**

1. Power down server, remove AC power
2. Open chassis
3. Remove all components except:
- 1x CPU (in socket 1)
- 2x DIMMs (minimum required, in correct slots for single CPU)
- No PCIe cards
- No drives
4. Reconnect power, attempt boot
5. If successful: **Add components one at a time to identify failure**

**Step 3: Clear NVRAM**

1. Power down, remove AC power
2. Locate **System Maintenance Switch** (near CPU1)
3. Set **position 6 to ON**
4. Apply AC power, press power button
5. Wait for POST message: "NVRAM defaults loaded"
6. Power off, set switch **position 6 to OFF**
7. Boot normally ([POST Troubleshooting](https://support.hpe.com/hpesc/public/docDisplay?docId=sd00002310en_us&page=GUID-05CCF6BB-87DB-44CB-923C-410D7136ADA5.html&docLocale=en_US))

**Step 4: Reseat Components**

1. Reseat CPUs (remove, clean thermal paste, reinstall)
2. Reseat memory modules
3. Reseat RAID controller (if embedded, check cable connections)

**Step 5: Check RAID Controller**

```bash
# If POST hangs at "Detecting controllers"
# Boot into Intelligent Provisioning (F10)
# Navigate to Perform Maintenance > Smart Storage Administrator
# Check for:
# - Failed logical drives
# - Controller errors
# - Drive initialization timeouts
```

**If All Else Fails:**
- Replace suspect memory modules
- Try different CPU (if available)
- Update System ROM via Intelligent Provisioning recovery
- Contact HPE support (may be hardware failure)

### Smart Array Cache Failure

**Symptoms:**
- POST error: **1797 - "FBWC Cache disabled"**
- iLO shows: "Cache Status: Permanently Disabled"
- Significant write performance degradation

**Root Cause:**
- Smart Storage Battery failure (insufficient voltage)
- Cache module failure
- Firmware bug (Gen8 P420 with firmware < 6.60) ([Cache Failure](https://community.hpe.com/t5/proliant-servers-ml-dl-sl/the-cache-for-smart-array-p410i-in-embedded-slot-is-disabled/td-p/7042501))

**Diagnosis:**

```bash
# Check cache status
ssacli controller slot=0 show status

# Check battery status
ssacli controller slot=0 show detail | grep -i battery

# Example output:
# Cache Status: Temporarily Disabled (battery charging)
# Cache Status: Permanently Disabled (battery failed)
```

**Resolution Steps:**

**Step 1: Update Firmware (CRITICAL)**

Firmware **6.60+** changes "Permanently Disabled" to "Temporarily Disabled" on battery failure, allowing cache re-enable after battery replacement ([No-Battery Write Cache](https://community.hpe.com/t5/proliant-servers-ml-dl-sl/no-battery-write-cache-enable-after-smart-array-battery-fail/td-p/7074506)).

```bash
# Download Smart Array firmware from HPE support
# Flash via SPP or Intelligent Provisioning
```

**Step 2: Replace Smart Storage Battery**

**Gen8-Gen9 Battery Replacement:**

1. Identify battery location:
- Gen8: Usually near PSU or on riser card
- Gen9: Shared battery (single battery for multiple controllers) ([Gen9 Battery Sharing](https://community.hpe.com/t5/proliant-servers-ml-dl-sl/write-back-cache-battery-for-ml150-gen9-smart-array-p440/td-p/7059095?nobounce))

2. Power down server (full shutdown, remove AC power)

3. Remove old battery:
- Disconnect battery cable
- Remove battery from bracket

4. Install new HPE 96W Smart Storage Battery:
- Part number: **815983-001** or **727258-B21**
- Insert into bracket
- Connect cable (ensure firm connection)

5. Reseat cache module:
- Remove P440ar controller (if applicable)
- Remove cache module
- Clean contacts with isopropyl alcohol
- Reinstall cache module
- Reinstall controller

6. Apply AC power, boot server

**Step 3: Verify Cache Re-enabled**

```bash
# Wait 10-15 minutes for battery to charge
ssacli controller slot=0 show status

# Expected output:
# Cache Status: OK
# Battery/Capacitor Status: OK
```

**Step 4: If Cache Remains Disabled (Firmware < 6.60)**

On older firmware, cache module may remain permanently disabled even after battery replacement due to persistent NVRAM flag ([FBWC Permanently Disabled](https://community.hpe.com/t5/proliant-servers-ml-dl-sl/hpe-smart-storage-battery-for-hpe-smart-array-p440-4g-controller/td-p/6898218)).

**Workaround:**
1. Update firmware to 6.60+
2. Replace cache module **and** battery together
3. Clear controller NVRAM (consult HPE support)

**Prevention:**
- Monitor battery health monthly: `ssacli controller slot=0 show detail | grep -i battery`
- Replace batteries every **3-5 years** (proactive replacement)
- Keep spare batteries on hand

### Fan Runaway

**Symptoms:**
- Fans immediately ramp to 100% speed on boot
- Persistent high fan noise (70+ dB)
- iLO thermal log shows "Thermal Alert" or "Unsupported Configuration"

**Common Causes:**
1. Third-party hard drives installed
2. Unsupported PCIe cards
3. iLO firmware bug
4. Missing AMS daemon (Gen11)
5. Thermal sensor failure

**Troubleshooting:**

**Step 1: Check iLO Firmware Version**

```bash
# Check iLO version
curl -k -u admin:password https://ilo-ip/redfish/v1/Managers/1/ | jq '.FirmwareVersion'

# Known problematic versions:
# - iLO 5 v3.04 (DL380 Gen10 fan noise issue)
# - Update to v3.05+ to resolve
```

**Step 2: Install/Verify AMS (Gen11)**

```bash
# Check if AMS running
sudo systemctl status amsd

# If not installed:
sudo dnf install amsd
sudo systemctl enable --now amsd

# Verify iLO communication
sudo journalctl -u amsd | tail -20
```

**Step 3: BIOS Thermal Configuration Reset**

1. Boot server, press **F9**
2. Navigate to **Advanced Options > Fan and Thermal Options > Thermal Configuration**
3. Change to **Max Cooling**
4. **Save and Reboot**
5. Boot again, press **F9**
6. Change back to **Optimal Cooling**
7. **Save and Reboot** ([Fan Noise Fix](https://www.globalonetechnology.com/blog/how-to-fix-excessive-fan-noise-in-hpe-proliant-servers/))

**Step 4: Remove Unsupported Hardware**

- Replace third-party drives with HPE-certified drives
- Remove non-HPE PCIe cards (test if noise stops)

**Step 5: Manual Fan Control (LAST RESORT)**

```bash
# WARNING: Disables automatic thermal management
# Use ONLY if you understand risks
curl -k -u admin:password -X PATCH https://ilo-ip/redfish/v1/Chassis/1/ThermalSubsystem/FanPercentMinimum \
-H "Content-Type: application/json" \
-d '{"FanPercentAdjust": 40}'

# Monitor temperatures continuously
watch -n 5 'hpasmcli -s "SHOW TEMP"'
```

### Firmware Update Failure

**Symptoms:**
- Firmware update stuck at "Flashing..."
- Server won't boot after firmware update
- iLO unresponsive after update

**Recovery Steps:**

**Scenario 1: iLO Firmware Update Failed**

1. **Force iLO Reset:**
```bash
# Hold iLO reset button (rear of server) for 10 seconds
# Or via SSH:
ssh admin@ilo-ip
reset /map1
```

2. **Network Flash Recovery:**
- Remove AC power for 30 seconds
- Reconnect power (do not boot OS)
- iLO detects corrupted firmware
- Automatically attempts DHCP-based flash recovery ([iLO Flash Recovery](https://support.hpe.com/hpesc/public/docDisplay?docId=a00048142en_us&page=GUID-355C5AE4-6D7B-4973-B1C9-26ECAD6B7FEC.html&docLocale=en_US))

**Scenario 2: System ROM (BIOS) Update Failed**

1. **Boot to Intelligent Provisioning:**
- Power on server
- Press **F10** at POST
- If Intelligent Provisioning loads: **Update System ROM via IP**

2. **If Intelligent Provisioning Fails:**
- Access UEFI Shell (F11 at boot)
- Mount USB drive with System ROM `.fwpkg` file
- Flash manually via UEFI shell commands

3. **NVRAM Clear (if POST fails completely):**
- Use System Maintenance Switch method (see POST Hang section)

**Prevention:**
- **Never interrupt firmware updates** (wait for completion, even if slow)
- Use UPS for firmware updates (prevent power loss)
- Update iLO firmware **before** System ROM
- Use official SPP ISO for guided updates

---

## 13. Automation with Redfish API

### Redfish Overview

**Redfish** is a RESTful API standard developed by DMTF for modern datacenter hardware management. HPE iLO 4/5/6 fully implements the Redfish specification.

**Key Features:**
- **RESTful architecture** (HTTP/HTTPS)
- **JSON payloads** (human-readable)
- **Hypermedia-driven** (self-documenting API)
- **Authentication**: Basic Auth, Session Token, OAuth 2.0
- **Transport**: HTTPS (port 443)

**Redfish vs Legacy IPMI:**

| Feature | IPMI | Redfish |
|---------|------|---------|
| Architecture | Binary protocol (UDP/RMCP) | RESTful (HTTPS) |
| Data Format | Binary | JSON |
| Security | Weak (MD5 auth) | Strong (TLS 1.2+, token-based) |
| Discoverability | None (requires manual docs) | Self-documenting (hypermedia links) |
| Standardization | Proprietary extensions | DMTF standard |

### Redfish API Endpoints

**Root Endpoint:**
```
https://ilo-ip/redfish/v1/
```

**Authentication not required** for root endpoint.

**Key Resource Endpoints:**

| Resource | Endpoint | Description |
|----------|----------|-------------|
| Service Root | `/redfish/v1/` | Entry point, metadata |
| Systems | `/redfish/v1/Systems/1/` | Server hardware info |
| Managers | `/redfish/v1/Managers/1/` | iLO information |
| Chassis | `/redfish/v1/Chassis/1/` | Physical chassis data |
| Sessions | `/redfish/v1/SessionService/Sessions/` | Authentication sessions |
| Accounts | `/redfish/v1/AccountService/Accounts/` | User management |

### Authentication

#### Basic Authentication

```bash
# Simple GET request with basic auth
curl -k -u admin:password https://ilo-ip/redfish/v1/Systems/1/ | jq .
```

#### Session-Based Authentication (Recommended)

**Create Session:**
```bash
# Create session token
curl -k -X POST https://ilo-ip/redfish/v1/SessionService/Sessions/ \
-H "Content-Type: application/json" \
-H "OData-Version: 4.0" \
-d '{
"UserName": "admin",
"Password": "password"
}' \
-i

# Response headers include:
# X-Auth-Token: <session_token>
# Location: /redfish/v1/SessionService/Sessions/<session_id>
```

**Use Session Token:**
```bash
# Store token
TOKEN="<session_token_from_above>"

# Make authenticated requests
curl -k -H "X-Auth-Token: $TOKEN" \
-H "OData-Version: 4.0" \
-H "Accept: application/json" \
https://ilo-ip/redfish/v1/Systems/1/
```

**Delete Session:**
```bash
curl -k -X DELETE \
-H "X-Auth-Token: $TOKEN" \
https://ilo-ip/redfish/v1/SessionService/Sessions/<session_id>
```

### Common Redfish Operations

#### Get Server Power State

```bash
curl -k -u admin:password https://ilo-ip/redfish/v1/Systems/1/ | jq '.PowerState'

# Output: "On" or "Off"
```

#### Power On Server

```bash
curl -k -u admin:password -X POST https://ilo-ip/redfish/v1/Systems/1/Actions/ComputerSystem.Reset \
-H "Content-Type: application/json" \
-d '{
"ResetType": "On"
}'
```

**Reset Types:**
- `On`: Power on
- `ForceOff`: Immediate power off (like holding power button)
- `GracefulShutdown`: OS-initiated shutdown
- `ForceRestart`: Hard reset
- `Nmi`: Non-Maskable Interrupt (for debugging OS hangs)
- `PushPowerButton`: Simulate pressing power button

#### Get System Information

```bash
# Get full system details
curl -k -u admin:password https://ilo-ip/redfish/v1/Systems/1/ | jq .

# Extract specific fields
curl -k -u admin:password https://ilo-ip/redfish/v1/Systems/1/ | jq '{
Model: .Model,
SerialNumber: .SerialNumber,
Manufacturer: .Manufacturer,
BiosVersion: .BiosVersion,
ProcessorSummary: .ProcessorSummary,
MemorySummary: .MemorySummary,
PowerState: .PowerState,
IndicatorLED: .IndicatorLED
}'
```

#### Get Thermal Data

```bash
# Get temperature sensors
curl -k -u admin:password https://ilo-ip/redfish/v1/Chassis/1/Thermal | jq '.Temperatures[] | {
Name: .Name,
ReadingCelsius: .ReadingCelsius,
UpperThresholdCritical: .UpperThresholdCritical,
Status: .Status
}'

# Get fan speeds
curl -k -u admin:password https://ilo-ip/redfish/v1/Chassis/1/Thermal | jq '.Fans[] | {
Name: .Name,
Reading: .Reading,
Units: .ReadingUnits,
Status: .Status
}'
```

#### Get Power Metrics

```bash
# Get power consumption
curl -k -u admin:password https://ilo-ip/redfish/v1/Chassis/1/Power | jq '.PowerControl[0] | {
PowerConsumedWatts: .PowerConsumedWatts,
PowerCapacityWatts: .PowerCapacityWatts,
AverageConsumedWatts: .PowerMetrics.AverageConsumedWatts,
MaxConsumedWatts: .PowerMetrics.MaxConsumedWatts
}'
```

#### Mount Virtual Media

```bash
# Get virtual media status
curl -k -u admin:password https://ilo-ip/redfish/v1/Managers/1/VirtualMedia/

# Mount CD-ROM ISO
curl -k -u admin:password -X PATCH https://ilo-ip/redfish/v1/Managers/1/VirtualMedia/2/ \
-H "Content-Type: application/json" \
-d '{
"Image": "http://fileserver.local/isos/ubuntu-22.04.iso",
"Inserted": true
}'

# Unmount
curl -k -u admin:password -X PATCH https://ilo-ip/redfish/v1/Managers/1/VirtualMedia/2/ \
-H "Content-Type: application/json" \
-d '{
"Inserted": false
}'
```

### Python Automation with Redfish

**Install python-redfish Library:**

```bash
pip install redfish
```

**Example Script (Power On Servers):**

```python
#!/usr/bin/env python3
import redfish

# Server list
servers = [
{"ip": "192.168.1.100", "user": "admin", "password": "password"},
{"ip": "192.168.1.101", "user": "admin", "password": "password"},
{"ip": "192.168.1.102", "user": "admin", "password": "password"}
]

for server in servers:
try:
# Create Redfish client
client = redfish.redfish_client(
base_url=f"https://{server['ip']}",
username=server['user'],
password=server['password']
)

# Login
client.login(auth="session")

# Get power state
response = client.get("/redfish/v1/Systems/1/")
power_state = response.dict["PowerState"]

print(f"{server['ip']}: Current state: {power_state}")

# Power on if off
if power_state == "Off":
body = {"ResetType": "On"}
client.post("/redfish/v1/Systems/1/Actions/ComputerSystem.Reset", body=body)
print(f"{server['ip']}: Powered on")

# Logout
client.logout()

except Exception as e:
print(f"{server['ip']}: Error: {e}")
```

**Example Script (Collect System Inventory):**

```python
#!/usr/bin/env python3
import redfish
import json

def get_system_info(ilo_ip, user, password):
"""Collect comprehensive system information via Redfish."""

client = redfish.redfish_client(
base_url=f"https://{ilo_ip}",
username=user,
password=password
)

client.login(auth="session")

# Get system info
system = client.get("/redfish/v1/Systems/1/").dict
manager = client.get("/redfish/v1/Managers/1/").dict
chassis = client.get("/redfish/v1/Chassis/1/").dict

inventory = {
"hostname": system.get("HostName"),
"model": system.get("Model"),
"serial_number": system.get("SerialNumber"),
"bios_version": system.get("BiosVersion"),
"ilo_firmware": manager.get("FirmwareVersion"),
"processor_count": system.get("ProcessorSummary", {}).get("Count"),
"processor_model": system.get("ProcessorSummary", {}).get("Model"),
"total_memory_gb": system.get("MemorySummary", {}).get("TotalSystemMemoryGiB"),
"power_state": system.get("PowerState"),
"health_status": system.get("Status", {}).get("Health")
}

client.logout()

return inventory

# Example usage
ilo_ip = "192.168.1.100"
info = get_system_info(ilo_ip, "admin", "password")
print(json.dumps(info, indent=2))
```

### Ansible Automation

**Install HPE Ansible Collection:**

```bash
ansible-galaxy collection install hpe.ilo
```

**Example Playbook (Power Management):**

```yaml
---
- name: Power on HPE ProLiant servers
hosts: localhost
gather_facts: no

vars:
ilo_servers:
- ip: 192.168.1.100
user: admin
password: password
- ip: 192.168.1.101
user: admin
password: password

tasks:
- name: Power on servers
hpe.ilo.ilo_redfish_command:
category: Systems
command: PowerOn
baseuri: "{{ item.ip }}"
username: "{{ item.user }}"
password: "{{ item.password }}"
loop: "{{ ilo_servers }}"
ignore_errors: yes
```

**Example Playbook (Firmware Update):**

```yaml
---
- name: Update HPE ProLiant firmware
hosts: localhost
gather_facts: no

vars:
ilo_ip: 192.168.1.100
ilo_user: admin
ilo_password: password
firmware_image: "http://fileserver/firmware/ilo5_270.bin"

tasks:
- name: Update iLO firmware
hpe.ilo.ilo_redfish_command:
category: Manager
command: VirtualMediaInsert
baseuri: "{{ ilo_ip }}"
username: "{{ ilo_user }}"
password: "{{ ilo_password }}"
virtual_media:
image_url: "{{ firmware_image }}"
media_types:
- CD
- DVD

- name: Trigger firmware update
hpe.ilo.ilo_redfish_command:
category: Manager
command: FirmwareUpdate
baseuri: "{{ ilo_ip }}"
username: "{{ ilo_user }}"
password: "{{ ilo_password }}"
```

---

## 14. Appendices

### Appendix A: ssacli Quick Reference

**Common ssacli Commands:**

| Command | Description |
|---------|-------------|
| `ssacli controller all show config` | Show all controller configurations |
| `ssacli controller slot=0 show status` | Show controller status |
| `ssacli controller slot=0 pd all show` | Show all physical drives |
| `ssacli controller slot=0 ld all show` | Show all logical drives |
| `ssacli controller slot=0 create type=ld drives=1I:1:1,1I:1:2 raid=1` | Create RAID-1 array |
| `ssacli controller slot=0 create type=ld drives=1I:1:1,1I:1:2,1I:1:3,1I:1:4 raid=10` | Create RAID-10 array |
| `ssacli controller slot=0 ld 1 delete` | Delete logical drive 1 |
| `ssacli controller slot=0 modify rebuildpriority=high` | Set rebuild priority |
| `ssacli controller slot=0 array A add spares=allunassigned` | Add spare drives |
| `ssacli controller slot=0 modify hbamode=on forced` | Enable HBA mode (P420/P440, fw 6.60+) |

### Appendix B: iLO Quick Reference

**iLO Access Methods:**

| Method | URL/Command | Notes |
|--------|-------------|-------|
| Web Interface | `https://ilo-ip/` | HTTPS port 443 |
| SSH | `ssh admin@ilo-ip` | Port 22 |
| HTML5 Console | Web Interface → Remote Console → HTML5 | No plugins required |
| Redfish API | `https://ilo-ip/redfish/v1/` | RESTful API |
| hponcfg (local) | `sudo hponcfg -g` | Requires OS-level access |

**Default Credentials (Factory Reset):**
- Username: `Administrator`
- Password: **Printed on server pull-out tag** (unique per server)

**Reset iLO to Factory Defaults:**

```xml
<!-- reset_ilo.xml -->
<RIBCL VERSION="2.0">
<LOGIN USER_LOGIN="Administrator" PASSWORD="password">
<RIB_INFO MODE="write">
<FACTORY_DEFAULTS/>
</RIB_INFO>
</LOGIN>
</RIBCL>
```

```bash
sudo hponcfg -f reset_ilo.xml
```

### Appendix C: Firmware Update Workflow

**Recommended Firmware Update Sequence:**

```
1. Backup AHS Log (Information > Active Health System > Download)
2. Document current firmware versions
3. Download latest SPP ISO from HPE Support
4. Update iLO Firmware
├─> Via iLO Web Interface (Administration > Firmware)
└─> Or via Redfish API
5. Reboot server (iLO firmware takes effect)
6. Update System ROM (BIOS)
├─> Via Intelligent Provisioning (F10 > Perform Maintenance > Update Firmware)
└─> Or via SPP Bootable ISO
7. Update Smart Array Controller Firmware
8. Update NIC Firmware
9. Update Power Management Firmware
10. Final reboot
11. Verify all components updated: Review SPP update log
```

### Appendix D: Platform Generation Matrix

| Generation | Years | Xeon Platform | DDR Type | PCIe Gen | iLO | Key Feature |
|------------|-------|---------------|----------|----------|-----|-------------|
| G6 | 2009-2010 | Xeon 5500/5600 | DDR3 | Gen2 | iLO2 | First modern |
| G7 | 2010-2012 | Xeon 5600 | DDR3-1333 | Gen2 | iLO3 | Hyper-Threading |
| G8 | 2012-2014 | Xeon E5-2600 | DDR3-1600 | Gen3 | iLO4 | Silicon Root of Trust |
| G9 | 2014-2017 | Xeon E5-2600 v3/v4 | DDR4 | Gen3 | iLO4 | RESTful API maturity |
| Gen10 | 2017-2021 | Xeon Scalable | DDR4 | Gen3 | iLO5 | Secure Compute Lifecycle |
| Gen10+ | 2021-2022 | Xeon Scalable Gen3 | DDR4 | Gen4 | iLO5 | PCIe Gen4 |
| Gen11 | 2022-2025 | Xeon Scalable Gen4, AMD EPYC 9004+ | DDR5 | Gen5 | iLO6 | DDR5, PCIe Gen5 |
| Gen12 | 2024+ | Xeon Scalable Gen5, AMD EPYC 9005 | DDR5 | Gen5 | iLO7 | Quantum-resistant crypto |

### Appendix E: OEM Terminology Glossary

| Term | Full Name | Description |
|------|-----------|-------------|
| AHS | Active Health System | Continuous hardware health monitoring and logging (1600+ parameters) |
| AMS | Agentless Management Service | OS-level daemon for hardware monitoring without agents (Gen10+) |
| ASIC | Application-Specific Integrated Circuit | Custom chip (e.g., iLO management processor) |
| BBWC | Battery-Backed Write Cache | RAID cache with battery backup (G6-G7 era) |
| FBWC | Flash-Backed Write Cache | RAID cache with NAND flash backup (Gen8+) |
| HBA | Host Bus Adapter | Passthrough mode for RAID controllers (no RAID, direct disk access) |
| iLO | Integrated Lights-Out | HPE's out-of-band management processor |
| IML | Integrated Management Log | Hardware event log stored in iLO |
| IP | Intelligent Provisioning | UEFI-based deployment environment for OS/firmware installation |
| NUMA | Non-Uniform Memory Access | Memory architecture where CPUs have local + remote memory |
| RBSU | ROM-Based Setup Utility | BIOS/UEFI configuration interface (press F9 at boot) |
| Redfish | - | DMTF RESTful API standard for hardware management |
| SPP | Service Pack for ProLiant | Bundled firmware/driver ISO released twice yearly |
| SmartMemory | - | HPE-certified ECC memory with health monitoring |

### Appendix F: Useful Resources

**Official HPE Documentation:**
- [HPE Support and Certification Matrices](https://www.hpe.com/us/en/collaterals/collateral.a50010841enw.html)
- [HPE Service Pack for ProLiant](https://support.hpe.com/docs/display/public/a00sppdocen_US/spp/)
- [iLO RESTful API Documentation](https://developer.hpe.com/platform/ilo-restful-api/home/)
- [HPE Redfish API Reference (iLO 5)](https://hewlettpackard.github.io/ilo-rest-api-docs/ilo5/)

**Community Resources:**
- [HPE Community Forums](https://community.hpe.com/)
- [ServeTheHome HPE Content](https://www.servethehome.com/tag/hpe/)
- [Reddit r/homelab](https://www.reddit.com/r/homelab/) - Homelab discussions
- [Reddit r/DataHoarder](https://www.reddit.com/r/DataHoarder/) - Storage-focused discussions

**Linux Driver Documentation:**
- [Kernel hpsa Driver Documentation](https://docs.kernel.org/scsi/hpsa.html)
- [Debian HP/ProLiant Wiki](https://wiki.debian.org/HP/ProLiant)

**Tools:**
- [HPE Power Advisor](https://paonline56.itcs.hpe.com/?Page=Index) - Power consumption planning
- [python-redfish Library](https://pypi.org/project/redfish/) - Python Redfish automation
- [Ansible HPE Collection](https://galaxy.ansible.com/hpe/ilo) - Ansible automation

---

## Conclusion

This guide provides a comprehensive foundation for managing HPE ProLiant servers and iLO remote management systems in enterprise and homelab environments. The ProLiant platform has evolved significantly from G6 to Gen11, with each generation bringing improvements in performance, security, and management capabilities.

**Key Takeaways:**

1. **Firmware Lifecycle Management:**
- Use SPP for consistent, tested firmware updates
- Update iLO before System ROM
- Keep firmware current for security and stability

2. **Linux Compatibility:**
- hpsa driver provides excellent Linux support for Smart Array controllers
- RHEL, Ubuntu, and Proxmox are well-supported
- Install hp-health, ssacli, and amsd for full hardware monitoring

3. **Remote Management:**
- iLO provides powerful out-of-band management
- Redfish API enables automation at scale
- Dedicate management network for security

4. **Homelab Considerations:**
- Gen10+ servers offer best balance of performance, power, and noise
- Update firmware to reduce fan noise
- Monitor power consumption for cost optimization

5. **Security:**
- Silicon Root of Trust (Gen10+) provides hardware-based firmware validation
- Enable UEFI Secure Boot for OS integrity
- Isolate iLO management network

**Further Learning:**
- Experiment with Redfish API automation
- Build Ansible playbooks for infrastructure-as-code
- Contribute to community knowledge sharing

This field guide will be updated as new ProLiant generations and firmware capabilities are released.

---

**Document Version:** 1.0 
**Last Updated:** 2026-05-25 
**Author:** Compiled from official HPE documentation, community resources, and production experience 
**License:** Free for personal and professional use, attribution appreciated

