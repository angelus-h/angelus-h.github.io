# Datadog PostgreSQL Monitoring Setup Guide

**Server Example:** `service-a-database-el8.service-a-001.prod.iad2.dc.company.internal` 
**PostgreSQL Version:** 16.10 
**RHEL Version:** 8 
**Last Updated:** April 1, 2026

---

## Overview

This guide demonstrates how to configure Datadog Agent for PostgreSQL monitoring with Database Monitoring (DBM) enabled. The configuration includes:

- Basic metrics collection (connections, locks, database size)
- Deep Database Monitoring (query performance, slow queries)
- Query samples and execution plans
- Active query monitoring
- Database configuration tracking

---

## Prerequisites

- Datadog Agent 7.x installed on the database server
- PostgreSQL 9.6+ (example uses PostgreSQL 16.10)
- Root/sudo access for configuration
- PostgreSQL superuser access for initial setup

---

## Step 1: Create Monitoring User in PostgreSQL

**Connect to PostgreSQL as postgres user:**

```bash
sudo -u postgres psql
```

**Create dedicated monitoring user:**

```sql
-- Create monitoring user with password
CREATE USER monitoring WITH PASSWORD 'your-secure-password';

-- Grant required permissions for basic monitoring
GRANT pg_monitor TO monitoring;

-- Grant connection permission to all databases
GRANT CONNECT ON DATABASE postgres TO monitoring;

-- For Database Monitoring (DBM) - grant additional permissions
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;
GRANT SELECT ON pg_stat_statements TO monitoring;

-- Exit psql
\q
```

**Verify user creation:**

```bash
sudo -u postgres psql -c '\du monitoring'
```

**Expected output:**

```
List of roles
Role name | Attributes 
------------+------------
monitoring |
```

---

## Step 2: Configure PostgreSQL for Statement Tracking

**Edit PostgreSQL configuration:**

```bash
sudo vi /var/lib/pgsql/data/postgresql.conf
```

**Add/modify these settings:**

```ini
# Enable pg_stat_statements extension
shared_preload_libraries = 'pg_stat_statements'

# Track query execution time
track_activity_query_size = 4096 # Increase from default 1024 to avoid truncation
pg_stat_statements.track = all
pg_stat_statements.max = 10000

# Optional: Enable additional tracking
track_io_timing = on
track_functions = all
```

**Restart PostgreSQL:**

```bash
sudo systemctl restart postgresql
sudo systemctl status postgresql
```

---

## Step 3: Configure Datadog Agent

**Create PostgreSQL integration config:**

```bash
sudo vi /etc/datadog-agent/conf.d/postgres.d/conf.yaml
```

**Production configuration example:**

```yaml
init_config:

instances:
- host: localhost
port: 5432
username: monitoring
password: your-secure-password
dbm: true # Enable Database Monitoring
tags:
- env:prod
- datacenter:iad2
- service:service-a-database
- appcode:service-a-001

# Optional: Collect metrics from specific databases
# If omitted, metrics are collected from all databases
# dbname: postgres

# Deep Database Monitoring features
query_metrics:
enabled: true # Collect query performance metrics
query_samples:
enabled: true # Collect query samples with execution plans
query_activity:
enabled: true # Monitor active queries
settings:
enabled: true # Track PostgreSQL configuration changes
```

**Set proper permissions:**

```bash
sudo chmod 600 /etc/datadog-agent/conf.d/postgres.d/conf.yaml
sudo chown dd-agent:dd-agent /etc/datadog-agent/conf.d/postgres.d/conf.yaml
```

---

## Step 4: Restart Datadog Agent

```bash
sudo systemctl restart datadog-agent
sudo systemctl status datadog-agent
```

---

## Step 5: Verify Configuration

**Check Datadog Agent status:**

```bash
sudo datadog-agent status | grep -A 20 'postgres ('
```

**Expected output (SUCCESS):**

```
postgres (23.4.0)
-----------------
Instance ID: postgres:c56046b032cf7c94 [OK]
Configuration Source: file:/etc/datadog-agent/conf.d/postgres.d/conf.yaml[0]
Total Runs: 927
Metric Samples: Last Run: 682, Total: 628,751
Events: Last Run: 0, Total: 0
Database Monitoring Activity Samples: Last Run: 1, Total: 1,265
Database Monitoring Health Events: Last Run: 1, Total: 1
Database Monitoring Metadata Samples: Last Run: 1, Total: 72
Database Monitoring Query Metrics: Last Run: 1, Total: 1,389
Database Monitoring Query Samples: Last Run: 1, Total: 1,303
Service Checks: Last Run: 1, Total: 927
Average Execution Time : 21ms
Last Execution Date : 2026-04-01 15:30:59 EDT
metadata:
version.major: 16
version.minor: 10
```

