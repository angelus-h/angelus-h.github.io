# Datadog Agent Configuration Guide

**Category:** Monitoring / Configuration 
**Last Updated:** April 1, 2026 
**Applies To:** the company Infrastructure (RHEL 8/9)

---

## Overview

Complete guide for installing and configuring Datadog Agent on the company Enterprise Linux servers with corporate proxy, custom NTP, and proper tagging.

**Key Requirements:**
- Corporate proxy configuration (proxy.company.internal:3128)
- Custom NTP server (clock.corp.company.internal)
- No logs collection (SumoLogic handles logs)
- Process monitoring enabled
- Network performance monitoring disabled
- Proper tagging (appcode, env, service, datacenter, team, role)

---

## Installation

### Prerequisites

```bash
# Ensure corporate proxy is accessible
curl -I -x http://proxy.company.internal:3128 https://api.datadoghq.com

# Verify NTP server
ntpdate -q clock.corp.company.internal

# Check RHEL version
cat /etc/redhat-release
```

### Install Datadog Agent

**On RHEL 8/9:**

```bash
# Set environment variables
export DD_API_KEY="your-datadog-api-key"
export DD_SITE="datadoghq.com"

# Download and install (single command)
DD_API_KEY=$DD_API_KEY \
DD_SITE=$DD_SITE \
bash -c "$(curl -L https://s3.amazonaws.com/dd-agent/scripts/install_script_agent7.sh)"
```

**If using corporate proxy during installation:**

```bash
export https_proxy=http://proxy.company.internal:3128
export http_proxy=http://proxy.company.internal:3128

DD_API_KEY=$DD_API_KEY \
DD_SITE=$DD_SITE \
bash -c "$(curl -L https://s3.amazonaws.com/dd-agent/scripts/install_script_agent7.sh)"
```

**Manual RPM installation (if network issues):**

```bash
# Download RPM
curl -O https://yum.datadoghq.com/stable/7/x86_64/datadog-agent-7.77.1-1.x86_64.rpm

# Install
sudo yum install -y datadog-agent-7.77.1-1.x86_64.rpm
```

### Verify Installation

```bash
# Check service status
sudo systemctl status datadog-agent

# Check agent version
sudo datadog-agent version

# Expected output:
# Agent 7.77.1 - Meta: github.com/DataDog/datadog-agent@...
```

---

## Main Configuration

### File: `/etc/datadog-agent/datadog.yaml`

**Minimal Production Configuration:**

```yaml
# =========================================
# DATADOG AGENT CONFIGURATION
# Generated: 2026-04-01
# Environment: the company Production
# =========================================

# === REQUIRED SETTINGS ===
api_key: YOUR_DATADOG_API_KEY_HERE
site: datadoghq.com

# === HOSTNAME ===
hostname: server.example.com

# === TAGS ===
tags:
- appcode:service-a-001      # Application code (from CMDB)
- env:prod         # Environment (prod/preprod)
- service:service-a-frontend   # Service name
- datacenter:rdu2      # Datacenter location (rdu2/iad2)
- team:ops-team        # Team responsible
- role:frontend       # Server role

# === PROXY CONFIGURATION ===
proxy:
https: http://proxy.company.internal:3128
http: http://proxy.company.internal:3128

# === LOGS COLLECTION ===
# CRITICAL: Logs are handled by SumoLogic, NOT Datadog
logs_enabled: false

# === PROCESS MONITORING ===
process_config:
enabled: "true" # STRING not boolean (Datadog v7.76+ requirement)
process_discovery:
enabled: true
container_collection:
enabled: false

# === APM (Application Performance Monitoring) ===
apm_config:
enabled: false # Not used in this deployment

# === NETWORK MONITORING ===
# Note: Basic network metrics (system.net.*) are available WITHOUT this
network_config:
enabled: false # NPM disabled (premium feature)

# === AGENT PERFORMANCE TUNING ===
aggregator_flush_interval: 15
forwarder_timeout: 20
check_runners: 4

# === METADATA COLLECTION ===
enable_metadata_collection: true
enable_gohai: true
```

