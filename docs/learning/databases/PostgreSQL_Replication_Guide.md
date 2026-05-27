# PostgreSQL Streaming Replication - Complete Guide

**Version:** PostgreSQL 12+  
**Type:** Production-Ready Reference  
**Audience:** DBAs, SREs, DevOps Engineers

---

## TABLE OF CONTENTS

1. [Overview](#overview)
2. [Core Concepts](#core-concepts)
3. [Quick Status Checks](#quick-status-checks)
4. [Initial Setup](#initial-setup)
5. [Monitoring & Validation](#monitoring--validation)
6. [Common Issues](#common-issues)
7. [Maintenance](#maintenance)
8. [Best Practices](#best-practices)

---

## OVERVIEW

This guide covers **PostgreSQL streaming replication** setup, monitoring, and troubleshooting for production environments. It uses the **pg_basebackup** method for initial setup and focuses on **asynchronous physical replication** for disaster recovery scenarios.

**Use case:** PRIMARY-STANDBY architecture where:
- PRIMARY handles all production read/write traffic
- STANDBY replicates continuously for disaster recovery (DR)
- STANDBY can be used for read-only queries (analytics, reporting)

---

## CORE CONCEPTS

### Server Roles

| **Role** | **Description** | **pg_is_in_recovery()** |
|----------|-----------------|-------------------------|
| **PRIMARY** | Read/write server, production traffic | `false` |
| **STANDBY** | Read-only replica, disaster recovery | `true` |

### Key Components

- **WAL (Write-Ahead Log):** Transaction logs streamed from PRIMARY to STANDBY
- **Replication Slot:** Ensures WAL files are retained on PRIMARY until STANDBY processes them
- **standby.signal:** File in STANDBY data directory (PostgreSQL 12+) indicating STANDBY mode
- **primary_conninfo:** STANDBY configuration specifying how to connect to PRIMARY

### Replication Flow

```
PRIMARY (write/read)
    ↓ (WAL streaming)
STANDBY (read-only)
```

**WAL streaming:**
1. Transaction committed on PRIMARY
2. WAL written to PRIMARY disk
3. WAL streamed to STANDBY via replication connection
4. STANDBY writes WAL to disk
5. STANDBY replays WAL (applies changes to data files)

---

## QUICK STATUS CHECKS

### Am I PRIMARY or STANDBY?

```bash
psql -U postgres -c "SELECT pg_is_in_recovery();"
```

**Output:**
- `false` → PRIMARY (read/write mode)
- `true` → STANDBY (recovery mode, replaying WAL)

---

### PRIMARY: Do I See My STANDBY?

```bash
psql -U postgres -c "
SELECT 
    application_name,
    client_addr,
    state,
    sync_state,
    pg_wal_lsn_diff(sent_lsn, replay_lsn) AS lag_bytes
FROM pg_stat_replication;
"
```

**Healthy output:**
```
 application_name |  client_addr  |   state   | sync_state | lag_bytes 
------------------+---------------+-----------+------------+-----------
 walreceiver      | STANDBY_IP    | streaming | async      | 0
```

**Empty result (0 rows):** STANDBY not connected!

---

### STANDBY: Replication Delay

```bash
psql -U postgres -c "
SELECT 
    now() - pg_last_xact_replay_timestamp() AS replication_delay;
"
```

**Healthy output:**
```
 replication_delay 
-------------------
 00:00:00.047      (47 milliseconds - excellent!)
```

---

## INITIAL SETUP

### Prerequisites

- Two PostgreSQL 12+ servers (PRIMARY and STANDBY)
- Network connectivity between servers (port 5432)
- Sufficient disk space on STANDBY (equal to PRIMARY database size)

---

### Step 1: Configure PRIMARY

#### 1.1 PostgreSQL Configuration

Edit `postgresql.conf`:

```ini
# Replication Settings
wal_level = replica                    # Enable replication
max_wal_senders = 10                   # Max STANDBY connections
max_replication_slots = 10             # Max replication slots
archive_mode = off                     # Streaming only (not archive-based)
hot_standby = on                       # STANDBY can accept queries

# WAL Retention (PostgreSQL 13+)
wal_keep_size = 1024                   # 1GB WAL retention
```

**Apply:**
```bash
sudo systemctl reload postgresql
```

---

#### 1.2 Create Replication User

```sql
CREATE USER replicator WITH REPLICATION PASSWORD 'SECRET_PASSWORD';
```

**Verify:**
```bash
psql -U postgres -c "
SELECT rolname, rolreplication 
FROM pg_roles 
WHERE rolname = 'replicator';
"
```

---

#### 1.3 Allow Replication Connections

Edit `pg_hba.conf` (add this line with STANDBY IP):

```
# TYPE  DATABASE        USER         ADDRESS              METHOD
host    replication     replicator   STANDBY_IP/32        md5
```

**Reload:**
```bash
sudo systemctl reload postgresql
```

---

#### 1.4 Create Replication Slot

```bash
psql -U postgres -c "
SELECT pg_create_physical_replication_slot('standby_slot');
"
```

**Verify:**
```bash
psql -U postgres -c "
SELECT slot_name, slot_type, active 
FROM pg_replication_slots;
"
```

Expected:
```
  slot_name   | slot_type | active 
--------------+-----------+--------
 standby_slot | physical  | f       (false until STANDBY connects)
```

---

### Step 2: Setup STANDBY (pg_basebackup method)

#### 2.1 Stop PostgreSQL on STANDBY

```bash
sudo systemctl stop postgresql
```

---

#### 2.2 Clear Data Directory

```bash
# WARNING: This deletes all existing data!
sudo rm -rf /var/lib/pgsql/data
sudo mkdir -p /var/lib/pgsql/data
sudo chown postgres:postgres /var/lib/pgsql/data
sudo chmod 700 /var/lib/pgsql/data
```

---

#### 2.3 Run pg_basebackup

```bash
sudo -u postgres pg_basebackup \
  -h PRIMARY_HOST \
  -U replicator \
  -D /var/lib/pgsql/data \
  -Fp \
  -Xs \
  -P \
  -R \
  --slot=standby_slot
```

**Parameters:**
- `-h PRIMARY_HOST` - PRIMARY server hostname/IP
- `-U replicator` - Replication user
- `-D /var/lib/pgsql/data` - Target data directory
- `-Fp` - Plain format (not tar)
- `-Xs` - Stream WAL during backup
- `-P` - Show progress (%)
- **`-R`** - Auto-create `standby.signal` and recovery config
- `--slot=standby_slot` - Use replication slot

**Expected time:** 1-4 hours (depends on database size)

**Progress display:**
```
1234567/5000000 kB (24%), 1/1 tablespace
```

---

#### 2.4 Verify Configuration Files

```bash
# standby.signal must exist
ls -la /var/lib/pgsql/data/standby.signal

# Check recovery settings
cat /var/lib/pgsql/data/postgresql.auto.conf | grep primary
```

Expected:
```
primary_conninfo = 'user=replicator password=... host=PRIMARY_HOST ...'
primary_slot_name = 'standby_slot'
```

---

#### 2.5 Start STANDBY

```bash
sudo systemctl start postgresql
```

**Verify recovery mode:**
```bash
psql -U postgres -c "SELECT pg_is_in_recovery();"
```

**Must return:** `true`

---

### Step 3: Validation

#### On PRIMARY:

```bash
# 1. STANDBY connected?
psql -U postgres -c "
SELECT application_name, client_addr, state 
FROM pg_stat_replication;
"

# 2. Replication slot active?
psql -U postgres -c "
SELECT slot_name, active 
FROM pg_replication_slots;
"

# 3. Replication lag?
psql -U postgres -c "
SELECT pg_wal_lsn_diff(sent_lsn, replay_lsn) AS lag_bytes 
FROM pg_stat_replication;
"
```

Expected:
- State: `streaming`
- Slot active: `true`
- Lag: `0` or `< 1000000` bytes (< 1 MB)

#### On STANDBY:

```bash
# Recovery mode?
psql -U postgres -c "SELECT pg_is_in_recovery();"
# Must be: true

# Replication delay?
psql -U postgres -c "
SELECT now() - pg_last_xact_replay_timestamp() AS delay;
"
# Should be: < 10 seconds
```

---

## MONITORING & VALIDATION

### Complete Health Check Script

```bash
#!/bin/bash
# replication_health.sh

PRIMARY_HOST="primary.example.com"
STANDBY_HOST="standby.example.com"

echo "=== PRIMARY Status ==="
ssh $PRIMARY_HOST "psql -U postgres -c '
SELECT 
    application_name,
    client_addr,
    state,
    pg_size_pretty(pg_wal_lsn_diff(sent_lsn, replay_lsn)) AS lag
FROM pg_stat_replication;
'"

echo ""
echo "=== STANDBY Status ==="
ssh $STANDBY_HOST "psql -U postgres -c '
SELECT 
    pg_is_in_recovery() AS in_recovery,
    now() - pg_last_xact_replay_timestamp() AS delay;
'"

echo ""
echo "=== Replication Slot ==="
ssh $PRIMARY_HOST "psql -U postgres -c '
SELECT slot_name, active 
FROM pg_replication_slots;
'"
```

---

### Key Metrics to Monitor

| **Metric** | **Command** | **Healthy Value** |
|------------|-------------|-------------------|
| STANDBY connected | `pg_stat_replication` | state = `streaming` |
| Replication lag (bytes) | `pg_wal_lsn_diff(sent_lsn, replay_lsn)` | `< 100 MB` |
| Replication delay (time) | `now() - pg_last_xact_replay_timestamp()` | `< 10 seconds` |
| Replication slot active | `pg_replication_slots.active` | `true` |
| STANDBY recovery mode | `pg_is_in_recovery()` | `true` |

---

### Monitoring Alert Thresholds

**WARNING alerts:**
- Replication lag > 100 MB
- Replication delay > 30 seconds

**CRITICAL alerts:**
- `pg_stat_replication` empty (STANDBY disconnected)
- Replication slot inactive (`active = false`)
- **STANDBY not in recovery mode** (`pg_is_in_recovery() = false`) → **SPLIT-BRAIN!**

---

## COMMON ISSUES

### Issue #1: Split-Brain (Both Servers are PRIMARY)

**Symptoms:**
- PRIMARY: `pg_is_in_recovery() = false` ✓
- STANDBY: `pg_is_in_recovery() = false` ❌ **WRONG!**

**Diagnosis:**
```bash
# Check both servers
psql -U postgres -c "SELECT pg_is_in_recovery();"
```

**Cause:**
- `standby.signal` file missing on STANDBY
- STANDBY promoted to PRIMARY accidentally
- Recovery configuration lost after restart

**Solution:**
Rebuild STANDBY using pg_basebackup (see Setup section)

---

### Issue #2: STANDBY Not Connecting

**Symptoms:**
- PRIMARY: `pg_stat_replication` empty
- STANDBY: `pg_is_in_recovery() = true` but not streaming

**Diagnosis:**
```bash
# Check STANDBY logs
sudo journalctl -u postgresql -n 100 | grep -i "could not connect"
```

**Possible causes & fixes:**

#### Missing standby.signal:
```bash
sudo touch /var/lib/pgsql/data/standby.signal
sudo chown postgres:postgres /var/lib/pgsql/data/standby.signal
sudo systemctl restart postgresql
```

#### Wrong primary_conninfo:
```bash
# Check configuration
cat /var/lib/pgsql/data/postgresql.auto.conf | grep primary_conninfo

# Fix if missing/wrong
echo "primary_conninfo = 'user=replicator password=SECRET_PASSWORD host=PRIMARY_HOST port=5432'" \
  | sudo tee -a /var/lib/pgsql/data/postgresql.auto.conf
sudo systemctl restart postgresql
```

#### pg_hba.conf blocks STANDBY:
```bash
# On PRIMARY, check:
grep replication /var/lib/pgsql/data/pg_hba.conf

# Should have:
# host replication replicator STANDBY_IP/32 md5

# Add if missing, then reload:
sudo systemctl reload postgresql
```

#### Network connectivity:
```bash
# From STANDBY, test:
ping -c 3 PRIMARY_HOST
nc -zv PRIMARY_HOST 5432
```

---

### Issue #3: High Replication Lag

**Symptoms:**
- `pg_wal_lsn_diff(sent_lsn, replay_lsn)` > 1 GB

**Possible causes:**

1. **Network bottleneck** - slow link between PRIMARY and STANDBY
   - Test: `iperf3` between servers
   - Fix: Upgrade network bandwidth

2. **STANDBY slow disk I/O** - WAL replay slow
   - Test: `iostat -x 1 10` on STANDBY
   - Fix: Use faster disks (SSD/NVMe)

3. **HIGH write load on PRIMARY** - generating WAL too fast
   - Test: `SELECT pg_current_wal_lsn();` twice, 10 seconds apart
   - Fix: Optimize queries, batch writes

---

### Issue #4: Replication Slot Inactive

**Symptoms:**
- `pg_replication_slots.active = false`

**Causes & fixes:**

1. **STANDBY stopped:**
   ```bash
   sudo systemctl start postgresql
   ```

2. **STANDBY not using slot** (missing `primary_slot_name`):
   ```bash
   echo "primary_slot_name = 'standby_slot'" \
     | sudo tee -a /var/lib/pgsql/data/postgresql.auto.conf
   sudo systemctl restart postgresql
   ```

3. **Slot deleted on PRIMARY:**
   ```bash
   psql -U postgres -c "
   SELECT pg_create_physical_replication_slot('standby_slot');
   "
   ```

---

## MAINTENANCE

### Failover (Promote STANDBY to PRIMARY)

**When:** PRIMARY failed, need to switch to STANDBY

```bash
# On STANDBY
sudo -u postgres pg_ctl promote -D /var/lib/pgsql/data

# Verify
psql -U postgres -c "SELECT pg_is_in_recovery();"
# Should now be: false (PRIMARY mode)
```

**WARNING:** One-way operation! Old PRIMARY must be rebuilt as STANDBY.

---

### Safe Patching Strategy

**CRITICAL:** Always patch STANDBY first, PRIMARY last!

**Why:** PRIMARY handles production traffic. If STANDBY patching fails, PRIMARY is unaffected (zero downtime).

**Procedure:**

1. **Patch STANDBY:**
   ```bash
   # Stop PostgreSQL
   sudo systemctl stop postgresql
   
   # Verify standby.signal exists BEFORE reboot!
   ls -la /var/lib/pgsql/data/standby.signal
   
   # Apply patches
   sudo dnf update -y
   
   # Reboot (if kernel update)
   sudo reboot
   
   # After reboot, verify recovery mode
   psql -U postgres -c "SELECT pg_is_in_recovery();"
   # MUST BE: true
   ```

2. **Validate STANDBY is healthy:**
   ```bash
   # PRIMARY sees STANDBY?
   ssh PRIMARY_HOST "psql -U postgres -c 'SELECT state FROM pg_stat_replication;'"
   # MUST BE: streaming
   ```

3. **ONLY THEN patch PRIMARY:**
   ```bash
   sudo systemctl stop postgresql
   sudo dnf update -y
   sudo reboot
   
   # After reboot, verify PRIMARY mode
   psql -U postgres -c "SELECT pg_is_in_recovery();"
   # MUST BE: false
   ```

---

### Delete Replication Slot (Decommission STANDBY)

```bash
# On PRIMARY
psql -U postgres -c "
SELECT pg_drop_replication_slot('standby_slot');
"
```

**WARNING:** Only delete if STANDBY is permanently decommissioned!

---

## BEST PRACTICES

### 1. **Always Use Replication Slots**

Without slots, PRIMARY may delete WAL files before STANDBY processes them → replication breaks.

```bash
# Check slot exists
psql -U postgres -c "SELECT * FROM pg_replication_slots;"
```

---

### 2. **Monitor Disk Space on PRIMARY**

Inactive replication slots can accumulate WAL files → disk full!

```bash
# Check WAL directory size
du -sh /var/lib/pgsql/data/pg_wal
```

**Alert threshold:** > 10 GB WAL files

---

### 3. **Use Monitoring Alerts**

**Mandatory alerts:**
- STANDBY not in recovery mode (split-brain detection)
- Replication lag > 100 MB
- Replication slot inactive

**Example Prometheus/Alertmanager rule:**
```yaml
- alert: PostgreSQLStandbyNotInRecovery
  expr: pg_in_recovery{server="standby"} == 0
  for: 5m
  labels:
    severity: critical
  annotations:
    summary: "STANDBY is NOT in recovery mode (split-brain!)"
```

---

### 4. **Test Failover Quarterly**

Practice promotes STANDBY to PRIMARY in non-prod environment.

**Steps:**
1. Promote STANDBY to PRIMARY
2. Measure RTO (Recovery Time Objective)
3. Rebuild old PRIMARY as new STANDBY
4. Document lessons learned

---

### 5. **Backup standby.signal Before Patching**

```bash
# ALWAYS backup before reboot
sudo cp /var/lib/pgsql/data/standby.signal /root/standby.signal.backup
sudo cp /var/lib/pgsql/data/postgresql.auto.conf /root/postgresql.auto.conf.backup
```

If lost after reboot → STANDBY becomes PRIMARY → SPLIT-BRAIN!

---

## QUICK REFERENCE

```bash
# === Check server role ===
psql -U postgres -c "SELECT pg_is_in_recovery();"
# false = PRIMARY, true = STANDBY

# === PRIMARY: See STANDBY? ===
psql -U postgres -c "SELECT * FROM pg_stat_replication;"

# === PRIMARY: Replication lag ===
psql -U postgres -c "
SELECT pg_size_pretty(pg_wal_lsn_diff(sent_lsn, replay_lsn)) 
FROM pg_stat_replication;
"

# === STANDBY: Replication delay ===
psql -U postgres -c "
SELECT now() - pg_last_xact_replay_timestamp() AS delay;
"

# === PRIMARY: Replication slot status ===
psql -U postgres -c "SELECT slot_name, active FROM pg_replication_slots;"

# === STANDBY: Verify configuration ===
ls -la /var/lib/pgsql/data/standby.signal
cat /var/lib/pgsql/data/postgresql.auto.conf | grep primary
```

---

## RELATED RESOURCES

- [PostgreSQL Documentation - High Availability](https://www.postgresql.org/docs/current/high-availability.html)
- [PostgreSQL Documentation - Streaming Replication](https://www.postgresql.org/docs/current/warm-standby.html)
- [pg_basebackup Documentation](https://www.postgresql.org/docs/current/app-pgbasebackup.html)

---

**Last Updated:** 2026-05-26  
**Version:** 1.0  
**License:** MIT
