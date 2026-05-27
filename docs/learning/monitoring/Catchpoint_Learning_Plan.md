# Catchpoint Quick Reference

**Comprehensive guide to Catchpoint synthetic monitoring and real user monitoring (RUM) for the company SRE teams**

---

## Table of Contents

1. [Introduction](#introduction)
2. [Catchpoint Architecture](#catchpoint-architecture)
3. [Core Concepts](#core-concepts)
4. [Synthetic Monitoring](#synthetic-monitoring)
5. [Real User Monitoring (RUM)](#real-user-monitoring-rum)
6. [Test Configuration](#test-configuration)
7. [Alerts & Notifications](#alerts--notifications)
8. [Dashboards & Reports](#dashboards--reports)
9. [Performance Analysis](#performance-analysis)
10. [the company Specific Configuration](#red-hat-specific-configuration)
11. [Integration with Other Tools](#integration-with-other-tools)
12. [Best Practices](#best-practices)
13. [Troubleshooting](#troubleshooting)

---

## Introduction

### What is Catchpoint?

**Catchpoint** is a digital experience monitoring platform that provides:
- **Synthetic Monitoring** - Proactive testing from global locations
- **Real User Monitoring (RUM)** - Actual user experience tracking
- **Network Insights** - BGP, DNS, CDN monitoring
- **API Monitoring** - REST, SOAP, GraphQL endpoint testing
- **Transaction Monitoring** - Multi-step user journey testing

### Catchpoint at the company

**the company uses Catchpoint for:**
- External service availability monitoring (customer-facing)
- API endpoint performance tracking
- Global latency monitoring from different regions
- SSL/TLS certificate expiration monitoring
- DNS resolution performance
- Third-party service dependency monitoring

**Access:**
- **URL:** https://portal.catchpoint.com (or the company-specific instance)
- **Authentication:** the company SSO
- **Teams:** Organized by product/service teams

### Why Catchpoint?

**Proactive Monitoring:**
- Detect issues **before** customers report them
- Simulate user journeys from different locations
- Validate external dependencies (CDNs, APIs, DNS)

**Global Visibility:**
- Test from 850+ global locations (cities worldwide)
- ISP diversity (Verizon, AT&T, Deutsche Telekom, etc.)
- Browser diversity (Chrome, Firefox, Safari, Edge)

**Complementary to Internal Monitoring:**
- **Internal:** Prometheus, SignalFx (infrastructure, apps)
- **External:** Catchpoint (customer experience, external endpoints)

---

## Catchpoint Architecture

### High-Level Overview

```
┌──────────────────────────────────────────────────────────────┐
│           Catchpoint Platform            │
│                                │
│ ┌──────────────┐  ┌──────────────┐  ┌──────────────┐   │
│ │ Synthetic  │  │   RUM   │  │  Network  │   │
│ │ Monitoring │  │  (Beacon)  │  │  Insights  │   │
│ └──────┬───────┘  └──────┬───────┘  └──────┬───────┘   │
│     │          │          │       │
│ ┌──────▼───────────────────▼───────────────────▼───────┐  │
│ │     Test Execution & Data Collection       │  │
│ └──────────────────────────┬────────────────────────────┘  │
│               │                 │
│ ┌──────────────────────────▼────────────────────────────┐  │
│ │  Alerts, Dashboards, Reports, Integrations      │  │
│ └───────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────┘
▲
│
┌─────────────┴─────────────┐
│  Global Test Nodes   │
│ (850+ locations worldwide)│
└───────────────────────────┘
▲
│
┌─────────────┴─────────────┐
│  Your Services/APIs   │
│ (External Endpoints)   │
└───────────────────────────┘
```

### Data Collection Methods

1. **Synthetic Tests** - Automated tests from Catchpoint nodes
2. **RUM Beacon** - JavaScript snippet on web pages
3. **Network Tests** - BGP, DNS, Traceroute
4. **API Tests** - HTTP/HTTPS requests to endpoints

---

## Core Concepts

### 1. Test Types

**Web Tests:**
- **Web - Page Load** - Full page load (HTML, CSS, JS, images)
- **Web - Transaction** - Multi-step user journey
- **Web - Single URL** - Simple HTTP GET request

**API Tests:**
- **API** - REST, SOAP, GraphQL endpoints
- **API - Multi-Step** - Chained API calls

**Network Tests:**
- **DNS** - DNS resolution time
- **Ping** - ICMP ping test
- **Traceroute** - Network path analysis
- **BGP** - Border Gateway Protocol monitoring

**SSL Tests:**
- **SSL Certificate** - Expiration and validity check

### 2. Test Nodes

**Node Types:**
- **Backbone** - Data center locations (fast, reliable)
- **Last Mile** - Residential ISPs (realistic user experience)
- **Mobile** - 3G/4G/5G networks
- **Enterprise** - the company internal nodes (optional)

**Node Selection:**
- Choose nodes based on user geography
- Test from multiple ISPs for diversity
- Balance cost vs. coverage

### 3. Metrics

**Performance Metrics:**
- **Response Time** - Total time to load resource
- **DNS Time** - DNS lookup duration
- **Connect Time** - TCP connection establishment
- **SSL Time** - TLS handshake duration
- **Wait Time** - Time to first byte (TTFB)
- **Content Download** - Time to download response body

**Availability Metrics:**
- **Success Rate** - Percentage of successful tests
- **Error Rate** - HTTP errors, timeouts, DNS failures

**Web Vitals (RUM):**
- **LCP (Largest Contentful Paint)** - Loading performance
- **FID (First Input Delay)** - Interactivity
- **CLS (Cumulative Layout Shift)** - Visual stability

### 4. Test Frequency

**Options:**
- **1 minute** - Critical endpoints (high cost)
- **5 minutes** - Standard monitoring
- **15 minutes** - Less critical endpoints
- **Hourly** - Periodic checks
- **On-demand** - Manual test execution

**Cost Consideration:**
- Higher frequency = more test runs = higher cost
- Balance monitoring needs with budget

---

## Synthetic Monitoring

### Web - Page Load Test

**Purpose:** Monitor full page load performance

**Configuration:**

**Basic Settings:**
- **Name:** `production - Homepage Load Time`
- **URL:** `https://production.company.internal`
- **Test Type:** Web - Page Load
- **Frequency:** 5 minutes

**Node Selection:**
- **Locations:** New York, London, Tokyo (customer regions)
- **ISP:** Verizon, AT&T (US), BT (UK), NTT (Japan)
- **Browser:** Chrome (Latest)

**Thresholds:**
- **Page Load Time:** <3 seconds (warning), >5 seconds (critical)
- **Availability:** <98% (critical)

**Advanced Options:**
- **User Agent:** Custom user agent string
- **Headers:** Custom HTTP headers (authentication, etc.)
- **Simulate Conditions:** Throttle bandwidth, add latency

**Use Case:**
Monitor customer-facing homepage from different global locations to detect regional performance issues.

---

### Web - Transaction Test

**Purpose:** Test multi-step user journeys

**Example: Login Flow**

**Steps:**
1. **Navigate to Login Page:** `https://sso.company.internal/login`
2. **Enter Username:** Input field `username`
3. **Enter Password:** Input field `password`
4. **Click Login Button:** Submit form
5. **Verify Dashboard:** Check for element `#dashboard`

**Configuration:**

**Transaction Script (Visual Editor or Selenium):**
```javascript
// Step 1: Navigate
await driver.get('https://sso.company.internal/login');

// Step 2: Enter credentials
await driver.findElement(By.id('username')).sendKeys('testuser');
await driver.findElement(By.id('password')).sendKeys('testpass');

// Step 3: Submit
await driver.findElement(By.id('submit')).click();

// Step 4: Verify success
await driver.wait(until.elementLocated(By.id('dashboard')), 10000);
```

**Validation:**
- Element exists: `#dashboard`
- URL contains: `/dashboard`
- Response code: 200

**Alert Conditions:**
- Transaction fails (any step)
- Total time >10 seconds

**Use Case:**
Ensure critical user flows (login, checkout, form submission) work globally.

---

### API Test

**Purpose:** Monitor REST API endpoints

**Example: API Endpoint Monitoring**

**Configuration:**

**Request:**
```http
GET https://api.production.company.internal/v1/health
```

**Headers:**
```
Authorization: Bearer <token>
Content-Type: application/json
```

**Expected Response:**
```json
{
"status": "healthy",
"version": "1.2.3"
}
```

**Validation:**
- HTTP Status Code: `200`
- Response Body Contains: `"status": "healthy"`
- Response Time: <500ms

**Thresholds:**
- Response Time: >500ms (warning), >1000ms (critical)
- Error Rate: >1% (critical)

**Use Case:**
Monitor API availability and performance for external integrations.

---

### DNS Test

**Purpose:** Monitor DNS resolution performance and correctness

**Configuration:**

**Domain:** `production.company.internal`

**DNS Server:** `8.8.8.8` (Google), `1.1.1.1` (Cloudflare), or ISP default

**Expected Result:**
- **A Record:** `192.0.2.10`
- **CNAME:** `cdn.company.internal`

**Thresholds:**
- DNS Resolution Time: >200ms (warning), >500ms (critical)
- DNS Failure: Alert immediately

**Use Case:**
Detect DNS propagation issues, verify DNS changes, monitor DNS provider performance.

---

### SSL Certificate Test

**Purpose:** Monitor SSL/TLS certificate expiration and validity

**Configuration:**

**URL:** `https://production.company.internal`

**Checks:**
- **Certificate Expiration:** Alert if <30 days
- **Certificate Validity:** Verify chain of trust
- **Protocol Version:** TLS 1.2+ required
- **Cipher Strength:** Strong ciphers only

**Thresholds:**
- Expires in <30 days: Warning
- Expires in <7 days: Critical
- Invalid certificate: Critical

**Use Case:**
Prevent certificate expiration outages (happened to many major companies).

---

## Real User Monitoring (RUM)

### RUM Beacon Setup

**Purpose:** Collect real user performance data

**Implementation:**

**Step 1: Add JavaScript Beacon**

Add to `<head>` of HTML pages:
```html
<script src="https://rum-collector.catchpoint.com/beacon.js"></script>
<script>
window.CPRUM = window.CPRUM || [];
CPRUM.push(['init', {
appKey: 'YOUR_APP_KEY',
version: '1.2.3',
customData: {
userId: 'user123',
region: 'us-east'
}
}]);
</script>
```

**Step 2: Configure Collection**

**Metrics Collected:**
- Page load times
- DNS, Connect, SSL times
- Resource load times (CSS, JS, images)
- Web Vitals (LCP, FID, CLS)
- JavaScript errors
- AJAX call performance

**Step 3: Define Sessions**

**Session Attributes:**
- **User ID** - Identify individual users
- **Session ID** - Group page views into sessions
- **Custom Dimensions** - Product, feature, A/B test variant

---

### RUM Analysis

**Accessing RUM Data:**

1. **Navigate to RUM** → **Applications**
2. Select your application (e.g., `production Web App`)
3. View dashboards and drill-down reports

**Key Reports:**

**Performance Overview:**
- Median, P75, P95, P99 page load times
- Trend over time
- Comparison: Before vs. After deployment

**Geographic Distribution:**
- Page load time by country/city
- Identify slow regions

**Browser/Device Breakdown:**
- Performance by browser (Chrome, Firefox, Safari)
- Performance by device type (Desktop, Mobile, Tablet)

**Page-Level Analysis:**
- Which pages are slowest?
- Most visited pages
- Bounce rate by page

**Error Tracking:**
- JavaScript errors
- Failed AJAX calls
- Console errors

---

### Web Vitals Monitoring

**Core Web Vitals:**

**1. LCP (Largest Contentful Paint)**
- **Good:** <2.5 seconds
- **Needs Improvement:** 2.5-4.0 seconds
- **Poor:** >4.0 seconds

**What it measures:** Loading performance

**2. FID (First Input Delay)**
- **Good:** <100ms
- **Needs Improvement:** 100-300ms
- **Poor:** >300ms

**What it measures:** Interactivity (delay from user click to browser response)

**3. CLS (Cumulative Layout Shift)**
- **Good:** <0.1
- **Needs Improvement:** 0.1-0.25
- **Poor:** >0.25

**What it measures:** Visual stability (unexpected layout shifts)

**Catchpoint Dashboard:**
- View Web Vitals scores
- Trend analysis (improving or degrading?)
- Alert when thresholds exceeded

---

## Test Configuration

### Creating a Test

**Step 1: Navigate to Tests**
1. **Tests** → **New Test**

**Step 2: Select Test Type**
- Web, API, DNS, SSL, etc.

**Step 3: Configure Test**

**Example: API Test**

**General:**
- **Name:** `production API - Health Check`
- **Division:** `production Team`
- **Product:** `production`
- **Folder:** `Production Endpoints`

**Request:**
- **URL:** `https://api.production.company.internal/v1/health`
- **Method:** `GET`
- **Headers:** Authorization, Content-Type

**Validation:**
- **Status Code:** `200`
- **Response Time:** <500ms
- **Response Body:** Contains `"status": "healthy"`

**Schedule:**
- **Frequency:** 5 minutes
- **Nodes:** 3 locations (New York, London, Singapore)

**Alerts:**
- **Threshold:** 2 consecutive failures
- **Recipients:** production SRE team
- **Notification:** Email, PagerDuty

---

### Advanced Configuration

**Custom Headers:**
```http
Authorization: Bearer eyJhbGc...
X-API-Key: 1234567890
X-Request-ID: test-12345
```

**POST Request with Body:**
```json
{
"action": "test",
"parameters": {
"foo": "bar"
}
}
```

**Response Validation (Regex):**
```regex
"status":\s*"healthy"
"version":\s*"\d+\.\d+\.\d+"
```

**Custom Metrics:**
- Extract values from response body
- Track over time
- Alert on thresholds

**Example:**
```json
{
"queue_depth": 42
}
```

Extract `queue_depth` value → Chart trend → Alert if >100

---

## Alerts & Notifications

### Creating Alerts

**Step 1: Test Settings → Alerts**

**Alert Conditions:**

**Threshold-Based:**
- Response time >1000ms
- Availability <99%
- Error rate >5%

**Consecutive Failures:**
- **2 consecutive failures** - Transient issues filtered
- **3 consecutive failures** - High confidence in real issue

**Availability:**
- Alert when availability <98% over 15 minutes

**Alert Levels:**
- **Critical** - Immediate action required
- **Warning** - Monitor closely
- **Informational** - FYI only

---

### Notification Channels

**Email:**
- Send to team distribution list
- Include test details, chart, link to Catchpoint

**PagerDuty:**
- Integrate with PagerDuty service
- Create incident when critical alert fires

**Slack:**
- Post to channel (e.g., #production-alerts)
- Include performance charts

**Webhook:**
- POST alert data to custom endpoint
- Trigger custom automation

**SMS:**
- For critical alerts only (high cost)

---

### Alert Suppression

**Maintenance Windows:**
- Suppress alerts during planned maintenance
- Schedule: Start time, end time
- Affected tests: All or specific tests

**Example:**
```
Maintenance: Database Migration
Start: 2026-03-20 02:00 UTC
End: 2026-03-20 04:00 UTC
Suppress: All production API tests
```

---

## Dashboards & Reports

### Creating Dashboards

**Step 1: Dashboards → New Dashboard**

**Dashboard Components:**

**1. Charts**
- Line chart: Response time trend
- Bar chart: Availability by location
- Pie chart: Error distribution

**2. Scorecards**
- Single metric display (Availability: 99.5%)
- Color-coded (green/yellow/red)

**3. Tables**
- Test results by location
- Top 10 slowest tests

**4. Maps**
- Geographic performance visualization
- Heatmap of latency

---

### Dashboard Example: production Service Health

**Row 1: Key Metrics**
- Scorecard: Availability (99.8%)
- Scorecard: Avg Response Time (450ms)
- Scorecard: Error Rate (0.2%)

**Row 2: Trends**
- Line Chart: Response time (last 24h)
- Line Chart: Availability (last 7d)

**Row 3: Geographic**
- Map: Performance by region
- Table: Response time by city

**Row 4: Details**
- Table: Active alerts
- Table: Recent test failures

---

### Reports

**Scheduled Reports:**

**Configuration:**
- **Frequency:** Daily, weekly, monthly
- **Recipients:** Team email list
- **Format:** PDF, CSV, Excel
- **Content:** Performance summary, SLA metrics, top issues

**Example Weekly Report:**
```
production API Performance Report
Week of March 13-19, 2026

Summary:
- Availability: 99.7% (target: 99.5%) 
- Avg Response Time: 420ms (target: <500ms) 
- Total Tests: 2,016
- Failures: 6

Top Issues:
1. London node timeout (3 occurrences)
2. Tokyo slow response (2 occurrences)

Recommendations:
- Investigate London ISP routing
- Review Tokyo CDN configuration
```

---

## Performance Analysis

### Waterfall Analysis

**Purpose:** Identify performance bottlenecks in page load

**Waterfall Chart Shows:**
1. **DNS Lookup** - Time to resolve domain
2. **TCP Connection** - Time to establish connection
3. **SSL Handshake** - TLS negotiation time
4. **Request Sent** - Time to send HTTP request
5. **Waiting (TTFB)** - Time to first byte from server
6. **Content Download** - Time to download response
7. **Resource Loading** - CSS, JS, images, fonts

**Example Analysis:**

**Slow Page Load: 8 seconds**

**Waterfall shows:**
- DNS: 50ms 
- Connect: 100ms 
- SSL: 200ms 
- Wait (TTFB): 5000ms **BOTTLENECK**
- Download: 2650ms

**Root Cause:** Slow server response (TTFB = 5s)

**Action:** Optimize backend query, add caching

---

### Performance Comparison

**Before/After Deployment:**

**Scenario:** Deploy new caching layer

**Before:**
- Avg Response Time: 2500ms
- P95 Response Time: 4000ms

**After:**
- Avg Response Time: 300ms (88% improvement)
- P95 Response Time: 500ms (87.5% improvement)

**Catchpoint Feature:**
- Compare time periods
- Overlay charts (before vs. after)
- Calculate % change

---

### Geographic Performance Analysis

**Problem:** Users in APAC report slow performance

**Catchpoint Analysis:**

**Test Results by Region:**
- **Americas:** 300ms 
- **EMEA:** 400ms 
- **APAC:** 2500ms 

**Drill-Down (APAC):**
- **Tokyo:** 600ms 
- **Singapore:** 800ms 
- **Sydney:** 3500ms **ISSUE**

**Root Cause:** No CDN edge in Australia

**Solution:** Deploy CloudFront edge location in Sydney

**After Fix:**
- **Sydney:** 400ms 

---

## the company Specific Configuration

### production Service Monitoring

**Key Endpoints to Monitor:**

**1. Public API**
- **URL:** `https://api.production.company.internal/v1/health`
- **Frequency:** 5 minutes
- **Locations:** Global (US, EU, APAC)
- **Alert:** Availability <99%

**2. Web Console**
- **URL:** `https://console.production.company.internal`
- **Type:** Web - Page Load
- **Frequency:** 15 minutes
- **Locations:** New York, London, Singapore
- **Alert:** Load time >5 seconds

**3. Authentication Endpoint**
- **URL:** `https://sso.company.internal/auth/realms/production`
- **Type:** API
- **Frequency:** 5 minutes
- **Alert:** Response time >1000ms

**4. SSL Certificate**
- **URL:** `https://production.company.internal`
- **Type:** SSL Certificate
- **Frequency:** Daily
- **Alert:** Expires in <30 days

---

### OPS Infrastructure Monitoring

**Service-C API:**
- **URL:** `https://service-cweb.engineering.company.internal/service-c/api`
- **Test:** API health check
- **Frequency:** 10 minutes

**Dist-Git:**
- **URL:** `https://service-b.devel.company.internal`
- **Test:** Web - Page Load
- **Frequency:** 15 minutes

**Service-A (Module Build Service):**
- **URL:** `https://service-a.engineering.company.internal/module-build-service/1/about`
- **Test:** API
- **Frequency:** 10 minutes

---

### Integration with the company Monitoring Stack

**Catchpoint → PagerDuty:**
- Critical alerts create PagerDuty incidents
- On-call engineer notified

**Catchpoint → Slack:**
- Post alerts to #production-alerts, #ops-monitoring
- Include performance charts

**Catchpoint → Jira:**
- Auto-create tickets for persistent issues
- Track remediation

**Catchpoint Data → Grafana:**
- Export metrics to Prometheus
- Visualize alongside internal metrics

---

## Integration with Other Tools

### PagerDuty Integration

**Setup:**
1. **Settings** → **Integrations** → **PagerDuty**
2. Enter PagerDuty Integration Key
3. Map alert severities to PagerDuty urgencies
4. Test integration

**Alert Flow:**
```
Catchpoint Test Fails
↓
Alert Triggered (Critical)
↓
PagerDuty Incident Created
↓
On-Call Engineer Notified
```

---

### Slack Integration

**Setup:**
1. Create Slack webhook URL
2. **Settings** → **Integrations** → **Slack**
3. Configure message template

**Message Template:**
```
🚨 Catchpoint Alert: {{testName}}

Status: {{status}}
Location: {{nodeName}}
Response Time: {{responseTime}}ms
Error: {{errorMessage}}

View: {{testUrl}}
```

---

### Prometheus Exporter (If Available)

**Export Metrics:**
- Response time
- Availability
- Error count

**Prometheus Query:**
```promql
catchpoint_response_time{test="production-api-health"} > 1000
```

**Grafana Dashboard:**
- Combine Catchpoint (external) + SignalFx (internal) metrics
- Unified view of service health

---

## Best Practices

### 1. Test Coverage

**Cover Critical User Journeys:**
- Homepage load
- Login flow
- API health checks
- Key transactions (checkout, form submission)

**Geographic Diversity:**
- Test from regions where users are located
- Include emerging markets (APAC, LATAM)

**ISP Diversity:**
- Mix backbone (fast) and last-mile (realistic) nodes
- Test from major ISPs (Verizon, AT&T, etc.)

---

### 2. Alert Configuration

**Avoid Alert Fatigue:**
- Use consecutive failures (2-3) to filter transients
- Set realistic thresholds based on baselines
- Suppress alerts during maintenance

**Severity Levels:**
- **Critical:** Service down, <95% availability
- **Warning:** Degraded performance, >500ms response time
- **Info:** Minor issues, trend monitoring

---

### 3. Cost Optimization

**Balance Frequency vs. Cost:**
- Critical endpoints: 5 minutes
- Standard endpoints: 15 minutes
- Non-critical: Hourly

**Node Selection:**
- Use minimum nodes needed for coverage
- Backbone nodes cheaper than last-mile

**Deactivate Unused Tests:**
- Review quarterly
- Disable tests for decommissioned services

---

### 4. Performance Baselines

**Establish Baselines:**
- Run tests for 2 weeks
- Calculate P50, P95, P99
- Set alert thresholds above baseline + margin

**Example:**
```
Baseline P95: 400ms
Alert Threshold: 500ms (P95 + 25%)
Critical Threshold: 800ms (P95 + 100%)
```

---

### 5. Regular Reviews

**Monthly Review:**
- Test coverage (all critical endpoints monitored?)
- Alert effectiveness (signal vs. noise ratio)
- Performance trends (improving or degrading?)

**Quarterly Cleanup:**
- Remove obsolete tests
- Update alert thresholds
- Optimize node selection

---

## Troubleshooting

### Issue 1: High False Positive Rate

**Symptoms:**
- Alerts firing frequently
- Tests pass on retry
- No real customer impact

**Debugging:**

1. **Check Node Stability:**
- Are failures from specific node?
- Switch to different node/ISP

2. **Increase Consecutive Failures:**
- Change from 1 → 2 or 3 consecutive failures

3. **Review Thresholds:**
- Are thresholds too aggressive?
- Relax thresholds based on baseline

**Solution:**
- Use 2-3 consecutive failures
- Exclude unreliable nodes
- Set thresholds at P95 + buffer

---

### Issue 2: Test Results Don't Match Internal Monitoring

**Symptoms:**
- Catchpoint shows slow performance
- Internal monitoring (SignalFx) shows normal

**Possible Causes:**

1. **Geographic Difference:**
- Catchpoint tests from external locations
- Internal monitoring from data center

2. **Network Path:**
- External tests traverse internet, CDN
- Internal tests on private network

3. **Caching:**
- Internal requests bypass CDN
- External requests hit CDN (cache miss)

**Analysis:**
- Compare test locations (Catchpoint node vs. internal)
- Review network path (traceroute)
- Check CDN performance

---

### Issue 3: SSL Certificate Alert Not Firing

**Symptoms:**
- Certificate expiring soon
- No alert received

**Debugging:**

1. **Check Test Configuration:**
- SSL test enabled?
- Expiration threshold set (e.g., <30 days)?

2. **Check Alert Settings:**
- Alert configured for SSL test?
- Recipients correct?

3. **Manual Test:**
- Run SSL test on-demand
- Verify expiration date detected

**Solution:**
- Enable SSL certificate test
- Set alert threshold to 30 days
- Test alert notification

---

### Issue 4: RUM Data Missing

**Symptoms:**
- No RUM data appearing in dashboard
- Beacon script loaded but no metrics

**Debugging:**

1. **Check Beacon Loading:**
- Open browser DevTools → Network
- Verify beacon.js loaded (200 OK)

2. **Check Beacon Calls:**
- DevTools → Network → Filter: catchpoint
- Verify POST requests to rum-collector

3. **Check Configuration:**
- App Key correct?
- Domain whitelisted?

4. **Check Ad Blockers:**
- Ad blockers may block RUM beacon
- Test in incognito mode

**Solution:**
- Verify app key in beacon code
- Whitelist domain in Catchpoint settings
- Document ad blocker impact (expected data loss)

---

## Summary

### Key Takeaways

1. **Catchpoint = External Monitoring**
- Proactive testing from global locations
- Complements internal monitoring (SignalFx, Prometheus)

2. **Synthetic Monitoring:**
- Test critical endpoints, user journeys
- Alert before customers notice issues

3. **Real User Monitoring:**
- Actual user experience data
- Web Vitals, page load times, errors

4. **Global Coverage:**
- 850+ test nodes worldwide
- ISP and geographic diversity

5. **Integration:**
- PagerDuty (incident management)
- Slack (team notifications)
- Jira (issue tracking)

### Next Steps

**Beginner:**
1. Log into Catchpoint portal
2. View existing tests for your service
3. Understand test results (waterfall, metrics)
4. Subscribe to alert notifications

**Intermediate:**
5. Create new test (API or page load)
6. Configure alerts with thresholds
7. Build custom dashboard
8. Set up PagerDuty integration

**Advanced:**
9. Implement RUM for web applications
10. Create transaction tests for critical flows
11. Analyze performance trends
12. Optimize test coverage and costs

---

## References

- **Catchpoint Documentation:** https://support.catchpoint.com
- **Catchpoint API Reference:** https://io.catchpoint.com/ui/Content/api/rest-api.aspx
- **Web Vitals:** https://web.dev/vitals/
- **the company Internal Wiki:** [Link to the company Catchpoint documentation]
- **SSL Certificate Best Practices:** https://www.ssl.com/guide/ssl-certificate-expiration-monitoring/

---

**Last Updated:** 2026-03-19
**Author:** Infrastructure Team (the company OPS Team)
**Audience:** the company SRE teams
**License:** Internal the company use