**Key indicators of success:**

- Instance ID shows `[OK]`
- `Metric Samples` are being collected
- `Database Monitoring Query Metrics` > 0
- `Database Monitoring Query Samples` > 0
- `Service Checks` successful

---

## Step 6: View Metrics in Datadog UI

**Login to Datadog:**

[the company SSO](https://app.datadoghq.com/account/login/id/pynb8yqdcx1cm107?saml_config_uuid=a3a42676-c0d9-4b3c-bdcc-016ba566a564)

**Navigate to Database Monitoring:**

```
1. APM → Database Monitoring
2. Select: PostgreSQL
3. Filter: service:service-a-database
4. View:
- Query Performance
- Slow Queries (> 100ms)
- Lock Waits
- Connection Pool Statistics
```

**View Infrastructure Metrics:**

```
1. Metrics → Explorer
2. Select metrics:
- postgresql.connections
- postgresql.database_size
- postgresql.locks
- postgresql.max_connections
- postgresql.percent_usage_connections
3. Filter: host:service-a-database-el8.service-a-001.prod.iad2.dc.company.internal
```

---

## Key Metrics Collected

### Connection Metrics

| Metric | Description | Alert Threshold |
|--------|-------------|-----------------|
| `postgresql.connections` | Active connections | > 80% of max |
| `postgresql.max_connections` | Max connections limit | - |
| `postgresql.percent_usage_connections` | Connection usage % | > 80% |

### Performance Metrics

| Metric | Description | Alert Threshold |
|--------|-------------|-----------------|
| `postgresql.locks` | Lock count | > 100 |
| `postgresql.deadlocks` | Deadlock count | > 0 |
| `postgresql.bgwriter.checkpoints_timed` | Scheduled checkpoints | - |
| `postgresql.bgwriter.checkpoints_req` | Forced checkpoints | > 10% of total |

### Database Size Metrics

| Metric | Description | Alert Threshold |
|--------|-------------|-----------------|
| `postgresql.database_size` | Database size (bytes) | > 80% of disk |
| `postgresql.table_size` | Table size (bytes) | - |
| `postgresql.index_size` | Index size (bytes) | - |

### Query Performance (DBM)

| Metric | Description | Alert Threshold |
|--------|-------------|-----------------|
| `postgresql.queries.avg_time` | Average query time | > 100ms |
| `postgresql.queries.p95_time` | 95th percentile | > 500ms |
| `postgresql.queries.p99_time` | 99th percentile | > 1000ms |
| `postgresql.queries.slow` | Slow query count (> 1s) | > 10/min |

---

## Troubleshooting

### Issue 1: Query Samples Are Truncated

**Symptom:**

```
WARN | Statement with query_signature=258dc8d79b12c93c was truncated. 
Query size: 1023, track_activity_query_size: 1024
```

**Solution:**

Increase `track_activity_query_size` in PostgreSQL config:

```bash
sudo vi /var/lib/pgsql/data/postgresql.conf
```

```ini
track_activity_query_size = 4096 # Increase from default 1024
```

```bash
sudo systemctl restart postgresql
sudo systemctl restart datadog-agent
```

---

### Issue 2: Permission Denied on pg_stat_statements

**Symptom:**

```
ERROR | (postgres.py:123) | permission denied for view pg_stat_statements
```

**Solution:**

Grant proper permissions:

```sql
sudo -u postgres psql
GRANT SELECT ON pg_stat_statements TO monitoring;
GRANT pg_monitor TO monitoring;
\q
```

---

### Issue 3: Connection Refused

**Symptom:**

```
ERROR | could not connect to server: Connection refused
```

**Solution:**

Check PostgreSQL is listening on localhost:

```bash
sudo -u postgres psql -c "SHOW listen_addresses;"
```

If not `localhost` or `*`, edit `postgresql.conf`:

```ini
listen_addresses = 'localhost'
```

Check `pg_hba.conf` allows local connections:

```bash
sudo vi /var/lib/pgsql/data/pg_hba.conf
```

Add this line:

```
host  all       monitoring   127.0.0.1/32      md5
```

Restart PostgreSQL:

```bash
sudo systemctl restart postgresql
```

---

### Issue 4: No DBM Metrics Collected

**Symptom:**

```
Database Monitoring Query Metrics: Last Run: 0, Total: 0
```

**Solution:**

Verify pg_stat_statements extension is loaded:

```sql
sudo -u postgres psql
SELECT * FROM pg_extension WHERE extname = 'pg_stat_statements';
```

If not found, create it:

```sql
CREATE EXTENSION pg_stat_statements;
```

Verify shared_preload_libraries:

```sql
SHOW shared_preload_libraries;
```

Should return `pg_stat_statements`. If not, edit `postgresql.conf` and restart.

---

## Security Best Practices

### 1. Use Strong Passwords

```sql
-- Generate secure password
openssl rand -base64 32

-- Update monitoring user password
ALTER USER monitoring WITH PASSWORD 'new-secure-password';
```

### 2. Restrict Permissions

```sql
-- Only grant necessary permissions
GRANT pg_monitor TO monitoring;
GRANT SELECT ON pg_stat_statements TO monitoring;

-- Do NOT grant:
-- - Superuser
-- - CREATEDB
-- - CREATEROLE
```

### 3. Secure Configuration File

```bash
# Restrict file permissions
sudo chmod 600 /etc/datadog-agent/conf.d/postgres.d/conf.yaml
sudo chown dd-agent:dd-agent /etc/datadog-agent/conf.d/postgres.d/conf.yaml

# Verify no world-readable
ls -l /etc/datadog-agent/conf.d/postgres.d/conf.yaml
# Should show: -rw------- (600)
```

### 4. Rotate Passwords Regularly

```bash
# Every 90 days:
1. Generate new password
2. Update PostgreSQL user
3. Update Datadog config
4. Restart agent
5. Verify monitoring works
```

---

## Example Dashboard Queries

### Top 5 Databases by Size

```
top(avg:postgresql.database_size{service:service-a-database} by {db}, 5, 'mean', 'desc')
```

### Connection Usage Over Time

```
(avg:postgresql.connections{service:service-a-database} / avg:postgresql.max_connections{service:service-a-database}) * 100
```

### Slow Query Count (> 1 second)

```
sum:postgresql.queries.slow{service:service-a-database,query_time:>1000}
```

### Lock Waits per Second

```
per_second(sum:postgresql.locks{service:service-a-database,mode:ExclusiveLock})
```

---

## Automated Setup with Ansible

**Playbook example:**

```yaml
---
- name: Configure Datadog PostgreSQL Monitoring
hosts: database_servers
become: yes

vars:
monitoring_password: "{{ vault_monitoring_password }}"
datacenter: "iad2"
environment: "prod"

tasks:
- name: Create monitoring user in PostgreSQL
postgresql_user:
name: monitoring
password: "{{ monitoring_password }}"
role_attr_flags: LOGIN
state: present
become_user: postgres

- name: Grant pg_monitor role
postgresql_privs:
database: postgres
roles: monitoring
type: group
objs: pg_monitor
state: present
become_user: postgres

- name: Create postgres.d directory
file:
path: /etc/datadog-agent/conf.d/postgres.d
state: directory
owner: dd-agent
group: dd-agent
mode: '0755'

- name: Deploy PostgreSQL integration config
template:
src: postgres.yaml.j2
dest: /etc/datadog-agent/conf.d/postgres.d/conf.yaml
owner: dd-agent
group: dd-agent
mode: '0600'
notify: restart datadog-agent

handlers:
- name: restart datadog-agent
systemd:
name: datadog-agent
state: restarted
```

**Template (`postgres.yaml.j2`):**

```yaml
init_config:

instances:
- host: localhost
port: 5432
username: monitoring
password: {{ monitoring_password }}
dbm: true
tags:
- env:{{ environment }}
- datacenter:{{ datacenter }}
- service:{{ inventory_hostname_short }}

query_metrics:
enabled: true
query_samples:
enabled: true
query_activity:
enabled: true
settings:
enabled: true
```

---

## References

- **Datadog PostgreSQL Integration:** https://docs.datadoghq.com/integrations/postgres/
- **Database Monitoring Setup:** https://docs.datadoghq.com/database_monitoring/setup_postgres/
- **PostgreSQL Metrics:** https://docs.datadoghq.com/integrations/postgres/?tab=host#metrics
- **Troubleshooting Guide:** https://docs.datadoghq.com/database_monitoring/setup_postgres/troubleshooting

---

**Author:** Infrastructure Team (ops-lead@company.com) 
**Production Server:** service-a-database-el8.service-a-001.prod.iad2.dc.company.internal 
**Last Verified:** April 1, 2026
