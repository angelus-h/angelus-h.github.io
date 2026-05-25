---
description: PromQL quick reference - query fundamentals, aggregation, functions, rate calculations, alerting, and real-world monitoring patterns.
---

# PromQL Quick Reference

**Target Audience:** DevOps Engineers, SREs, Platform Engineers
**Prerequisites:** Basic understanding of metrics, time-series data, and Kubernetes/containerized environments

---

## Learning Path Overview

```
Week 1: Fundamentals → Week 2: Advanced Queries → Week 3: Real-World Application
```

---

## Week 1: PromQL Fundamentals

### Day 1-2: Introduction to Prometheus and PromQL

**Concepts:**
- What is Prometheus and how does it work?
- Time-series data model (metrics, labels, timestamps)
- Metric naming conventions
- Understanding the Prometheus UI

**Hands-On:**
- Install Prometheus locally or use a demo instance
- Explore the Expression Browser
- Run your first queries: `up`, `prometheus_build_info`

**Resources:**
- [Prometheus Official Docs - First Steps](https://prometheus.io/docs/introduction/first_steps/)
- [Prometheus Data Model](https://prometheus.io/docs/concepts/data_model/)

---

### Day 3-4: Metric Types

**Concepts:**
- **Counter:** Monotonically increasing value (e.g., http_requests_total)
- **Gauge:** Value that can go up or down (e.g., memory_usage_bytes)
- **Histogram:** Distribution of observations (e.g., request_duration_seconds)
- **Summary:** Similar to histogram with quantiles

**Hands-On:**
- Query different metric types
- Understand when to use `rate()` vs `irate()` for counters
- Query histogram buckets and percentiles

**Example Queries:**
```promql
# Counter - rate of HTTP requests per second
rate(http_requests_total[5m])

# Gauge - current memory usage
process_resident_memory_bytes

# Histogram - 95th percentile latency
histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))
```

---

### Day 5-6: Selectors and Matchers

**Concepts:**
- Instant vectors vs range vectors
- Label matching (=, !=, =~, !~)
- Time durations and range selectors
- Offset modifier

**Hands-On:**
- Filter metrics by labels
- Query historical data with offset
- Combine multiple label matchers

**Example Queries:**
```promql
# Exact match
http_requests_total{job="api-server", method="GET"}

# Regex match
http_requests_total{endpoint=~"/api/.*"}

# Multiple conditions
http_requests_total{status!="200", job="api-server"}

# Range vector (last 5 minutes)
http_requests_total{job="api-server"}[5m]

# Offset (1 hour ago)
http_requests_total{job="api-server"} offset 1h
```

---

### Day 7: Week 1 Practice

**Challenge:**
- Query CPU usage for a specific pod
- Find pods with memory usage > 1GB
- Calculate request rate over the last 10 minutes
- Compare current metrics vs 1 hour ago

---

## Week 2: Advanced PromQL

### Day 8-9: Operators

**Concepts:**
- Arithmetic operators (+, -, *, /, %, ^)
- Comparison operators (==, !=, >, <, >=, <=)
- Logical operators (and, or, unless)
- Vector matching (one-to-one, one-to-many)

**Hands-On:**
- Calculate percentages
- Filter results with comparison operators
- Combine multiple metrics

**Example Queries:**
```promql
# CPU usage percentage
100 - (avg(irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)

# Memory usage percentage
(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100

# Filter pods using > 80% CPU
(rate(container_cpu_usage_seconds_total[5m]) / container_spec_cpu_quota) * 100 > 80
```

---

### Day 10-11: Aggregation Functions

**Concepts:**
- `sum()`, `avg()`, `max()`, `min()`, `count()`
- `count_values()`, `topk()`, `bottomk()`
- `quantile()`, `stddev()`, `stdvar()`
- Aggregation with `by` and `without`

**Hands-On:**
- Aggregate metrics across multiple instances
- Find top N resource consumers
- Calculate percentiles

**Example Queries:**
```promql
# Total requests across all instances
sum(rate(http_requests_total[5m]))

# Average CPU by namespace
avg(rate(container_cpu_usage_seconds_total[5m])) by (namespace)

# Top 5 pods by memory usage
topk(5, container_memory_usage_bytes{pod!=""})

# 90th percentile response time
quantile(0.90, rate(http_request_duration_seconds_sum[5m]))
```

---

### Day 12-13: Functions

**Concepts:**
- Rate functions: `rate()`, `irate()`, `increase()`
- Time functions: `time()`, `timestamp()`, `day_of_week()`
- Math functions: `abs()`, `ceil()`, `floor()`, `round()`
- Prediction functions: `predict_linear()`, `deriv()`
- Label manipulation: `label_replace()`, `label_join()`

**Hands-On:**
- Calculate growth rates
- Predict future values
- Transform labels dynamically

**Example Queries:**
```promql
# Request rate (per-second average over 5m)
rate(http_requests_total[5m])

# Instantaneous rate
irate(http_requests_total[5m])

# Total increase over 1 hour
increase(http_requests_total[1h])

# Predict disk usage in 4 hours
predict_linear(node_filesystem_free_bytes[1h], 4*3600)

# Replace label values
label_replace(up{job="api"}, "env", "production", "job", "api")
```

---

### Day 14: Week 2 Practice

**Challenge:**
- Calculate error rate percentage across services
- Find the top 10 namespaces by network traffic
- Predict when disk will be full
- Create a query for 99th percentile latency by endpoint

---

## Week 3: Real-World Kubernetes/OpenShift Queries

### Day 15-16: Pod and Container Metrics

**Common Queries:**
```promql
# Pods in CrashLoopBackOff
kube_pod_container_status_waiting_reason{reason="CrashLoopBackOff"} > 0

# Container restarts in last hour
increase(kube_pod_container_status_restarts_total[1h]) > 0

# Pods not ready
kube_pod_status_ready{condition="false"} == 1

# CPU throttling
rate(container_cpu_cfs_throttled_seconds_total[5m]) > 0

# Memory usage vs limits
container_memory_usage_bytes / container_spec_memory_limit_bytes * 100
```

---

### Day 17-18: Node and Cluster Metrics

**Common Queries:**
```promql
# Node CPU usage
100 - (avg by (node) (irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)

# Node memory pressure
kube_node_status_condition{condition="MemoryPressure", status="true"} == 1

# Disk pressure
kube_node_status_condition{condition="DiskPressure", status="true"} == 1

# Available nodes
count(kube_node_info)

# Cluster CPU capacity
sum(kube_node_status_allocatable{resource="cpu"})
```

---

### Day 19-20: Application Performance Monitoring

**Common Queries:**
```promql
# Request rate by service
sum(rate(http_requests_total[5m])) by (service)

# Error rate percentage
sum(rate(http_requests_total{status=~"5.."}[5m])) / sum(rate(http_requests_total[5m])) * 100

# P95 latency
histogram_quantile(0.95, sum(rate(http_request_duration_seconds_bucket[5m])) by (le, service))

# Apdex score (target: 500ms, tolerance: 2s)
(
 sum(rate(http_request_duration_seconds_bucket{le="0.5"}[5m])) +
 sum(rate(http_request_duration_seconds_bucket{le="2"}[5m])) / 2
) / sum(rate(http_request_duration_seconds_count[5m]))
```

---

### Day 21: Advanced Techniques

**Subqueries:**
```promql
# Max CPU usage in the last hour, sampled every 5 minutes
max_over_time(rate(container_cpu_usage_seconds_total[5m])[1h:5m])
```

**Recording Rules:**
- Pre-compute expensive queries
- Improve dashboard performance

**Alerting Rules:**
- Define alert conditions
- Use `for` clause to avoid flapping
- Add meaningful annotations

**Best Practices:**
- Avoid high-cardinality queries
- Use recording rules for complex calculations
- Limit time ranges for large datasets
- Use regex matchers carefully (performance impact)

---

## Practical Exercise: LUMINO MCP Server Context

Apply your PromQL knowledge to real Platform/Tekton scenarios:

### Exercise 1: Pipeline Performance
```promql
# Average pipeline duration by namespace
avg(tekton_pipelinerun_duration_seconds_sum) by (namespace)

# Pipeline failure rate
sum(rate(tekton_pipelinerun_count{status="Failed"}[5m])) by (pipeline)
/ sum(rate(tekton_pipelinerun_count[5m])) by (pipeline) * 100
```

### Exercise 2: Resource Bottlenecks
```promql
# Pods waiting for scheduling (resource constraints)
count(kube_pod_status_phase{phase="Pending"}) by (namespace)

# Nodes at >80% memory
count(
 (node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes)
 / node_memory_MemTotal_bytes > 0.8
)
```

### Exercise 3: Anomaly Detection
```promql
# Detect sudden CPU spikes (>50% increase from baseline)
(
 rate(container_cpu_usage_seconds_total[5m])
 - rate(container_cpu_usage_seconds_total[5m] offset 1h)
) / rate(container_cpu_usage_seconds_total[5m] offset 1h) > 0.5
```

---

## Tools and Resources

### Interactive Learning
- [PromLens](https://promlens.com/) - Visual query builder
- [Prometheus Demo](https://demo.promlabs.com/) - Live playground
- [Grafana Play](https://play.grafana.org/) - Test dashboards

### Documentation
- [PromQL Official Docs](https://prometheus.io/docs/prometheus/latest/querying/basics/)
- [PromQL Cheat Sheet](https://promlabs.com/promql-cheat-sheet/)
- [Robust Perception Blog](https://www.robustperception.io/blog)

### Practice Datasets
- [node_exporter](https://github.com/prometheus/node_exporter) - System metrics
- [kube-state-metrics](https://github.com/kubernetes/kube-state-metrics) - K8s object metrics
- [cAdvisor](https://github.com/google/cadvisor) - Container metrics

### Visualization
- Grafana dashboards
- Prometheus Console Templates
- Custom visualization with PromQL API

---

## Success Criteria

By the end of this learning plan, you should be able to:

- [ ] Write queries to monitor pod/container health
- [ ] Calculate resource utilization percentages
- [ ] Create alerting rules for critical conditions
- [ ] Build dashboard queries for SLIs/SLOs
- [ ] Optimize queries for performance
- [ ] Troubleshoot Tekton pipeline issues using metrics
- [ ] Predict resource exhaustion trends
- [ ] Aggregate metrics across multiple dimensions

---

## Next Steps

After mastering PromQL:
- Learn Grafana dashboard creation
- Explore Thanos for long-term storage
- Study Alertmanager for alert routing
- Integrate metrics with LUMINO MCP tools
- Contribute custom PromQL queries to observability stack

---

**Last Updated:** 2026-03-13
**Maintainer:** Documentation Team
