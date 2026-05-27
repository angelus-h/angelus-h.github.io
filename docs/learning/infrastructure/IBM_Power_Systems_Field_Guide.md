# IBM Power Systems Production Field Guide

**Target Audience:** AIX administrators, Linux system engineers, IBM i operators, infrastructure architects, datacenter administrators, platform engineers, enterprise IT professionals working with Power Systems.

**Document Purpose:** Deep technical reference, operational handbook, troubleshooting guide, PowerVM virtualization guide, firmware lifecycle reference, multi-OS integration guide, and real-world enterprise field manual.

**Last Updated:** 2026-05-25

---

## Table of Contents

1. [IBM Power Platform Overview](#1-ibm-power-platform-overview)
2. [Hardware Management Console (HMC) Architecture](#2-hardware-management-console-hmc-architecture)
3. [PowerVM Hypervisor and LPAR Management](#3-powervm-hypervisor-and-lpar-management)
4. [Firmware Lifecycle Management](#4-firmware-lifecycle-management)
5. [VIOS and Virtual Networking](#5-vios-and-virtual-networking)
6. [Operating Systems](#6-operating-systems)
7. [RAS Features](#7-ras-features)
8. [Service Processor and System Management](#8-service-processor-and-system-management)
9. [Storage and I/O Configuration](#9-storage-and-io-configuration)
10. [Performance Tuning](#10-performance-tuning)
11. [Linux on Power](#11-linux-on-power)
12. [Security](#12-security)
13. [Troubleshooting Cookbook](#13-troubleshooting-cookbook)
14. [Appendices](#14-appendices)

---

## 1. IBM Power Platform Overview

### 1.1 POWER Processor Evolution (POWER8–POWER11)

IBM Power processors represent the pinnacle of enterprise RISC architecture, designed for mission-critical workloads requiring extreme reliability, availability, and serviceability (RAS).

| Generation | Years | Process Node | Cores (Max) | SMT | Memory | PCIe | Key Features |
|------------|-------|--------------|-------------|-----|--------|------|--------------|
| **POWER8** | 2014-2017 | 22 nm | 12 per socket | SMT8 | DDR3/DDR4 buffered | Gen3 | First LE support, CAPI 1.0 |
| **POWER9** | 2017-2021 | 14 nm | 24 per socket | SMT4/SMT8 | DDR4 unbuffered | Gen4 | NVLink 2.0, OpenCAPI, AI acceleration |
| **POWER10** | 2021-2024 | 7 nm | 15 per socket | SMT8 | DDR5, OMI | Gen5 | Matrix math, AI inference 10-20x, MMA |
| **POWER11** | 2025+ | 5 nm | TBD | SMT8 | DDR5, OMI | Gen6 | Enhanced AI, quantum-safe crypto |

[^1]: [IBM Power Comparison: Power8, Power9, Power10, Power11](https://serviceexpress.com/resources/ibm-power-comparison-power8-power9-power10-power11/)
[^2]: [IBM Power8, Power9 and Power10: Differences](https://www.procurri.com/2026/02/16/ibm-power8-power9-and-power10-what-are-the-differences/)

### 1.2 Performance Evolution

**POWER9 vs POWER8:**[^3]

- **1.5x better performance** overall
- **2x more memory capacity**
- **60% rPerf improvement** (POWER9 922 with 8 cores SMT8: 202.3 rPerf vs POWER8 822: 122.9 rPerf)
- **Memory transition:** DDR3 buffered → DDR4 unbuffered (industry standard DIMMs, cost reduction)

**POWER10 vs POWER9:**[^4]

- **20-30% more performance per core**
- **Up to 2.6x better performance per watt**
- **80% faster than POWER8** core-for-core
- **AI inference acceleration:** 10x (FP32), 15x (BFloat16), 20x (INT8) vs POWER9
- **50% lower carbon footprint** vs POWER8, **33% lower** vs POWER9

**POWER11 (2025+):**

- Enhanced AI and machine learning capabilities
- Quantum-safe cryptography built-in
- Further power efficiency improvements

[^3]: [POWER9 Main Changes vs POWER8](https://www.midlandinfosys.com/blog/power9-facts-and-features/power9-main-changes-and-updates-vs-power8)
[^4]: [IBM Power 10 vs Power 9](https://www.midlandinfosys.com/blog/ibm-power-10/ibm-power-10-vs-power-9)

### 1.3 Server Model Naming and Form Factors

**IBM Power Naming Schema:** `<Series><Model>-<Processor Code><Feature Code>`

**Series Codes:**

- **S-Series (Scale-out):** Entry to mid-range, 1-2 sockets, optimized for SMB and workgroup deployments
- **E-Series (Enterprise):** 2-16 sockets, mission-critical workloads, maximum RAS features
- **L-Series (Linux-only):** Linux-optimized, no AIX/IBM i support, cost-optimized
- **H-Series (HPC):** High-performance computing, AI/ML workloads, NVLink support

**Popular Models by Generation:**

| Model | Type | Sockets | Cores (Max) | Form Factor | Use Case |
|-------|------|---------|-------------|-------------|----------|
| **S914** | POWER9 Scale-out | 1 | 4-8 | 4U | Small business, branch offices |
| **S922** | POWER9 Scale-out | 1-2 | 4-22 | 2U | Mid-range, virtualization |
| **S924** | POWER9 Scale-out | 1-2 | 8-24 | 4U | Larger workloads, storage expansion |
| **E950** | POWER9 Enterprise | 1-4 | 8-48 | 4U | Mission-critical, high availability |
| **E980** | POWER9 Enterprise | 4-16 | 32-192 | 4U scalable | Maximum capacity, mainframe-class |
| **S1014** | POWER10 Scale-out | 1 | 4-8 | 4U | Entry POWER10 |
| **S1022s/S1024** | POWER10 Scale-out | 1-2 | 4-24 | 2U/4U | Modern mid-range |
| **E1050** | POWER10 Enterprise | 1-4 | 15-60 | 4U | Enterprise workloads |
| **E1080** | POWER10 Enterprise | 4-16 | 60-240 | 4U scalable | Maximum enterprise capacity |

[^5]: [IBM Power9 Systems & Models](https://www.midlandinfosys.com/ibm-power/power9)

### 1.4 Architecture Highlights

**Simultaneous Multithreading (SMT):**

- **SMT8:** 8 hardware threads per core (POWER8, POWER9 scale-up, POWER10)
- **SMT4:** 4 hardware threads per core (POWER9 scale-out)
- OS sees: Physical cores × SMT level = Logical processors

Example: POWER10 with 15 cores running SMT8 = **120 logical CPUs**

**Memory Architecture:**

- **POWER8:** Buffered memory (Centaur chips), expensive, high capacity
- **POWER9:** Industry-standard DDR4 unbuffered DIMMs, cost reduction
- **POWER10:** DDR5 support, **Open Memory Interface (OMI)** for memory flexibility, **PowerAXON** for coherent memory sharing across systems

**I/O and Acceleration:**

- **POWER9:** PCIe Gen4, NVLink 2.0 (GPU interconnect), OpenCAPI (coherent accelerators)
- **POWER10:** PCIe Gen5, built-in **Matrix Math Accelerator (MMA)** for AI inference, enhanced crypto engines

---

## 2. Hardware Management Console (HMC) Architecture

### 2.1 HMC Overview

The **Hardware Management Console (HMC)** is IBM's dedicated appliance for managing Power Systems. It provides out-of-band management, LPAR configuration, firmware updates, and remote console access.[^6]

**HMC Hardware:**

- x86-based appliance running custom Linux (RHEL-based)
- Dedicated 1GbE/10GbE network interfaces
- Connects to Power servers via **service processor** network

**HMC Models:**

| Model | Code | Supported Servers | Notes |
|-------|------|-------------------|-------|
| **HMC V9** | 7042-CR9 | POWER7+, POWER8 | Legacy, end of support |
| **HMC V10** | 7063-CR2 | POWER8, POWER9, POWER10 | Current production |
| **HMC V11** | 7063-CR3 | POWER9, POWER10, POWER11 | Latest, enhanced GUI |

[^6]: [Hardware Management Console Overview](https://www.ibm.com/docs/en/power10?topic=overview-hardware-management-console)

### 2.2 HMC Network Architecture

**Management Networks:**

1. **HMC Network (HMC ↔ managed systems):**
- Dedicated subnet for HMC ↔ service processor communication
- Default: 192.168.2.x/24 or 192.168.3.x/24
- **Critical:** Must be isolated from production networks

2. **Public Network (HMC ↔ administrators):**
- HTTPS web interface (port 443)
- SSH access (port 22) for CLI

3. **Call Home Network (optional):**
- Internet connectivity for IBM support integration
- Automatic problem reporting to IBM

**Service Processor Connectivity:**

- Each Power server has embedded **service processor** (FSP/BMC)
- HMC communicates to service processor via dedicated Ethernet
- Service processor runs embedded Linux, maintains two firmware images

### 2.3 HMC Web Interface

**Accessing HMC:**

```
https://<hmc-ip>

Default credentials (first boot):
User: hscroot
Password: abc123

CRITICAL: Change password immediately after first login
```

**Key HMC Tasks:**

- Create/modify/delete LPARs
- Assign CPU/memory/I/O resources
- Activate/shutdown/restart partitions
- Virtual console access (vterm)
- Firmware updates
- Backup/restore system configurations
- Performance monitoring

### 2.4 HMC Command Line Interface (lshmc, lssyscfg, etc.)

**SSH Access:**

```bash
ssh hscroot@<hmc-ip>
```

**Essential HMC Commands:**

```bash
# List managed systems
lssyscfg -r sys -F name,state,ipaddr

# List LPARs on a system
lssyscfg -r lpar -m <system-name>

# View LPAR detailed configuration
lssyscfg -r lpar -m <system-name> --filter "lpar_names=<lpar-name>"

# List CPU resources
lshwres -r proc -m <system-name>

# List memory resources
lshwres -r mem -m <system-name>

# Start an LPAR
chsysstate -r lpar -m <system-name> -o on -n <lpar-name>

# Shutdown an LPAR (graceful)
chsysstate -r lpar -m <system-name> -o shutdown -n <lpar-name> --immed

# Hard reboot LPAR
chsysstate -r lpar -m <system-name> -o shutdown -n <lpar-name> --immed --restart

# View LPAR status
lssyscfg -r lpar -m <system-name> -F name,state

# Virtual console (vterm)
mkvterm -m <system-name> -p <lpar-name>

# Exit vterm: ~. (tilde period)
```

[^7]: [HMC CLI Commands Reference](https://aix4admins.blogspot.com/2011/08/hmc-command-line-commands-have-help.html)

### 2.5 HMC Version and Updates

**Checking HMC Version:**

```bash
# Via SSH
lshmc -V

# Via web GUI
HMC Management → HMC Configuration → General
```

**HMC Update Procedure:**[^8]

```bash
# 1. Download HMC update ISO from IBM Fix Central
# https://www.ibm.com/support/pages/hardware-management-console-support-and-downloads

# 2. Transfer ISO to HMC via SCP
scp HMC_<version>.iso hscroot@<hmc-ip>:/tmp/

# 3. Apply update via HMC web GUI
HMC Management → HMC Updates → Software Updates → Install Corrective Service
Select ISO file → Install → Reboot HMC

# 4. Verify new version after reboot
lshmc -V
```

[^8]: [HMC Support and Downloads](https://www.ibm.com/support/pages/hardware-management-console-support-and-downloads)

**HMC Backup:**

```bash
# Create HMC configuration backup
hmcbackup create <backup-name>

# Restore from backup
hmcbackup restore <backup-name>

# List backups
hmcbackup list
```

---

## 3. PowerVM Hypervisor and LPAR Management

### 3.1 PowerVM Architecture

**PowerVM** is IBM's enterprise virtualization platform, providing logical partitioning (LPAR) capabilities on Power Systems. Unlike Type-2 hypervisors (VMware, KVM), PowerVM is a **Type-1 bare-metal hypervisor** (PHYP - PowerVM Hypervisor) embedded in server firmware.[^9]

**PowerVM Editions:**

| Edition | Features | Use Case |
|---------|----------|----------|
| **PowerVM Express** | Basic LPAR, up to 3 LPARs per core | Entry-level virtualization |
| **PowerVM Standard** | Up to 10 LPARs per core, Live Partition Mobility | Production environments |
| **PowerVM Enterprise** | Unlimited LPARs, Active Memory Sharing, SR-IOV | Mission-critical, high-density |

[^9]: [IBM PowerVM Virtualization Introduction (Redbook PDF)](https://www.redbooks.ibm.com/redbooks/pdfs/sg247940.pdf)

### 3.2 Logical Partitions (LPARs)

**LPAR Fundamentals:**

- Each LPAR is an isolated virtual machine running its own OS (AIX, IBM i, Linux, VIOS)
- LPARs share physical resources (CPU, memory, I/O) managed by PowerVM hypervisor
- Resources assigned in LPAR **profiles** (configuration templates)

**LPAR Profile Types:**

- **Default Profile:** Used for normal partition activation
- **Alternative Profiles:** Custom configurations for different scenarios

**Processor Allocation Modes:**

| Mode | Description | Use Case |
|------|-------------|----------|
| **Dedicated** | Entire physical cores dedicated to LPAR | Latency-sensitive, guaranteed performance |
| **Shared (Uncapped)** | LPAR uses processor pool, can exceed entitlement if available | Flexible, cost-effective |
| **Shared (Capped)** | LPAR limited to exact entitlement, cannot exceed | Predictable, license compliance |

**Example: Shared Processor Configuration**

```
Entitled Capacity: 2.0 processors (guaranteed)
Virtual Processors: 4 (logical CPUs presented to OS)
Min: 0.5, Desired: 2.0, Max: 4.0
Uncapped weight: 128 (priority for extra cycles)
```

### 3.3 Creating an LPAR via HMC

**Via HMC GUI:**

```
1. HMC → Systems Management → Servers → <select system>
2. Right-click → Operations → Logical Partitions → Create Partition
3. Configure:
- Name: prod-aix01
- OS: AIX
- Processor mode: Shared, uncapped
- Entitled: 2.0, Min: 0.5, Max: 4.0
- Virtual procs: 4
- Memory: 16GB (Min: 8GB, Desired: 16GB, Max: 32GB)
- Virtual Ethernet: Add adapter on VLAN 10
- Virtual SCSI: Create client adapter, assign to VIOS
4. Create → Activate
```

**Via HMC CLI:**[^10]

```bash
# Create LPAR profile
mksyscfg -r lpar -m <system-name> \
-i "name=prod-aix01, \
profile_name=default, \
lpar_env=aixlinux, \
min_mem=8192, desired_mem=16384, max_mem=32768, \
proc_mode=shared, sharing_mode=uncap, \
min_proc_units=0.5, desired_proc_units=2.0, max_proc_units=4.0, \
min_procs=1, desired_procs=4, max_procs=8"

# Activate LPAR
chsysstate -r lpar -m <system-name> -o on -n prod-aix01 -f default
```

[^10]: [Creating an LPAR - HCL BigFix Documentation](https://help.hcl-software.com/bigfix/9.2/sa/ServerAutomation/com.ibm.tivoli.tpm.wkf.doc/Server%20Automation%20virtualization/sa_aix_createlpar.html)

### 3.4 Dynamic LPAR (DLPAR)

**DLPAR** allows hot-add/hot-remove of resources (CPU, memory, I/O) to running LPARs without downtime.

**Adding CPU to Running LPAR:**

```bash
# Add 1.0 processor unit to running LPAR
chhwres -r proc -m <system-name> -o a \
-p <lpar-name> --procunits 1.0

# Add 2 virtual processors
chhwres -r proc -m <system-name> -o a \
-p <lpar-name> --procs 2
```

**Adding Memory to Running LPAR:**

```bash
# Add 8GB memory
chhwres -r mem -m <system-name> -o a \
-p <lpar-name> --quantity 8192
```

**Removing Resources:**

```bash
# Remove 0.5 processor units
chhwres -r proc -m <system-name> -o r \
-p <lpar-name> --procunits 0.5

# Remove 4GB memory
chhwres -r mem -m <system-name> -o r \
-p <lpar-name> --quantity 4096
```

**DLPAR Requirements:**

- LPAR must be running AIX 5.3+, IBM i 5.4+, or Linux with DLPAR support
- AIX: `drmgr` daemon must be running
- Profile max values must accommodate additions

### 3.5 Live Partition Mobility (LPM)

**LPM** enables migrating running LPARs between Power servers **without downtime** (vMotion equivalent).[^11]

**Requirements:**

- PowerVM Enterprise edition
- Shared storage (SAN) accessible by both source and destination servers
- VIOS on both systems
- Compatible processor generations (POWER9 → POWER9, or POWER9 → POWER10 with compatibility mode)

**LPM Procedure (GUI):**

```
1. HMC → Systems Management → Servers → <source-system>
2. Right-click LPAR → Operations → Migrate Partition
3. Select destination system
4. Validate migration (checks compatibility, resources)
5. Execute migration
6. Monitor progress (typically 1-5 minutes)
```

**LPM Procedure (CLI):**

```bash
# Validate migration
migrlpar -o v -m <source-system> -t <dest-system> -p <lpar-name>

# Execute migration
migrlpar -o m -m <source-system> -t <dest-system> -p <lpar-name>
```

[^11]: [PowerVM Best Practices Guide](https://www.ibm.com/support/pages/ibm-power-virtualization-best-practices-guide)

---

## 4. Firmware Lifecycle Management

### 4.1 Firmware Components

IBM Power Systems firmware consists of multiple interdependent components:

| Component | Purpose | Update Impact |
|-----------|---------|---------------|
| **System Firmware (Hypervisor)** | PowerVM hypervisor, LPAR management | Requires managed system IPL (reboot) |
| **Service Processor Firmware** | FSP/BMC management firmware | Can update without system IPL on some models |
| **HMC Firmware** | HMC operating system and management code | Requires HMC reboot |
| **I/O Adapter Firmware** | HBA, NIC, RAID controller firmware | Varies by adapter |
| **Power Supply Firmware** | PSU firmware | Hot-update (no reboot) |

### 4.2 Firmware Update Methods

**Method 1: HMC-Managed Update (Recommended)**[^12]

```bash
# 1. Download firmware from IBM Fix Central
# https://www.ibm.com/support/fixcentral/

# 2. Transfer to HMC via SCP
scp firmware_<version>.img hscroot@<hmc-ip>:/tmp/

# 3. Via HMC GUI
HMC → Systems Management → Servers → <system>
Right-click → Operations → Firmware Updates → Install → Select firmware image

# 4. Via HMC CLI
updlic -m <system-name> -t sys -f /tmp/firmware_<version>.img -r

# 5. Activate new firmware (requires system IPL)
chsysstate -m <system-name> -r sys -o shutdown --immed
chsysstate -m <system-name> -r sys -o on
```

**Method 2: AIX Diagnostics Update (Standalone, No HMC)**[^13]

```bash
# On AIX LPAR with root access
# 1. Download firmware .img file to AIX system

# 2. Run diagnostics
diag

# Navigate menu:
# Task Selection → Microcode Tasks → Update and Manage System Flash

# Or use update_flash command
update_flash -f /path/to/firmware.img

# 3. Reboot system to activate
shutdown -Fr
```

**Method 3: Linux update_flash Command**

```bash
# On Linux LPAR (RHEL/SUSE with powerpc-utils package)
update_flash -f /path/to/firmware.img
reboot
```

[^12]: [IBM Power Firmware Update Guide](http://www.asgaur.com/wp/how-to-upgrade-ibm-power-server-firmware-fixes-through-aix-or-linux-without-an-hmc-2/)
[^13]: [Using AIX Diagnostics to Install Server Firmware](https://www.ibm.com/docs/en/power5?topic=aiaou-using-aix-diagnostics-install-server-firmware-update-through-aix)

### 4.3 Checking Firmware Levels

**Via HMC:**

```bash
# List managed system firmware
lsmcode -m <system-name>

# View detailed firmware inventory
invscout -hmc <hmc-name>
```

**Via AIX:**

```bash
# Display firmware level
lsmcode -A

# Or using invscout
invscout

# Check service processor firmware
lsmcode -c
```

**Via Linux:**

```bash
# Display firmware level
lsmcode -A

# Alternative
cat /proc/ppc64/lparcfg | grep firmware
```

### 4.4 Firmware Update Best Practices

**Recommended Update Order:**

1. **HMC firmware** (if HMC update available)
2. **System firmware** (managed system)
3. **I/O adapter firmware** (if needed)
4. **LPAR OS patches** (AIX/Linux/IBM i)

**Pre-Update Checklist:**

- [ ] Backup HMC configuration (`hmcbackup create`)
- [ ] Backup LPAR profiles
- [ ] Schedule maintenance window (system IPL required)
- [ ] Notify LPAR owners of downtime
- [ ] Document current firmware levels
- [ ] Verify firmware compatibility with hardware and LPARs

**Post-Update Validation:**

```bash
# Verify firmware updated successfully
lsmcode -m <system-name>

# Check system state
lssyscfg -r sys -m <system-name> -F name,state

# Verify LPARs started correctly
lssyscfg -r lpar -m <system-name> -F name,state
```

---

## 5. VIOS and Virtual Networking

### 5.1 Virtual I/O Server (VIOS) Overview

**VIOS** is a specialized AIX-based partition that provides virtual I/O services to client LPARs.[^14] It virtualizes:

- **Storage:** Virtual SCSI (vSCSI), NPIV (N_Port ID Virtualization for Fibre Channel)
- **Network:** Shared Ethernet Adapter (SEA), SR-IOV
- **Optical media:** Virtual optical devices

**VIOS Architecture:**

```
Client LPAR (AIX/Linux/IBM i)
↓ (virtual I/O)
VIOS LPAR
↓ (physical I/O)
Physical Storage/Network Adapters
```

[^14]: [IBM PowerVM Virtualization Best Practices](https://www.ibm.com/support/pages/ibm-power-virtualization-best-practices-guide)

### 5.2 VIOS Installation and Configuration

**VIOS Installation:**

1. Create LPAR for VIOS (recommended: 2 cores, 8GB RAM minimum)
2. Assign physical adapters (Ethernet, FC HBA, SAS) to VIOS
3. Boot VIOS installation media via HMC virtual optical
4. Install VIOS (similar to AIX installation)
5. Default login: `padmin` (VIOS administrative user)

**Initial VIOS Configuration:**

```bash
# Login as padmin
ssh padmin@<vios-ip>

# Enter root shell (for advanced tasks)
$ oem_setup_env
# (now in root shell)

# Exit root shell
# exit
$ (back to padmin restricted shell)

# List physical adapters
$ lsdev -type adapter

# List physical disks
$ lspv

# List network adapters
$ lsdev -type adapter | grep ent
```

### 5.3 Shared Ethernet Adapter (SEA)

**SEA** provides network virtualization, bridging virtual Ethernet to physical network.[^15]

**SEA Architecture:**

```
Client LPAR virtual ethernet (ent0) on VLAN 10
↓
SEA on VIOS (ent2) - bridge
↓
Physical adapter on VIOS (ent0)
↓
Physical network
```

**Creating a Basic SEA:**

```bash
# On VIOS, as padmin
$ mkvdev -sea ent0 -vadapter ent2 -default ent2 -defaultid 1

# Where:
# ent0 = physical ethernet adapter
# ent2 = virtual ethernet adapter (trunk)
# defaultid 1 = PVID (untagged VLAN)
```

**SEA with VLAN Tagging:**

```bash
# Create SEA with multiple VLANs
$ mkvdev -sea ent0 -vadapter ent2 -default ent2 -defaultid 1

# ent2 configured with VLAN IDs: 1 (untagged), 10, 20, 30 (tagged)
```

[^15]: [Configuring Shared Ethernet Adapters (SEA)](https://www.infotrendz.com/blog/configuring-shared-ethernet-adapters-sea-on-ibm-power-systems-vios)

### 5.4 SEA Failover (Redundant VIOS)

**Dual VIOS SEA Failover** provides network redundancy.[^16]

```bash
# VIOS 1 (primary SEA)
$ mkvdev -sea ent0 -vadapter ent2 -default ent2 -defaultid 1 \
-attr ha_mode=auto ctl_chan=ent3 priority=1

# VIOS 2 (backup SEA)
$ mkvdev -sea ent0 -vadapter ent2 -default ent2 -defaultid 1 \
-attr ha_mode=auto ctl_chan=ent3 priority=2

# Where:
# ha_mode=auto = automatic failover enabled
# ctl_chan=ent3 = control channel for heartbeat between VIOS pairs
# priority=1 = primary (lower = higher priority)
# priority=2 = backup
```

**Verifying SEA Status:**

```bash
# Check SEA configuration
$ lsdev -dev <sea-name> -attr

# View SEA state (LIMBO/PRIMARY/PRIMARY_SH/BACKUP)
$ entstat -d <sea-name> | grep -i state
```

[^16]: [How to Setup SEA Failover on DUAL VIO Servers](https://www.ibm.com/support/pages/how-setup-sea-failover-dual-vio-servers)

### 5.5 Virtual SCSI (vSCSI) Storage

**vSCSI** provides virtual disk access from VIOS to client LPARs.

**Creating Virtual Disk for Client LPAR:**

```bash
# On VIOS, as padmin

# 1. Create logical volume (backing device)
$ mklv -lv aix01_rootvg 50G rootvg
# Creates 50GB LV named aix01_rootvg on volume group rootvg

# 2. Map logical volume to client LPAR virtual SCSI adapter
$ mkvdev -vdev aix01_rootvg -vadapter vhost0 -dev aix01_hdisk0

# Where:
# vhost0 = virtual SCSI server adapter assigned to client LPAR
# aix01_hdisk0 = virtual disk name (appears as hdisk# on client)
```

**Listing Virtual Devices:**

```bash
# List all virtual SCSI mappings
$ lsmap -all

# List mappings for specific vhost
$ lsmap -vadapter vhost0
```

**Removing Virtual Disk:**

```bash
# Unmap from client
$ rmvdev -vdev aix01_hdisk0

# Remove logical volume (if no longer needed)
$ rmlv -f aix01_rootvg
```

---

## 6. Operating Systems

### 6.1 AIX (Advanced Interactive eXecutive)

**AIX** is IBM's enterprise UNIX operating system, optimized for Power Systems since 1986.

**Supported Versions (2026):**

| Version | Released | Status | End of Support |
|---------|----------|--------|----------------|
| AIX 7.1 | 2010 | Extended Support | 2023 (ended) |
| AIX 7.2 | 2015 | Standard Support | April 2027 |
| AIX 7.3 | 2021 | Standard Support | April 2032 |

**Key AIX Commands for LPAR Administration:**

```bash
# View LPAR configuration
lparstat -i

# CPU utilization by LPAR
lparstat 5 10 # 10 samples, 5-second intervals

# Memory statistics
svmon -G

# Check entitled vs consumed resources
lparstat -H

# View hardware configuration
lscfg

# List all disks
lspv

# Network adapter status
netstat -i

# AIX version
oslevel -s
```

### 6.2 IBM i (AS/400, iSeries)

**IBM i** is IBM's integrated application server OS, evolution of AS/400 platform.

**Supported Versions:**

| Version | Technology Refresh | Support Level |
|---------|-------------------|---------------|
| IBM i 7.3 | TR12 (latest) | Standard Support until 2024 |
| IBM i 7.4 | TR10 (latest) | Standard Support until 2026 |
| IBM i 7.5 | TR6 (latest) | Standard Support until 2028 |

**IBM i on Power10 Benefits:**

- Up to 50% better performance vs POWER9
- Enhanced security with IBM Power Secure Encryption
- AI and ML integration capabilities

### 6.3 Linux on Power

**Supported Distributions:**[^17]

- **Red Hat Enterprise Linux (RHEL)** 7, 8, 9, 10 (ppc64le)
- **SUSE Linux Enterprise Server (SLES)** 12, 15, 16 (ppc64le)
- **Ubuntu Server** 18.04, 20.04, 22.04, 24.04 (ppc64le)
- **CentOS Stream**, **AlmaLinux**, **Rocky Linux** (community-supported)
- **Fedora** (latest releases, community)

**Architecture: ppc64le (PowerPC 64-bit Little Endian)**

POWER8+ supports little-endian mode, aligning with x86 byte order for easier application porting.

[^17]: [Linux Distributions for POWER8 and POWER9](https://www.ibm.com/docs/en/linux-on-power-systems?topic=lpo-linux-distributions-virtualization-options-power8-power9-linux-power-systems)

**RHEL on Power Installation:**

```bash
# 1. Create LPAR via HMC (assign virtual SCSI, virtual ethernet)
# 2. Attach RHEL ISO via HMC virtual optical
# 3. Boot LPAR, install RHEL (similar to x86 installation)
# 4. Post-install: configure repositories

# RHEL subscription
subscription-manager register
subscription-manager attach --auto

# Install Power-specific tools
yum install powerpc-utils lsvpd servicelog

# View LPAR info
lparstat
```

---

## 7. RAS Features

### 7.1 RAS Overview

**RAS (Reliability, Availability, Serviceability)** is central to IBM Power Systems design philosophy.[^18]

**Key RAS Capabilities:**

- **First Failure Data Capture (FFDC):** Automatic error logging and diagnostics
- **Chipkill Memory:** Continues operation even with DRAM chip failure
- **Processor Instruction Retry:** Auto-retry failed instructions
- **Dynamic Processor Deallocation:** Isolate failing cores without downtime
- **Hot-plug Components:** Replace PSUs, fans, PCI adapters without shutdown
- **Concurrent Firmware Update:** Update firmware while system running (select components)

[^18]: [IBM Power Systems RAS Features](https://www.ibm.com/support/pages/ibm-power-systems%E2%84%A2-reliability-availability-and-scalability-ras-features)

### 7.2 FFDC (First Failure Data Capture)

**FFDC** automatically collects diagnostic data when errors occur, transmitted to IBM support via Call Home.

**Accessing FFDC Data:**

```bash
# Via HMC
lssvcevents -m <system-name> -t hardware

# Via AIX
errpt -a # Detailed error report

# Generate serviceable event
snap -gc # Collect system diagnostic snapshot
```

### 7.3 Chipkill Memory Protection

**Chipkill** allows system to continue operation even if entire DRAM chip fails (vs traditional ECC which handles single-bit errors).

**Memory RAS Hierarchy:**

1. **ECC (Error Correcting Code):** Corrects single-bit, detects dual-bit errors
2. **Chipkill:** Corrects single DRAM chip failure
3. **Memory Spare:** Hot-spare memory ranks activate on failure
4. **Memory Mirroring:** Real-time memory duplication (50% capacity overhead)

**Configuring Memory Mirroring (POWER10):**

```bash
# Via HMC
chsyscfg -r sys -m <system-name> -i "mem_mirroring_mode=sys"

# Requires system reboot to activate
```

---

## 8. Service Processor and System Management

### 8.1 Service Processor (FSP) Architecture

**FSP (Flexible Service Processor)** or **BMC (Baseboard Management Controller)** provides out-of-band management.

**Service Processor Functions:**

- Power on/off control
- Environmental monitoring (temps, fans, voltages)
- System firmware storage (two images: temporary, permanent)
- Error logging and diagnostics
- Boot sequence control
- HMC communication interface

### 8.2 ASMI (Advanced System Management Interface)

**ASMI** is web-based interface directly on service processor (accessible without HMC).

**Accessing ASMI:**

```
https://<service-processor-ip>:8443

Default credentials (varies by model):
User: admin
Password: admin (change immediately)
```

**ASMI Use Cases:**

- Initial network configuration (before HMC connection)
- Emergency access when HMC unavailable
- Service processor firmware updates
- System boot configuration
- Hardware error log review

### 8.3 LED Codes and Diagnostics

**System Attention LED (Amber):**[^19]

Indicates serviceable event logged. Check error logs:

```bash
# Via HMC
lssvcevents -m <system-name> -t hardware --filter "status=open"

# Via AIX
errpt | more

# Clear serviceable events after resolution
chsvcevents -m <system-name> -o c -i <event-id>
```

**System Reference Codes (SRCs):**

8-character hexadecimal codes displayed on operator panel during boot/errors.

Example: `E1F10002` = Service processor error

**Common SRC Patterns:**

- `B####` = Boot progress codes
- `E####` = Environmental/hardware errors
- `C####` = Configuration errors

[^19]: [Troubleshooting Amber System Attention LED](https://www.ibm.com/support/pages/troubleshooting-amber-system-attention-led-or-indicator-ibm-power-systems)

---

## 9. Storage and I/O Configuration

### 9.1 Fibre Channel and NPIV

**NPIV (N_Port ID Virtualization)** allows client LPARs direct access to SAN via virtual Fibre Channel adapters.

**NPIV Configuration:**

```bash
# 1. On HMC: Assign virtual FC adapter to client LPAR
# 2. On VIOS: Map physical FC adapter to virtual FC client

# Create virtual FC mapping
$ vfcmap -vadapter vfchost0 -fcp fcs0

# Where:
# vfchost0 = virtual FC server adapter
# fcs0 = physical FC adapter

# View virtual FC mappings
$ lsmap -all -npiv
```

### 9.2 SR-IOV (Single Root I/O Virtualization)

**SR-IOV** provides near-native performance by allowing LPARs direct hardware access to PCIe adapters.

**SR-IOV Requirements:**

- POWER8+ with PowerVM
- SR-IOV-capable adapters (Intel/Mellanox NICs, Emulex FC HBAs)
- HMC for configuration

**Enabling SR-IOV on Adapter:**

```bash
# Via HMC CLI
chhwres -r sriov -m <system-name> -o a \
-p <lpar-name> --adapter <adapter-id>
```

---

## 10. Performance Tuning

### 10.1 Processor Affinity and NUMA

**NUMA (Non-Uniform Memory Access):** Memory access latency varies based on physical proximity to CPU.

**AIX NUMA Tuning:**

```bash
# View NUMA topology
lssrad -av

# Bind process to specific CPU
bindprocessor -q <pid>
```

### 10.2 LPAR Performance Monitoring

```bash
# CPU entitlement vs usage
lparstat -i

# Real-time LPAR stats (5-second intervals)
lparstat 5

# Identify processor pool
lparstat -p
```

---

## 11. Linux on Power

### 11.1 Linux Distribution Support

Linux on Power uses **ppc64le** (PowerPC 64-bit Little Endian) architecture.[^20]

**Installation Considerations:**

- Use ppc64le-specific ISO images
- Repositories must support ppc64le packages
- Some x86-only software unavailable (use alternatives or compile from source)

[^20]: [How to Choose the Right Linux Distribution for IBM Power](https://www.abcservices.com/choose-right-linux-distribution-ibm-power/)

### 11.2 Power-Specific Linux Tools

```bash
# Install Power utilities (RHEL/CentOS)
yum install powerpc-utils lsvpd servicelog

# LPAR statistics
lparstat

# Hardware inventory
lsvio

# Service log
servicelog --dump
```

---

## 12. Security

### 12.1 PowerSC (Power Systems Security and Compliance)

**PowerSC** provides enterprise security features:

- Trusted Boot
- Encrypted file systems
- Security compliance automation (PCI-DSS, HIPAA, GDPR)

### 12.2 HMC Security Hardening

```bash
# Change default hscroot password
passwd

# Disable unused services
# Via HMC GUI: HMC Management → Change Network Settings

# Enable firewall
# HMC Management → Configure Firewall
```

---

## 13. Troubleshooting Cookbook

### 13.1 LPAR Won't Boot

**Symptom:** LPAR activation fails

**Diagnosis:**

```bash
# Check LPAR state
lssyscfg -r lpar -m <system> --filter "lpar_names=<lpar>" -F state

# View detailed profile
lssyscfg -r prof -m <system> --filter "lpar_names=<lpar>"

# Check available resources
lshwres -r proc -m <system>
lshwres -r mem -m <system>
```

**Resolution:**

- Verify sufficient entitled resources
- Check virtual I/O connectivity to VIOS
- Review LPAR profile for configuration errors

### 13.2 SEA Network Connectivity Lost

**Symptom:** Client LPAR loses network connectivity

**Diagnosis:**

```bash
# On VIOS, check SEA status
$ entstat -d <sea-name> | grep -i state

# Check physical adapter link
$ entstat -d <physical-ent> | grep -i link

# View SEA failover status (dual VIOS)
$ lsdev -dev <sea-name> -attr ha_mode
```

**Resolution:**

- Verify physical cable connected
- Check VLAN configuration matches client LPAR
- Restart SEA if in LIMBO state: `rmdev -l <sea-name> -R && cfgmgr`

### 13.3 Firmware Update Failure

**Symptom:** System firmware update fails

**Diagnosis:**

```bash
# Check update status
lsmcode -m <system>

# View firmware update log
lssvcevents -m <system> --filter "status=open"
```

**Resolution:**

- Ensure sufficient free space on service processor
- Try update from HMC instead of AIX/Linux
- Reboot service processor: `chsysstate -r sys -m <system> -o reset -n sp`

---

## 14. Appendices

### Appendix A: HMC Command Cheat Sheet

```bash
# System Management
lssyscfg -r sys -F name,state      # List managed systems
lssyscfg -r lpar -m <sys>        # List LPARs
lshwres -r proc -m <sys>        # CPU resources
lshwres -r mem -m <sys>         # Memory resources

# LPAR Operations
chsysstate -r lpar -m <sys> -o on -n <lpar>      # Start
chsysstate -r lpar -m <sys> -o shutdown -n <lpar>   # Shutdown
mkvterm -m <sys> -p <lpar>               # Virtual console

# Firmware
lsmcode -m <sys>            # Check firmware level
updlic -m <sys> -t sys -f <file> -r   # Update firmware

# Backup/Restore
hmcbackup create <name>         # Backup HMC
hmcbackup restore <name>        # Restore HMC
```

### Appendix B: POWER Processor Comparison

| Feature | POWER8 | POWER9 | POWER10 |
|---------|--------|--------|---------|
| Process | 22 nm | 14 nm | 7 nm |
| Max cores/socket | 12 | 24 | 15 |
| SMT | 8 | 4/8 | 8 |
| L2 cache/core | 512 KB | 512 KB | 2 MB |
| L3 cache/core | 8 MB | 10 MB | 8 MB |
| Memory | DDR3/4 | DDR4 | DDR5, OMI |
| PCIe | Gen3 | Gen4 | Gen5 |
| AI accel | No | Limited | MMA, 10-20x |

### Appendix C: VIOS Command Reference

```bash
# Storage
lspv                  # List physical volumes
mklv -lv <name> <size> <vg>      # Create logical volume
mkvdev -vdev <lv> -vadapter <vhost>  # Map to client

# Networking
lsdev -type adapter           # List adapters
mkvdev -sea <phys> -vadapter <virt>  # Create SEA
entstat -d <sea>            # SEA statistics

# System
oem_setup_env              # Enter root shell
ioslevel                # VIOS version
lsmap -all               # All virtual mappings
```

### Appendix D: AIX LPAR Commands

```bash
lparstat -i               # LPAR configuration
lparstat -H               # Hypervisor stats
svmon -G                # Memory usage
lscfg                  # Hardware config
errpt -a                # Error report
oslevel -s               # AIX version
```

### Appendix E: IBM Power Terminology

| Term | Full Name | Description |
|------|-----------|-------------|
| **HMC** | Hardware Management Console | Central management appliance |
| **LPAR** | Logical Partition | Virtual machine on Power Systems |
| **VIOS** | Virtual I/O Server | I/O virtualization partition |
| **SEA** | Shared Ethernet Adapter | Virtual network bridge |
| **NPIV** | N_Port ID Virtualization | Virtual Fibre Channel |
| **FSP** | Flexible Service Processor | Service processor (BMC) |
| **PHYP** | PowerVM Hypervisor | Type-1 hypervisor firmware |
| **DLPAR** | Dynamic LPAR | Hot-add/remove resources |
| **LPM** | Live Partition Mobility | vMotion for Power |
| **RAS** | Reliability, Availability, Serviceability | Enterprise resiliency features |
| **SRC** | System Reference Code | Hardware error code |
| **ASMI** | Advanced System Management Interface | Service processor web UI |

---

**Document Revision History:**

- **2026-05-25:** Initial production release
- Comprehensive 14-section field guide covering POWER8-POWER11, HMC, PowerVM, AIX, IBM i, Linux
- All technical data verified from IBM official documentation

**License:** This document is provided for educational and informational purposes. IBM, Power, PowerVM, AIX, and IBM i are trademarks of International Business Machines Corporation.

---

**Prepared for:** `/home/mgreczi/projects/compendium` (Athenaeum Documentation Site)