### Configuration by Application

#### Service-A Servers (9 hosts)

```yaml
api_key: ad1c08df3ba84bb8a68f2ff06a85a4dd # Service-A-specific key
tags:
- appcode:service-a-001
- env:prod # or preprod
- service:service-a-frontend # or service-a-backend, service-a-database
- datacenter:rdu2 # or iad2
- team:ops-team
- role:frontend # or backend, database
```

#### Service-B Servers (6 hosts)

```yaml
api_key: 5e8f9a2c1b3d4e5f6a7b8c9d0e1f2a3b # Service-B-specific key
tags:
- appcode:dgit-001
- env:prod # or preprod
- service:dgit-git # or dgit-database
- datacenter:rdu2 # or iad2
- team:ops-team
- role:git-server # or database
```

#### Service-C Servers (15 hosts)

```yaml
api_key: 9bf5da6daf764350a4e2b95d0e670ab3 # Service-C-specific key
tags:
- appcode:service-c-001
- env:prod # or preprod
- service:service-c-hub # or service-c-web, service-c-util, service-c-database
- datacenter:rdu2 # or iad2
- team:ops-team # or sp-rhel-build (Windows Builder)
- role:hub-external # or hub-internal, web, utility, database
```

---

## NTP Integration

### File: `/etc/datadog-agent/conf.d/ntp.d/conf.yaml`

**Why Needed:** Default Datadog NTP servers (datadog.pool.ntp.org) are blocked by corporate firewall.

**Configuration:**

```yaml
# NTP Integration - Corporate NTP Server
# File: /etc/datadog-agent/conf.d/ntp.d/conf.yaml

init_config:

instances:
- host: clock.corp.company.internal
port: 123
version: 3
timeout: 5
```

**Create Directory (if not exists):**

```bash
sudo mkdir -p /etc/datadog-agent/conf.d/ntp.d
```

**Deploy Configuration:**

```bash
sudo tee /etc/datadog-agent/conf.d/ntp.d/conf.yaml > /dev/null <<'EOF'
init_config:
instances:
- host: clock.corp.company.internal
port: 123
version: 3
timeout: 5
EOF
```

**Restart Agent:**

```bash
sudo systemctl restart datadog-agent
```

**Verify NTP Integration:**

```bash
sudo datadog-agent status | grep -A 10 "ntp ("
```

**Expected Output:**

```
ntp (1.14.0)
Instance ID: ntp [OK]
Configuration Source: file:/etc/datadog-agent/conf.d/ntp.d/conf.yaml
Total Runs: 123
Metric Samples: Last Run: 1, Total: 123
Events: Last Run: 0, Total: 0
Service Checks: Last Run: 1, Total: 123
Average Execution Time : 15ms
```

---

## Service Management

### Start/Stop/Restart

```bash
# Start agent
sudo systemctl start datadog-agent

# Stop agent
sudo systemctl stop datadog-agent

# Restart agent
sudo systemctl restart datadog-agent

# Enable auto-start on boot
sudo systemctl enable datadog-agent

# Check status
sudo systemctl status datadog-agent
```

### Check Agent Status

```bash
# Full status (comprehensive)
sudo datadog-agent status

# Check specific integration
sudo datadog-agent status | grep -A 10 "ntp ("

# Check connectivity
sudo datadog-agent status | grep "Connectivity"

# Expected output:
# Connectivity
#  ==========
#   https://app.datadoghq.com - API Key ending with: ...b521d
```

### Logs

```bash
# Agent logs
sudo tail -100 /var/log/datadog/agent.log

# Follow logs in real-time
sudo tail -f /var/log/datadog/agent.log

# Search for errors
sudo grep ERROR /var/log/datadog/agent.log

# NTP-specific logs
sudo grep ntp /var/log/datadog/agent.log
```

---

## Troubleshooting

### Agent Not Starting

**Check logs:**

```bash
sudo journalctl -u datadog-agent -n 50
```

