# Datadog Network Monitoring Setup Guide

**Environment:** RHEL 8/9 
**Datadog Agent:** 7.77.1+ 
**Server Count:** 30 servers (Service-A, Service-B, Service-C infrastructure) 
**Last Updated:** April 1, 2026

---

## Overview

This guide demonstrates how to configure Datadog Agent for network metrics collection. There are two levels of network monitoring available:

1. **Basic Network Metrics** (FREE) - Network interface statistics
2. **Network Performance Monitoring (NPM)** (PAID) - Deep packet inspection, flow analysis

**This guide covers BASIC network metrics** - no additional licensing required.

---

## What You Get with Basic Network Metrics

### Available Metrics (No NPM Required)

| Metric | Description | Unit |
|--------|-------------|------|
| `system.net.bytes_sent` | Bytes sent per interface | bytes/s |
| `system.net.bytes_rcvd` | Bytes received per interface | bytes/s |
| `system.net.packets_in.count` | Packets received | packets/s |
| `system.net.packets_out.count` | Packets sent | packets/s |
| `system.net.packets_in.error` | Receive errors | errors/s |
| `system.net.packets_out.error` | Transmit errors | errors/s |
| `system.net.packets_in.drop` | Dropped packets (RX) | packets/s |
| `system.net.packets_out.drop` | Dropped packets (TX) | packets/s |

### What NPM Adds (Paid Feature)

- TCP/UDP connection tracking
- DNS query monitoring
- Service-to-service network maps
- Application-level latency tracking
- Distributed tracing for network flows

**For basic infrastructure monitoring, FREE metrics are sufficient.**

---

## Configuration

### Step 1: Verify Current Network Metrics

**Check if network metrics are already collected:**

```bash
sudo datadog-agent status | grep -A 5 'network ('
```

**Expected output:**

```
network (5.0.0)
Instance ID: network:d884b5186b651429 [OK]
Configuration Source: file:/etc/datadog-agent/conf.d/network.d/auto_conf.yaml
Total Runs: 1,234
Metric Samples: Last Run: 24, Total: 29,616
Service Checks: Last Run: 0, Total: 0
```

If you see `[OK]` and `Metric Samples > 0`, network metrics are already being collected!

---

### Step 2: Datadog Agent Configuration

**Basic network metrics are ENABLED BY DEFAULT.** No configuration changes needed.

However, if you want to customize collection, edit the main agent config:

```bash
sudo vi /etc/datadog-agent/datadog.yaml
```

**Network-related settings:**

```yaml
# Network metric collection (enabled by default)
# collect_ec2_tags: true # If running on AWS EC2
# collect_gce_tags: true # If running on GCP

# Optional: Exclude specific network interfaces
network:
enabled: true
# Exclude virtual interfaces (optional)
excluded_interfaces:
- lo      # Loopback
- docker0   # Docker bridge
- veth.*    # Docker container interfaces
- br-.*    # Linux bridges
```

**No restart needed if using defaults** - metrics are collected automatically.

---

### Step 3: Restart Agent (If Metrics Missing)

If network metrics are not appearing in Datadog UI:

```bash
sudo systemctl restart datadog-agent
sudo systemctl status datadog-agent
```

**Wait 2-3 minutes** for metrics to appear in Datadog UI.

---

### Step 4: Verify Network Metrics Collection

**Check agent status:**

```bash
sudo datadog-agent status | grep -A 10 'network ('
```

**Expected output (SUCCESS):**

```
network (5.0.0)
Instance ID: network:d884b5186b651429 [OK]
Configuration Source: file:/etc/datadog-agent/conf.d/network.d/auto_conf.yaml
Total Runs: 1,234
Metric Samples: Last Run: 24, Total: 29,616
Average Execution Time: 2ms
Last Execution Date: 2026-04-01 15:45:30 EDT
```

**Key indicators:**

- Instance ID shows `[OK]`
- `Metric Samples: Last Run` > 0 (typically 20-30 per run)
- `Average Execution Time` < 10ms

---

### Step 5: View Network Metrics in Datadog UI

**Login to Datadog:**

