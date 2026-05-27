# SignalFx / Splunk Observability Quick Reference

**Comprehensive guide to SignalFx (Splunk Observability Cloud) for the company SRE teams**

---

## Table of Contents

1. [Introduction](#introduction)
2. [SignalFx Architecture](#signalfx-architecture)
3. [Core Concepts](#core-concepts)
4. [Metrics & APM](#metrics--apm)
5. [Dashboards & Charts](#dashboards--charts)
6. [Detectors & Alerts](#detectors--alerts)
7. [Log Observer](#log-observer)
8. [Infrastructure Monitoring](#infrastructure-monitoring)
9. [the company Specific Configuration](#red-hat-specific-configuration)
10. [SignalFx MCP Server](#signalfx-mcp-server)
11. [Practical Examples - the company Use Cases](#practical-examples---red-hat-use-cases)
12. [Best Practices](#best-practices)
13. [Troubleshooting](#troubleshooting)

---

## Introduction

### What is SignalFx?

**SignalFx** (now part of **Splunk Observability Cloud**) is a real-time monitoring and observability platform for cloud-native applications and infrastructure.

**Key Capabilities:**
- **Infrastructure Monitoring** - Metrics from hosts, containers, Kubernetes
- **APM (Application Performance Monitoring)** - Distributed tracing
- **Real User Monitoring (RUM)** - Frontend performance
- **Log Observer** - Centralized log analysis
- **Detectors & Alerts** - Intelligent alerting with analytics

### SignalFx at the company

**the company uses SignalFx for:**
- production platform monitoring
- OPS team infrastructure observability
- Application performance tracking (APM)
- Real-time alerting and incident detection
- Service reliability metrics (SLIs/SLOs)

**Access:**
- **URL:** https://company.signalfx.com (or specific instance URL)
- **Authentication:** the company SSO
- **Teams:** Organized by service/product teams

---

## SignalFx Architecture

### High-Level Components

![Diagram: technologies-monitoring-SignalFx_Splunk_Observability_Learning_Plan-c38a8161](../../images/diagrams/technologies-monitoring-SignalFx_Splunk_Observability_Learning_Plan-c38a8161.png)


### Data Collection Methods

1. **Smart Agent** - SignalFx native agent (legacy)
2. **OpenTelemetry Collector** - Modern, vendor-neutral collector
3. **Cloud Integrations** - AWS, GCP, Azure native metrics
4. **Kubernetes Integration** - Native K8s metrics via agent

---

## Core Concepts

### 1. Metrics

**Metric Types:**
- **Gauge** - Current value at a point in time (e.g., CPU usage)
- **Counter** - Cumulative value (e.g., request count)
- **Cumulative Counter** - Always increasing counter

**Metric Time Series (MTS):**
- Each unique combination of metric name + dimensions = 1 MTS
- Example: `cpu.utilization{host=server1, env=prod}` = 1 MTS

**Dimensions:**
- Key-value pairs that identify metric sources
- Common dimensions: `host`, `service`, `cluster`, `namespace`, `env`

### 2. Detectors

**Purpose:** Monitor metrics and trigger alerts based on conditions

**Detector Types:**
- **Threshold** - Static threshold (e.g., CPU > 80%)
- **Sudden Change** - Detect anomalies
- **Heartbeat** - Check if metrics stopped reporting
- **Resource Running Out** - Predict capacity exhaustion
- **Outlier** - Detect outliers in a group

### 3. Dashboards

**Dashboard Components:**
- **Charts** - Visualizations of metrics
- **Detectors** - Embedded alert status
- **Text Notes** - Documentation
- **Event Overlays** - Deployments, incidents

**Dashboard Groups:**
- Organize related dashboards
- Typical structure: Service → Component → Detail

### 4. Teams

**Team Organization:**
- Teams control access to dashboards, detectors, integrations
- Notifications sent to team members
- Common the company teams: production, OPS, Service-C, etc.

---

## Metrics & APM

### Infrastructure Metrics

**Host Metrics:**
```
cpu.utilization      # CPU usage percentage
memory.utilization     # Memory usage percentage
disk.utilization      # Disk usage percentage
network.total       # Network throughput
```

**Kubernetes Metrics:**
```
kubernetes.pod_phase        # Pod status
kubernetes.container_cpu_limit   # CPU limits
kubernetes.container_memory_limit  # Memory limits
kubernetes.deployment_available   # Available replicas
```

**Application Metrics (Custom):**
```
http.requests        # Request count
http.request.duration    # Request latency
http.errors         # Error count
cache.hits         # Cache hit rate
```

### APM (Distributed Tracing)

**Trace Components:**
- **Trace** - End-to-end request flow
- **Span** - Single operation in trace
- **Service Map** - Visual dependency graph

**Instrumenting Applications:**

**Java (OpenTelemetry):**
```java
// Add to JVM arguments
-javaagent:/path/to/splunk-otel-javaagent.jar
-Dsplunk.access.token=YOUR_TOKEN
-Dsplunk.realm=YOUR_REALM
-Dotel.service.name=my-service
-Dotel.resource.attributes=deployment.environment=production
```

**Python (OpenTelemetry):**
```python
from opentelemetry import trace
from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

trace.set_tracer_provider(TracerProvider())
otlp_exporter = OTLPSpanExporter(endpoint="https://ingest.YOUR_REALM.signalfx.com")
trace.get_tracer_provider().add_span_processor(BatchSpanProcessor(otlp_exporter))
```

**Viewing Traces:**
1. Navigate to **APM** → **Traces**
2. Search by service, operation, tags
3. Click trace to see waterfall view
4. Analyze latency, errors, dependencies

---

## Dashboards & Charts

### Creating Dashboards

**Step 1: Create Dashboard**
1. Navigate to **Dashboards** → **Create Dashboard**
2. Name: `[Service] - [Component] Overview`
3. Add to Dashboard Group

**Step 2: Add Charts**

**Chart Types:**
- **Line Chart** - Time series trends
- **Single Value** - Current metric value
- **Heatmap** - Distribution over time
- **Table** - Metric comparison
- **Event Feed** - Event timeline

### Chart Configuration

**Basic Line Chart Example:**

**Metric:** `cpu.utilization`

**Filters (Dimensions):**
```
cluster = production-production
namespace = production-system
```

**Plot:**
- **Signal:** `cpu.utilization`
- **Aggregation:** `Mean` (or `Max`, `P95`)
- **Group By:** `host` or `pod_name`

**Analytics:**
- **Rollup:** `Average` over `1 minute`
- **Display:** Line chart

### SignalFlow (Advanced)

**SignalFlow Language:** For complex analytics

**Example: Error Rate Calculation**
```python
A = data('http.requests', filter=filter('service', 'my-service')).sum()
B = data('http.errors', filter=filter('service', 'my-service')).sum()
C = (B / A) * 100
C.publish(label='Error Rate %')
```

**Example: P95 Latency**
```python
data('http.request.duration', rollup='rate').percentile(pct=95).publish(label='P95 Latency')
```

---

## Detectors & Alerts

### Creating Detectors

**Step 1: Navigate to Detectors**
1. **Alerts** → **Detectors** → **New Detector**

**Step 2: Choose Detector Type**

**Static Threshold Example:**

**Metric:** `cpu.utilization`
**Condition:** `When cpu.utilization > 80%`
**Filters:** `cluster = production-production`
**Duration:** `For at least 5 minutes`
**Severity:** `Critical`

**Step 3: Configure Notifications**

**Recipients:**
- Team: `production SRE`
- Integration: Slack, PagerDuty, Email

**Message Template:**
```
{{ruleName}}
Service: {{dimensions.service}}
Host: {{dimensions.host}}
Value: {{inputs.A.value}} (threshold: {{ruleSeverity}})
Dashboard: {{detectorUrl}}
```

### Detector Best Practices

**1. Use Meaningful Names**
```
Good: "production - API Server CPU High"
Bad: "Detector 42"
```

**2. Add Context in Detector Description**
```
This detector monitors production API server CPU usage.
Threshold: 80% for 5 minutes
Runbook: https://wiki.example.com/production-cpu-high
```

**3. Set Appropriate Durations**
- **Transient spikes:** 5-10 minutes
- **Sustained issues:** 15-30 minutes
- **Capacity planning:** 1+ hour

**4. Use Auto-Clear**
Enable auto-clear when metric drops below threshold for X minutes

---

## Log Observer

### Accessing Logs

**Navigate:** **Log Observer** → **Logs**

**Log Sources:**
- Kubernetes pod logs (via Fluentd/Fluent Bit)
- Application logs (via OpenTelemetry)
- Infrastructure logs (syslog, journald)

### Log Search

**Basic Search:**
```
kubernetes.namespace_name:production-system AND error
```

**Field Filters:**
- `kubernetes.pod_name`
- `kubernetes.namespace_name`
- `severity` (INFO, WARN, ERROR)
- `message` (log content)

**Time Range:** Last 15 minutes, 1 hour, 1 day, custom

### Log Aggregation

**Count by Field:**
```
Group by: kubernetes.pod_name
Aggregation: Count
```

**Result:** Number of log entries per pod

**Visualize:**
- Bar chart
- Table
- Timeline

### Log-to-Trace Correlation

**If application uses OpenTelemetry:**
- Click log entry
- See related trace ID
- Jump to trace view

**Benefit:** Full context from log → trace → metrics

---

## Infrastructure Monitoring

### Kubernetes Monitoring

**SignalFx Navigator:**
1. **Infrastructure** → **Kubernetes Navigator**
2. Select cluster, namespace, or pod
3. View real-time metrics and health

**Key Metrics:**
- **Pod Phase:** Running, Pending, Failed
- **Container Restarts:** Restart count
- **CPU/Memory Utilization:** Resource usage
- **Network I/O:** Traffic patterns

**Example: Find Pods with High Restart Count**
1. Navigate to Kubernetes Navigator
2. Filter: `kubernetes.container_restart_count > 5`
3. View affected pods

### Host Monitoring

**Host List View:**
1. **Infrastructure** → **Hosts**
2. Filter by tag: `env:production`
3. Sort by CPU, memory, or disk usage

**Drill-Down:**
- Click host → View detailed metrics
- See running processes
- Check disk I/O, network traffic

---

## the company Specific Configuration

### production Monitoring Setup

**Namespace Monitoring:**
- Each production namespace reports metrics
- Standard dimensions: `cluster`, `namespace`, `service`

**Key Services Monitored:**
- `production-api-server`
- `production-pipeline-service`
- `production-results-api`
- `rhdh` (the company Developer Hub)

**Dashboard Hierarchy:**
```
production Dashboard Group
├── production - Platform Overview
├── production - Pipeline Performance
├── production - API Server Health
└── production - Database Metrics
```

### OPS Team Dashboards

**Common OPS Dashboards:**
- **Service-C Infrastructure** - Build system monitoring
- **Dist-Git Health** - Git repository metrics
- **Service-A (Module Build Service)** - Module builds
- **RHSM-Pulp** - Subscription management

**Access Control:**
- Team: `OPS`
- Members: OPS engineers
- Shared: Read-only access for broader teams

### Custom Metrics for the company Services

**Example: Tekton Pipeline Metrics**

**Metrics sent via OpenTelemetry:**
```
tekton.pipelinerun.duration     # Pipeline execution time
tekton.pipelinerun.success.count   # Successful runs
tekton.pipelinerun.failure.count   # Failed runs
tekton.taskrun.duration       # Task execution time
```

**Detector Example:**
```
Alert when: tekton.pipelinerun.failure.count > 10
Duration: 15 minutes
Team: production SRE
```

---

## SignalFx MCP Server

### Overview

**SignalFx MCP Server** (if available at the company) provides programmatic access to SignalFx data via Model Context Protocol.

**Capabilities:**
- Query metrics via MCP tools
- Retrieve detector status
- Get dashboard URLs
- Search logs programmatically

### Example MCP Tools (Hypothetical)

**Tool 1: Query Metric**
```python
mcp.call("signalfx.query_metric",
metric="cpu.utilization",
filters={"cluster": "production-production"},
timerange="1h")
```

**Tool 2: Get Detector Status**
```python
mcp.call("signalfx.get_detector_status",
detector_name="production - API Server CPU High")
```

**Tool 3: Search Logs**
```python
mcp.call("signalfx.search_logs",
query="kubernetes.namespace_name:production-system AND error",
timerange="15m")
```

### Using SignalFx MCP with Claude Code

**Example Investigation:**

**User:** "Check if there are CPU issues in production production cluster"

**Claude (using SignalFx MCP):**
```python
# Query CPU metrics
cpu_data = mcp.call("signalfx.query_metric",
metric="cpu.utilization",
filters={"cluster": "production-production", "namespace": "production-system"},
timerange="1h")

# Check active alerts
alerts = mcp.call("signalfx.get_active_alerts",
filters={"cluster": "production-production"})

# Search for error logs
logs = mcp.call("signalfx.search_logs",
query="kubernetes.namespace_name:production-system AND cpu",
timerange="30m")
```

**Result:** Integrated view of metrics, alerts, and logs without leaving Claude Code.

---

## Practical Examples - the company Use Cases

### Example 1: production Pipeline Performance Dashboard

**Use Case:** Monitor Tekton pipeline performance in production

**Metrics to Track:**
1. **Pipeline Duration** - `tekton.pipelinerun.duration`
2. **Success Rate** - `(success_count / total_count) * 100`
3. **Queue Time** - Time from creation to start
4. **Task Failure Rate** - Failed tasks by type

**Dashboard Layout:**

**Row 1: Overview**
- Single Value: Total Pipelines (last 24h)
- Single Value: Success Rate %
- Single Value: Avg Duration

**Row 2: Trends**
- Line Chart: Pipeline duration over time (P50, P95, P99)
- Line Chart: Pipeline count by status (success, failure)

**Row 3: Details**
- Table: Top 10 slowest pipelines
- Heatmap: Task execution times

**Detector:**
```
Alert when:
- Pipeline success rate < 95% for 30 minutes
- P95 duration > 20 minutes for 30 minutes
```

---

### Example 2: OPS Service-C Infrastructure Monitoring

**Use Case:** Monitor Service-C build system health

**Key Metrics:**
1. **Builder Availability** - `service-c.builder.available`
2. **Build Queue Depth** - `service-c.queue.depth`
3. **Build Success Rate** - `service-c.build.success_rate`
4. **Hub Responsiveness** - `service-c.hub.response_time`

**Dashboard:**

**Row 1: System Health**
- Single Value: Available Builders
- Single Value: Queue Depth
- Line Chart: Queue depth trend

**Row 2: Build Performance**
- Line Chart: Builds per hour
- Line Chart: Success rate %
- Heatmap: Build duration by architecture

**Detector:**
```
Alert when:
- Available builders < 10 for 10 minutes (Critical)
- Queue depth > 100 for 30 minutes (Warning)
- Success rate < 90% for 1 hour (Critical)
```

---

### Example 3: Incident Response Dashboard

**Use Case:** Quick incident overview for on-call engineers

**Dashboard Purpose:** Single pane of glass during incidents

**Sections:**

**1. Active Alerts**
- List of all firing detectors
- Grouped by severity (Critical, Major, Minor)

**2. Service Health**
- production API availability
- Database connection pool
- Message queue depth

**3. Error Rates**
- HTTP 5xx errors by service
- Application exceptions
- Pipeline failures

**4. Resource Saturation**
- CPU utilization (all clusters)
- Memory utilization
- Disk usage

**5. Recent Deployments**
- Event feed: Last 1 hour deployments
- Correlation: Issues after deploy?

**Usage During Incident:**
1. On-call receives PagerDuty alert
2. Open Incident Response dashboard
3. Identify affected services
4. Correlate with recent changes
5. Drill down to specific service dashboard

---

### Example 4: Capacity Planning Report

**Use Case:** Forecast resource needs for next quarter

**Metrics:**
- CPU utilization trends (last 90 days)
- Memory utilization trends
- Disk usage growth rate
- Network bandwidth usage

**Dashboard:**

**Charts:**
1. **CPU Trend** - Historical + linear projection
2. **Memory Trend** - Current usage + forecast
3. **Disk Growth** - GB per day growth rate
4. **Top Resource Consumers** - Which services use most?

**Analytics (SignalFlow):**
```python
# Linear projection of disk usage
A = data('disk.utilization').mean(by=['host']).sum()
B = A.timeshift('7d') # Compare to 1 week ago
growth_rate = (A - B) / 7 # Daily growth
forecast_30d = A + (growth_rate * 30)
forecast_30d.publish(label='Disk Usage Forecast (30 days)')
```

**Detector:**
```
Alert when:
- Forecasted disk usage > 90% in next 30 days (Warning)
- Forecasted memory usage > 85% in next 30 days (Warning)
```

---

## Best Practices

### 1. Dashboard Organization

**Naming Convention:**
```
[Team/Service] - [Component] - [Purpose]

Examples:
- production - API Server - Overview
- OPS - Service-C - Build Performance
- RHDH - PostgreSQL - Database Health
```

**Dashboard Groups:**
- Group related dashboards
- Hierarchy: Service → Component → Detail

### 2. Metric Naming

**Use Consistent Naming:**
```
Good:
- http.requests.count
- http.requests.duration
- http.requests.errors

Bad:
- requests
- request_time
- http_errors
```

**Include Units in Descriptions:**
- `cpu.utilization` - Percentage (0-100)
- `memory.used` - Bytes
- `http.request.duration` - Milliseconds

### 3. Detector Configuration

**Severity Levels:**
- **Critical** - Immediate impact, requires action
- **Major** - Significant impact, action needed soon
- **Minor** - Low impact, informational
- **Warning** - Potential issue, monitor

**Avoid Alert Fatigue:**
- Set appropriate thresholds
- Use auto-clear
- Aggregate similar alerts
- Mute known issues during maintenance

### 4. Dimension Strategy

**Common Dimensions:**
- `env` - production, staging, dev
- `cluster` - kubernetes cluster name
- `namespace` - kubernetes namespace
- `service` - application service name
- `version` - deployment version

**Use for:**
- Filtering (show only production)
- Grouping (compare across environments)
- Alerting (alert only for production)

### 5. Team Access Control

**Principle of Least Privilege:**
- Teams own their dashboards/detectors
- Read-only access for cross-team visibility
- Admin access limited to team leads

### 6. Documentation

**Add Context to Dashboards:**
- Text note at top: Purpose, owners, runbook links
- Chart descriptions: What metric means
- Detector runbooks: What to do when alert fires

**Example Dashboard Note:**
```
Purpose: Monitor production pipeline performance
Owner: production SRE Team (@production-sre)
Runbook: https://wiki.company.internal/production-pipeline-alerts
Updated: 2026-03-19
```

---

## Troubleshooting

### Issue 1: Missing Metrics

**Symptoms:**
- Dashboard shows "No data"
- Metric not appearing in search

**Debugging:**

1. **Check Data Source:**
```
# Verify agent is running
kubectl get pods -n signalfx-agent

# Check agent logs
kubectl logs -n signalfx-agent signalfx-agent-xyz
```

2. **Check Metric Name:**
- Navigate to **Metrics Finder**
- Search for metric pattern
- Verify exact name and dimensions

3. **Check Filters:**
- Are filters too restrictive?
- Remove filters one by one
- Check dimension values exist

**Solution:**
- Restart agent if not sending data
- Correct metric name in query
- Adjust filters to match actual dimensions

---

### Issue 2: Noisy Alerts

**Symptoms:**
- Alert firing frequently
- False positives

**Debugging:**

1. **Review Detector Conditions:**
- Is threshold too sensitive?
- Is duration too short?

2. **Check Metric Behavior:**
- View metric chart over 7 days
- Identify normal fluctuations
- Adjust threshold above noise level

**Solutions:**
- Increase threshold (e.g., 80% → 85%)
- Increase duration (5 min → 15 min)
- Use analytics (e.g., detect sudden change, not static threshold)
- Mute during known maintenance windows

---

### Issue 3: Dashboard Slow to Load

**Symptoms:**
- Dashboard takes >10 seconds to load
- Timeout errors

**Debugging:**

1. **Check Chart Complexity:**
- How many time series (MTS)?
- Long time range (7 days vs. 1 hour)?

2. **Optimize Charts:**
- Reduce time range for detail charts
- Use rollups (aggregate to 1-minute intervals)
- Limit number of dimensions in `Group By`

**Example Optimization:**

**Before:**
```
Metric: cpu.utilization
Group By: host, pod_name, container_name
Time Range: 7 days
MTS: 10,000+
```

**After:**
```
Metric: cpu.utilization
Group By: pod_name (only)
Rollup: Mean over 5 minutes
Time Range: 24 hours
MTS: 500
```

**Result:** Faster load time

---

### Issue 4: Can't Find Dashboard

**Symptoms:**
- Dashboard existed, now missing
- No access to team dashboard

**Debugging:**

1. **Check Team Membership:**
- Are you member of correct team?
- Contact team admin to add you

2. **Search by Name:**
- Use global search (top right)
- Search for dashboard keywords

3. **Check Dashboard Group:**
- Navigate to Dashboard Groups
- Find correct group
- Dashboard may be moved

**Solution:**
- Request team access
- Bookmark important dashboards
- Use Dashboard Groups for organization

---

## the company Specific Tips

### 1. Finding Your Team's Dashboards

**Common the company Teams:**
- production SRE
- OPS Team
- Service-C Infrastructure
- RHDH (the company Developer Hub)

**Navigate:**
1. **Dashboards** → **Dashboard Groups**
2. Find your team's group
3. Explore dashboards

### 2. production Metrics Naming Convention

**Pattern:** `production.<component>.<metric>`

**Examples:**
```
production.api.requests.count
production.pipeline.duration
production.database.connections
production.cache.hit_rate
```

### 3. OPS Infrastructure Conventions

**Pattern:** `<service>.<component>.<metric>`

**Examples:**
```
service-c.builder.available
service-c.hub.response_time
distgit.git.clone_duration
service-a.build.queue_depth
```

### 4. Integration with Internal Tools

**Common Integrations:**
- **PagerDuty** - Critical alerts
- **Slack** - Team notifications (#production-alerts, #ops-alerts)
- **Jira** - Auto-create tickets for sustained issues
- **Rover** - Service dependency tracking

### 5. Accessing SignalFx API

**API Endpoint:** `https://api.YOUR_REALM.signalfx.com`

**Authentication:** API Token (from Profile → Access Tokens)

**Example: Query Metric via API**
```bash
curl -X POST "https://api.us1.signalfx.com/v2/signalflow/execute" \
-H "X-SF-Token: YOUR_API_TOKEN" \
-H "Content-Type: application/json" \
-d '{
"program": "data(\"cpu.utilization\").mean().publish()",
"start": "-1h",
"stop": "Now"
}'
```

---

## Summary

### Key Takeaways

1. **SignalFx = Real-Time Observability**
- Metrics, traces, logs in one platform
- Powerful analytics and alerting

2. **the company Usage**
- production, OPS, and other teams monitor critical services
- Dashboards organized by team/service
- Integrated with PagerDuty, Slack, Jira

3. **Essential Skills**
- Navigate dashboards and charts
- Create/modify detectors
- Search logs effectively
- Understand the company-specific metrics

4. **SignalFx MCP Server**
- Programmatic access to SignalFx data
- Integrate with Claude Code for investigations
- Combine with CLI tools for comprehensive analysis

### Next Steps

**Beginner:**
1. Log into SignalFx (https://company.signalfx.com)
2. Explore your team's dashboards
3. Create a simple chart (CPU utilization)
4. Subscribe to team notifications

**Intermediate:**
5. Create a custom dashboard for your service
6. Set up a detector with alert
7. Use SignalFlow for analytics
8. Integrate logs with metrics

**Advanced:**
9. Build automated runbooks using SignalFx MCP
10. Implement SLI/SLO tracking
11. Create capacity planning dashboards
12. Train team on SignalFx best practices

---

## References

- **Splunk Observability Docs:** https://docs.splunk.com/Observability
- **SignalFx API Reference:** https://dev.splunk.com/observability/reference
- **OpenTelemetry Docs:** https://opentelemetry.io/docs/
- **the company Internal Wiki:** [Internal link to the company SignalFx documentation]
- **SignalFx MCP Server:** [Link to the company internal MCP server repo/docs]

---

**Last Updated:** 2026-03-19
**Author:** Infrastructure Team (the company OPS Team)
**Audience:** the company SRE teams
**License:** Internal the company use
