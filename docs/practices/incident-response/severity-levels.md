# Incident Severity Levels - REDESIGNED WITH ACTIONABLE TEMPLATES

**Version:** 2.0
**Date:** 2026-03-11
**Author:** Documentation Team
**Purpose:** Clear, actionable incident management guide with copy-paste ready templates

---

## CRITICAL ANALYSIS: What's Wrong with the Current SOP?

**Original SOP Location:** `https://gitlab.company.com/platform/docs/sop/-/blob/main/sre/incident-management-process.md#incident-severity-levels`

### Problem #1: Subjective, Unusable Definitions

**Current SOP says (lines 44, 66, 85):**

| Severity | Definition |
|----------|------------|
| S1 | "affecting all or **a large number** of Platform users" |
| S2 | "affecting **a large number** of Platform users" |
| S3 | "affecting **a small number** of Platform users" |

 **What does "large" mean?** 10 users? 100? 1000?
 **During an incident, you have NO TIME to philosophize about definitions.**
 **S1 and S2 use the SAME WORDS ("large number") - how do you decide?**

**What's needed:** Objective numbers (%, user count, error rates)

---

### Problem #2: Contradictory Examples

**Current SOP says:**

- **S1 (line 48):** "Total outage that would **block a release**"
- **S2 (line 66):** "but do **not** cause a complete outage or **block a release**"
- **S2 example (line 70):** "**builds in one of the Platform clusters not accessible**"

 **CONTRADICTION:** If builds are not accessible, that **BLOCKS A RELEASE**!
 **So is it S1 or S2?** The SOP contradicts itself!

**What's needed:** Clear "IS Severity X" vs "NOT Severity X" examples

---

### Problem #3: No Decision Tree

**Current SOP:** Requires reading 3 paragraphs to decide severity during an active incident.

 **You have 10 seconds to make a decision, not 10 minutes!**

**What's needed:** Visual decision tree with yes/no questions

---

### Problem #4: Response Section = Admin Checklist, NOT Actionable Steps

**Current SOP says (S1 Response, lines 56-62):**
> - Immediate response required.
> - Allocate all available resources.
> - Incident Tracking Tool.
> - Update Platform Status Banner immediately.
> - Regular updates to Platform users.
> - Formal PMR hosted.
> - Formal RCA shared with Platform users.

 **Problems:**
- "Immediate response" - OK, but **WHAT EXACTLY DO I DO?**
- "Allocate all resources" - **HOW? Who do I call? In what order?**
- "Regular updates" - **HOW OFTEN?** Every 5 minutes? Every hour?
- "Update banner immediately" - **WITH WHAT TEXT?**

 **No step-by-step workflow**
 **No message templates**
 **No timing guidance**

**This is a POLICY document, not a PLAYBOOK.**

---

### Problem #5: No Communication Templates

**What you need during an incident:**
- "Copy this message into Slack"
- "Use this banner text"
- "Send this email"

**What the current SOP gives you:**
- "Update banner immediately" (no text provided)
- "Regular updates to users" (no template provided)
- "Send email to platform-announce" (no template provided)

**During a critical incident, you don't have time to write messages from scratch.**

---

### Problem #6: Confused Roles

**Current SOP says:**
- Line 8: "First Responder can choose to become either IC or Tech Lead, **or both**"
- Line 116: "Incident Owner is **by default** the First Responder"
- Line 138: "Incident Technical Lead is **by default** the First Responder"
- Line 154: "First responder can choose to become either IC or Tech Lead"

 **So the same person is:**
- First Responder
- Incident Owner
- Technical Lead
- Incident Commander (?)

**During an incident, it's IMPOSSIBLE to fill 4 roles at once!**

---

### What This Redesign Fixes

