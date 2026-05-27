# Datadog Quick Reference

**Level:** Beginner → Advanced 
**Duration:** 6-8 weeks (5-7 hours/week) 
**Target Audience:** DevOps Engineers, SREs, System Administrators 
**Prerequisites:** Linux basics, REST API knowledge, YAML/JSON familiarity

---

## Overview

Comprehensive guide to Datadog infrastructure monitoring and observability platform with practical examples tailored for enterprise environments and production infrastructure.

**What You'll Learn:**
- Install and configure Datadog Agent
- Collect and visualize metrics
- Create and customize dashboards
- Configure and manage alerts
- Application Performance Monitoring (APM)
- Best practices for enterprise monitoring

---

## Module 1: Datadog Fundamentals

### 1.1 Architecture Overview

**Architecture Diagram:**

![Datadog Architecture](../assets/images/datadog-architecture.png)

**Key Concepts:**
- **Agent:** Metrics collector running on each host
- **Tags:** Metadata for filtering and grouping
- **Integrations:** Pre-built connectors (PostgreSQL, Redis, etc.)
- **Dashboard:** Visual representation of metrics
- **Monitor:** Alert configuration based on thresholds

---

### 1.2 Installation (RHEL 8/9)

**Quick Install:**

```bash
# Set environment variables
export DD_API_KEY="your-api-key"
export DD_SITE="datadoghq.com"

# Install agent
DD_API_KEY=$DD_API_KEY \
DD_SITE=$DD_SITE \
bash -c "$(curl -L https://s3.amazonaws.com/dd-agent/scripts/install_script_agent7.sh)"

# Start service
sudo systemctl start datadog-agent
sudo systemctl enable datadog-agent

# Verify
sudo datadog-agent status
```

**Corporate Proxy Setup:**

```yaml
# /etc/datadog-agent/datadog.yaml
proxy:
https: http://proxy.company.internal:3128
http: http://proxy.company.internal:3128
```

**Practice 1.2:** Install on Test Server

```bash
# Install
export DD_API_KEY="your-key"
bash -c "$(curl -L https://s3.amazonaws.com/dd-agent/scripts/install_script_agent7.sh)"

# Configure tags
sudo vi /etc/datadog-agent/datadog.yaml

tags:
- env:dev
- team:ops
- service:test-server

# Restart
sudo systemctl restart datadog-agent

# Check connectivity
sudo datadog-agent status | grep "Connectivity"
```

---

### 1.3 Tagging Strategy

**Best Practices:**

```yaml
# /etc/datadog-agent/datadog.yaml
tags:
- appcode:app-001    # Application (from CMDB)
- env:prod        # Environment
- service:web-frontend  # Service name
- datacenter:rdu2    # Location
- team:ops-team      # Owner
- role:frontend     # Server role
```

**Tag Usage:**

```
# Filter metrics
avg:system.cpu.user{env:prod,service:web-frontend}

# Group by
avg:system.memory.used{*} by {datacenter}

# Multi-filter
avg:system.disk.used{env:prod,appcode:app-001,datacenter:rdu2}
```

---

## Module 2: Metrics Collection

### 2.1 System Metrics

**Default Metrics:**

| Metric | Description | Unit |
|--------|-------------|------|
| `system.cpu.user` | CPU usage | % |
| `system.mem.used` | Memory used | bytes |
| `system.disk.used` | Disk used | bytes |
| `system.load.1` | 1-min load avg | value |
| `system.net.bytes_sent` | Network sent | bytes/s |
| `system.uptime` | Uptime | seconds |

**Practice 2.1:** View System Metrics

```
1. Login: [Company SSO](https://app.datadoghq.com/)
2. Metrics → Explorer
3. Select: system.cpu.user
4. Filter: env:prod
5. Group by: host
6. Time range: Past 1 Hour
```

---

### 2.2 Integration Metrics (PostgreSQL)

**Configuration:**

```yaml
# /etc/datadog-agent/conf.d/postgres.d/conf.yaml
init_config:

instances:
- host: localhost
port: 5432
username: datadog
password: 'secure-password'
dbname: postgres
tags:
- service:postgresql
- env:prod
```

