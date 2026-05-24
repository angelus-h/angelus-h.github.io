# OpenTelemetry Collector Configuration Guide

**Comprehensive guide to configuring and deploying OpenTelemetry Collector for metrics, traces, and logs.**

---

## Table of Contents

1. [Introduction](#introduction)
2. [Architecture Overview](#architecture-overview)
3. [Core Components](#core-components)
4. [Deployment Modes](#deployment-modes)
5. [Configuration Structure](#configuration-structure)
6. [Receivers](#receivers)
7. [Processors](#processors)
8. [Exporters](#exporters)
9. [Extensions](#extensions)
10. [Complete Examples](#complete-examples)
11. [Kubernetes Deployment](#kubernetes-deployment)
12. [Best Practices](#best-practices)
13. [Troubleshooting](#troubleshooting)

---

## Introduction

### What is OpenTelemetry Collector?

**OpenTelemetry Collector** is a vendor-agnostic proxy that can:
- **Receive** telemetry data (metrics, traces, logs)
- **Process** and transform the data
- **Export** to one or more backends (Prometheus, Jaeger, Datadog, etc.)

### Why Use OTel Collector?

**Benefits:**
- **Vendor-agnostic** - Switch backends without changing instrumentation
- **Centralized configuration** - Manage telemetry pipelines in one place
- **Buffering & retry** - Handle backend outages gracefully
- **Data transformation** - Filter, enrich, and batch telemetry
- **Multi-backend support** - Send data to multiple destinations

### Use Cases

1. **Agent Mode** - Deployed alongside applications (DaemonSet in K8s)
2. **Gateway Mode** - Centralized collector for large-scale deployments
3. **Hybrid Mode** - Agent → Gateway → Backends

---

## Architecture Overview

```

 OpenTelemetry Collector 
 
 
 Receivers > Processors > Exporters 
 
 
 
 Extensions (optional) 
 - Health Check, pprof, zpages, etc. 
 


Data Flow: Receivers → Processors → Exporters
```

**Key Concepts:**
- **Receivers** - Entry points for telemetry data
- **Processors** - Transform, filter, batch data in pipelines
- **Exporters** - Send data to backends (Prometheus, Jaeger, etc.)
- **Pipelines** - Connect receivers → processors → exporters
- **Extensions** - Additional services (health checks, pprof)

---

## Core Components

### 1. Receivers

Receivers collect telemetry data from various sources.

**Common Receivers:**

| Receiver | Purpose | Protocol |
|----------|---------|----------|
| `otlp` | OpenTelemetry native format | gRPC, HTTP |
| `prometheus` | Scrape Prometheus metrics | HTTP/Pull |
| `jaeger` | Receive Jaeger traces | gRPC, Thrift |
| `zipkin` | Receive Zipkin traces | HTTP |
| `hostmetrics` | Collect host metrics (CPU, memory) | - |
| `k8s_cluster` | Kubernetes cluster metrics | K8s API |
| `filelog` | Tail log files | - |

### 2. Processors

Processors modify telemetry data in pipelines.

**Common Processors:**

| Processor | Purpose |
|-----------|---------|
| `batch` | Batch data before export (reduces network overhead) |
| `memory_limiter` | Prevent OOM by limiting memory usage |
| `attributes` | Add/remove/update attributes |
| `filter` | Drop unwanted data based on rules |
| `resource` | Add resource attributes (cluster, namespace) |
| `k8sattributes` | Enrich with Kubernetes metadata |
| `tail_sampling` | Sample traces based on criteria |

### 3. Exporters

Exporters send telemetry to backends.

**Common Exporters:**

| Exporter | Backend | Data Types |
|----------|---------|------------|
| `otlp` | OTLP-compatible backends | Metrics, Traces, Logs |
| `prometheus` | Prometheus | Metrics |
| `prometheusremotewrite` | Prometheus remote write | Metrics |
| `jaeger` | Jaeger | Traces |
| `zipkin` | Zipkin | Traces |
| `loki` | Grafana Loki | Logs |
| `datadog` | Datadog | Metrics, Traces, Logs |
| `logging` | stdout (debugging) | Metrics, Traces, Logs |

### 4. Extensions

Extensions provide additional services.

**Common Extensions:**

| Extension | Purpose |
|-----------|---------|
| `health_check` | HTTP endpoint for health checks |
| `pprof` | Golang profiling endpoint |
| `zpages` | Debug pages (pipelines, traces) |
| `memory_ballast` | Reduce GC pressure |

---

## Deployment Modes

### Agent Mode

**Purpose:** Deployed on each host/node to collect local telemetry.

**Characteristics:**
- **Deployment:** DaemonSet (K8s) or per-host agent
- **Scope:** Node-level metrics, application traces/logs
- **Resources:** Low memory/CPU footprint
- **Use Case:** Collect and forward to gateway or backend

**Example Use Cases:**
- Collect host metrics (CPU, memory, disk)
- Receive application traces via OTLP
- Forward to central gateway

### Gateway Mode

**Purpose:** Centralized collector for aggregation and processing.

**Characteristics:**
- **Deployment:** Deployment/StatefulSet (K8s) with multiple replicas
- **Scope:** Cluster-wide or cross-cluster telemetry
- **Resources:** Higher memory/CPU (buffering, batching)
- **Use Case:** Aggregate data from agents, process, export to backends

**Example Use Cases:**
- Aggregate traces from multiple agents
- Apply tail sampling for high-volume traces
- Export to multiple backends (Jaeger, Datadog, S3)

### Hybrid Mode

**Architecture:**

```
Application → OTel Agent (DaemonSet) → OTel Gateway → Backends
 (Collect, enrich) (Aggregate, sample, export)
```

**Benefits:**
- **Agents** handle local collection and resource enrichment
- **Gateway** performs expensive operations (tail sampling, aggregation)
- **Scalability** - Add gateway replicas as needed

---

## Configuration Structure

### Basic YAML Structure

```yaml
# OpenTelemetry Collector Configuration
receivers:
 # Define data sources
 otlp:
 protocols:
 grpc:
 http:

processors:
 # Define data transformations
 batch:

exporters:
 # Define backends
 prometheus:
 endpoint: "0.0.0.0:8889"

extensions:
 # Additional services
 health_check:

service:
 extensions: [health_check]
 pipelines:
 metrics:
 receivers: [otlp]
 processors: [batch]
 exporters: [prometheus]
```

### Service Section

The `service` section defines:
- **Extensions** to enable
- **Pipelines** connecting receivers → processors → exporters

**Pipeline Types:**
- `metrics` - Metrics pipeline
- `traces` - Traces pipeline
- `logs` - Logs pipeline

**Example:**

```yaml
service:
 extensions: [health_check, pprof]
 pipelines:
 metrics:
 receivers: [otlp, prometheus]
 processors: [batch, memory_limiter]
 exporters: [prometheusremotewrite]
 traces:
 receivers: [otlp, jaeger]
 processors: [batch, tail_sampling]
 exporters: [jaeger, datadog]
```

---

## Receivers

### OTLP Receiver

**Purpose:** Receive OpenTelemetry native format (metrics, traces, logs).

**Configuration:**

```yaml
receivers:
 otlp:
 protocols:
 grpc:
 endpoint: 0.0.0.0:4317
 http:
 endpoint: 0.0.0.0:4318
```

**Use Case:** Applications instrumented with OpenTelemetry SDKs.

### Prometheus Receiver

**Purpose:** Scrape Prometheus metrics from targets.

**Configuration:**

```yaml
receivers:
 prometheus:
 config:
 scrape_configs:
 - job_name: 'otel-collector'
 scrape_interval: 10s
 static_configs:
 - targets: ['localhost:8888']
 - job_name: 'kubernetes-pods'
 kubernetes_sd_configs:
 - role: pod
 relabel_configs:
 - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
 action: keep
 regex: true
```

**Use Case:** Collect metrics from Prometheus exporters or annotated pods.

### Jaeger Receiver

**Purpose:** Receive Jaeger traces.

**Configuration:**

```yaml
receivers:
 jaeger:
 protocols:
 grpc:
 endpoint: 0.0.0.0:14250
 thrift_http:
 endpoint: 0.0.0.0:14268
```

**Use Case:** Migrate from Jaeger to OTel without changing instrumentation.

### Host Metrics Receiver

**Purpose:** Collect host-level metrics (CPU, memory, disk, network).

**Configuration:**

```yaml
receivers:
 hostmetrics:
 collection_interval: 10s
 scrapers:
 cpu:
 memory:
 disk:
 filesystem:
 network:
 load:
 paging:
 processes:
```

**Use Case:** Monitor node health in Kubernetes DaemonSet deployments.

### Kubernetes Cluster Receiver

**Purpose:** Collect Kubernetes cluster metrics.

**Configuration:**

```yaml
receivers:
 k8s_cluster:
 auth_type: serviceAccount
 node_conditions_to_report: [Ready, MemoryPressure, DiskPressure]
 allocatable_types_to_report: [cpu, memory, storage]
```

**Use Case:** Collect cluster-level metrics (node status, pod count, resource usage).

### Filelog Receiver

**Purpose:** Tail log files and parse them.

**Configuration:**

```yaml
receivers:
 filelog:
 include:
 - /var/log/myapp/*.log
 operators:
 - type: regex_parser
 regex: '^(?P<timestamp>\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}) (?P<level>\w+) (?P<message>.*)$'
 timestamp:
 parse_from: attributes.timestamp
 layout: '%Y-%m-%d %H:%M:%S'
```

**Use Case:** Ingest application logs from files.

---

## Processors

### Batch Processor

**Purpose:** Batch telemetry data to reduce network overhead.

**Configuration:**

```yaml
processors:
 batch:
 timeout: 10s
 send_batch_size: 1000
 send_batch_max_size: 1500
```

**Recommendation:** **Always use batch processor** for production deployments.

### Memory Limiter Processor

**Purpose:** Prevent OOM by limiting memory usage.

**Configuration:**

```yaml
processors:
 memory_limiter:
 check_interval: 1s
 limit_mib: 512
 spike_limit_mib: 128
```

**Recommendation:** **Place first in processor chain** to protect collector.

### Attributes Processor

**Purpose:** Add, update, or delete attributes.

**Configuration:**

```yaml
processors:
 attributes:
 actions:
 - key: environment
 value: production
 action: insert
 - key: cluster_name
 value: platform-prod
 action: upsert
 - key: internal_ip
 action: delete
```

**Use Case:** Add cluster/environment metadata to all telemetry.

### Resource Processor

**Purpose:** Add resource-level attributes.

**Configuration:**

```yaml
processors:
 resource:
 attributes:
 - key: cluster.name
 value: platform-production
 action: insert
 - key: cloud.provider
 value: aws
 action: insert
```

**Use Case:** Tag all telemetry with cluster/cloud information.

### K8s Attributes Processor

**Purpose:** Enrich telemetry with Kubernetes metadata.

**Configuration:**

```yaml
processors:
 k8sattributes:
 auth_type: serviceAccount
 passthrough: false
 extract:
 metadata:
 - k8s.namespace.name
 - k8s.pod.name
 - k8s.pod.uid
 - k8s.deployment.name
 - k8s.node.name
 labels:
 - tag_name: app
 key: app.kubernetes.io/name
 from: pod
```

**Use Case:** Add pod/namespace/deployment metadata to traces and metrics.

### Filter Processor

**Purpose:** Drop unwanted telemetry based on rules.

**Configuration:**

```yaml
processors:
 filter/drop_health_checks:
 metrics:
 metric:
 - 'metric.name == "http.server.duration" and attributes["http.route"] == "/health"'
```

**Use Case:** Drop health check metrics to reduce cardinality.

### Tail Sampling Processor (Traces)

**Purpose:** Sample traces based on attributes or policies.

**Configuration:**

```yaml
processors:
 tail_sampling:
 decision_wait: 10s
 num_traces: 100
 policies:
 - name: errors
 type: status_code
 status_code:
 status_codes: [ERROR]
 - name: slow_requests
 type: latency
 latency:
 threshold_ms: 1000
 - name: probabilistic
 type: probabilistic
 probabilistic:
 sampling_percentage: 10
```

**Use Case:** Keep all error traces, sample 10% of successful traces.

---

## Exporters

### Prometheus Exporter

**Purpose:** Expose metrics in Prometheus format.

**Configuration:**

```yaml
exporters:
 prometheus:
 endpoint: "0.0.0.0:8889"
 namespace: otel
 const_labels:
 cluster: platform-prod
```

**Use Case:** Prometheus scrapes metrics from collector's `/metrics` endpoint.

### Prometheus Remote Write Exporter

**Purpose:** Push metrics to Prometheus remote write endpoint.

**Configuration:**

```yaml
exporters:
 prometheusremotewrite:
 endpoint: "https://prometheus.example.com/api/v1/write"
 headers:
 Authorization: "Bearer ${PROMETHEUS_TOKEN}"
 resource_to_telemetry_conversion:
 enabled: true
```

**Use Case:** Send metrics to Prometheus, Thanos, Cortex, or Mimir.

### OTLP Exporter

**Purpose:** Send data to OTLP-compatible backends.

**Configuration:**

```yaml
exporters:
 otlp:
 endpoint: "jaeger-collector:4317"
 tls:
 insecure: false
 cert_file: /etc/certs/client.crt
 key_file: /etc/certs/client.key
```

**Use Case:** Send to Jaeger, Grafana Tempo, or other OTLP backends.

### Jaeger Exporter

**Purpose:** Send traces to Jaeger.

**Configuration:**

```yaml
exporters:
 jaeger:
 endpoint: "jaeger-collector:14250"
 tls:
 insecure: true
```

**Use Case:** Export traces to Jaeger for analysis.

### Loki Exporter

**Purpose:** Send logs to Grafana Loki.

**Configuration:**

```yaml
exporters:
 loki:
 endpoint: "http://loki:3100/loki/api/v1/push"
 labels:
 resource:
 cluster: "cluster.name"
 namespace: "k8s.namespace.name"
```

**Use Case:** Centralize logs in Loki for querying with LogQL.

### Logging Exporter (Debug)

**Purpose:** Print telemetry to stdout for debugging.

**Configuration:**

```yaml
exporters:
 logging:
 loglevel: debug
 sampling_initial: 5
 sampling_thereafter: 200
```

**Use Case:** Debugging collector pipelines during development.

---

## Extensions

### Health Check Extension

**Purpose:** Provide HTTP health check endpoint.

**Configuration:**

```yaml
extensions:
 health_check:
 endpoint: 0.0.0.0:13133
 path: /health
```

**Use Case:** Kubernetes liveness/readiness probes.

### pprof Extension

**Purpose:** Golang profiling endpoint.

**Configuration:**

```yaml
extensions:
 pprof:
 endpoint: 0.0.0.0:1777
```

**Use Case:** Profile collector performance.

### zpages Extension

**Purpose:** Debug pages for inspecting pipelines.

**Configuration:**

```yaml
extensions:
 zpages:
 endpoint: 0.0.0.0:55679
```

**Use Case:** Debug pipeline status at `http://localhost:55679/debug/tracez`.

---

## Complete Examples

### Example 1: Agent Mode (Kubernetes DaemonSet)

**Purpose:** Collect host metrics and forward application traces to gateway.

```yaml
receivers:
 otlp:
 protocols:
 grpc:
 endpoint: 0.0.0.0:4317
 hostmetrics:
 collection_interval: 30s
 scrapers:
 cpu:
 memory:
 disk:
 filesystem:
 network:

processors:
 memory_limiter:
 check_interval: 1s
 limit_mib: 256
 batch:
 timeout: 10s
 resource:
 attributes:
 - key: cluster.name
 value: platform-production
 action: insert
 k8sattributes:
 auth_type: serviceAccount
 passthrough: false
 extract:
 metadata:
 - k8s.namespace.name
 - k8s.pod.name
 - k8s.node.name

exporters:
 otlp:
 endpoint: "otel-gateway:4317"
 tls:
 insecure: true

extensions:
 health_check:
 endpoint: 0.0.0.0:13133

service:
 extensions: [health_check]
 pipelines:
 metrics:
 receivers: [hostmetrics]
 processors: [memory_limiter, batch, resource]
 exporters: [otlp]
 traces:
 receivers: [otlp]
 processors: [memory_limiter, batch, k8sattributes]
 exporters: [otlp]
```

### Example 2: Gateway Mode (Centralized)

**Purpose:** Aggregate traces, apply tail sampling, export to Jaeger and Prometheus.

```yaml
receivers:
 otlp:
 protocols:
 grpc:
 endpoint: 0.0.0.0:4317

processors:
 memory_limiter:
 check_interval: 1s
 limit_mib: 2048
 batch:
 timeout: 10s
 send_batch_size: 10000
 tail_sampling:
 decision_wait: 10s
 policies:
 - name: errors
 type: status_code
 status_code:
 status_codes: [ERROR]
 - name: probabilistic
 type: probabilistic
 probabilistic:
 sampling_percentage: 5

exporters:
 jaeger:
 endpoint: "jaeger-collector:14250"
 tls:
 insecure: true
 prometheusremotewrite:
 endpoint: "https://prometheus-remote-write.example.com/api/v1/write"
 headers:
 Authorization: "Bearer ${PROMETHEUS_TOKEN}"

extensions:
 health_check:
 pprof:

service:
 extensions: [health_check, pprof]
 pipelines:
 traces:
 receivers: [otlp]
 processors: [memory_limiter, batch, tail_sampling]
 exporters: [jaeger]
 metrics:
 receivers: [otlp]
 processors: [memory_limiter, batch]
 exporters: [prometheusremotewrite]
```

### Example 3: Prometheus Scraping + OTLP Export

**Purpose:** Scrape Prometheus metrics from Kubernetes pods, export to Prometheus remote write.

```yaml
receivers:
 prometheus:
 config:
 scrape_configs:
 - job_name: 'kubernetes-pods'
 kubernetes_sd_configs:
 - role: pod
 namespaces:
 names:
 - platform-system
 - default
 relabel_configs:
 - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
 action: keep
 regex: true
 - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_port]
 action: replace
 target_label: __address__
 regex: (.+)
 replacement: $1:8080

processors:
 memory_limiter:
 limit_mib: 512
 batch:

exporters:
 prometheusremotewrite:
 endpoint: "https://prometheus.example.com/api/v1/write"
 resource_to_telemetry_conversion:
 enabled: true

service:
 pipelines:
 metrics:
 receivers: [prometheus]
 processors: [memory_limiter, batch]
 exporters: [prometheusremotewrite]
```

---

## Kubernetes Deployment

### DaemonSet (Agent Mode)

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
 name: otel-agent
 namespace: observability
spec:
 selector:
 matchLabels:
 app: otel-agent
 template:
 metadata:
 labels:
 app: otel-agent
 spec:
 serviceAccountName: otel-agent
 containers:
 - name: otel-collector
 image: otel/opentelemetry-collector-k8s:0.94.0
 args:
 - "--config=/conf/otel-agent-config.yaml"
 env:
 - name: KUBE_NODE_NAME
 valueFrom:
 fieldRef:
 fieldPath: spec.nodeName
 resources:
 limits:
 memory: 512Mi
 cpu: 200m
 requests:
 memory: 256Mi
 cpu: 100m
 ports:
 - containerPort: 4317 # OTLP gRPC
 - containerPort: 13133 # Health check
 volumeMounts:
 - name: config
 mountPath: /conf
 livenessProbe:
 httpGet:
 path: /
 port: 13133
 readinessProbe:
 httpGet:
 path: /
 port: 13133
 volumes:
 - name: config
 configMap:
 name: otel-agent-config
```

### Deployment (Gateway Mode)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
 name: otel-gateway
 namespace: observability
spec:
 replicas: 3
 selector:
 matchLabels:
 app: otel-gateway
 template:
 metadata:
 labels:
 app: otel-gateway
 spec:
 serviceAccountName: otel-gateway
 containers:
 - name: otel-collector
 image: otel/opentelemetry-collector-contrib:0.94.0
 args:
 - "--config=/conf/otel-gateway-config.yaml"
 resources:
 limits:
 memory: 4Gi
 cpu: 2
 requests:
 memory: 2Gi
 cpu: 1
 ports:
 - containerPort: 4317 # OTLP gRPC
 - containerPort: 8889 # Prometheus exporter
 - containerPort: 13133 # Health check
 volumeMounts:
 - name: config
 mountPath: /conf
 livenessProbe:
 httpGet:
 path: /
 port: 13133
 readinessProbe:
 httpGet:
 path: /
 port: 13133
 volumes:
 - name: config
 configMap:
 name: otel-gateway-config
---
apiVersion: v1
kind: Service
metadata:
 name: otel-gateway
 namespace: observability
spec:
 selector:
 app: otel-gateway
 ports:
 - name: otlp-grpc
 port: 4317
 targetPort: 4317
 - name: prometheus
 port: 8889
 targetPort: 8889
```

### RBAC (ServiceAccount)

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
 name: otel-agent
 namespace: observability
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
 name: otel-agent
rules:
 - apiGroups: [""]
 resources:
 - nodes
 - nodes/stats
 - nodes/proxy
 - pods
 - services
 - endpoints
 verbs: ["get", "list", "watch"]
 - apiGroups: ["apps"]
 resources:
 - deployments
 - replicasets
 - daemonsets
 - statefulsets
 verbs: ["get", "list", "watch"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
 name: otel-agent
roleRef:
 apiGroup: rbac.authorization.k8s.io
 kind: ClusterRole
 name: otel-agent
subjects:
 - kind: ServiceAccount
 name: otel-agent
 namespace: observability
```

---

## Best Practices

### 1. Always Use Batch Processor

**Why:** Reduces network overhead by batching telemetry data.

**Configuration:**
```yaml
processors:
 batch:
 timeout: 10s
 send_batch_size: 1000
```

### 2. Use Memory Limiter First in Chain

**Why:** Prevents OOM crashes by limiting memory usage.

**Configuration:**
```yaml
processors:
 memory_limiter:
 check_interval: 1s
 limit_mib: 512
```

**Processor Order:**
```yaml
processors: [memory_limiter, batch, k8sattributes]
```

### 3. Use Tail Sampling for High-Volume Traces

**Why:** Reduces trace volume while keeping important traces (errors, slow requests).

**Configuration:**
```yaml
processors:
 tail_sampling:
 policies:
 - name: errors
 type: status_code
 status_code:
 status_codes: [ERROR]
 - name: probabilistic
 type: probabilistic
 probabilistic:
 sampling_percentage: 5
```

### 4. Add Resource Attributes for Context

**Why:** Makes telemetry queryable by cluster, environment, or region.

**Configuration:**
```yaml
processors:
 resource:
 attributes:
 - key: cluster.name
 value: platform-production
 action: insert
```

### 5. Use Health Checks for Kubernetes Probes

**Configuration:**
```yaml
extensions:
 health_check:
 endpoint: 0.0.0.0:13133
```

**Kubernetes Probes:**
```yaml
livenessProbe:
 httpGet:
 path: /
 port: 13133
readinessProbe:
 httpGet:
 path: /
 port: 13133
```

### 6. Monitor Collector Metrics

**Self-monitoring:**
```yaml
receivers:
 prometheus:
 config:
 scrape_configs:
 - job_name: 'otel-collector'
 scrape_interval: 10s
 static_configs:
 - targets: ['localhost:8888']
```

**Key Metrics:**
- `otelcol_receiver_accepted_spans` - Received spans
- `otelcol_exporter_sent_spans` - Exported spans
- `otelcol_processor_batch_batch_send_size` - Batch sizes
- `otelcol_processor_refused_spans` - Dropped spans

### 7. Use TLS for Production Backends

**Configuration:**
```yaml
exporters:
 otlp:
 endpoint: "jaeger-collector:4317"
 tls:
 insecure: false
 cert_file: /etc/certs/client.crt
 key_file: /etc/certs/client.key
 ca_file: /etc/certs/ca.crt
```

### 8. Set Resource Limits in Kubernetes

**DaemonSet (Agent):**
```yaml
resources:
 limits:
 memory: 512Mi
 cpu: 200m
 requests:
 memory: 256Mi
 cpu: 100m
```

**Deployment (Gateway):**
```yaml
resources:
 limits:
 memory: 4Gi
 cpu: 2
 requests:
 memory: 2Gi
 cpu: 1
```

---

## Troubleshooting

### Issue 1: Collector OOM Crashes

**Symptoms:**
- Collector pod restarting frequently
- `OOMKilled` in pod status

**Solution:**
1. Add `memory_limiter` processor
2. Increase memory limits in Kubernetes
3. Reduce batch sizes

```yaml
processors:
 memory_limiter:
 limit_mib: 512
 spike_limit_mib: 128
 batch:
 send_batch_size: 500 # Reduce from default
```

### Issue 2: High Latency in Traces

**Symptoms:**
- Slow trace delivery to backends
- Traces delayed by 30+ seconds

**Solution:**
1. Reduce `batch.timeout`
2. Increase `tail_sampling.decision_wait`

```yaml
processors:
 batch:
 timeout: 5s # Reduce from 10s
 tail_sampling:
 decision_wait: 5s # Reduce if acceptable
```

### Issue 3: Data Not Appearing in Backend

**Symptoms:**
- No metrics/traces in Prometheus/Jaeger
- Collector shows no errors

**Debugging Steps:**

1. **Enable logging exporter:**
```yaml
exporters:
 logging:
 loglevel: debug

service:
 pipelines:
 traces:
 receivers: [otlp]
 processors: [batch]
 exporters: [logging, jaeger] # Add logging
```

2. **Check collector logs:**
```bash
kubectl logs -n observability otel-gateway-xyz --tail=100
```

3. **Verify receiver endpoints:**
```bash
kubectl exec -it otel-gateway-xyz -- netstat -tuln | grep 4317
```

4. **Test connectivity to backend:**
```bash
kubectl exec -it otel-gateway-xyz -- curl -v http://jaeger-collector:14268
```

### Issue 4: Kubernetes Metadata Missing

**Symptoms:**
- No `k8s.namespace.name` or `k8s.pod.name` attributes

**Solution:**
1. Verify ServiceAccount has RBAC permissions
2. Enable `k8sattributes` processor

```yaml
processors:
 k8sattributes:
 auth_type: serviceAccount
 passthrough: false
 extract:
 metadata:
 - k8s.namespace.name
 - k8s.pod.name
```

### Issue 5: Prometheus Scraping Fails

**Symptoms:**
- Collector metrics not appearing in Prometheus
- Prometheus target shows `down`

**Solution:**

1. **Verify Prometheus exporter endpoint:**
```bash
kubectl exec -it otel-gateway-xyz -- curl http://localhost:8889/metrics
```

2. **Check Service configuration:**
```yaml
apiVersion: v1
kind: Service
metadata:
 name: otel-gateway
 annotations:
 prometheus.io/scrape: "true"
 prometheus.io/port: "8889"
 prometheus.io/path: "/metrics"
```

---

## Company / Platform Specific Examples

### Example: Platform Production Setup

**Agent DaemonSet Config:**

```yaml
receivers:
 otlp:
 protocols:
 grpc:
 endpoint: 0.0.0.0:4317
 hostmetrics:
 collection_interval: 30s
 scrapers:
 cpu:
 memory:
 disk:
 network:

processors:
 memory_limiter:
 limit_mib: 256
 batch:
 timeout: 10s
 resource:
 attributes:
 - key: cluster.name
 value: platform-production
 action: insert
 - key: environment
 value: production
 action: insert
 k8sattributes:
 auth_type: serviceAccount
 extract:
 metadata:
 - k8s.namespace.name
 - k8s.pod.name
 - k8s.deployment.name

exporters:
 otlp:
 endpoint: "otel-gateway.observability.svc.cluster.local:4317"
 tls:
 insecure: true

service:
 pipelines:
 metrics:
 receivers: [hostmetrics]
 processors: [memory_limiter, batch, resource]
 exporters: [otlp]
 traces:
 receivers: [otlp]
 processors: [memory_limiter, batch, k8sattributes]
 exporters: [otlp]
```

**Gateway Config:**

```yaml
receivers:
 otlp:
 protocols:
 grpc:
 endpoint: 0.0.0.0:4317

processors:
 memory_limiter:
 limit_mib: 2048
 batch:
 timeout: 10s
 send_batch_size: 10000
 tail_sampling:
 decision_wait: 10s
 policies:
 - name: errors_and_slow
 type: composite
 composite:
 max_total_spans_per_second: 1000
 policy_order: [errors, slow_requests, probabilistic]
 composite_sub_policy:
 - name: errors
 type: status_code
 status_code:
 status_codes: [ERROR]
 - name: slow_requests
 type: latency
 latency:
 threshold_ms: 500
 - name: probabilistic
 type: probabilistic
 probabilistic:
 sampling_percentage: 5

exporters:
 otlp:
 endpoint: "jaeger-collector.observability.svc.cluster.local:4317"
 prometheusremotewrite:
 endpoint: "https://prometheus.company.com/api/v1/write"
 headers:
 Authorization: "Bearer ${PROMETHEUS_TOKEN}"

service:
 pipelines:
 traces:
 receivers: [otlp]
 processors: [memory_limiter, batch, tail_sampling]
 exporters: [otlp]
 metrics:
 receivers: [otlp]
 processors: [memory_limiter, batch]
 exporters: [prometheusremotewrite]
```

---

## References

- **OpenTelemetry Docs:** https://opentelemetry.io/docs/collector/
- **Collector Configuration:** https://opentelemetry.io/docs/collector/configuration/
- **Receivers:** https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/receiver
- **Processors:** https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/processor
- **Exporters:** https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/exporter
- **Kubernetes Deployment:** https://opentelemetry.io/docs/kubernetes/
- **CNCF Project:** https://www.cncf.io/projects/opentelemetry/

---

**Last Updated:** 2026-03-19
**Author:** Documentation Team (Company Infrastructure Team)
**License:** CC BY-SA 4.0