| Problem | Current SOP | This Redesign |
|---------|-------------|---------------|
| **Severity decision** | Subjective ("large") | Objective (%, counts, error rates) |
| **Decision speed** | Read 3 paragraphs | 10-second decision tree |
| **Examples** | Contradictory | Clear IS/NOT IS format |
| **Actions** | Policy list ("update banner") | Step-by-step ("Minute 1: do this, Minute 2: do that") |
| **Communication** | No templates | 15+ copy-paste templates |
| **Timing** | Vague ("regular") | Specific (every 15/20/30 min) |
| **Roles** | Confused (1 person = 4 roles) | Clear separation |

---

## Quick Severity Decision (10 Seconds)

**Use this decision tree FIRST, read details later:**

```
Can users perform core workflows?
 
 NO (login broken, all pipelines fail)
 SEVERITY 1
 
 YES, but degraded
 
 How many users affected?
 >50% users OR all clusters → SEVERITY 1
 10-50% users OR multiple clusters → SEVERITY 2
 <10% users OR single cluster → SEVERITY 3
 
 What's the error rate?
 >90% errors on critical path → SEVERITY 1
 50-90% errors → SEVERITY 2
 <50% errors → SEVERITY 3
 
 YES, fully functional → NOT AN INCIDENT
```

**Core workflows:** Login, Build, Integration, Pipeline, Release

---

## Severity 1: Total Service Outage

### Definition (Objective Criteria)

**At least ONE of these is true:**
- 100% of core service unavailable
- >90% error rate on critical workflows
- ALL Platform clusters affected
- Complete data loss affecting all users
- Security breach exposing user data

**Core Services:** Build, Integration, Pipeline, Release, Login/SSO

---

### Real Examples: IS vs NOT Severity 1

#### **IS Severity 1:**
- Login completely broken - **nobody can access Platform**
- All pipelines failing with 500 errors **across all clusters**
- Database corruption - **all user data inaccessible**
- Complete cluster outage **affecting all users**
- SSO service down - **100% authentication failures**

#### **NOT Severity 1 (likely S2 or S3):**
- Pipelines slow but completing successfully → S2 or S3
- One cluster degraded, others working normally → S2
- UI latency high but service accessible → S2 or S3
- Single tenant affected → S3 or support ticket
- 50% error rate (not >90%) → S2

---

### Your First 5 Minutes - Step by Step

**When you detect or are alerted to a potential S1 incident:**

---

#### ⏱ **MINUTE 1: Declare Incident in Incident Tracking Tool**

**Action:**
1. Navigate to: `https://incident-tracker.company.com/`
2. Click: **"Create New Incident"**
3. Copy-paste this template into the form:

** Copy-Paste Template:**
```
Title: [YYYY-MM-DD] [SERVICE] Total Outage - [Brief Description]

Example: 2026-03-11 Platform Login Total Outage - SSO Service Down

Impact: All Platform users unable to login. Service completely unavailable.
Start time: 14:32 UTC
Affected services: Login/SSO, All Platform clusters
Initial symptoms: 100% login failures, 502 errors on auth endpoints

Status: Investigation started
Severity: Critical (Severity 1)
```

**Real Example (Fill in the blanks):**
```
Title: 2026-03-11 Platform Login Total Outage - SSO Service Down

Impact: All Platform users unable to login. Service completely unavailable.
Start time: 14:32 UTC
Affected services: Login/SSO, All Platform clusters
Initial symptoms: 100% login failures, 502 errors on auth endpoints

Status: Investigation started
Severity: Critical (Severity 1)
```

---

#### ⏱ **MINUTE 2: Post Initial Slack Message**

**Channel:** `#announce-platform-incidents` (auto-created by Incident Tracking Tool as `#incident-<ID>`)

** Copy-Paste Template:**
```
 SEVERITY 1 INCIDENT DECLARED 

Impact: [Core Service] completely unavailable
Users affected: All Platform users
Started: [HH:MM UTC]
Status: Investigation ongoing

I am the Incident Commander. Updates every 15 minutes in this channel.

Incident Tracking Tool: [paste link from step 1]
```