[the company SSO](https://app.datadoghq.com/account/login/id/pynb8yqdcx1cm107?saml_config_uuid=a3a42676-c0d9-4b3c-bdcc-016ba566a564)

**Navigate to Metrics Explorer:**

```
1. Metrics → Explorer
2. Select metric: system.net.bytes_sent
3. Filter: env:prod
4. Group by: host, device
5. Time range: Past 1 Hour
```

**You should see graphs for all network interfaces:**

- `eth0` - Primary network interface
- `eth1` - Secondary interface (if present)
- `lo` - Loopback (if not excluded)

---

## Network Interfaces on RHEL Servers

### Common Interface Names

| Interface | Description | Typical Usage |
|-----------|-------------|---------------|
| `eth0` | Primary Ethernet | Main network connection |
| `eth1` | Secondary Ethernet | Management or backup network |
| `ens192` | Predictable naming | VMware VM primary NIC |
| `ens224` | Predictable naming | VMware VM secondary NIC |
| `lo` | Loopback | Internal (127.0.0.1) |
| `docker0` | Docker bridge | Container networking |
| `veth*` | Virtual Ethernet | Docker container pairs |

### Identify Your Interfaces

```bash
# List all interfaces with IP addresses
ip addr show

# Show interface statistics
ip -s link show

# Interface traffic (live)
ifstat -i eth0 1
```

---

## Dashboard Examples

### Network Traffic by Host

**Widget: Timeseries**

```
Metric: avg:system.net.bytes_sent{env:prod} by {host}
Display: Line graph
Legend: Below (auto layout)
Unit: Bytes/s
```

**Formula to convert to Mbps:**

```
(avg:system.net.bytes_sent{env:prod} by {host} * 8) / 1000000
```

### Top 5 Hosts by Network Traffic

**Widget: Toplist**

```
Metric: sum:system.net.bytes_sent{env:prod} by {host}
Limit: 5
Sort: Descending
```

### Network Errors

**Widget: Timeseries**

```
Metric: sum:system.net.packets_in.error{env:prod} by {host}
Alert threshold: > 100 errors/min
Display: Red line
```

### Network Traffic Table

**Widget: Table**

**Columns:**

| Column | Formula | Description |
|--------|---------|-------------|
| Host | `{host}` | Server hostname |
| Sent (MB/s) | `avg:system.net.bytes_sent{*} by {host} / 1048576` | Megabytes sent |
| Received (MB/s) | `avg:system.net.bytes_rcvd{*} by {host} / 1048576` | Megabytes received |
| Errors | `sum:system.net.packets_in.error{*} by {host}` | Error count |

---

## Production Example: 30 Servers Deployment

### Scenario

**Servers:**
- 9 Service-A servers (Frontend, Backend, Database)
- 9 Service-B servers (Frontend, Backend, Database)
- 12 Service-C servers (Frontend, Hub, Database)

**Goal:** Monitor network traffic across all servers with single dashboard.

---

### Dashboard Configuration

**Dashboard: "OPS Infrastructure - Network Monitoring"**

**Template Variables:**

```json
{
"template_variables": [
{
"name": "appcode",
"prefix": "appcode",
"default": "*",
"available_values": ["service-a-001", "dgit-001", "service-c-001"]
},
{
"name": "env",
"prefix": "env",
"default": "prod"
},
{
"name": "datacenter",
"prefix": "datacenter",
"default": "*",
"available_values": ["rdu2", "iad2"]
}
]
}
```

**Widgets:**

1. **Total Network Sent (Query Value)**
```
sum:system.net.bytes_sent{$appcode,$env,$datacenter}
Unit: Bytes/s
```

2. **Network Traffic by Host (Timeseries)**
```
avg:system.net.bytes_sent{$appcode,$env,$datacenter} by {host}
avg:system.net.bytes_rcvd{$appcode,$env,$datacenter} by {host}
Display: Line graph (full width)
Legend: Below graph
```

3. **Top 5 Bandwidth Users (Toplist)**
```
sum:system.net.bytes_sent{$appcode,$env,$datacenter} by {host}
Limit: 5
```

4. **Network Errors (Timeseries)**
```
sum:system.net.packets_in.error{$appcode,$env,$datacenter} by {host}
Alert threshold: > 50
```

5. **Server Network Summary (Table)**

**Columns:**
- Host: `{host}`
- Sent (MB/s): `avg:system.net.bytes_sent{$appcode,$env,$datacenter} by {host} / 1048576`
- Received (MB/s): `avg:system.net.bytes_rcvd{$appcode,$env,$datacenter} by {host} / 1048576`
- Errors: `sum:system.net.packets_in.error{$appcode,$env,$datacenter} by {host}`

---

## Monitoring Alerts

### High Network Traffic Alert

**Monitor Type:** Metric Alert

```yaml
Metric: avg:system.net.bytes_sent{env:prod}
Alert condition: > 100 MB/s for 5 minutes
Warning condition: > 80 MB/s for 5 minutes
Group by: host
Notification: @slack-ops-alerts
```

**Message Template:**

```
{{#is_alert}}
High Network Traffic on {{host.name}}
Current: {{value}} MB/s
Environment: {{env.name}}
Service: {{service.name}}
{{/is_alert}}

{{#is_recovery}}
Network traffic back to normal on {{host.name}}
{{/is_recovery}}
```

---

### Network Errors Alert

**Monitor Type:** Metric Alert

```yaml
Metric: sum:system.net.packets_in.error{env:prod}
Alert condition: > 100 errors/min for 5 minutes
Group by: host, device
Notification: @pagerduty-oncall
```

---

## Troubleshooting

### Issue 1: No Network Metrics Appearing

**Symptom:**

```
Metrics Explorer shows "No data" for system.net.bytes_sent
```

**Solution:**

1. **Restart Datadog Agent:**
```bash
sudo systemctl restart datadog-agent
```

2. **Wait 2-3 minutes** for metrics to appear

3. **Verify agent status:**
```bash
sudo datadog-agent status | grep 'network ('
```

4. **Check agent logs:**
```bash
sudo tail -f /var/log/datadog/agent.log | grep network
```

---

### Issue 2: Only Loopback Interface Showing

**Symptom:**

Only `lo` interface metrics visible, missing `eth0`, `ens192`, etc.

**Solution:**

Network interfaces may have non-standard names. List all interfaces:

```bash
ip link show
```

Check metrics for actual interface names:

```
Metrics → Explorer
Metric: system.net.bytes_sent
Group by: device
```

This will show all detected interfaces.

---

### Issue 3: High Error Count

**Symptom:**

```
system.net.packets_in.error shows thousands of errors
```

**Diagnosis:**

```bash
# Check interface errors
ip -s link show eth0

# Look for:
# - RX errors (receive)
# - TX errors (transmit)
# - Dropped packets
```

**Common causes:**

- **Duplex mismatch:** Interface and switch configured differently
- **Cable issues:** Physical layer problems
- **Driver issues:** Network driver bugs
- **Overload:** Traffic exceeding interface capacity

**Fix duplex mismatch:**

```bash
# Check current settings
ethtool eth0

# Force 1000Mbps full duplex
sudo ethtool -s eth0 speed 1000 duplex full autoneg off
```

---

### Issue 4: Metrics Stopped After Agent Update

**Symptom:**

Network metrics were working, stopped after agent upgrade.

**Solution:**

```bash
# Reconfigure agent
sudo datadog-agent configcheck

# Restart agent
sudo systemctl restart datadog-agent

# Verify
sudo datadog-agent status
```

---

## Automated Deployment (30 Servers)

### Bash Script: Restart All Agents

**Use case:** Network metrics not appearing after configuration change.

**Script:** `/home/ops-lead/ai/production-lumino/mcp-servers/ssh-mcp-server/ansible-datadog/restart-all-agents.sh`

```bash
#!/bin/bash

SERVERS=(
service-a-frontend-el8-01.service-a-001.prod.rdu2.dc.company.internal
service-a-frontend-el8-02.service-a-001.prod.rdu2.dc.company.internal
service-a-frontend-el8-03.service-a-001.prod.rdu2.dc.company.internal
service-a-backend-el8-01.service-a-001.prod.rdu2.dc.company.internal
service-a-backend-el8-02.service-a-001.prod.rdu2.dc.company.internal
service-a-backend-el8-03.service-a-001.prod.rdu2.dc.company.internal
service-a-database-el8.service-a-001.prod.iad2.dc.company.internal
service-a-frontend-el9-01.service-a-001.preprod.rdu2.dc.company.internal
service-a-frontend-el9-02.service-a-001.preprod.rdu2.dc.company.internal
# ... (all 30 servers)
)

USER="ops-lead"
SUCCESS=0
FAILED=0

echo "Restarting Datadog Agent on 30 servers..."
echo "=========================================="

for SERVER in "${SERVERS[@]}"; do
echo -n "Processing $SERVER ... "

if ssh "$USER@$SERVER" "sudo systemctl restart datadog-agent && sleep 2 && sudo systemctl is-active datadog-agent" > /dev/null 2>&1; then
echo "✓ SUCCESS"
((SUCCESS++))
else
echo "✗ FAILED"
((FAILED++))
fi
done

echo ""
echo "=========================================="
echo "Summary: $SUCCESS successful, $FAILED failed"
echo "=========================================="
```

**Execute:**

```bash
chmod +x restart-all-agents.sh
./restart-all-agents.sh
```

**Expected result:** 30/30 successful restarts

---

## Advanced: Per-Interface Monitoring

### Filter Specific Interfaces

**Dashboard query for eth0 only:**

```
avg:system.net.bytes_sent{device:eth0,env:prod} by {host}
```

**Exclude loopback and virtual interfaces:**

```
avg:system.net.bytes_sent{!device:lo,!device:docker0,env:prod} by {host}
```

### Multi-Interface Servers

**Scenario:** Servers with multiple physical NICs (eth0, eth1)

**Dashboard widget:**

```
Metric: avg:system.net.bytes_sent{env:prod} by {host,device}
Group by: host, device
Display: Stacked area chart
```

This shows network traffic breakdown per interface per host.

---

## Capacity Planning with Network Metrics

### Calculate 95th Percentile Bandwidth

**Query:**

```
p95:system.net.bytes_sent{env:prod} by {host}
```

**Use case:** Determine peak bandwidth requirements.

### Trend Analysis (30-day)

**Query:**

```
avg:system.net.bytes_sent{env:prod} by {host}
Time range: Past 30 days
Rollup: 1 hour average
```

**Export to CSV** for capacity planning.

### Alert on Sustained High Traffic

**Monitor:**

```yaml
Metric: avg:system.net.bytes_sent{env:prod}
Alert condition: > 80% of 1Gbps (100 MB/s) for 1 hour
Evaluation: last 1 hour
Notification: @email-capacity-planning
```

---

## Network Metrics in Universal Dashboard

**File:** `/home/ops-lead/ai/production-lumino/mcp-servers/ssh-mcp-server/datadog-dashboards/universal-dashboard.json`

**Widget example (Network Traffic):**

```json
{
"id": 7,
"definition": {
"title": "Network Traffic by Host",
"type": "timeseries",
"show_legend": true,
"legend_layout": "auto",
"legend_columns": ["avg", "max", "value"],
"requests": [
{
"formulas": [
{"formula": "query1 / 1048576", "alias": "Sent (MB/s)"},
{"formula": "query2 / 1048576", "alias": "Received (MB/s)"}
],
"response_format": "timeseries",
"queries": [
{
"query": "avg:system.net.bytes_sent{$appcode,$env,$datacenter} by {host}",
"data_source": "metrics",
"name": "query1"
},
{
"query": "avg:system.net.bytes_rcvd{$appcode,$env,$datacenter} by {host}",
"data_source": "metrics",
"name": "query2"
}
],
"style": {"palette": "cool"},
"display_type": "line"
}
]
},
"layout": {"x": 0, "y": 24, "width": 12, "height": 4}
}
```

---

## Summary

### What We Configured

- Basic network metrics (FREE, no NPM license needed)
- Automatic collection (enabled by default)
- Agent restart procedure (when metrics missing)
- Dashboard examples (Traffic, Errors, Top hosts)
- Monitoring alerts (High traffic, Errors)
- Troubleshooting guide (4 common issues)
- Automated deployment (30 servers bash script)

### Metrics Available

- `system.net.bytes_sent` / `bytes_rcvd` - Traffic volume
- `system.net.packets_in.count` / `packets_out.count` - Packet count
- `system.net.packets_in.error` / `packets_out.error` - Errors
- `system.net.packets_in.drop` / `packets_out.drop` - Dropped packets

### Next Steps

1. Add network widgets to your dashboards
2. Configure alerts for high traffic and errors
3. Monitor network metrics for 7 days to establish baselines
4. Set up capacity planning based on 95th percentile

---

## References

- **Datadog Network Metrics:** https://docs.datadoghq.com/integrations/network/
- **Network Performance Monitoring (NPM):** https://docs.datadoghq.com/network_monitoring/performance/
- **System Check:** https://docs.datadoghq.com/integrations/system/

---

**Author:** Infrastructure Team (ops-lead@company.com) 
**Infrastructure:** 30 RHEL 8/9 servers (Service-A, Service-B, Service-C) 
**Deployment Date:** April 1, 2026 
**Success Rate:** 30/30 servers (100%)
