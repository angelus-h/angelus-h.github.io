# SumoLogic Quick Reference

**Level:** Beginner → Advanced 
**Duration:** 5-6 weeks (5-6 hours/week) 
**Target Audience:** DevOps Engineers, SREs, Security Engineers 
**Prerequisites:** Linux basics, log formats (syslog, JSON), regex knowledge

---

## Overview

SumoLogic is a cloud-based log management and analytics platform providing real-time insights into infrastructure and application behavior. At the company, SumoLogic handles all log collection and analysis.

**What You'll Learn:**
- Install and configure SumoLogic Collectors
- Configure log sources (files, syslog, containers)
- Log parsing and field extraction
- Sumo Query Language (SumoQL)
- Create dashboards and alerts
- Security and compliance monitoring
- Log-based troubleshooting

---

## Module 1: SumoLogic Fundamentals

### 1.1 Architecture

**Components:**

```
┌─────────────────────────────────────────────┐
│    SumoLogic Cloud Platform       │
│ ┌──────────────────────────────────────┐  │
│ │ Log Analytics Engine        │  │
│ │ - Indexing, Search, Aggregation   │  │
│ └──────────────────────────────────────┘  │
└─────────────────────────────────────────────┘
↑ HTTPS (compressed logs)
┌─────────────────────────────────────────────┐
│  SumoLogic Collector (local/remote)    │
│ - Local Files (/var/log/*)         │
│ - Syslog (UDP/TCP 514)           │
│ - Docker/Kubernetes Logs          │
│ - Script outputs              │
└─────────────────────────────────────────────┘
↑ Log files
┌─────────────────────────────────────────────┐
│  Your Infrastructure (Apps, Servers, K8s) │
└─────────────────────────────────────────────┘
```

**Key Concepts:**
- **Collector:** Agent collecting and forwarding logs
- **Source:** Log input configuration
- **Source Category:** Logical log grouping
- **Field Extraction:** Parse structured data
- **Partition:** Data organization for faster searches

---

### 1.2 Collector Installation

**Linux Installation:**

```bash
# Download installer
wget https://collectors.sumologic.com/rest/download/linux/64

# Install
chmod +x SumoCollector_unix_XX.YY-ZZ.sh
sudo ./SumoCollector_unix_XX.YY-ZZ.sh -q \
-Vsumo.accessid=YOUR_ACCESS_ID \
-Vsumo.accesskey=YOUR_ACCESS_KEY \
-Vcollector.name="server-hostname"

# Start service
sudo systemctl start collector
sudo systemctl enable collector
```

**Collector Configuration:**

```json
{
"collector": {
"collectorType": "Installed",
"name": "service-a-frontend-el8",
"description": "Service-A Frontend Collector",
"category": "service-a/frontend/prod/rdu2",
"fields": {
"env": "prod",
"appcode": "service-a-001",
"datacenter": "rdu2"
}
}
}
```

**Practice 1.2:** Install Collector

```bash
# Install
sudo ./SumoCollector_unix_XX.YY-ZZ.sh -q \
-Vsumo.accessid=YOUR_ID \
-Vsumo.accesskey=YOUR_KEY \
-Vcollector.name="test-server-01"

# Verify
sudo systemctl status collector

# Check UI: Manage Data → Collection → Collectors
```

---

### 1.3 Source Configuration

**Local File Source:**

```json
{
"source": {
"name": "Apache Access Logs",
"sourceType": "LocalFile",
"pathExpression": "/var/log/apache2/access*.log",
"category": "apache/access",
"automaticDateParsing": true,
"fields": {
"service": "apache",
"log_type": "access"
}
}
}
```

**Syslog Source:**

```json
{
"source": {
"name": "Syslog Messages",
"sourceType": "Syslog",
"protocol": "UDP",
"port": 514,
"category": "syslog/system"
}
}
```

**Practice 1.3:** Configure Apache Log Source