**Real Example:**
```
 SEVERITY 1 INCIDENT DECLARED 

Impact: Platform Login completely unavailable - users cannot access the platform
Users affected: All Platform users
Started: 14:32 UTC
Status: Investigation ongoing

I am the Incident Commander. Updates every 15 minutes in this channel.

Incident Tracking Tool: https://incident-tracker.company.com/incidents/12345
```

---

#### ⏱ **MINUTE 3: Update Platform Status Banner**

**Action:**
1. Go to: `https://github.com/company/platform-deployments`
2. Edit: `components/platform-info/base/banner.yaml`
3. Set `enabled: true`
4. Copy-paste banner text:

** Copy-Paste Banner Text:**
```
 SERVICE DISRUPTION: [Service] is currently unavailable. We are investigating. Updates: [Slack channel link]
```

**Real Example:**
```
 SERVICE DISRUPTION: Platform Login is currently unavailable. We are investigating. Updates: https://company.slack.com/archives/C12345
```

**How to update:**
```yaml
banner:
 enabled: true
 variant: "error"
 message: " SERVICE DISRUPTION: Platform Login is currently unavailable. We are investigating. Updates: https://company.slack.com/archives/C12345"
```

5. Create PR (request fast-track approval for S1 incidents)

---

#### ⏱ **MINUTE 4: Notify #platform-users**

**Channel:** `#platform-users`

** Copy-Paste Template:**
```
We are aware of an issue affecting [Service]. All Platform users are currently unable to [action].

Our team is investigating and working on a fix.

For real-time updates, please follow: [incident channel link]
```

**Real Example:**
```
We are aware of an issue affecting Platform Login. All Platform users are currently unable to access the platform.

Our team is investigating and working on a fix.

For real-time updates, please follow: #incident-2026-03-11-login-outage
```

---

#### ⏱ **MINUTE 5: Send Email Notifications**

**To:**
- `platform-announce@company.com`
- `platform@company.com`
- `outage-list@company.com`

**Subject Line Template:**
```
CRITICAL: Platform [Service] Outage - [Brief Description]
```

** Copy-Paste Email Body:**
```
Subject: CRITICAL: Platform Login Outage - SSO Service Down

Dear Platform Users,

We are currently experiencing a critical service outage affecting Platform Login.

IMPACT:
- All users unable to access Platform platform
- Started: 14:32 UTC (March 11, 2026)
- Estimated users affected: All Platform users

STATUS:
- Investigation in progress
- Team actively working on resolution
- Updates every 15 minutes

REAL-TIME UPDATES:
- Slack: #incident-2026-03-11-login-outage
- Status banner: https://platform.company.com

We apologize for the inconvenience and will provide updates as soon as we have more information.

SRE Incident Commander
```

---

### During the Incident: Regular Updates (Every 15 Minutes)

**Even if you have NO new information, post an update every 15 minutes.**

#### **Template: No Progress Yet**

```
⏱ 15-minute update:

Status: Still investigating root cause

Investigation threads:
 • Recent deployments - No suspicious changes in last 2 hours
 • SSO service logs - Analyzing error patterns
 • Cluster health - CPU/memory normal

Service still unavailable. Next update in 15 minutes.
```

---

#### **Template: Progress Made**

```
⏱ 15-minute update:

Status: Root cause identified

Finding: SSO certificate expired at 14:30 UTC
Mitigation: Deploying new certificate (ETA: 5 minutes)

Service still unavailable. Next update in 15 minutes or when service recovers.
```

---

### Resolution Phase

#### When Service Recovers

** Slack Message Template:**
```
 SERVICE RECOVERED

Resolution time: [HH:MM UTC]
Total downtime: [X minutes]

Root cause: [Brief explanation]
Fix applied: [What was done]

Monitoring for stability. Will update if any issues arise.

Thank you for your patience.
```

**Real Example:**
```
 SERVICE RECOVERED

Resolution time: 14:47 UTC
Total downtime: 15 minutes

Root cause: SSO certificate expired
Fix applied: New certificate deployed and service restarted

Monitoring for stability. Will update if any issues arise.

Thank you for your patience.
```

---

