---
description: Prometheus quick reference - architecture, service discovery, PromQL, alerting, Kubernetes integration, and production deployment.
---

# Prometheus Complete Quick Reference

**Target Audience:** DevOps Engineers, SREs, Platform Engineers, System Administrators
**Prerequisites:** Basic Linux knowledge, understanding of HTTP, basic YAML, containerization concepts

---

## Learning Path Overview

```
Week 1-2: Foundations → Week 3: PromQL Mastery → Week 4: Advanced Features → Week 5-6: Production & Ecosystem
```

---

## Week 1: Prometheus Foundations

### Day 1-2: Introduction to Monitoring & Prometheus

**Concepts:**
- Why monitoring matters (Observability pillars: metrics, logs, traces)
- Prometheus architecture overview
- Push vs Pull model (why Prometheus uses pull)
- Time-series databases fundamentals
- Prometheus vs other monitoring solutions (Nagios, Zabbix, InfluxDB)

**Hands-On:**
- Install Prometheus locally (binary or Docker)
- Explore the web UI (http://localhost:9090)
- Understand the configuration file structure
- View internal Prometheus metrics

**Resources:**
- [Prometheus Official Docs - Introduction](https://prometheus.io/docs/introduction/overview/)
- [CNCF Prometheus Project](https://www.cncf.io/projects/prometheus/)

**Lab:**
```bash
# Download and run Prometheus
wget https://github.com/prometheus/prometheus/releases/download/v*/prometheus-*.tar.gz
tar xvfz prometheus-*.tar.gz
cd prometheus-*
./prometheus --config.file=prometheus.yml

# Access UI
open http://localhost:9090
```

---

### Day 3-4: Prometheus Architecture Deep Dive

**Concepts:**
- **Prometheus Server:** TSDB, retrieval, HTTP server
- **Exporters:** Applications that expose metrics
- **Pushgateway:** For short-lived jobs
- **Alertmanager:** Alert routing and management
- **Client Libraries:** Instrument your code
- **Service Discovery:** Dynamic target discovery

**Architecture Diagram:**
```

 Prometheus Server 
 
 Retrieval → TSDB → HTTP Server 
 Engine (Storage) (API) 
 

 ↓ scrape ↓ query
 
 Exporters Grafana 
 Targets Dashboards 
 
 ↓ alerts
 
 Alertmanager → Email, Slack, PagerDuty
 
```

**Hands-On:**
- Draw the architecture
- Identify components in your local setup
- Read metrics from Prometheus itself

---

### Day 5-6: Data Model & Metric Types

**Concepts:**
- **Metric:** Measurement with a name and labels
- **Labels:** Key-value pairs for dimensions
- **Samples:** Timestamp + float64 value
- **Notation:** `metric_name{label1="value1", label2="value2"}`

**Metric Types:**
1. **Counter:** Cumulative, only increases (resets on restart)
 - Example: `http_requests_total`, `errors_total`
2. **Gauge:** Current value, can go up/down
 - Example: `temperature_celsius`, `memory_usage_bytes`
3. **Histogram:** Distribution of values in buckets
 - Example: `http_request_duration_seconds`
 - Generates: `_bucket`, `_sum`, `_count`
4. **Summary:** Similar to histogram, with quantiles
 - Example: `rpc_duration_seconds`
 - Generates: `{quantile="0.5"}`, `_sum`, `_count`

**Naming Conventions:**
- Use base unit (seconds, not milliseconds)
- Suffix with unit: `_bytes`, `_seconds`, `_total`
- Use `_total` for counters

**Hands-On:**
- Query different metric types in Prometheus UI
- Understand when to use each type
- Create sample metrics manually

**Example Queries:**
```promql
# View all metrics
up

# Counter
prometheus_http_requests_total

# Gauge
process_resident_memory_bytes

# Histogram
prometheus_http_request_duration_seconds_bucket
```

---

### Day 7: Configuration & Scraping

**Concepts:**
- `prometheus.yml` structure
- Global config, scrape configs, alerting configs
- Scrape interval and timeout
- Static targets vs service discovery
- Relabeling

**Basic Configuration:**
```yaml
global:
 scrape_interval: 15s # How often to scrape targets
 evaluation_interval: 15s # How often to evaluate rules
 external_labels:
 cluster: 'production'
 region: 'us-east-1'

scrape_configs:
 - job_name: 'prometheus'
 static_configs:
 - targets: ['localhost:9090']

 - job_name: 'node_exporter'
 static_configs:
 - targets: ['localhost:9100']
 labels:
 env: 'production'
```

**Hands-On:**
- Modify prometheus.yml
- Add new scrape jobs
- Verify targets in UI (Status → Targets)
- Reload configuration (SIGHUP or API)

**Reload Configuration:**
```bash
# Method 1: SIGHUP
kill -HUP <prometheus-pid>

# Method 2: HTTP API (if --web.enable-lifecycle)
curl -X POST http://localhost:9090/-/reload
```

---

## Week 2: Exporters & Instrumentation

### Day 8-9: Node Exporter

**Concepts:**
- What is node_exporter?
- System metrics collection (CPU, memory, disk, network)
- Collector flags (enable/disable specific collectors)

**Installation:**
```bash
# Download and run
wget https://github.com/prometheus/node_exporter/releases/download/v*/node_exporter-*.tar.gz
tar xvfz node_exporter-*.tar.gz
cd node_exporter-*
./node_exporter

# Verify metrics
curl http://localhost:9100/metrics
```

**Key Metrics:**
```promql
# CPU usage
node_cpu_seconds_total

# Memory
node_memory_MemTotal_bytes
node_memory_MemAvailable_bytes

# Disk
node_filesystem_size_bytes
node_filesystem_free_bytes

# Network
node_network_receive_bytes_total
node_network_transmit_bytes_total
```

**Hands-On:**
- Install node_exporter
- Add it to Prometheus config
- Query node metrics
- Calculate CPU/memory percentages

---

### Day 10-11: Kubernetes Monitoring

**Concepts:**
- **kube-state-metrics:** K8s object state (pods, deployments, etc.)
- **cAdvisor:** Container resource usage (built into kubelet)
- **kubelet:** Node and pod metrics
- Kubernetes service discovery

**Key Exporters:**
1. **kube-state-metrics:** Desired vs actual state
2. **cAdvisor:** Container performance
3. **node_exporter:** Node hardware/OS metrics

**Prometheus in Kubernetes:**
```yaml
# Service discovery example
scrape_configs:
 - job_name: 'kubernetes-pods'
 kubernetes_sd_configs:
 - role: pod
 relabel_configs:
 - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
 action: keep
 regex: true
 - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
 action: replace
 target_label: __metrics_path__
 regex: (.+)
```

**Hands-On:**
- Deploy Prometheus on Kubernetes (Helm chart)
- Explore kube-state-metrics
- Query pod and container metrics
- Understand service discovery

**Key K8s Metrics:**
```promql
# Pod status
kube_pod_status_phase

# Container restarts
kube_pod_container_status_restarts_total

# Resource requests/limits
kube_pod_container_resource_requests
kube_pod_container_resource_limits

# Deployment replicas
kube_deployment_spec_replicas
kube_deployment_status_replicas_available
```

---

### Day 12-13: Application Instrumentation

**Concepts:**
- Prometheus client libraries (Go, Python, Java, etc.)
- Instrumenting your code
- Custom metrics
- Best practices for metric naming

**Python Example:**
```python
from prometheus_client import Counter, Histogram, Gauge, start_http_server
import time

# Metrics
request_count = Counter('http_requests_total', 'Total HTTP requests', ['method', 'endpoint'])
request_duration = Histogram('http_request_duration_seconds', 'HTTP request duration')
active_users = Gauge('active_users', 'Number of active users')

# Instrument your code
@request_duration.time()
def process_request(method, endpoint):
 request_count.labels(method=method, endpoint=endpoint).inc()
 time.sleep(0.1) # Simulate work

# Start metrics server
start_http_server(8000)
```

**Hands-On:**
- Create a simple Python/Go application
- Add Prometheus instrumentation
- Expose metrics on /metrics endpoint
- Scrape from Prometheus

---

### Day 14: Pushgateway

**Concepts:**
- When to use Pushgateway (batch jobs, cron)
- Push vs pull trade-offs
- Metric staleness
- Grouping keys

**Usage Example:**
```bash
# Push metrics
echo "some_metric 3.14" | curl --data-binary @- http://localhost:9091/metrics/job/my_batch_job

# Push with instance label
cat <<EOF | curl --data-binary @- http://localhost:9091/metrics/job/batch/instance/server1
# TYPE task_duration_seconds gauge
task_duration_seconds 42.5
EOF
```

**Hands-On:**
- Run Pushgateway
- Push metrics from a bash script
- Query pushed metrics in Prometheus
- Understand metric lifecycle

---

## Week 3: PromQL Mastery

**Note:** See `PromQL_Learning_Plan_EN.md` for detailed PromQL training.

### Day 15-17: PromQL Fundamentals
- Selectors and matchers
- Range vectors vs instant vectors
- Operators (arithmetic, comparison, logical)
- Functions (rate, irate, increase, etc.)

### Day 18-19: PromQL Aggregations
- sum, avg, max, min, count
- topk, bottomk, quantile
- Aggregation with `by` and `without`

### Day 20-21: PromQL Advanced
- Subqueries
- Prediction functions
- Label manipulation
- Best practices and optimization

---

## Week 4: Advanced Prometheus Features

### Day 22-23: Recording Rules

**Concepts:**
- Pre-compute expensive queries
- Reduce query load
- Create aggregated metrics

**Configuration:**
```yaml
# prometheus.yml
rule_files:
 - "recording_rules.yml"

# recording_rules.yml
groups:
 - name: cpu_rules
 interval: 30s
 rules:
 - record: instance:node_cpu_utilization:rate5m
 expr: |
 100 - (
 avg by (instance) (
 irate(node_cpu_seconds_total{mode="idle"}[5m])
 ) * 100
 )

 - record: job:http_requests:rate5m
 expr: sum(rate(http_requests_total[5m])) by (job)
```

**Hands-On:**
- Create recording rules
- Verify in UI (Status → Rules)
- Query recorded metrics
- Measure performance improvement

---

### Day 24-25: Alerting Rules & Alertmanager

**Alerting Rules:**
```yaml
# alerting_rules.yml
groups:
 - name: instance_alerts
 rules:
 - alert: InstanceDown
 expr: up == 0
 for: 5m
 labels:
 severity: critical
 annotations:
 summary: "Instance {{ $labels.instance }} is down"
 description: "{{ $labels.instance }} has been down for more than 5 minutes."

 - alert: HighMemoryUsage
 expr: |
 (node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes)
 / node_memory_MemTotal_bytes * 100 > 90
 for: 10m
 labels:
 severity: warning
 annotations:
 summary: "High memory usage on {{ $labels.instance }}"
```

**Alertmanager Configuration:**
```yaml
# alertmanager.yml
global:
 resolve_timeout: 5m

route:
 group_by: ['alertname', 'cluster']
 group_wait: 10s
 group_interval: 10s
 repeat_interval: 12h
 receiver: 'default'
 routes:
 - match:
 severity: critical
 receiver: 'pagerduty'
 - match:
 severity: warning
 receiver: 'slack'

receivers:
 - name: 'default'
 email_configs:
 - to: 'team@example.com'

 - name: 'slack'
 slack_configs:
 - api_url: 'https://hooks.slack.com/services/XXX'
 channel: '#alerts'

 - name: 'pagerduty'
 pagerduty_configs:
 - service_key: 'YOUR_SERVICE_KEY'
```

**Hands-On:**
- Create alerting rules
- Setup Alertmanager
- Configure receivers (email, Slack, PagerDuty)
- Test alert firing and resolution
- Implement silences and inhibitions

---

### Day 26-27: Service Discovery

**Concepts:**
- Static configs vs dynamic discovery
- Supported SD mechanisms: Kubernetes, Consul, EC2, Azure, etc.
- Relabeling: filter and transform targets
- Meta labels

**Kubernetes SD Example:**
```yaml
scrape_configs:
 - job_name: 'kubernetes-pods'
 kubernetes_sd_configs:
 - role: pod
 relabel_configs:
 # Only scrape pods with annotation: prometheus.io/scrape=true
 - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
 action: keep
 regex: true

 # Custom metrics path
 - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
 action: replace
 target_label: __metrics_path__
 regex: (.+)

 # Custom port
 - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
 action: replace
 regex: ([^:]+)(?::\d+)?;(\d+)
 replacement: $1:$2
 target_label: __address__

 # Add namespace as label
 - source_labels: [__meta_kubernetes_namespace]
 target_label: kubernetes_namespace
```

**Consul SD Example:**
```yaml
scrape_configs:
 - job_name: 'consul-services'
 consul_sd_configs:
 - server: 'localhost:8500'
 services: ['web', 'api', 'database']
```

**Hands-On:**
- Configure Kubernetes SD
- Use relabeling to filter targets
- Explore meta labels
- Implement custom SD with file_sd_configs

---

### Day 28: Federation & Remote Storage

**Federation:**
Hierarchical Prometheus setup for large-scale monitoring.

```yaml
# Parent Prometheus federating from children
scrape_configs:
 - job_name: 'federate'
 scrape_interval: 15s
 honor_labels: true
 metrics_path: '/federate'
 params:
 'match[]':
 - '{job="prometheus"}'
 - '{__name__=~"job:.*"}'
 static_configs:
 - targets:
 - 'child-prometheus-1:9090'
 - 'child-prometheus-2:9090'
```

**Remote Write/Read:**
```yaml
# Send data to long-term storage
remote_write:
 - url: "http://thanos-receive:19291/api/v1/receive"

remote_read:
 - url: "http://thanos-query:19291/api/v1/read"
```

**Hands-On:**
- Setup federation between two Prometheus instances
- Configure remote write to external storage
- Query federated metrics

---

## Week 5: Production Deployment & Best Practices

### Day 29-30: High Availability & Scaling

**Concepts:**
- Prometheus limitations (single node, no clustering)
- HA setup (multiple identical Prometheus instances)
- Deduplication in queries
- Sharding strategies

**HA Setup:**
```
 
 Prometheus A Prometheus B 
 (replica 1) (replica 2) 
 
 
 
 ↓
 
 Thanos 
 (Querier) 
 
```

**Scaling Strategies:**
1. **Vertical Scaling:** Bigger instance (limited)
2. **Functional Sharding:** Different Prometheus per team/service
3. **Horizontal Sharding:** hashmod relabeling
4. **Long-term Storage:** Thanos, Cortex, M3DB

**Hands-On:**
- Deploy two identical Prometheus instances
- Configure Thanos sidecar
- Query with deduplication
- Implement sharding with hashmod

---

### Day 31-32: Storage & Retention

**Concepts:**
- TSDB internals (blocks, chunks, WAL)
- Retention time vs size
- Storage requirements calculation
- Compaction and downsampling

**Storage Configuration:**
```yaml
# Command-line flags
--storage.tsdb.path=/data
--storage.tsdb.retention.time=15d
--storage.tsdb.retention.size=50GB
```

**Storage Calculation:**
```
needed_disk_space = retention_time_seconds * ingested_samples_per_second * bytes_per_sample

# Example: 10k samples/s, 15 days retention, ~2 bytes/sample compressed
= 15 * 24 * 3600 * 10000 * 2
= ~25 GB
```

**Hands-On:**
- Monitor Prometheus storage metrics
- Configure retention policies
- Analyze TSDB directory structure
- Backup and restore TSDB

---

### Day 33-34: Security & Authentication

**Concepts:**
- TLS encryption
- Basic authentication
- OAuth/OIDC integration
- Network policies
- RBAC in Kubernetes

**TLS Configuration:**
```yaml
# prometheus.yml
global:
 scrape_interval: 15s

scrape_configs:
 - job_name: 'secure-service'
 scheme: https
 tls_config:
 ca_file: /etc/prometheus/ca.crt
 cert_file: /etc/prometheus/client.crt
 key_file: /etc/prometheus/client.key
 basic_auth:
 username: 'prometheus'
 password_file: /etc/prometheus/password
```

**Web Configuration:**
```yaml
# web-config.yml
tls_server_config:
 cert_file: /etc/prometheus/server.crt
 key_file: /etc/prometheus/server.key

basic_auth_users:
 admin: $2y$12$hashed_password
```

**Hands-On:**
- Enable TLS on Prometheus
- Configure basic authentication
- Secure exporters with TLS
- Implement RBAC in Kubernetes

---

### Day 35: Performance Optimization

**Best Practices:**
1. **Cardinality Management:**
 - Avoid high-cardinality labels (user IDs, timestamps)
 - Limit unique label combinations
 - Use recording rules for expensive queries

2. **Query Optimization:**
 - Avoid `count()` without `by()`
 - Use recording rules for dashboards
 - Limit time ranges
 - Use `topk()` instead of sorting all results

3. **Resource Tuning:**
 - Increase memory for large setups
 - Tune scrape intervals
 - Optimize retention settings

**Monitoring Prometheus:**
```promql
# Scrape duration
scrape_duration_seconds

# Samples ingested
prometheus_tsdb_head_samples_appended_total

# Query duration
prometheus_http_request_duration_seconds

# Storage usage
prometheus_tsdb_storage_blocks_bytes
```

**Hands-On:**
- Identify slow queries
- Reduce cardinality
- Create recording rules for dashboards
- Tune Prometheus resource limits

---

## Week 6: Ecosystem & Real-World Implementation

### Day 36-37: Grafana Integration

**Concepts:**
- Prometheus as Grafana datasource
- Dashboard creation
- Variables and templating
- Alerting in Grafana

**Dashboard Best Practices:**
- Use dashboard variables for filtering
- Implement drill-down panels
- Create reusable dashboards
- Follow naming conventions

**Hands-On:**
- Add Prometheus datasource to Grafana
- Create dashboards for:
 - Node metrics
 - Kubernetes cluster overview
 - Application performance
- Setup Grafana alerts
- Export/import dashboards

---

### Day 38-39: Thanos for Long-Term Storage

**Concepts:**
- Thanos architecture
- Components: Sidecar, Store, Query, Compactor, Ruler
- Unlimited retention with object storage
- Global query view

**Thanos Components:**
```

 Prometheus 
 + Sidecar 
 
 
 → Object Storage 
 Prometheus (S3, GCS, etc.) 
 + Sidecar 
 ↑
 
 
 
 
 Store Compactor 
 
 
 
 Query ← Dashboard queries
 
```

**Hands-On:**
- Deploy Thanos components
- Configure object storage backend
- Query across multiple Prometheus instances
- Implement downsampling

---

### Day 40-41: Real-World Scenarios

**Scenario 1: Kubernetes Platform Monitoring**
- Deploy full Prometheus stack (Operator)
- Monitor:
 - Cluster resources
 - Node health
 - Application performance
 - Tekton pipelines (Platform context)

**Scenario 2: Multi-Cluster Monitoring**
- Federation or Thanos
- Centralized alerting
- Cross-cluster queries

**Scenario 3: Application SLO Monitoring**
- Define SLIs (latency, availability, error rate)
- Create SLO dashboards
- Alert on SLO violations

**Hands-On:**
- Implement end-to-end monitoring for a real application
- Create runbooks for alerts
- Optimize for production
- Document monitoring strategy

---

### Day 42: Course Review & Certification Path

**Review:**
- Core concepts recap
- Troubleshooting common issues
- Advanced topics overview

**Certification:**
- No official Prometheus certification (yet)
- Alternative: CNCF Certifications (CKA, CKAD mention Prometheus)
- Contribute to Prometheus project

**Next Steps:**
- Implement Prometheus in production
- Explore Cortex, VictoriaMetrics, Mimir
- Contribute to LUMINO MCP Server (add Prometheus tools)
- Join Prometheus community

---

## Practical Projects

### Project 1: Personal Infrastructure Monitoring
- Setup: Local VM or Raspberry Pi cluster
- Monitor: System metrics, Docker containers
- Dashboard: Grafana with custom panels
- Alerts: Email/Slack notifications

### Project 2: Kubernetes Monitoring Stack
- Deploy: Prometheus Operator
- Monitor: Full cluster (nodes, pods, services)
- Integrate: Tekton pipeline metrics (LUMINO context)
- Alerting: PagerDuty integration

### Project 3: Application Observability
- Instrument: Your own application (Python/Go/Java)
- Expose: Custom business metrics
- Monitor: SLIs/SLOs
- Alert: On SLO violations

### Project 4: Multi-Cloud Monitoring
- Setup: Prometheus on AWS, GCP, Azure
- Federation: Central Thanos deployment
- Query: Unified view across clouds
- Costs: Track cloud resource costs

---

## Troubleshooting Guide

### Common Issues

**1. Targets Down:**
```promql
# Check target status
up{job="your-job"} == 0

# Diagnose
- Network connectivity
- Firewall rules
- Target endpoint health
- Authentication issues
```

**2. High Memory Usage:**
```promql
# Check ingestion rate
rate(prometheus_tsdb_head_samples_appended_total[5m])

# Solutions
- Reduce scrape frequency
- Limit cardinality
- Increase memory
- Implement sharding
```

**3. Slow Queries:**
```promql
# Identify slow queries
topk(10, prometheus_http_request_duration_seconds_sum{handler="/api/v1/query"})

# Solutions
- Use recording rules
- Limit time ranges
- Optimize PromQL
- Add indexes (advanced)
```

**4. Storage Issues:**
```bash
# Check disk usage
df -h /data/prometheus

# Solutions
- Reduce retention
- Clean old blocks
- Expand storage
- Enable remote write
```

---

## Resources

### Official Documentation
- [Prometheus Docs](https://prometheus.io/docs/)
- [PromQL Basics](https://prometheus.io/docs/prometheus/latest/querying/basics/)
- [Best Practices](https://prometheus.io/docs/practices/)

### Books
- "Prometheus: Up & Running" by Brian Brazil
- "Monitoring with Prometheus" by James Turnbull

### Courses
- [Udemy: Prometheus Monitoring](https://www.udemy.com/topic/prometheus/)
- [Pluralsight: Prometheus Deep Dive](https://www.pluralsight.com/)

### Community
- [Prometheus Users Mailing List](https://groups.google.com/forum/#!forum/prometheus-users)
- [CNCF Slack #prometheus](https://slack.cncf.io/)
- [GitHub Discussions](https://github.com/prometheus/prometheus/discussions)

### Tools
- [PromLens](https://promlens.com/) - Query builder
- [Prometheus Operator](https://github.com/prometheus-operator/prometheus-operator)
- [Awesome Prometheus](https://github.com/roaldnefs/awesome-prometheus)

---

## Success Criteria

By completing this learning plan, you should be able to:

- [ ] Explain Prometheus architecture and components
- [ ] Deploy and configure Prometheus in production
- [ ] Write efficient PromQL queries
- [ ] Instrument applications with custom metrics
- [ ] Create recording and alerting rules
- [ ] Configure service discovery for dynamic environments
- [ ] Integrate with Grafana for visualization
- [ ] Implement high availability and long-term storage
- [ ] Optimize Prometheus performance
- [ ] Troubleshoot common issues
- [ ] Monitor Kubernetes/OpenShift clusters
- [ ] Design SLO-based alerting

---

## LUMINO MCP Server Integration

**Relevance to Your Work:**
- LUMINO uses Prometheus for Kubernetes metrics
- PromQL knowledge enhances LUMINO tool development
- Potential new LUMINO tools:
 - `prometheus_advanced_query` - Complex PromQL builder
 - `prometheus_recording_rule_validator` - Validate rules before deployment
 - `prometheus_cardinality_analyzer` - Detect high-cardinality labels
 - `prometheus_slo_calculator` - SLO compliance checker

**Action Items:**
- Integrate Prometheus best practices in LUMINO
- Document Prometheus queries used by LUMINO tools
- Create Prometheus troubleshooting runbooks

---

**Last Updated:** 2026-03-13
**Maintainer:** Documentation Team
**Related:** See `PromQL_Learning_Plan_EN.md` for detailed PromQL training