**PostgreSQL User Setup:**

```sql
CREATE USER datadog WITH PASSWORD 'secure-password';
GRANT SELECT ON pg_stat_database TO datadog;
GRANT pg_monitor TO datadog;
```

**Verification:**

```bash
sudo datadog-agent status | grep -A 10 "postgres ("
```

**Key Metrics:**
- `postgresql.connections` - Active connections
- `postgresql.database_size` - Database size
- `postgresql.locks` - Lock count
- `postgresql.max_connections` - Max connections

---

### 2.3 Custom Metrics (DogStatsD)

**Python Example:**

```python
from datadog import initialize, statsd

options = {
'statsd_host': '127.0.0.1',
'statsd_port': 8125
}
initialize(**options)

# Counter
statsd.increment('page.views', tags=['page:home'])

# Gauge
statsd.gauge('queue.length', 42, tags=['queue:processing'])

# Histogram
statsd.histogram('request.duration', 0.234, tags=['endpoint:/api'])
```

**Bash Example:**

```bash
# Send metric via UDP
echo "custom.metric:123|g|#env:dev" | nc -u -w1 localhost 8125
```

**Practice 2.3:** Custom Metric Test

```bash
# Enable DogStatsD
# /etc/datadog-agent/datadog.yaml
use_dogstatsd: true

# Restart agent
sudo systemctl restart datadog-agent

# Send test metrics
for i in {1..100}; do
VALUE=$((RANDOM % 100))
echo "test.metric:${VALUE}|g|#env:dev" | nc -u -w1 localhost 8125
sleep 1
done

# View in UI: Metrics Explorer → test.metric
```

---

## Module 3: Dashboards

### 3.1 Dashboard Types

**Timeboard:** Time-synchronized graphs for correlation 
**Screenboard:** Flexible layout, various widget types

**Widget Types:**
- **Timeseries** - Metrics over time (line graph)
- **Query Value** - Single number display
- **Table** - Tabular data with color coding
- **Heatmap** - Distribution visualization
- **Toplist** - Top N values
- **Check Status** - Service check status

**Practice 3.1:** Create First Dashboard

```
1. Dashboards → New Dashboard
2. Name: "Infrastructure Monitoring"
3. Layout: Ordered
4. Add Widget → Timeseries
- Metric: avg:system.cpu.user{team:ops} by {host}
- Title: "CPU Usage by Host"
5. Add Widget → Query Value
- Metric: count_nonzero(avg:system.cpu.user{team:ops})
- Title: "Total Hosts"
6. Save
```

---

### 3.2 Query Language

**Syntax:**

```
<AGGREGATION>:<METRIC>{<SCOPE>} [by {<GROUPING>}]
```

**Examples:**

```
# Average CPU per host
avg:system.cpu.user{env:prod} by {host}

# Max memory per datacenter
max:system.mem.used{*} by {datacenter}

# Sum network traffic by service
sum:system.net.bytes_sent{*} by {service}

# 95th percentile disk usage
p95:system.disk.in_use{*} by {device}
```

**Formulas:**

```
# CPU + Memory combined
(avg:system.cpu.user{*} + avg:system.mem.pct_usable{*}) / 2

# Uptime in days
avg:system.uptime{*} / 86400

# Disk free percentage
100 - (avg:system.disk.in_use{*} * 100)
```

---

### 3.3 Template Variables

**Dynamic Filtering:**

```json
{
"template_variables": [
{
"name": "env",
"prefix": "env",
"default": "*",
"available_values": ["prod", "preprod", "dev"]
},
{
"name": "datacenter",
"prefix": "datacenter",
"default": "*"
}
]
}
```

**Usage in Queries:**

```
avg:system.cpu.user{$env,$datacenter} by {host}
```

**Practice 3.3:** Universal Dashboard