#### Update Banner (Service Restored)

** Banner Message Template:**
```
 RESOLVED: [Service] issue has been resolved. Service is now operational.
```

**Real Example:**
```yaml
banner:
 enabled: true
 variant: "success"
 message: " RESOLVED: Platform Login issue has been resolved. Service is now operational."
```

**Action:** Keep banner for 1 hour, then set `enabled: false`

---

#### Email Users (Resolution)

**Subject:** `RESOLVED: Platform [Service] Outage`

** Email Template:**
```
Subject: RESOLVED: Platform Login Outage

Dear Platform Users,

The Platform Login outage has been RESOLVED.

SUMMARY:
- Impact: All users unable to access Platform platform
- Start time: 14:32 UTC
- Resolution time: 14:47 UTC
- Total downtime: 15 minutes
- Users affected: All Platform users

ROOT CAUSE:
SSO service certificate expired at 14:30 UTC, causing all authentication requests to fail.

RESOLUTION:
New certificate deployed and SSO service restarted.

NEXT STEPS:
We will conduct a full Root Cause Analysis (RCA) and share the findings within 5 business days.
Action items will be created to prevent recurrence.

We apologize for the disruption and thank you for your patience.

SRE Team
```

---

### Post-Incident: RCA Requirements (Within 5 Business Days)

#### Required Activities

- **Day 1:** Schedule Post-Mortem Review (PMR) meeting
- **Day 2-3:** Complete Incident Tracking Tool document with full timeline
- **Day 4:** Host PMR meeting (60 minutes, blameless)
- **Day 5:** Publish final RCA to Platform users

---

#### Incident Tracking Tool Document Structure

** Complete RCA Template:**

```markdown
## Executive Summary
On March 11, 2026 at 14:32 UTC, Platform Login experienced a total outage affecting all users.
The outage lasted 15 minutes and was caused by an expired SSO certificate.
Service was restored at 14:47 UTC after deploying a new certificate.

## Impact
- **Severity:** 1 (Critical)
- **Duration:** 15 minutes (14:32 - 14:47 UTC)
- **Users affected:** All Platform users (~5,000 users)
- **Business impact:** Complete service unavailability - users unable to login or access any Platform features

## Timeline

| Time (UTC) | Event |
|------------|-------|
| 14:30 | SSO certificate expired (unknown to team at this time) |
| 14:32 | First PagerDuty alert triggered: "SSO service returning 502 errors" |
| 14:33 | Incident Commander joined incident channel, declared Severity 1 |
| 14:34 | Incident Tracking Tool incident created |
| 14:35 | Banner updated, Slack #platform-users notified, emails sent |
| 14:38 | Root cause identified: SSO certificate expiry |
| 14:42 | New certificate generated |
| 14:45 | Certificate deployed to production |
| 14:47 | Service recovered and verified - users able to login |
| 14:48 | Resolution message posted, banner updated |

## Root Cause (5 Whys Analysis)

**Immediate cause:** SSO service certificate expired.

**5 Whys:**
1. Why did service fail? → Certificate expired
2. Why did certificate expire? → No automatic renewal process in place
3. Why no renewal process? → Certificate was manually created 2 years ago
4. Why manual creation? → No automation for certificate lifecycle management
5. Why no automation? → Certificate management not included in deployment checklist

**Root cause:** Lack of automated certificate lifecycle management process.

## Resolution

1. Generated new SSO certificate with 1-year validity
2. Deployed new certificate to production SSO service
3. Restarted SSO service pods
4. Verified login functionality across all clusters
5. Monitored error rate returned to 0%

## What Worked Well

 Monitoring detected issue within 2 minutes of certificate expiry
 Team responded quickly (incident declared within 1 minute)
 Root cause identified in 6 minutes
 Communication was clear and frequent (every 15 minutes)
 Banner and email notifications sent promptly

## Areas for Improvement

 No alerting on certificate expiry (30-day warning would have prevented this)
 No automated certificate renewal (manual process error-prone)
 Certificate expiry date not tracked in any monitoring system

## Action Items

| Action | Owner | Due Date | JIRA | Status |
|--------|-------|----------|------|--------|
| Implement automated cert renewal (Let's Encrypt or similar) | Platform Team | 2026-03-25 | PLAT-1234 | Open |
| Add certificate expiry monitoring with 30-day warning alerts | Observability | 2026-03-18 | PLAT-1235 | Open |
| Document certificate management process in runbook | SRE Team | 2026-03-15 | PLAT-1236 | Open |
| Audit all service certificates for upcoming expiry dates | Security | 2026-03-20 | PLAT-1237 | Open |
| Create quarterly certificate review process | SRE Lead | 2026-03-22 | PLAT-1238 | Open |

## Lessons Learned

1. **Prevention:** Automation is critical for certificate lifecycle management
2. **Detection:** Proactive monitoring (30-day warnings) prevents outages
3. **Response:** Clear templates and procedures enabled fast incident declaration
4. **Communication:** Regular updates (every 15 min) kept stakeholders informed

## Supporting Data

- PagerDuty alert screenshot: [link]
- Grafana error rate dashboard: [link]
- SSO service logs: [link]
- Certificate expiry details: [link]
```