```bash
# Create source config
cat > /tmp/apache-source.json <<'EOF'
{
"api.version": "v1",
"source": {
"name": "Apache Access Logs",
"sourceType": "LocalFile",
"pathExpression": "/var/log/apache2/access*.log",
"category": "test/apache/access"
}
}
EOF

# Add source via API
curl -u "ACCESS_ID:ACCESS_KEY" \
-X POST -H "Content-Type: application/json" \
-d @/tmp/apache-source.json \
"https://api.sumologic.com/api/v1/collectors/ID/sources"

# Verify: Search → _sourceCategory="test/apache/access"
```

---

## Module 2: Log Parsing & Field Extraction

### 2.1 Parse Operators

**Basic Parsing:**

```sql
-- Apache access log
_sourceCategory=apache/access
| parse "* - - [*] \"* * HTTP/*\" * *" as 
client_ip, timestamp, method, url, http_version, status_code, bytes_sent

-- Example:
-- 192.168.1.1 - - [01/Apr/2026:12:30:45 +0000] "GET /api/users HTTP/1.1" 200 1234
```

**Regex Parsing:**

```sql
_sourceCategory=app/logs
| parse regex "(?<timestamp>\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}) \[(?<level>\w+)\] (?<message>.*)"

-- Example:
-- 2026-04-01 12:30:45 [ERROR] Connection timeout
```

**JSON Parsing:**

```sql
_sourceCategory=app/json
| json field=_raw "user_id", "action", "timestamp"
| where action="login"
```

**Practice 2.1:** Custom App Log Parsing

```sql
-- Log format: 2026-04-01 12:30:45 INFO [UserService] User logged in

_sourceCategory=test/app/logs
| parse "* * [*] *" as timestamp, level, component, message
| where level="ERROR"
| count by component
```

---

### 2.2 Field Extraction Rules (FER)

**Create FER:**

```
UI Path: Manage Data → Logs → Field Extraction Rules → Add

Rule Name: Apache Access Fields
Scope: _sourceCategory=apache/access

Parse Expression:
parse "* - - [*] \"* * HTTP/*\" * *" as 
client_ip, timestamp, method, url, http_version, status_code, bytes_sent

Fields: client_ip, method, url, status_code, bytes_sent
```

**Benefits:**
- Auto-extraction in all searches
- Faster query execution
- Consistent field names

---

## Module 3: Sumo Query Language (SumoQL)

### 3.1 Search Syntax

**Basic Search:**

```sql
-- Keyword
error

-- Source category
_sourceCategory=apache/access

-- Field filter
status_code=500

-- Time range with aggregation
_sourceCategory=apache/access status_code=500
| timeslice 1h
| count by _timeslice
```

**Operators:**

```sql
-- Logical
level=ERROR AND component=UserService

-- Wildcards
url="/api/*"

-- Regex
message matches "timeout.*connection"

-- Numeric
status_code >= 400
```

---

### 3.2 Aggregation Operators

**Common Aggregations:**

```sql
-- Count
_sourceCategory=apache/access
| count by status_code
| sort by _count desc

-- Sum
_sourceCategory=apache/access
| sum(bytes_sent) by url
| limit 10

-- Average
_sourceCategory=app/response_time
| avg(response_time) by endpoint

-- Percentiles
_sourceCategory=app/latency
| pct(response_time, 50, 75, 90, 95, 99)
```

**Practice 3.2:** Top 10 Slowest Endpoints

```sql
_sourceCategory=service-a/api/access
| parse "* * * *ms" as method, endpoint, status, response_time
| avg(response_time) by endpoint
| sort by _avg desc
| limit 10
```

---

### 3.3 Time Series Queries

**Timeslice:**

```sql
_sourceCategory=apache/access
| timeslice 5m
| count by _timeslice
```

**Outlier Detection:**

```sql
_sourceCategory=app/errors
| timeslice 1m
| count by _timeslice
| outlier _count
```

**Practice 3.3:** Error Spike Detection

```sql
_sourceCategory=service-a/app/logs level=ERROR
| timeslice 5m
| count by _timeslice
| where _count > 10
```

---

## Module 4: Dashboards & Visualizations

### 4.1 Dashboard Creation