```
Template Variables:
- $appcode (mbs-001, dgit-001, brew-001, *)
- $env (prod, preprod, *)
- $datacenter (rdu2, iad2, *)

Widgets:
1. Total Hosts: count_nonzero(avg:system.cpu.user{$appcode,$env,$datacenter})
2. CPU by Host: avg:system.cpu.user{$appcode,$env,$datacenter} by {host}
3. Memory by Host: avg:system.mem.used{$appcode,$env,$datacenter} by {host}

Use Cases:
- $appcode=mbs-001 → 9 servers
- $appcode=* → All 30 servers
- $env=prod, $datacenter=rdu2 → Prod RDU2 only
```

**Example Dashboard JSON:**

Complete example dashboard configuration available at: 
[example-dashboard.json](example-dashboard.json)

This example includes:
- Total Hosts counter
- CPU Usage timeseries (full width, legends below)
- Memory Usage timeseries (full width, legends below)
- Template variable for environment filtering

---

## Module 4: Monitoring & Alerts

### 4.1 Monitor Types

**Metric Monitor:**

```yaml
Alert when: avg:system.cpu.user{service:web-frontend} > 80
Time window: last 5 minutes
Notification: @slack-ops-team
```

**Anomaly Monitor:**

```yaml
Alert when: system.load.1{*} deviates from normal pattern
Algorithm: Agile
Sensitivity: Medium
```

**Composite Monitor:**

```yaml
Alert when: (CPU > 80%) AND (Memory > 80%)
```

**Practice 4.1:** Create CPU Alert

```
1. Monitors → New Monitor → Metric
2. Detection method: Threshold
3. Metric: avg:system.cpu.user{service:web-frontend}
4. Alert threshold: > 80
5. Warning threshold: > 60
6. Evaluation: last 5 minutes
7. Notify:
- @ops-lead@company.com
- @slack-ops-team
8. Message template:
{{#is_alert}}
CPU HIGH on {{host.name}}
Current: {{value}}%
{{/is_alert}}
9. Save
```

---

### 4.2 Notification Channels

**Email:** `@user@example.com` 
**Slack:** `@slack-channel-name` 
**PagerDuty:** `@pagerduty-service`

**Multi-level Alerts:**

```
Critical (> 90%):
- @pagerduty-oncall
- @slack-critical
- @email-oncall

Warning (> 80%):
- @slack-warnings
- @email-team

Recovery:
- @slack-info
```

---

### 4.3 Downtime Scheduling

**Maintenance Window:**

```
Monitors → Manage Downtime
Scope: service:database AND env:prod
Start: 2026-04-10 02:00 UTC
Duration: 4 hours
Reason: "Database Patching - JIRA: OPS-1234"
Notify: @slack-ops-team
```

---

## Module 5: APM (Application Performance Monitoring)

### 5.1 APM Concepts

**Distributed Tracing:**

```
User Request: GET /api/users/123
├─ Frontend Service (250ms)
└─ Backend Service (180ms)
├─ Database Query (50ms)
└─ Cache Lookup (10ms)
Total: 250ms
```

**Key Terms:**
- **Trace:** Complete request path through system
- **Span:** Single operation (function call, DB query)
- **Service:** Application component
- **Resource:** Specific endpoint or operation

---

### 5.2 APM Setup (Python/Flask)

**Installation:**

```bash
pip install ddtrace
```

**Automatic Instrumentation:**

```bash
DD_SERVICE="api-backend" \
DD_ENV="prod" \
DD_VERSION="2.5.1" \
ddtrace-run python app.py
```

**Manual Instrumentation:**

```python
from ddtrace import tracer

@tracer.wrap(service="mbs-backend", resource="calculate_stats")
def calculate_statistics(data):
with tracer.trace("data_processing") as span:
span.set_tag("data_size", len(data))
result = process(data)
return result
```

**Practice 5.2:** Flask APM

```python
from flask import Flask
from ddtrace import patch_all

patch_all() # Auto-instrument

app = Flask(__name__)

@app.route('/api/data')
def get_data():
# Automatically traced
return {"data": [1, 2, 3]}

if __name__ == '__main__':
app.run()
```