**Common Issues:**

1. **Invalid API Key**
```
Error: API key is invalid
```
Solution: Verify API key in `/etc/datadog-agent/datadog.yaml`

2. **Proxy Connection Failed**
```
Error: dial tcp: lookup api.datadoghq.com: no such host
```
Solution: Verify proxy configuration

3. **Permission Issues**
```
Error: Permission denied
```
Solution: Ensure dd-agent user has correct permissions

### NTP Integration Not Working

**Check NTP server accessibility:**

```bash
ntpdate -q clock.corp.company.internal
```

**Expected Output:**

```
server 10.x.x.x, stratum 2, offset 0.000123, delay 0.00234
```

**If not accessible:**

```bash
# Check firewall
sudo firewall-cmd --list-all

# Test NTP port
telnet clock.corp.company.internal 123
```

### Process Monitoring Not Starting

**Issue:** `process_config.enabled: true` (boolean) not working on Datadog v7.76+

**Solution:** Use STRING value

```yaml
process_config:
enabled: "true" # STRING not boolean
```

**Verify:**

```bash
sudo datadog-agent status | grep "process ("
```

### Network Metrics Not Appearing

**Wait Time:** Network metrics can take 15-30 minutes to appear after agent start

**Check metrics collection:**

```bash
sudo datadog-agent status | grep "network ("
```

**If still not appearing:**

```bash
# Restart agent
sudo systemctl restart datadog-agent

# Wait 15-30 minutes
# Check Datadog UI: Metrics Explorer → system.net.bytes_sent
```

---

## Ansible Automation

### Inventory File

**File:** `inventories/production.yml`

```yaml
all:
children:
service-a:
hosts:
service-a-frontend-el8.service-a-001.prod.rdu2.dc.company.internal:
dd_service: service-a-frontend
dd_role: frontend
dd_env: prod
dd_datacenter: rdu2
```

### Group Variables

**File:** `group_vars/all.yml`

```yaml
---
# Global Variables for All Hosts

dd_site: "datadoghq.com"
dd_agent_version: "7.77.1"

# Proxy configuration
dd_proxy_https: "http://proxy.company.internal:3128"
dd_proxy_http: "http://proxy.company.internal:3128"

# Feature flags
dd_logs_enabled: false # SumoLogic handles logs
dd_process_monitoring_enabled: true
dd_apm_enabled: false
dd_network_monitoring_enabled: false

# NTP configuration
dd_ntp_server: "clock.corp.company.internal"
dd_ntp_port: 123

# Agent settings
dd_aggregator_flush_interval: 15
dd_forwarder_timeout: 20
dd_check_runners: 4
dd_enable_metadata_collection: true
dd_enable_gohai: true

# SSH user
ansible_user: ops-lead
ansible_become: true
```

**File:** `group_vars/service-a.yml`

```yaml
---
# Service-A-Specific Variables

dd_api_key: "ad1c08df3ba84bb8a68f2ff06a85a4dd"
dd_appcode: "service-a-001"
dd_team: "ops-team"
```

### Playbook

**File:** `deploy-datadog.yml`

```yaml
---
- name: Deploy Datadog Agent to all servers
hosts: all
become: true
gather_facts: true

roles:
- datadog-agent

post_tasks:
- name: Display Datadog Agent status
ansible.builtin.command: systemctl status datadog-agent --no-pager
register: agent_status_output
changed_when: false
failed_when: false

- name: Show agent version
ansible.builtin.command: datadog-agent version
register: agent_version
changed_when: false

- name: Summary
ansible.builtin.debug:
msg: |
====================================
Datadog Agent Deployment Summary
====================================
Host: {{ inventory_hostname }}
Application: {{ dd_appcode }}
Service: {{ dd_service }}
Environment: {{ dd_env }}
Datacenter: {{ dd_datacenter }}
Role: {{ dd_role }}
Agent Version: {{ agent_version.stdout_lines[0] | default('Unknown') }}
Status: {{ agent_status_output.stdout_lines[2] | default('Unknown') }}
====================================
```