**Panel Types:**
- **Line Chart** - Time series (request rate, errors)
- **Bar Chart** - Comparisons (status codes, URLs)
- **Pie Chart** - Distribution (log levels, services)
- **Table** - Detailed data
- **Single Value** - One number (total errors)

**Practice 4.1:** Infrastructure Dashboard

```
Dashboard: Service-A Infrastructure Logs

Panel 1 - Log Volume
_sourceCategory=service-a/*
| timeslice 1h
| count by _timeslice

Panel 2 - Errors by Service
_sourceCategory=service-a/* level=ERROR
| timeslice 1h
| count by service, _timeslice

Panel 3 - Top Errors
_sourceCategory=service-a/* level=ERROR
| count by message
| top 10

Panel 4 - HTTP Errors
_sourceCategory=service-a/api/access
| where status_code >= 400
| count by status_code
```

---

### 4.2 Dashboard Variables

**Variable Configuration:**

```
Variable: environment
Type: Dropdown
Options: prod, preprod, dev

Query:
_sourceCategory=service-a/* env={{environment}}
| count by _timeslice
```

---

## Module 5: Alerts & Monitoring

### 5.1 Scheduled Search Alerts

**Error Rate Alert:**

```
Name: High Error Rate - Service-A Frontend
Query:
_sourceCategory=service-a/frontend/logs level=ERROR
| timeslice 5m
| count by _timeslice

Condition: > 50 errors in 5 minutes
Notifications:
- Email: ops-team@company.com
- Slack: #ops-alerts
- PagerDuty: oncall
```

**Practice 5.1:** Database Connection Alert

```
Name: DB Connection Pool Exhaustion
Query:
_sourceCategory=service-a/database
| parse "connections: */100" as active
| where active > 90

Condition: Any results
Frequency: Every 2 minutes
```

---

### 5.2 Real-Time Alerts

**Use Cases:**
- Security incidents
- Critical failures
- Payment issues

**Configuration:**

```
Type: Real-Time
Query:
_sourceCategory=security/auth level=CRITICAL
| where message matches "*unauthorized*"

Notification: Immediate
```

---

## Module 6: Security & Compliance

### 6.1 Security Monitoring

**Failed Login Detection:**

```sql
_sourceCategory=auth/logs
| parse "* failed login from *" as user, ip_address
| count by user, ip_address
| where _count > 5
```

**Privilege Escalation:**

```sql
_sourceCategory=system/audit
| parse "user * executed sudo *" as user, command
| where command matches "chmod|chown|passwd"
```

**Brute Force Detection:**

```sql
_sourceCategory=auth/ssh
| parse "Failed password for * from *" as user, ip
| timeslice 1m
| count by ip, _timeslice
| where _count >= 5
```

**Practice 6.1:** Unauthorized Access Monitor

```sql
_sourceCategory=security/*
| where status in ("403", "401")
| timeslice 5m
| count by client_ip, _timeslice
| where _count > 10
```

---

### 6.2 Compliance Logging

**PCI-DSS:**

```sql
_sourceCategory=payment/logs
| parse "card_number=* user=*" as card, user
| count by user, action
```

**GDPR:**

```sql
_sourceCategory=gdpr/logs
| parse "data_subject=* action=*" as subject, action
| where action in ("access", "modify", "delete")
```

---

## Module 7: Advanced Queries

### 7.1 Lookup Tables

**Static Lookup:**

```sql
_sourceCategory=apache/access
| parse "* - -" as client_ip
| lookup location, risk from /Library/Lookup/ip_locations 
on ip=client_ip
| where risk="high"
```

---

### 7.2 Subqueries

**Nested Query:**

```sql
-- Find high-error IPs, then all their requests
_sourceCategory=apache/access
| where status_code >= 500
| count by client_ip
| where _count > 10
| join (
_sourceCategory=apache/access
| parse "* - -" as client_ip
) on client_ip
```

---

### 7.3 Transaction Analysis

**Session Tracking:**

