# SignalFx Detector False Positive Mitigation - Case Study & Best Practices

**Team:** SP Resilience Engineering (OPS) 
**Date:** April 2026 
**Author:** Infrastructure Team 
**Status:** Production Implementation Complete 
**Related Ticket:** [OPS-5079](https://company.atlassian.net/browse/OPS-5079)

---

## Executive Summary

In April 2026, we identified and resolved a systemic issue affecting 10 critical availability detectors across Dist-Git, Service-A, and Service-C systems. These detectors were generating false positive alerts due to transient OpenTelemetry collector network connectivity issues.

**Impact:**
- **Before:** ~1-3% false positive rate from OTel collector network blips
- **After:** 0% false positive rate (target)
- **Systems Protected:** Dist-Git, Module Build Service (Service-A), Service-C
- **Detectors Updated:** 10 critical availability detectors
- **Configuration Standard:** 15-minute grace period for all availability detectors

**Key Lesson:** Observability pipeline health ≠ Application health. Detectors must distinguish between collector issues and actual service failures.

---

## Problem Statement

### The Incident (April 13, 2026)

**PagerDuty Incident:** [Q047B7T2DY14KE](https://company.pagerduty.com/incidents/Q047B7T2DY14KE)

**What Happened:**
- Alert: "Dist-Git HOST AVAILABILITY" triggered at 19:07 UTC
- Investigation revealed: PostgreSQL was healthy (10 days uptime, no issues)
- Root cause: OTel collector lost network connectivity to `ingest.us1.signalfx.com` for 4 minutes
- Result: False positive alert → Unnecessary page-out to on-call engineer

**Timeline:**
```
15:20 ET │ OTel collector network connectivity issue begins
│ Timeout errors, 14 datapoints dropped
15:24 ET │ Collector auto-reconnects, metrics resume
19:07 UTC │ SignalFx detector triggers (missing metrics interpreted as host down)
19:13 UTC │ On-call acknowledges (investigates, identifies false positive)
19:21 UTC │ Alert auto-resolves (metrics flowing again)
```

### Why This Was a Problem

**Detector Configuration:**
```javascript
// OLD configuration:
A = data('cpu.utilization',
extrapolation='last_value',
maxExtrapolations=2    // Only 60 seconds grace period
).count(by=['host.name'])

detect(when(A < threshold(1))) // Triggers immediately, no sustained check
```

**The Issue:**
- **Grace period:** 60 seconds (2 datapoints × 30-sec resolution)
- **Collector outage:** 4 minutes
- **Result:** Grace period expired before collector reconnected → False positive

**Pattern Recognition:**
- This wasn't a one-off issue
- 10 detectors across 3 critical systems had similar configurations
- All were at risk of false positives from transient collector issues

---

## Root Cause Analysis

### Three Failure Domains

Understanding the problem requires recognizing three distinct failure domains:

| Domain | Component | Monitoring Method | Failure Mode |
|--------|-----------|-------------------|--------------|
| **1. Application** | PostgreSQL, HTTPD, RabbitMQ | Systemd service state, process metrics | Service crash, hang, misconfiguration |
| **2. Observability Pipeline** | OTel Collector | Collector health metrics | Network connectivity, collector restart |
| **3. Monitoring Backend** | SignalFx | API availability | Backend outage (rare) |

**The Core Problem:** Our detectors conflated failures in Domain 2 (collector) with failures in Domain 1 (application).

### Why Collector Network Blips Happen

**Common Causes:**
1. **Network maintenance:** Scheduled or emergency maintenance on egress path
2. **Firewall rule updates:** Temporary connectivity loss during rule deployment
3. **Collector restarts:** Package updates, configuration changes
4. **Load balancer issues:** SignalFx ingestion endpoint load balancing
5. **DNS resolution delays:** Transient DNS lookup failures
6. **Rate limiting:** Burst traffic exceeding collector send queue

**Key Insight:** These are **transient** (typically 2-10 minutes) and **self-healing** (collector auto-reconnects).

---

## Solution

### Configuration Standard: 15-Minute Grace Period

We established a consistent 15-minute grace period across all availability detectors:

**Two Implementation Patterns:**

#### Pattern 1: CPU/Metric Utilization-Based Detectors

```javascript
// NEW configuration:
A = data('cpu.utilization',
filter=filter('host.name', ...),
extrapolation='last_value',
maxExtrapolations=20    // 10 minutes @ 30-sec resolution
).count(by=['host.name'])

detect(
when(A < threshold(1), lasting='5m'), // 5 minutes sustained
auto_resolve_after='3d'
)
```

**Total grace period:** 10 min (extrapolation) + 5 min (sustained) = **15 minutes**

#### Pattern 2: Not-Reporting Detection (SystemD Services)

```javascript
// NEW configuration:
from signalfx.detectors.not_reporting import not_reporting

A = data('gauge.substate.running',
filter=filter('systemd_service', 'postgresql|httpd|rabbitmq-server')
)

not_reporting.detector(
stream=A,
duration='15m',      // 15 minutes before alerting
auto_resolve_after='3d'
)
```

**Total grace period:** **15 minutes**

---

## Implementation

### Detectors Updated (10)

#### Dist-Git (2 detectors)
1. **Dist-Git HOST AVAILABILITY** (`GhBVHGqAwAE`)
- Type: CPU utilization
- Change: `maxExtrapolations=2` → `20`, added `lasting='5m'`

2. **DIST-GIT POSTGRESQL AVAILABILTY** (`GhBXF4jA4AE`)
- Type: Not-reporting
- Change: `duration='5m'` → `'15m'`

#### Service-A - Module Build Service (5 detectors)
3. **Service-A Host Availability Detector** (`Gg23NACA0AA`)
4. **Service-A FRONTEND HTTPD AVAILABILTY** (`GhBRMpcA0AI`)
5. **Service-A RabbitMQ AVAILABILTY** (`GhBUIfrA4AA`)
6. **Service-A-WORKER STATUS** (`GhJ7gqhAwAA`)
7. **Service-A Fedmsg-Hub STATUS** (`GhJ6mAdA4AQ`)

#### Service-C (3 detectors)
8. **BREW DB AVAILABILITY** (`GfKiA0wA4AA`)
9. **BREW HUB HTTPD Availability** (`Gg7KHZFA0AQ`)
10. **Service-C UTIL KOjIRA Not Running** (`GX0p91TA4AA`)

### Implementation Method

**Tools:**
- SignalFx REST API v2
- curl + jq for API manipulation
- Bash scripts for bulk updates

**Process:**
1. Backup all detector configurations
2. Modify `programText` with new parameters
3. Validate changes
4. Apply via PUT `/v2/detector/{id}`
5. Verify updates

**Safety:**
- All original configurations backed up
- Changes applied incrementally
- Verification after each update
- Rollback plan prepared

---

## Results

### Expected Impact

| Scenario | Before | After |
|----------|--------|-------|
| **4-minute collector blip** | False positive alert | No alert (within grace period) |
| **Real service down (15+ min)** | Alert in 1-10 min | Alert in ~15 min |
| **False positive rate** | ~1-3% | Target: 0% |
| **Alert latency** | 1-10 minutes | ~15 minutes |

### Trade-offs

**Benefits:**
- Eliminates false positives from transient collector issues
- Reduces unnecessary on-call pages
- Improves alert reliability and trust
- Consistent configuration across critical systems

**Costs:**
- Slower alert time: 15 minutes vs. 1-10 minutes
- Acceptable for infrastructure services (databases, web servers)
- Still within SLA for incident response

**Decision:** The trade-off is worthwhile. On-call quality of life and alert trust outweigh 5-10 minute slower alerting for infrastructure issues.

---

## Best Practices & Lessons Learned

### 1. Distinguish Observability Pipeline from Application Health

**Anti-Pattern:**
```javascript
// BAD: Missing metrics = application down
detect(when(metric_count < 1)) // No grace period
```

**Best Practice:**
```javascript
// GOOD: Tolerate missing metrics, require sustained absence
detect(when(metric_count < 1, lasting='5m')) // Sustained check
+ extrapolation policy with grace period
```

### 2. Calculate Grace Periods in Human Time

**Formula:**
```
Grace Period (minutes) = (maxExtrapolations × Resolution) / 60

Example:
maxExtrapolations=20 × 30 seconds = 600 seconds = 10 minutes
```

**Recommendation:**
- Infrastructure services: 10-15 minutes
- Application services: 5-10 minutes
- Critical user-facing: 3-5 minutes

### 3. Use `lasting` Parameter for Sustained Conditions

**Why:** Prevents alerts from noisy, oscillating metrics.

**Example:**
```javascript
// Without lasting: Alert on single spike
detect(when(A > threshold(90)))

// With lasting: Alert only if sustained
detect(when(A > threshold(90), lasting='3m'))
```

### 4. Choose Appropriate Extrapolation Policy

| Policy | When to Use | Behavior |
|--------|-------------|----------|
| `'last_value'` | **Availability metrics** (host up/down) | Uses last observed value during gap |
| `'zero'` | **Rate metrics** (requests/sec) | Treats missing data as no activity |
| `'null'` | **Gauge metrics** (temperature) | No extrapolation, gap in data |

**For availability detectors:** Always use `'last_value'`

### 5. Separate Collector Health from Application Health

**Recommended Architecture:**

**Detector 1: Application Availability**
- Purpose: Monitor actual service health
- Grace period: 15 minutes (tolerate collector issues)
- Notification: PagerDuty (critical)

**Detector 2: OTel Collector Health** (separate detector)
- Purpose: Monitor collector export health
- Metric: `otelcol_exporter_send_failed_metric_points`
- Grace period: 3-5 minutes (collector-specific)
- Notification: Slack (warning only, no page)

**Benefit:** Clear root cause attribution - "collector issue" vs. "service down"

### 6. Not-Reporting vs. Manual Configuration

**SignalFx Not-Reporting Detector:**
```javascript
not_reporting.detector(stream=A, duration='15m')
```
- Simple single parameter
- Built-in handling of missing data
- Best for systemd service monitoring

**Manual Configuration:**
```javascript
A = data(..., extrapolation='last_value', maxExtrapolations=20)
detect(when(A < 1, lasting='5m'))
```
- More control over behavior
- Can combine with other conditions
- Best for complex availability checks

**When to use which:** Not-reporting for simple "is service running" checks. Manual for complex logic.

### 7. Two-Tier Severity (Advanced)

For services where early visibility is valuable:

```javascript
// Warning: Early signal (5 minutes)
detect(when(A < 1, lasting='5m')).publish('Service Availability Warning')
// Notification: Slack

// Critical: Confirmed outage (15 minutes)
detect(when(A < 1, lasting='15m')).publish('Service Availability Critical')
// Notification: PagerDuty
```

**Use case:** Team wants awareness of potential issues without immediate pages.

---

## Configuration Decision Tree

```
Is this an availability detector (service up/down)?
├─ YES
│ ├─ Does it monitor metrics from OTel collector?
│ │ ├─ YES → Apply 15-minute grace period
│ │ │    Pattern 1: maxExtrapolations=20 + lasting='5m'
│ │ │    Pattern 2: duration='15m'
│ │ └─ NO → Consider shorter grace period (5-10 min)
│ │
│ └─ Is it a systemd service state check?
│   ├─ YES → Use not_reporting.detector(duration='15m')
│   └─ NO → Use manual extrapolation + lasting
│
└─ NO (performance metric, disk usage, etc.)
└─ Use different tuning strategy
- Performance: Use percentiles, shorter lasting
- Disk usage: No extrapolation needed (slow-changing)
```

---

## Common Pitfalls to Avoid

### 1. Forgetting to Calculate Grace Period

**Mistake:**
```javascript
maxExtrapolations=5 // Developer thinks "5 minutes"
```

**Reality:** `5 × 30 seconds = 150 seconds = 2.5 minutes` 

**Fix:** Always calculate: `(maxExtrapolations × resolution) / 60`

### 2. Using Different Resolutions Without Adjusting

**Issue:** Detector resolution can vary (10s, 30s, 1m). Same `maxExtrapolations` value = different grace periods.

**Solution:** Document resolution in detector description, calculate grace period explicitly.

### 3. No `lasting` Parameter

**Mistake:**
```javascript
detect(when(A < 1)) // Triggers immediately
```

**Why it's bad:** Single missing datapoint can trigger alert, even with extrapolation.

**Fix:** Always use `lasting` for availability checks:
```javascript
detect(when(A < 1, lasting='5m'))
```

### 4. Extrapolation='zero' for Availability Metrics

**Mistake:**
```javascript
A = data('cpu.utilization', extrapolation='zero') // Wrong!
```

**Why:** Missing data = 0% CPU = host down (false positive)

**Fix:** Use `extrapolation='last_value'` for availability

### 5. Too Aggressive Thresholds

**Mistake:**
```javascript
detect(when(service_count < total_instances)) // Alert if ANY instance down
```

**Why:** Single instance restart = alert, even if service is healthy overall

**Fix:** Allow for rolling restarts:
```javascript
detect(when(service_count < (total_instances * 0.5))) // Alert if >50% down
```

---

## Monitoring Detector Health

### Key Metrics to Track

1. **False Positive Rate**
```
false_positive_rate = (false_positive_incidents / total_incidents) × 100
```
Target: <5% (ideally 0%)

2. **Alert Volume**
- Track incidents per detector per week
- Identify noisy detectors

3. **Mean Time to Alert (MTTA)**
- Average time from actual outage to alert
- Target: <20 minutes for infrastructure

4. **Flapping Rate**
- Detectors that trigger/clear repeatedly
- Indicates threshold or grace period too sensitive

### Recommended Dashboard

Create SignalFx dashboard:
- Detector trigger frequency (alerts per day)
- Alert duration distribution (<5m, 5-15m, >15m)
- OTel collector export failure rate
- Service availability metric coverage (% time with data)

---

## API Quick Reference

### Get Detector Configuration
```bash
curl -s -H "X-SF-TOKEN: $SIGNALFX_TOKEN" \
https://api.us1.signalfx.com/v2/detector/{DETECTOR_ID} | jq .
```

### Extract Program Text
```bash
curl -s -H "X-SF-TOKEN: $SIGNALFX_TOKEN" \
https://api.us1.signalfx.com/v2/detector/{DETECTOR_ID} | \
jq -r '.programText'
```

### Update Detector
```bash
# 1. Backup original
curl -s -H "X-SF-TOKEN: $SIGNALFX_TOKEN" \
https://api.us1.signalfx.com/v2/detector/{DETECTOR_ID} \
> detector_backup.json

# 2. Modify programText in JSON

# 3. Update
curl -X PUT \
-H "X-SF-TOKEN: $SIGNALFX_TOKEN" \
-H "Content-Type: application/json" \
-d @updated_detector.json \
https://api.us1.signalfx.com/v2/detector/{DETECTOR_ID}
```

### Verify Grace Period
```bash
# Check maxExtrapolations and resolution
curl -s -H "X-SF-TOKEN: $SIGNALFX_TOKEN" \
https://api.us1.signalfx.com/v2/detector/{DETECTOR_ID} | \
jq '{
maxExtrapolations: .programText | scan("maxExtrapolations=(\\d+)")[0],
resolution_ms: .labelResolutions | to_entries[0].value,
grace_period_min: (
(.programText | scan("maxExtrapolations=(\\d+)")[0] | tonumber) * 
(.labelResolutions | to_entries[0].value) / 60000
)
}'
```

---

## Testing & Validation

### Test Case 1: Collector Restart (Expected: No Alert)

**Objective:** Verify no alert for transient collector downtime

**Steps:**
```bash
# On preprod host
sudo systemctl restart otel-collector

# Wait 5 minutes
# Expected: NO PagerDuty alert
# Collector should reconnect within 2-3 minutes
```

**Pass Criteria:**
- No PagerDuty incident created
- Metrics resume flowing
- Detector remains green

### Test Case 2: Service Down (Expected: Alert at 15 min)

**Objective:** Verify alert for real service failure

**Steps:**
```bash
# On preprod host
sudo systemctl stop postgresql

# Wait 16 minutes
# Expected: PagerDuty alert at ~15 minute mark

sudo systemctl start postgresql
# Expected: Alert clears within 2 minutes
```

**Pass Criteria:**
- Alert triggers at 15-minute mark
- PagerDuty incident created
- Alert auto-resolves after service restart

---

## Rollback Procedure

If detector changes cause missed critical incidents:

### Quick Rollback
```bash
# 1. Restore from backup
curl -X PUT \
-H "X-SF-TOKEN: $SIGNALFX_TOKEN" \
-H "Content-Type: application/json" \
-d @detector_backup.json \
https://api.us1.signalfx.com/v2/detector/{DETECTOR_ID}

# 2. Notify team
# 3. Investigate why rollback was needed
```

### When to Rollback

**Rollback if:**
- Real outage takes >25 minutes to alert
- Multiple critical incidents missed in 48 hours
- Stakeholder escalation

**Do NOT rollback if:**
- Fewer false positives (this is the goal!)
- Alert latency ~15 min for real issues (acceptable)
- On-call feedback is positive

---

## Related Resources

### Documentation
- **Full Investigation:** `/home/ops-lead/ai/production-lumino/investigations/OPS-5079/`
- **Jira Ticket:** [OPS-5079](https://company.atlassian.net/browse/OPS-5079)
- **PagerDuty Incident:** [Q047B7T2DY14KE](https://company.pagerduty.com/incidents/Q047B7T2DY14KE)

### Runbooks
- [Dist-Git Host Availability](https://sp-resilience-team.pages.company.internal/ops-service-docs/sop/Dist-Git/host-availability.html)
- Update all runbooks to mention 15-minute grace period

### External References
- [SignalFx Signal Flow Documentation](https://docs.splunk.com/Observability/data-visualization/signalflow/signalflow-analytics-intro.html)
- [SignalFx Detector API](https://dev.splunk.com/observability/reference/api/detectors/latest)

---

## Recommendations for Other Teams

If you're experiencing similar false positive issues:

### 1. Audit Your Detectors

**Questions to ask:**
- Do your availability detectors tolerate missing metrics?
- What's the grace period? (Calculate: `maxExtrapolations × resolution`)
- Do you use `lasting` parameters?
- Have you separated collector health from app health?

### 2. Start with Most Critical Services

Prioritize:
- Production databases
- Customer-facing web services
- Message queues
- Critical background workers

### 3. Implement Incrementally

Don't change all detectors at once:
1. Update 1-2 detectors in preprod
2. Test thoroughly (collector restart, service down)
3. Monitor for 1 week
4. Expand to production if successful
5. Roll out to remaining detectors

### 4. Collect Feedback

- Survey on-call engineers
- Track before/after metrics
- Adjust based on real-world results

---

## Future Work

### Short-term
1. **OTel Collector Health Monitoring**
- Create separate detectors for collector export health
- Alert on sustained collector failures (warning, not critical)

2. **Detector Configuration Dashboard**
- Visualize grace periods across all detectors
- Identify detectors with <5 min grace periods
- Track detector flapping rate

### Long-term
3. **Automated Detector Audits**
- Script to check all detectors for proper grace periods
- Flag detectors at risk of false positives
- Suggest configuration improvements

4. **Detector Templates**
- Create standard templates for common patterns
- Enforce minimum grace periods in templates
- Automated validation in detector CI/CD

---

## Key Takeaways

1. **Observability pipeline health ≠ Application health** - Treat them as separate failure domains

2. **15-minute grace period is the new standard** for infrastructure availability detectors

3. **Always use `lasting` parameter** for availability checks (min 3-5 minutes)

4. **Calculate grace periods explicitly** - Don't rely on intuition about `maxExtrapolations`

5. **Test detector changes** in preprod before production

6. **Monitor success metrics** - Track false positive rate before/after

7. **Balance alert latency vs. reliability** - 15 minutes is acceptable for infrastructure

---

## Questions or Issues?

- **Slack:** #ops-team
- **Jira:** Create ticket with label `observability`
- **Owner:** Infrastructure Team (@ops-lead)

---

**Document Version:** 1.0 
**Last Updated:** April 14, 2026 
**Status:** Production Implementation Complete 
**Review Date:** July 2026 (3-month post-implementation review)