**Start with APM:**

```bash
DD_SERVICE="test-app" \
DD_ENV="dev" \
DD_VERSION="1.0.0" \
ddtrace-run python app.py
```

**View in UI:**

```
APM → Services → test-app
- Latency (p50, p75, p95, p99)
- Requests/second
- Error rate
- Service dependencies
```

---

## Module 6: Best Practices

### 6.1 Tagging Hierarchy

```yaml
# Level 1: Environment
env:prod / env:preprod / env:dev

# Level 2: Application
appcode:app-001 / appcode:app-002

# Level 3: Service
service:web-frontend / service:api-backend

# Level 4: Instance
datacenter:rdu2 / datacenter:iad2
role:frontend / role:backend
```

**Query Examples:**

```
# All production
env:prod

# MBS application
appcode:app-001

# MBS frontend in RDU2
appcode:app-001 AND service:web-frontend AND datacenter:rdu2
```

---

### 6.2 Baseline Establishment

**Calculate Baselines:**

```python
import pandas as pd

# Get 30 days of historical data
data = get_metrics("system.cpu.user", days=30)

baseline = {
"p50": data.quantile(0.50), # 45%
"p75": data.quantile(0.75), # 62%
"p90": data.quantile(0.90), # 78%
"p95": data.quantile(0.95), # 85%
"p99": data.quantile(0.99)  # 92%
}

# Alert threshold: p95 + 10%
alert_threshold = baseline["p95"] * 1.10 # 93.5%
```

---

### 6.3 Capacity Planning

**Trend Analysis:**

```python
from sklearn.linear_model import LinearRegression

# 90 days historical data
days = np.arange(0, 90).reshape(-1, 1)
cpu_avg = get_daily_avg_cpu(days=90)

model = LinearRegression()
model.fit(days, cpu_avg)

# Predict next 30 days
future = np.arange(90, 120).reshape(-1, 1)
predicted = model.predict(future)

print(f"Current: {cpu_avg[-1]:.1f}%")
print(f"Predicted (30d): {predicted[-1]:.1f}%")

# If predicted > 80%, scale up needed
```

---

## Module 7: Datadog API

### 7.1 Authentication

```bash
export DD_API_KEY="your-api-key"
export DD_APP_KEY="your-app-key"

# Test connectivity
curl -X GET "https://api.datadoghq.com/api/v1/validate" \
-H "DD-API-KEY: ${DD_API_KEY}" \
-H "DD-APPLICATION-KEY: ${DD_APP_KEY}"
```

---

### 7.2 Query Metrics

```bash
# Get CPU metrics (last hour)
curl -X GET "https://api.datadoghq.com/api/v1/query" \
-H "DD-API-KEY: ${DD_API_KEY}" \
-H "DD-APPLICATION-KEY: ${DD_APP_KEY}" \
-d "query=avg:system.cpu.user{service:web-frontend}" \
-d "from=$(date -d '1 hour ago' +%s)" \
-d "to=$(date +%s)"
```

**Python Example:**

```python
import requests
from datetime import datetime, timedelta

def get_cpu_avg(service, hours=24):
url = "https://api.datadoghq.com/api/v1/query"

end = int(datetime.now().timestamp())
start = int((datetime.now() - timedelta(hours=hours)).timestamp())

params = {
"query": f"avg:system.cpu.user{{service:{service}}}",
"from": start,
"to": end
}

headers = {
"DD-API-KEY": DD_API_KEY,
"DD-APPLICATION-KEY": DD_APP_KEY
}

response = requests.get(url, headers=headers, params=params)
data = response.json()

if data['status'] == 'ok' and data['series']:
points = data['series'][0]['points']
avg = sum([p[1] for p in points]) / len(points)
return avg

return None
```

---

## Module 8: Practical Projects

### Project 1: Infrastructure Monitoring Setup

**Objective:** Monitor 30 production servers (Service-A, Service-B, Service-C)

**Tasks:**