---

## Severity 2: Major Service Degradation

### Definition (Objective Criteria)

**At least ONE of these is true:**
- 50-90% error rate on critical workflows
- 10-50% of users affected
- Multiple clusters degraded (but not all)
- Significant performance degradation (>5x normal latency)
- Core service accessible but severely limited

---

### Real Examples: IS vs NOT Severity 2

#### **IS Severity 2:**
- Build service failing in 2 out of 4 clusters
- Pipeline execution success rate dropped from 95% to 60%
- Tekton UI loading extremely slowly (>10s page loads)
- Multiple tenants unable to access UI across several clusters

#### **NOT Severity 2:**
- Single cluster having issues (others normal) → S3
- UI slightly slow but usable (<5s) → S3
- All services completely down → S1
- Single user affected → Support ticket

---

### Your First 5 Minutes - Step by Step

#### ⏱ **MINUTE 1: Declare Incident in Incident Tracking Tool**

** Copy-Paste Template:**
```
Title: [YYYY-MM-DD] [SERVICE] Major Degradation - [Brief Description]

Example: 2026-03-11 Build Service Major Degradation - High Failure Rate

Impact: Build service experiencing high failure rate across multiple clusters
Start time: 15:20 UTC
Affected services: Build service (clusters: prd-01, prd-02)
Unaffected: Build service in prd-03, prd-04 working normally
Initial symptoms: 65% build failure rate, error: "resource quota exceeded"

Status: Investigation started
Severity: High (Severity 2)
```

---

#### ⏱ **MINUTE 2: Post Initial Slack Message**

**Channel:** Incident Tracking Tool auto-created incident channel

** Copy-Paste Template:**
```
 SEVERITY 2 INCIDENT DECLARED

Impact: [Service] experiencing major degradation
Users affected: Estimated [10-50%] of Platform users
Scope: [Specific clusters/components affected]
Unaffected: [What still works normally]
Started: [HH:MM UTC]

I am the Incident Commander. Updates every 20 minutes in this channel.

Incident Tracking Tool: [link]
```

**Real Example:**
```
 SEVERITY 2 INCIDENT DECLARED

Impact: Build service experiencing 65% failure rate
Users affected: Estimated 30% of Platform users
Scope: Builds in prd-01, prd-02 clusters affected
Unaffected: Builds in prd-03, prd-04 clusters working normally
Started: 15:20 UTC

I am the Incident Commander. Updates every 20 minutes in this channel.

Incident Tracking Tool: https://incident-tracker.company.com/incidents/12346
```

---

#### ⏱ **MINUTE 3: Update Platform Status Banner**

** Banner Text Template:**
```
 SERVICE DEGRADATION: [Service] is experiencing issues. Some users may be affected. We are investigating.
```