```sql
_sourceCategory=app/sessions
| parse "session_id=* action=*" as session_id, action
| transaction on session_id
with "action=login" as start
and "action=logout" as end
| fields session_id, _transaction_duration
```

---

## Module 8: Performance Optimization

### 8.1 Partitions

**Create Partition:**

```
Name: service-a_prod_logs
Routing: _sourceCategory=service-a/*/prod
Retention: 30 days
```

**Query:**

```sql
_index=service-a_prod_logs level=ERROR
| count by component
```

**Benefits:**
- Faster searches (pre-filtered)
- Cost optimization
- Better organization

---

### 8.2 Scheduled Views

**Pre-aggregated Data:**

```sql
-- Create view
_sourceCategory=apache/access
| timeslice 1h
| count by _timeslice, status_code, url

-- Save as: apache_hourly_stats
```

**Query View:**

```sql
_view=apache_hourly_stats
| where status_code >= 400
| sum(_count) by url
```

**Performance:** 10-100x faster than raw queries

---

## Module 9: Kubernetes Logging

### 9.1 Kubernetes Collector

**DaemonSet Configuration:**

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
name: sumologic-collector
namespace: sumologic
spec:
template:
spec:
containers:
- name: collector
image: sumologic/kubernetes-fluentd:latest
env:
- name: SUMO_ENDPOINT_URL
valueFrom:
secretKeyRef:
name: sumologic
key: endpoint-url
- name: SUMO_SOURCE_CATEGORY
value: "kubernetes/cluster"
volumeMounts:
- name: varlog
mountPath: /var/log
readOnly: true
```

---

### 9.2 Container Logs Query

**Pod Logs:**

```sql
_sourceCategory=kubernetes/cluster
| parse "namespace=* pod_name=*" as namespace, pod
| where namespace="build-service"
| count by pod
```

**Error Analysis:**

```sql
_sourceCategory=kubernetes/*/*
| json field=_raw "log" as message
| where message matches "*ERROR*"
| count by namespace, pod_name
```

---

## Module 10: Practical Projects

### Project 1: Centralized Logging (30 Servers)

**Objective:** Collect logs from 30 the company servers

**Tasks:**

1. **Install collectors** on all servers
2. **Configure sources:**
- /var/log/messages
- /var/log/secure
- /var/log/httpd/*
- /var/log/app/*.log
3. **Field Extraction Rules:**
- Syslog parsing
- Apache parsing
- App log parsing
4. **Dashboard:**
- Log volume
- Error rates
- Auth failures
- Top errors
5. **Alerts:**
- High error rate
- Failed auth
- Disk space

**Expected:**
- 30/30 collectors
- All sources configured
- 6+ dashboard panels
- 5+ alerts

---

### Project 2: Security Monitoring

**Objective:** Security event detection

**Tasks:**

1. **Failed SSH detection**
2. **Sudo tracking**
3. **File permission monitoring**
4. **Security dashboard**
5. **Real-time alerts**

---

### Project 3: Application Performance

**Objective:** Service-A performance tracking

**Tasks:**

1. **Response time tracking** (p50, p95, p99)
2. **Error rate monitoring**
3. **Slow query analysis**
4. **Performance dashboard**

---

## Summary

### Skills Acquired

1. **Architecture** - Collector, Sources, Platform
2. **Installation** - Linux collectors
3. **Parsing** - Parse operators, FER
4. **SumoQL** - Query language, aggregations
5. **Dashboards** - Visualizations
6. **Alerts** - Scheduled, real-time
7. **Security** - Threat detection
8. **Advanced** - Lookups, subqueries
9. **Performance** - Partitions, views
10. **Kubernetes** - Container logging

### Next Steps

1. **Production rollout**
2. **Log retention strategy**
3. **SIEM integration**
4. **Custom parsers**
5. **Automated remediation**

### Resources

- **SumoLogic Docs:** https://help.sumologic.com/
- **API Docs:** https://api.sumologic.com/docs/
- **Community:** https://support.sumologic.com/

---

**Author:** Infrastructure Team (ops-lead@company.com) 
**Last Updated:** April 1, 2026 
**Version:** 1.0