1. **Install agents** on all servers
2. **Configure tags:**
```yaml
appcode:app-001/dgit-001/brew-001
env:prod/preprod
service:frontend/backend/database
datacenter:rdu2/iad2
team:ops-team
role:server-role
```
3. **Create universal dashboard** with template variables
4. **Configure alerts:**
- CPU > 80% (5 min)
- Memory > 80% (5 min)
- Disk > 90% (immediate)
- Agent down (1 min)
5. **Setup PostgreSQL monitoring** on database servers

**Expected Outcome:**
- 30/30 servers monitored
- 1 universal dashboard
- 4 critical alerts
- PostgreSQL metrics visible

---

### Project 2: APM Implementation

**Objective:** Monitor Python Flask application performance

**Tasks:**

1. **Install ddtrace:**
```bash
pip install ddtrace
```

2. **Enable APM:**
```bash
DD_SERVICE="api-api" \
DD_ENV="prod" \
DD_VERSION="2.5.1" \
ddtrace-run python app.py
```

3. **Add custom spans:**
```python
@tracer.wrap(resource="user_lookup")
def find_user(user_id):
with tracer.trace("db.query"):
return db.query(User).get(user_id)
```

4. **Set performance baselines:**
- p50: < 100ms
- p95: < 250ms
- p99: < 500ms
- Error rate: < 0.1%

5. **Optimize slow queries** (> 500ms)

**Expected Outcome:**
- Service visible in APM
- Traces collected
- p95 latency < 250ms

---

### Project 3: Automated Reporting

**Objective:** Weekly infrastructure health report

**Python Script:**

```python
#!/usr/bin/env python3
import requests
from datetime import datetime, timedelta

def generate_weekly_report():
services = ['mbs-frontend', 'mbs-backend', 'mbs-database']

report = []
for service in services:
metrics = {
"service": service,
"cpu_avg": get_avg_cpu(service, days=7),
"memory_avg": get_avg_memory(service, days=7),
"uptime": get_uptime(service),
"incidents": get_incident_count(service, days=7)
}
report.append(metrics)

return report

def format_email(report):
html = f"""
<h1>Infrastructure Weekly Report</h1>
<p>Week: {datetime.now().strftime('%Y-W%W')}</p>
<table>
<tr><th>Service</th><th>CPU Avg</th><th>Memory Avg</th><th>Uptime</th><th>Incidents</th></tr>
{''.join([f"<tr><td>{r['service']}</td><td>{r['cpu_avg']:.1f}%</td><td>{r['memory_avg']:.1f} GB</td><td>{r['uptime']:.1f} days</td><td>{r['incidents']}</td></tr>" for r in report])}
</table>
"""
return html

if __name__ == '__main__':
report = generate_weekly_report()
email = format_email(report)
send_email(to="ops-team@company.com", subject="Weekly Report", body=email)
```

**Cron Job:**

```bash
# Every Monday at 9 AM
0 9 * * 1 /usr/bin/python3 /opt/scripts/weekly_report.py
```

---

## Summary

### Skills Acquired

1. **Datadog Architecture** - Agent, Backend, Integrations
2. **Agent Installation** - RHEL, proxy configuration
3. **Metrics Collection** - System, Integration, Custom
4. **Dashboard Creation** - Widgets, queries, variables
5. **Monitoring & Alerts** - Thresholds, notifications
6. **APM** - Distributed tracing, optimization
7. **Best Practices** - Tagging, baselines, capacity planning
8. **API Integration** - Automation, custom tools

### Next Steps

1. **Production Deployment** - Roll out to infrastructure
2. **SLO/SLI Definition** - Service Level Objectives
3. **Incident Response Integration** - Runbooks
4. **Cost Optimization** - Metric filtering, sampling
5. **Advanced APM** - Profiling, code optimization

### Resources

- **Datadog Docs:** https://docs.datadoghq.com/
- **API Reference:** https://docs.datadoghq.com/api/
- **GitHub:** https://github.com/DataDog
- **Community:** https://datadoghq.slack.com

---

**Last Updated:** 2026 
**Version:** 1.0