**Real Example:**
```yaml
banner:
 enabled: true
 variant: "warning"
 message: " SERVICE DEGRADATION: Build service is experiencing high failure rates in some clusters. We are investigating."
```

---

#### ⏱ **MINUTE 4: Notify #platform-users**

** Copy-Paste Template:**
```
We are aware of issues affecting [Service]. Some users may experience [specific impact].

Affected: [scope - e.g., specific clusters]
Not affected: [what still works]

Workaround (if available): [alternative approach]

Our team is investigating. Real-time updates: [incident channel link]
```

**Real Example:**
```
We are aware of issues affecting Build service. Some users may experience build failures.

Affected: Builds in prd-01 and prd-02 clusters
Not affected: Builds in prd-03 and prd-04 clusters are working normally

Workaround: Please retry failed builds in prd-03 or prd-04 clusters

Our team is investigating. Real-time updates: #incident-2026-03-11-build-degradation
```

---

#### ⏱ **MINUTE 5: Send Email (Recommended)**

**Note:** Email for S2 is **recommended** if impact is widespread.

**Subject:** `Platform [Service] Degradation - Investigation Underway`

** Email Template:**
```
Subject: Platform Build Service Degradation - Investigation Underway

Dear Platform Users,

We are currently experiencing degradation affecting Platform Build service.

IMPACT:
- Some users experiencing build failures
- Affected clusters: prd-01, prd-02
- Unaffected clusters: prd-03, prd-04 working normally
- Current failure rate: ~65%
- Started: 15:20 UTC (March 11, 2026)

STATUS:
- Investigation in progress
- Updates every 20 minutes

WORKAROUND:
- Users can retry builds in prd-03 or prd-04 clusters
- To specify cluster: [instructions if applicable]

UPDATES:
- Slack: #incident-2026-03-11-build-degradation
- Status page: https://platform.company.com

We will provide updates as we learn more.

SRE Incident Commander
```

---

### During the Incident: Regular Updates (Every 20 Minutes)

** Update Template:**
```
⏱ 20-minute update:

Status: [Investigating / Root cause identified / Mitigation in progress]

Investigation threads:
 • [Thread 1] - [Status]
 • [Thread 2] - [Status]
 • [Thread 3] - [Status]

Current impact: [% failure rate, user count, etc.]

Next update in 20 minutes.
```

**Real Example:**
```
⏱ 20-minute update:

Status: Root cause identified

Investigation threads:
 • Recent deployments - No relevant changes found
 • Resource quotas - FOUND: prd-01, prd-02 hit resource limits
 • Cluster health - CPU/memory maxed out in affected clusters

Current impact: 65% build failure rate in prd-01, prd-02

Mitigation: Increasing resource quotas now (ETA: 10 minutes)

Next update in 20 minutes.
```

---

### Resolution Phase

** Resolution Message Template:**
```
 SERVICE DEGRADED → NORMAL

Resolution time: [HH:MM UTC]
Total degradation period: [X minutes]

Root cause: [Brief explanation]
Fix applied: [What was done]

Build success rate returned to normal levels (>95%). Monitoring for stability.
```

** Banner Update:**
```yaml
banner:
 enabled: true
 variant: "success"
 message: " RESOLVED: Build service degradation resolved. Service operating normally."
```

** Resolution Email:**
```
Subject: RESOLVED: Platform Build Service Degradation

Dear Platform Users,

The Build service degradation has been RESOLVED.

SUMMARY:
- Impact: 65% build failure rate in prd-01, prd-02
- Start time: 15:20 UTC
- Resolution time: 16:02 UTC
- Total degradation: 42 minutes
- Root cause: Resource quota misconfiguration

RESOLUTION:
Resource quota limits adjusted in affected clusters. Build success rate returned to normal (>95%).

NEXT STEPS:
Full RCA will be shared within 5 business days with action items to prevent recurrence.

Service is now operating normally. Thank you for your patience.

SRE Team
```

---

### Post-Incident: RCA Requirements