### Run Deployment

```bash
cd /path/to/ansible-datadog

# Deploy to all servers
ansible-playbook -i inventories/production.yml deploy-datadog.yml

# Deploy to specific group
ansible-playbook -i inventories/production.yml deploy-datadog.yml --limit service-a
ansible-playbook -i inventories/production.yml deploy-datadog.yml --limit service-b
ansible-playbook -i inventories/production.yml deploy-datadog.yml --limit service-c

# Check mode (dry run)
ansible-playbook -i inventories/production.yml deploy-datadog.yml --check
```

---

## Verification Checklist

After deployment, verify:

- [ ] Agent service running: `systemctl status datadog-agent`
- [ ] Agent version correct: `datadog-agent version` (7.77.1)
- [ ] API connectivity: `datadog-agent status | grep Connectivity`
- [ ] NTP integration active: `datadog-agent status | grep "ntp ("`
- [ ] Process monitoring enabled: `datadog-agent status | grep "process ("`
- [ ] Logs collection disabled: `grep logs_enabled /etc/datadog-agent/datadog.yaml` → false
- [ ] Metrics appearing in Datadog UI (wait 5-10 minutes)
- [ ] Host visible in Infrastructure List
- [ ] Tags applied correctly (check in UI)

---

## Security Considerations

### No Logs Collection

**CRITICAL:** Logs must NOT be sent to Datadog

```yaml
logs_enabled: false
```

**Why:** the company policy - SumoLogic handles all log aggregation.

**Verify in Datadog UI:**
- Navigate to Logs section
- Should show: "Not Accessible" or "No Logs"

### API Key Protection

**Never commit API keys to git:**

```bash
# Use Ansible Vault for sensitive data
ansible-vault encrypt group_vars/service-a.yml

# Or use environment variables
export DD_API_KEY="..."
```

### Network Segmentation

All Datadog traffic goes through corporate proxy:
- No direct internet access from servers
- Proxy logs all connections
- Proxy enforces security policies

---

## Performance Impact

### Resource Usage

**Expected overhead per server:**
- CPU: +2-5% (agent + process monitoring)
- Memory: +100-150 MB (agent process)
- Network: ~100 KB/s (metrics upload)
- Disk: ~100 MB (agent binaries + logs)

**Not significant for:**
- Production servers (minimal impact)
- Development servers (no impact)

### Optimization

```yaml
# Reduce check frequency (if needed)
aggregator_flush_interval: 30 # Default: 15 seconds

# Reduce check runners (if needed)
check_runners: 2 # Default: 4
```

---

## Related Documentation

- **Dashboard Guide:** `Datadog-Infrastructure-Monitoring.md`
- **Network Monitoring:** `DATADOG-NETWORK-MONITORING-EXPLAINED.md`
- **Universal Installation Guide:** `DATADOG-AGENT-UNIVERSAL-GUIDE.md`

---

## Quick Reference

### Essential Commands

```bash
# Install agent
DD_API_KEY=$DD_API_KEY bash -c "$(curl -L https://s3.amazonaws.com/dd-agent/scripts/install_script_agent7.sh)"

# Check status
sudo datadog-agent status

# Restart agent
sudo systemctl restart datadog-agent

# View logs
sudo tail -f /var/log/datadog/agent.log

# Test connectivity
curl -v -x http://proxy.company.internal:3128 https://api.datadoghq.com
```

### Key Files

| File | Purpose |
|------|---------|
| `/etc/datadog-agent/datadog.yaml` | Main configuration |
| `/etc/datadog-agent/conf.d/ntp.d/conf.yaml` | NTP integration |
| `/var/log/datadog/agent.log` | Agent logs |
| `/opt/datadog-agent/` | Agent binaries |
| `/etc/systemd/system/datadog-agent.service` | Systemd service |

---

**Last Updated:** April 1, 2026 
**Maintainer:** Infrastructure Team (ops-lead@company.com) 
**Team:** OPS-TEAM
