# PagerDuty Learning Plan

**Comprehensive guide to PagerDuty for incident management and on-call operations at Company**

---

## Table of Contents

1. [Introduction](#introduction)
2. [PagerDuty Architecture](#pagerduty-architecture)
3. [Core Concepts](#core-concepts)
4. [Services & Integrations](#services--integrations)
5. [Incidents & Alerts](#incidents--alerts)
6. [On-Call Schedules](#on-call-schedules)
7. [Escalation Policies](#escalation-policies)
8. [Response Plays & Automation](#response-plays--automation)
9. [Postmortems & Analytics](#postmortems--analytics)
10. [PagerDuty MCP Server](#pagerduty-mcp-server)
11. [Company Specific Configuration](#red-hat-specific-configuration)
12. [Best Practices](#best-practices)
13. [Troubleshooting](#troubleshooting)

---

## Introduction

### What is PagerDuty?

**PagerDuty** is an incident management platform that connects monitoring tools, orchestrates on-call schedules, and automates incident response workflows.

**Key Capabilities:**
- **Incident Management** - Centralized alert aggregation and routing
- **On-Call Scheduling** - Manage who's on-call, when
- **Escalation Policies** - Automatic escalation if no response
- **Response Orchestration** - Runbooks, stakeholder notifications
- **Analytics** - Incident trends, MTTA/MTTR metrics
- **Integrations** - 700+ integrations (monitoring, chat, ticketing)

### PagerDuty at Company

**Company uses PagerDuty for:**
- Platform platform on-call rotation
- SRE team infrastructure alerts
- Service reliability incident management
- Multi-team escalation workflows
- Incident analytics and reporting

**Access:**
- **URL:** https://company.pagerduty.com
- **Authentication:** Company SSO
- **Mobile App:** iOS/Android for on-call engineers

---

## PagerDuty Architecture

### High-Level Flow

```

 Monitoring/Alerting Tools 
 (SignalFx, Prometheus, Datadog, CloudWatch, etc.) 

 
 Send Alert
 

 PagerDuty 
 
 
 Service > Incident > Escalation 
 (Integration) (Created) Policy 
 
 
 
 
 On-Call 
 Schedule 
 

 
 
 
 On-Call Engineer 
 (SMS, Push, Phone) 
 
```

### Key Components

1. **Services** - Represent a technical service (API, database, etc.)
2. **Incidents** - Triggered alerts requiring response
3. **On-Call Schedules** - Who is on-call, rotation patterns
4. **Escalation Policies** - Who to notify and when
5. **Integrations** - Inbound alerts from monitoring tools
6. **Response Plays** - Automated workflows for incidents
7. **Users & Teams** - Engineer accounts and team organization

---

## Core Concepts

### 1. Services

**What is a Service?**
- Represents a technical component (e.g., Platform API, Brew Database)
- Receives alerts from integrations
- Routes incidents to on-call engineers

**Service Components:**
- **Name:** `Platform - API Server`
- **Integration:** SignalFx, Prometheus, etc.
- **Escalation Policy:** Who to notify
- **Description:** What the service does

**Example:**
```
Service: Platform Pipeline Service
Integration: SignalFx (via API)
Escalation Policy: Platform SRE Primary
On-Call: John Doe (current)
```

### 2. Incidents

**Incident Lifecycle:**
```
Triggered → Acknowledged → Resolved
```

**States:**
- **Triggered** - New alert, needs attention
- **Acknowledged** - On-call engineer is working on it
- **Resolved** - Issue fixed, incident closed

**Incident Details:**
- **Title:** Alert description
- **Source:** Monitoring tool that sent alert
- **Urgency:** High or Low
- **Timeline:** Created, acknowledged, resolved times
- **Responders:** Who was notified

### 3. On-Call Schedules

**Schedule Types:**

**Primary On-Call:**
- First responder for all incidents
- 24/7 or business hours only
- Rotation: Weekly, daily, or custom

**Secondary/Backup:**
- Escalation target if primary doesn't respond
- Coverage for vacation/sick days

**Example Schedule:**
```
Week 1: Alice (Primary), Bob (Secondary)
Week 2: Bob (Primary), Charlie (Secondary)
Week 3: Charlie (Primary), Alice (Secondary)
```

### 4. Escalation Policies

**Purpose:** Define who gets notified and when

**Example Escalation:**
```
Level 1: On-Call Engineer (Primary) - Immediate
 > If no ACK in 5 minutes

Level 2: On-Call Engineer (Secondary) - 5 minutes later
 > If no ACK in 5 minutes

Level 3: Team Lead - 10 minutes later
 > If no ACK in 10 minutes

Level 4: Manager - 20 minutes later
```

### 5. Notification Rules

**User Notification Preferences:**
- **Phone Call** - Most urgent, wakes you up
- **SMS** - Text message
- **Push Notification** - Mobile app
- **Email** - Least urgent

**Notification Timing:**
```
Immediately: Push notification + SMS
5 minutes: Phone call (if not acknowledged)
10 minutes: Escalate to next level
```

---

## Services & Integrations

### Creating a Service

**Step 1: Navigate to Services**
1. **Services** → **Service Directory** → **New Service**

**Step 2: Configure Service**
- **Name:** `Platform - API Server`
- **Description:** `Monitors Platform API server health and performance`
- **Escalation Policy:** Select existing policy (e.g., `Platform SRE`)

**Step 3: Add Integration**
- **Integration Type:** SignalFx, Prometheus, Email, API
- **Integration Key:** Unique key for sending alerts

### Integration Types

**Monitoring Tools:**
- SignalFx / Splunk Observability
- Prometheus / Alertmanager
- Datadog
- New Relic
- CloudWatch

**Ticketing Systems:**
- Jira (bidirectional sync)
- ServiceNow

**Communication:**
- Slack (send updates to channel)
- Email (parse email alerts)

**API Integration:**
- Send alerts programmatically
- Custom scripts or tools

### SignalFx Integration Example

**In SignalFx:**
1. Create Detector (e.g., CPU > 80%)
2. Set Notification to PagerDuty
3. Use Integration Key from PagerDuty service

**Alert Flow:**
```
SignalFx Detector fires
 ↓
Sends alert to PagerDuty Integration Key
 ↓
PagerDuty creates Incident on Service
 ↓
Escalation Policy notifies On-Call Engineer
```

### Email Integration

**Use Case:** Parse email alerts from legacy systems

**Setup:**
1. Service → Add Integration → Email
2. Get unique email address: `service-xyz@company.pagerduty.com`
3. Configure monitoring tool to send alerts to that email

**Email Parsing:**
- Subject → Incident title
- Body → Incident description
- PagerDuty auto-extracts key details

---

## Incidents & Alerts

### Incident Management

**Viewing Incidents:**
1. **Incidents** → **All Incidents**
2. Filter: Status (Triggered, Acknowledged), Service, User

**Incident Actions:**

**Acknowledge:**
- Click **Acknowledge** on incident
- Stops escalation
- Signals: "I'm working on it"

**Reassign:**
- Assign to another engineer
- Use case: Handoff or expertise needed

**Resolve:**
- Click **Resolve** when issue fixed
- Incident closed

**Snooze:**
- Temporarily mute incident
- Auto-reopen after snooze period
- Use case: Waiting for deployment, monitoring

### Alert Grouping & Deduplication

**Problem:** Alert storms (100+ alerts in 1 minute)

**Solutions:**

**1. Alert Grouping (Intelligent)**
- Group similar alerts into single incident
- Example: 50 "CPU High" alerts → 1 incident

**2. Deduplication**
- Suppress duplicate alerts
- Dedup Key: Unique identifier (hostname, metric)

**Configuration:**
```
Service → Settings → Alert Grouping
- Intelligent: PagerDuty auto-groups similar alerts
- Time-based: Group alerts within X minutes
- Content-based: Group by custom field
```

### Incident Notes & Collaboration

**Timeline:**
- Automatic: ACK, reassign, resolve events
- Manual: Add notes during investigation

**Example Timeline:**
```
10:00 AM - Incident triggered (CPU > 80%)
10:01 AM - Alice acknowledged
10:02 AM - Alice: "Checking deployment logs"
10:05 AM - Alice: "Found memory leak in new version"
10:10 AM - Alice: "Rolling back deployment"
10:15 AM - Incident resolved
```

**Collaboration:**
- **Add Responders:** Invite other engineers to incident
- **Conference Bridge:** Auto-create Zoom/conference call
- **Slack Updates:** Sync incident to Slack channel

---

## On-Call Schedules

### Creating a Schedule

**Step 1: Navigate to Schedules**
1. **People** → **On-Call Schedules** → **New Schedule**

**Step 2: Configure Rotation**

**Name:** `Platform SRE - Primary`

**Time Zone:** `America/New_York` (or team's primary timezone)

**Rotation Type:**

**Weekly Rotation:**
```
Every week starting Monday 9:00 AM
Handoff time: Monday 9:00 AM
Rotation order: Alice → Bob → Charlie → Alice...
```

**Daily Rotation (Follow-the-Sun):**
```
Alice: 9 AM - 5 PM EST (Americas)
Bob: 5 PM - 1 AM EST (EMEA)
Charlie: 1 AM - 9 AM EST (APAC)
```

**Custom Rotation:**
- Specify exact dates/times
- Use case: Irregular schedules

### Overrides

**Temporary Changes:**

**Vacation Override:**
```
Alice: March 10-14 (on vacation)
Override: Bob covers Alice's shift
```

**How to Add:**
1. Schedule → Click time slot
2. **Create Override**
3. Select replacement engineer
4. Set start/end times

### Schedule Layers

**Multiple Layers for Coverage:**

**Layer 1: Primary On-Call (24/7)**
- Weekly rotation
- Engineers: Alice, Bob, Charlie

**Layer 2: Business Hours Only (9 AM - 5 PM)**
- Weekday coverage
- Additional engineers: Dave, Eve

**Layer 3: Weekends**
- Separate weekend rotation
- Engineers: Frank, Grace

**Result:** At any time, PagerDuty knows who's on-call by checking all active layers.

---

## Escalation Policies

### Creating an Escalation Policy

**Step 1: Navigate to Escalation Policies**
1. **People** → **Escalation Policies** → **New Escalation Policy**

**Step 2: Configure Levels**

**Example: Platform SRE Escalation**

**Level 1: Primary On-Call**
- **Target:** Schedule: `Platform SRE - Primary`
- **Escalation Timeout:** 5 minutes
- **Notification:** Immediately via SMS + Push

**Level 2: Secondary On-Call**
- **Target:** Schedule: `Platform SRE - Secondary`
- **Escalation Timeout:** 5 minutes
- **Notification:** If no ACK after 5 minutes

**Level 3: Team Lead**
- **Target:** User: `[Team Lead Name]`
- **Escalation Timeout:** 10 minutes
- **Notification:** If no ACK after 10 minutes

**Level 4: Manager**
- **Target:** User: `[Manager Name]`
- **Notification:** If no ACK after 20 minutes

### Escalation Policy Best Practices

**1. Balance Urgency vs. Noise**
- Critical alerts: Short timeout (5 min)
- Warning alerts: Longer timeout (15 min)

**2. Don't Over-Escalate**
- Most incidents resolved by primary on-call
- Escalation for true emergencies

**3. Define Clear Severity Levels**
- **Severity 1 (SEV1):** Immediate escalation
- **Severity 2 (SEV2):** Standard escalation
- **Severity 3 (SEV3):** Business hours only

**4. Test Escalations**
- Periodically test alert flow
- Verify notifications reach correct people

---

## Response Plays & Automation

### Response Plays

**What is a Response Play?**
- Pre-defined workflow for incident response
- Auto-executes actions when triggered

**Example: SEV1 Incident Response Play**

**Trigger:** Incident with urgency = High

**Actions:**
1. **Add Responders:** Notify team lead, manager
2. **Create Conference Bridge:** Auto-create Zoom link
3. **Send Slack Message:** Post to #platform-incidents channel
4. **Create Jira Ticket:** Auto-create incident ticket
5. **Send Status Page Update:** Notify customers (if applicable)

**Configuration:**
1. **Incident Workflows** → **Response Plays** → **New Play**
2. Define trigger conditions
3. Add actions
4. Assign to service

### Status Updates

**Stakeholder Communication:**

**During Incident:**
- Send updates to Slack channel
- Update status page
- Notify management

**Example Status Update:**
```
10:05 AM - Investigating: Platform API latency spike detected
10:15 AM - Identified: Database query causing slowdown
10:25 AM - Monitoring: Optimized query, monitoring for stability
10:35 AM - Resolved: API latency back to normal
```

### Runbooks

**Link Runbooks to Services:**

**Service Settings:**
```
Service: Platform API Server
Runbook URL: https://wiki.company.com/platform-api-runbooks
```

**Incident Screen Shows:**
- Runbook link prominently displayed
- On-call engineer clicks → Opens runbook
- Follows documented steps

**Runbook Content:**
- Troubleshooting steps
- Common failure scenarios
- Escalation contacts
- Known workarounds

---

## Postmortems & Analytics

### Postmortem (Post-Incident Review)

**After Resolving Incident:**

1. **Click incident** → **Run Postmortem**
2. **Fill template:**
 - **What happened?** Summary of incident
 - **Why did it happen?** Root cause
 - **What was the impact?** Services affected, duration
 - **How was it resolved?** Actions taken
 - **What can we improve?** Action items

**Example Postmortem:**
```
Incident: Platform API Latency Spike (2026-03-19)

What happened:
- API response time increased from 200ms to 5000ms
- 25% of requests timing out

Root Cause:
- Database query missing index
- Recent schema change introduced N+1 query

Impact:
- 30 minutes downtime
- 1000+ users affected

Resolution:
- Added database index
- Optimized query
- Deployed fix

Action Items:
1. Add index creation to schema migration checklist
2. Implement query performance tests in CI/CD
3. Create alert for database query duration
```

### Analytics Dashboard

**Navigate:** **Analytics** → **Incidents**

**Key Metrics:**

**MTTA (Mean Time to Acknowledge):**
- How quickly on-call responds
- Target: <5 minutes

**MTTR (Mean Time to Resolve):**
- How quickly incidents are fixed
- Target: <30 minutes (varies by severity)

**Incident Volume:**
- Incidents per day/week
- Trend: Increasing or decreasing?

**Top Services by Incidents:**
- Which services are noisiest?
- Focus improvement efforts

**On-Call Health:**
- How many incidents per on-call shift?
- Burnout risk if >10 incidents/week

**Charts:**
- Incident trend over time
- MTTA/MTTR by service
- Incident volume by severity
- On-call load distribution

### Reports

**Weekly/Monthly Reports:**

1. **Analytics** → **Reports**
2. Select date range
3. Export CSV or PDF

**Includes:**
- Total incidents
- MTTA/MTTR
- Top services
- Escalation rate

**Use Cases:**
- Team retrospectives
- Management reporting
- Capacity planning

---

## PagerDuty MCP Server

### Overview

**PagerDuty MCP Server** (if available at Company) provides programmatic access to PagerDuty via Model Context Protocol.

**Capabilities:**
- Query incidents
- Acknowledge/resolve incidents
- Get on-call schedule
- Retrieve escalation policies
- Create incidents programmatically

### Example MCP Tools (Hypothetical)

**Tool 1: Get Current Incidents**
```python
mcp.call("pagerduty.get_incidents",
 status="triggered",
 service="Platform API Server")
```

**Tool 2: Get On-Call Engineer**
```python
mcp.call("pagerduty.get_oncall",
 escalation_policy="Platform SRE Primary")
```

**Tool 3: Acknowledge Incident**
```python
mcp.call("pagerduty.acknowledge_incident",
 incident_id="PXYZ123")
```

**Tool 4: Create Incident**
```python
mcp.call("pagerduty.create_incident",
 service="Platform API Server",
 title="Manual escalation: Database connection pool exhausted",
 urgency="high")
```

### Using PagerDuty MCP with Claude Code

**Example: On-Call Investigation**

**User:** "Who's currently on-call for Platform?"

**Claude (using PagerDuty MCP):**
```python
# Get on-call engineer
oncall = mcp.call("pagerduty.get_oncall",
 escalation_policy="Platform SRE Primary")

# Get active incidents
incidents = mcp.call("pagerduty.get_incidents",
 status="triggered",
 service="Platform API Server")

# Response
print(f"On-Call: {oncall['user']['name']}")
print(f"Active Incidents: {len(incidents)}")
for inc in incidents:
 print(f" - {inc['title']} (since {inc['created_at']})")
```

**Result:** Instant visibility into on-call status and active incidents without opening PagerDuty UI.

---

## Company Specific Configuration

### Platform On-Call Setup

**Services:**
- `Platform - API Server`
- `Platform - Pipeline Service`
- `Platform - Database`
- `RHDH - Company Developer Hub`

**Schedule:**
- **Primary:** Weekly rotation, 24/7 coverage
- **Secondary:** Backup engineer
- **Time Zone:** UTC (global team) or EST (Americas-heavy)

**Escalation Policy:**
```
Level 1: Platform Primary On-Call (0 min)
Level 2: Platform Secondary On-Call (5 min)
Level 3: Platform Team Lead (10 min)
Level 4: Platform Manager (20 min)
```

### SRE Team On-Call

**Services:**
- `Brew - Build Infrastructure`
- `Dist-Git - Repository Service`
- `MBS - Module Build Service`
- `RHSM-Pulp - Subscription Management`

**Schedule:**
- **Business Hours:** 9 AM - 5 PM EST, weekdays
- **After-Hours:** Escalation to manager for critical issues

**Escalation Policy:**
```
Level 1: SRE Primary On-Call (0 min, business hours only)
Level 2: SRE Manager (5 min, after-hours critical alerts)
```

### Integration with Company Tools

**SignalFx/Splunk Observability:**
- Detectors send alerts to PagerDuty services
- Integration key per service

**Prometheus/Alertmanager:**
- Alertmanager routes to PagerDuty via webhook
- Severity mapped to PagerDuty urgency

**Jira Integration:**
- PagerDuty incident → Auto-create Jira ticket
- Bidirectional sync: Updates in Jira reflect in PagerDuty

**Slack Integration:**
- Incident notifications to #platform-incidents, #spre-alerts
- Acknowledge/resolve incidents from Slack

### Company SSO

**Login:** https://company.pagerduty.com

**Authentication:** Company corporate SSO

**User Provisioning:** Automatic via LDAP/Active Directory

---

## Best Practices

### 1. Service Organization

**Naming Convention:**
```
[Team/Product] - [Component]

Examples:
- Platform - API Server
- SRE - Brew Builders
- RHDH - PostgreSQL Database
```

**Service Hierarchy:**
- Parent Service: `Platform Platform`
- Child Services: `Platform API`, `Platform Pipelines`, `Platform DB`

### 2. Alert Hygiene

**Reduce Noise:**
- Only page for actionable alerts
- If alert doesn't require immediate action → Don't page

**Set Appropriate Urgency:**
- **High:** Service down, data loss, security breach
- **Low:** Warning, degraded performance (non-critical)

**Deduplication:**
- Use dedup keys to prevent alert storms
- Group related alerts into single incident

### 3. On-Call Rotation Best Practices

**Fair Distribution:**
- Rotate fairly among team members
- Avoid burnout (max 1 week on-call, then 3+ weeks off)

**Handoff Process:**
- Scheduled handoff time (e.g., Monday 9 AM)
- Outgoing engineer briefs incoming engineer
- Document ongoing incidents

**PTO Coverage:**
- Use overrides for vacation/sick days
- Ensure backup engineer is aware

### 4. Escalation Policy Design

**Goal:** Resolve incidents at lowest level

**Guidelines:**
- Primary on-call resolves 80%+ of incidents
- Escalation for complex issues or no response
- Manager escalation rare (critical outages only)

**Timeout Tuning:**
- Critical alerts: 5 minutes
- Warnings: 15-30 minutes

### 5. Runbook-Driven Response

**Every Service Should Have:**
- Runbook URL in service settings
- Common troubleshooting steps
- Escalation contacts
- Known issues and workarounds

**Runbook Template:**
```markdown
# Service: Platform API Server

## Common Alerts

### Alert: API Latency High

**Symptoms:** API response time > 2 seconds

**Troubleshooting:**
1. Check database query performance
2. Review recent deployments
3. Check resource usage (CPU, memory)

**Resolution:**
- If database slow: Run index optimization
- If deployment issue: Roll back
- If resource issue: Scale up pods

**Escalate to:** Database team if query optimization needed
```

### 6. Postmortem Every Incident

**Why:**
- Learn from failures
- Prevent repeat incidents
- Improve processes

**What to Document:**
- Timeline of events
- Root cause
- Action items (with owners and due dates)

**Share:**
- Post to team wiki
- Discuss in retrospective
- Update runbooks based on learnings

### 7. Monitor On-Call Health

**Watch For:**
- High incident volume (>10/week per person)
- Long shifts without breaks
- Repeated escalations (sign of gaps in training)

**Actions:**
- Reduce alert noise
- Improve runbooks
- Train team on common issues
- Consider adding more on-call engineers

---

## Troubleshooting

### Issue 1: Alerts Not Reaching On-Call

**Symptoms:**
- Incident triggered but no notification
- On-call engineer didn't receive alert

**Debugging:**

1. **Check Integration:**
 - Service → Integrations → Verify integration key
 - Test integration: Send test alert

2. **Check Escalation Policy:**
 - Service → Settings → Verify escalation policy assigned
 - Escalation policy → Verify on-call schedule linked

3. **Check User Notification Rules:**
 - User Profile → Notification Rules
 - Verify phone number, email correct
 - Test notifications

4. **Check On-Call Schedule:**
 - Is user actually on-call at that time?
 - Check for overrides or gaps

**Solution:**
- Fix integration key
- Assign correct escalation policy
- Update user contact info
- Fill schedule gaps

---

### Issue 2: Alert Storm (100+ Incidents)

**Symptoms:**
- Hundreds of incidents in short time
- All similar alerts

**Immediate Action:**
1. **Snooze duplicate incidents**
2. **Acknowledge one representative incident**
3. **Resolve underlying issue**

**Prevent Future Storms:**
1. **Enable Alert Grouping:**
 - Service → Settings → Intelligent Grouping
2. **Add Deduplication:**
 - Use dedup_key in alert payload
3. **Tune Monitoring:**
 - Increase alert threshold
 - Add dampening (fire only if condition persists 5+ minutes)

---

### Issue 3: Incident Not Escalating

**Symptoms:**
- Primary on-call didn't ACK
- Incident didn't escalate to secondary

**Debugging:**

1. **Check Escalation Policy Levels:**
 - Are multiple levels defined?
 - Are timeout values set correctly?

2. **Check Incident Timeline:**
 - When was incident triggered?
 - When should it have escalated?

3. **Manually Escalate:**
 - Incident → Actions → Escalate

**Solution:**
- Add secondary/tertiary levels to escalation policy
- Adjust timeout values
- Test escalation flow

---

### Issue 4: Can't Access PagerDuty

**Symptoms:**
- Login fails
- SSO error

**Debugging:**

1. **Check Company SSO:**
 - Try logging into other Company services
 - If SSO broken, contact IT

2. **Check User Account:**
 - Are you provisioned in PagerDuty?
 - Contact PagerDuty admin

3. **Check Network:**
 - VPN connected?
 - Firewall blocking access?

**Solution:**
- Reset password via Company SSO
- Request PagerDuty account provisioning
- Connect to VPN

---

## Summary

### Key Takeaways

1. **PagerDuty = Incident Management Hub**
 - Routes alerts from monitoring tools
 - Notifies on-call engineers
 - Orchestrates response workflows

2. **Core Workflow:**
 - Monitoring tool → Integration → Service → Escalation Policy → On-Call Engineer

3. **Essential Skills:**
 - Acknowledge/resolve incidents
 - Understand escalation policies
 - Read on-call schedules
 - Write postmortems

4. **PagerDuty MCP Server:**
 - Programmatic access to PagerDuty
 - Integrate with Claude Code for investigations
 - Query incidents, on-call status without UI

### Next Steps

**Beginner:**
1. Log into PagerDuty (https://company.pagerduty.com)
2. View your team's services
3. Check on-call schedule
4. Acknowledge a test incident

**Intermediate:**
5. Create a service with integration
6. Set up on-call schedule
7. Configure escalation policy
8. Test alert flow end-to-end

**Advanced:**
9. Build response plays for common incidents
10. Implement alert grouping/deduplication
11. Create custom reports and analytics
12. Integrate PagerDuty MCP with runbook automation

---

## References

- **PagerDuty Documentation:** https://support.pagerduty.com
- **PagerDuty API Reference:** https://developer.pagerduty.com/api-reference/
- **PagerDuty Best Practices:** https://www.pagerduty.com/resources/learn/incident-response-best-practices/
- **Company Internal Wiki:** [Link to Company PagerDuty documentation]
- **PagerDuty MCP Server:** [Link to Company internal MCP server repo/docs]

---

**Last Updated:** 2026-03-19
**Author:** Documentation Team (Company SRE Team)
**Audience:** Company SRE teams
**License:** Internal Company use