**For Severity 2:**
- Incident Tracking Tool document required (within 5 business days)
- Formal RCA shared with Platform users
- PMR meeting **optional** (recommended for high-impact S2 incidents)

---

## Severity 3: Minor Service Impact

### Definition (Objective Criteria)

**At least ONE of these is true:**
- <50% error rate
- <10% of users affected
- Single cluster/component affected (others working)
- Performance degradation but service usable (2-3x normal latency)
- Intermittent issues not blocking core workflows

---

### Real Examples: IS vs NOT Severity 3

#### **IS Severity 3:**
- Single cluster build service slow but completing builds
- UI latency increased to 3-5s (normally 1s) but functional
- One tenant unable to access specific namespace
- Intermittent 502 errors on non-critical API endpoint affecting <5% requests

#### **NOT Severity 3:**
- Multiple clusters affected → S2
- Total outage of any core service → S1
- Single user with wrong configuration → Support ticket, not incident

---

### Your First 5 Minutes - Step by Step

#### ⏱ **MINUTE 1-2: Declare Incident in Incident Tracking Tool**

** Copy-Paste Template:**
```
Title: [YYYY-MM-DD] [SERVICE] Minor Performance Degradation - [Scope]

Example: 2026-03-11 UI Minor Performance Degradation - Single Cluster

Impact: UI slow loading in prd-01 cluster only
Start time: 16:45 UTC
Affected services: Platform UI (prd-01 cluster only)
Unaffected: UI in prd-02, prd-03, prd-04 normal
Initial symptoms: Page load time 3-5s (normal: <1s), service functional

Status: Investigation started
Severity: Low (Severity 3)
```

---

#### ⏱ **MINUTE 3: Post Slack Message (Internal)**

**Channel:** Incident Tracking Tool incident channel

** Copy-Paste Template:**
```
 SEVERITY 3 INCIDENT

Impact: [Service] experiencing minor issues
Users affected: <10% of users
Scope: [Specific cluster/component]
Unaffected: [Other clusters/components]
Started: [HH:MM UTC]

Investigation ongoing. Updates every 30 minutes.

Incident Tracking Tool: [link]
```

**Real Example:**
```
 SEVERITY 3 INCIDENT

Impact: UI slow loading times
Users affected: <10% (prd-01 cluster users only)
Scope: Single cluster (prd-01)
Unaffected: UI in prd-02, prd-03, prd-04 performing normally
Started: 16:45 UTC

Investigation ongoing. Updates every 30 minutes.

Incident Tracking Tool: https://incident-tracker.company.com/incidents/12347
```

---

#### ⏱ **MINUTE 4-5: Consider Banner Update (OPTIONAL)**

**For Severity 3:**
- Banner update is **OPTIONAL**
- Update banner **IF** user-visible and users are actively reporting it
- Skip banner if only internal monitoring detected it and minimal user impact

** Banner Template (if used):**
```yaml
banner:
 enabled: true
 variant: "info"
 message: "ℹ NOTICE: Some users may experience slower UI performance in prd-01 cluster. Service remains functional. We are investigating."
```

---

### During the Incident: Regular Updates (Every 30 Minutes)

** Update Template:**
```
⏱ 30-minute update:

Status: [Investigating / Root cause identified]
Current state: [Service still slow / Issue intermittent / etc.]

Next update in 30 minutes or when resolved.
```

---

### Resolution Phase

** Resolution Message:**
```
 ISSUE RESOLVED

Resolution time: [HH:MM UTC]
Duration: [X minutes]

Root cause: [Brief explanation]
Fix: [What was done]

UI performance returned to normal (<1s page loads). Service operating normally. Incident closed.
```

** Banner (if used):**
```
Remove banner or update to:
 UI performance issue resolved in prd-01 cluster.
```

**Email:** **NOT required** for Severity 3

---

### Post-Incident: RCA Requirements

**For Severity 3:**
- Incident Tracking Tool document required (within 5 business days)
- Updates to affected users (if identifiable)
- No formal PMR meeting required
- RCA optional (but document learnings in Incident Tracking Tool)

---

## Quick Reference: Communication Matrix

| Severity | Slack Updates | Banner | Email | PMR Meeting | RCA Document |
|----------|---------------|--------|-------|-------------|--------------|
| **S1** | Every 15 min | Required immediately | Required | Required (formal, 60 min) | Required |
| **S2** | Every 20 min | Required | Recommended | Optional (recommended) | Required |
| **S3** | Every 30 min | If user-visible | Not required | Not required | Required |

---

## Summary: Why This Redesign Works

### **Old SOP Said:**
> "Immediate response required. Allocate all resources. Incident Tracking Tool. Update banner. Regular updates. Formal PMR. Formal RCA."

**Problem:** No actionable steps, no templates, no timing guidance.

---

### **New SOP Says:**

> **Minute 1:** Create Incident Tracking Tool with THIS template. [copy-paste ready]
>
> **Minute 2:** Post THIS message in Slack. [copy-paste ready]
>
> **Minute 3:** Update banner with THIS text. [copy-paste ready]
>
> **Minute 4:** Notify users with THIS message. [copy-paste ready]
>
> **Minute 5:** Send THIS email. [copy-paste ready]
>
> **Every 15 minutes:** Post THIS update. [copy-paste ready]
>
> **At resolution:** Post THIS message. [copy-paste ready]

**Solution:** Step-by-step workflow with copy-paste ready templates for every scenario.

---

## Appendix A: All Templates Quick Reference

### Severity 1 Templates

| Template | Link |
|----------|------|
| Incident Tracking Tool Title | [Minute 1](#⏱-minute-1-declare-incident-in-incident-tracker) |
| Slack Initial Message | [Minute 2](#⏱-minute-2-post-initial-slack-message) |
| Banner Text | [Minute 3](#⏱-minute-3-update-platform-status-banner) |
| User Notification | [Minute 4](#⏱-minute-4-notify-platform-users) |
| Email Template | [Minute 5](#⏱-minute-5-send-email-notifications) |
| 15-min Update (No Progress) | [During Incident](#-template-no-progress-yet) |
| 15-min Update (Progress) | [During Incident](#-template-progress-made) |
| Resolution Slack Message | [Resolution Phase](#when-service-recovers) |
| Resolution Banner | [Resolution Phase](#update-banner-service-restored) |
| Resolution Email | [Resolution Phase](#email-users-resolution) |
| Complete RCA Document | [Post-Incident](#-complete-rca-template) |

### Severity 2 Templates

| Template | Link |
|----------|------|
| All S2 Templates | [Severity 2 Section](#severity-2-major-service-degradation) |

### Severity 3 Templates

| Template | Link |
|----------|------|
| All S3 Templates | [Severity 3 Section](#severity-3-minor-service-impact) |

---

## Appendix B: Incident Commander Role Clarity

**What the Incident Commander DOES:**
- Coordinates communication (uses templates above)
- Ensures regular updates (every 15/20/30 min based on severity)
- Keeps timeline documented in Incident Tracking Tool
- Manages stakeholder expectations
- Facilitates post-incident review

**What the Incident Commander DOES NOT do:**
- Debug the technical issue (that's the Tech Lead's job)
- Write code or deploy fixes
- Dive deep into logs (delegate to engineers)

**Mental Model:**
> You are the **orchestra conductor**, not the musician. Your job is to keep everyone in sync, not to play every instrument yourself.

---

**Document Version:** 2.0
**Created:** 2026-03-11
**Based on:**
- Current SOP: `https://gitlab.company.com/platform/docs/sop/-/blob/main/sre/incident-management-process.md`
- Incident Manager Handbook: `/home/user/learning-plans/incident-manager-kezikonyv.md` (2026-03-10)

**Author:** Documentation Team
**Reviewed by:** Claude Code

---

**Next Steps:**
1. Review this document with SRE team
2. Get feedback on templates
3. Test templates in next incident drill
4. Propose changes to GitLab SOP
5. Update team training materials
