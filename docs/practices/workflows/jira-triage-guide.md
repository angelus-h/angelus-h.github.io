# Jira Ticket Triage - Practical Guide with Examples 

**Version:** 1.0
**Date:** 2026-03-11
**Author:** Documentation Team
**Purpose:** How to triage Jira tickets effectively during IC shifts, backlog grooming, and incident response

---

## Table of Contents

1. [What is Triage?](#what-is-triage)
2. [When to Triage](#when-to-triage)
3. [The 5-Question Framework](#the-5-question-framework)
4. [Severity vs Priority vs Impact](#severity-vs-priority-vs-impact)
5. [Real Examples: Good vs Bad Triage](#real-examples-good-vs-bad-triage)
6. [Common Ticket Types & How to Handle](#common-ticket-types--how-to-handle)
7. [Red Flags: Immediate Action Required](#red-flags-immediate-action-required)
8. [Communication Templates](#communication-templates)
9. [Anti-Patterns & Mistakes](#anti-patterns--mistakes)
10. [Triage Decision Tree](#triage-decision-tree)

---

## What is Triage?

**Triage** is the process of **quickly assessing** incoming tickets to:
- Determine **urgency and priority**
- Assign to the **right team/person**
- Identify **missing information**
- Decide **immediate vs later** action
- Prevent tickets from **falling through cracks**

**Mental Model:**
> Triage is NOT about solving the problem. It's about **routing the problem to the right solver at the right time.**

---

## When to Triage

### **1. During IC Shift**
- **Frequency:** Continuous (as tickets arrive)
- **Goal:** Identify critical issues, escalate if needed
- **Time per ticket:** 2-5 minutes max

### **2. Backlog Grooming**
- **Frequency:** Weekly/bi-weekly
- **Goal:** Clean up backlog, prioritize work
- **Time per ticket:** 5-10 minutes

### **3. Post-Incident**
- **Frequency:** After major incidents
- **Goal:** Ensure action items are properly tracked
- **Time per ticket:** 10-15 minutes (detailed)

### **4. New Ticket Alert**
- **Frequency:** Real-time (Slack/email notifications)
- **Goal:** Quick assessment, route to right channel
- **Time per ticket:** 1-2 minutes (initial glance)

---

## The 5-Question Framework

**Ask these 5 questions for EVERY ticket:**

### **1. What is actually broken?**
- Is this a **bug** (something works incorrectly)?
- Is this a **feature request** (new functionality)?
- Is this a **question** (need information)?
- Is this an **incident** (active outage)?

### **2. Who is affected?**
- **All users** (production outage)?
- **Specific team/tenant** (isolated issue)?
- **Single user** (user error or edge case)?
- **Internal only** (development/staging)?

### **3. What is the business impact?**
- **Blocking work** (users cannot complete tasks)?
- **Degraded experience** (slower but functional)?
- **Inconvenience** (workaround available)?
- **Nice-to-have** (no immediate impact)?

### **4. What information is missing?**
- **Steps to reproduce?**
- **Error messages/logs?**
- **Affected service/cluster?**
- **When did it start?**
- **Contact information?**

### **5. What is the right next action?**
- **Immediate escalation** (incident response)?
- **Assign to team** (route to experts)?
- **Request more info** (ask reporter)?
- **Close/redirect** (not our scope)?
- **Backlog** (prioritize later)?

---

## Severity vs Priority vs Impact

### **Severity** (Technical)
How broken is the system?

| Severity | Definition | Example |
|----------|------------|---------|
| **Critical** | Complete service outage | Login broken for all users |
| **High** | Major degradation | 50% build failure rate |
| **Medium** | Partial functionality broken | One feature not working |
| **Low** | Minor issue | UI typo, cosmetic bug |

---

### **Priority** (Business)
How urgently must this be fixed?

| Priority | Definition | Timeframe |
|----------|------------|-----------|
| **P1** | Drop everything | Immediate (< 1 hour) |
| **P2** | Next sprint | 1-2 weeks |
| **P3** | Backlog | Months |
| **P4** | Nice-to-have | When we have time |

---

### **Impact** (User)
How many users are affected?

| Impact | Users Affected | Business Loss |
|--------|----------------|---------------|
| **High** | >50% users | Major revenue/productivity loss |
| **Medium** | 10-50% users | Moderate impact |
| **Low** | <10% users | Minimal impact |
| **None** | Internal only | No user-facing impact |

---

### **The Matrix: Severity + Impact = Priority**

| Severity | Impact | Priority | Example |
|----------|--------|----------|---------|
| Critical | High | **P1** | All users cannot login |
| Critical | Low | **P2** | Admin tool broken, 2 users affected |
| High | High | **P1** | 60% build failures across clusters |
| High | Medium | **P2** | Specific feature broken for 20% users |
| Medium | Low | **P3** | Single user reports UI glitch |
| Low | Any | **P4** | Typo in documentation |

---

## Real Examples: Good vs Bad Triage

### **Example 1: Login Issues**

#### **BAD Triage**
**Ticket:** INFRA-1234 - "Users can't login"

**Bad response:**
> "Assigned to Platform Team. Medium priority."

**Why it's bad:**
- No clarification: ALL users or one user?
- No severity assessment
- Medium priority for potential P1 incident?
- No immediate action taken

---

#### **GOOD Triage**
**Ticket:** INFRA-1234 - "Users can't login"

**Good response:**
```
Quick triage assessment:

Q: How many users affected?
A: Checking Grafana - login error rate currently 2% (not 100%)

Q: Since when?
A: Spike started 10 minutes ago

Q: Which service?
A: SSO service in prd-01 cluster only

**Decision:**
- Severity: High (partial outage)
- Impact: Low (single cluster, 2% error rate)
- Priority: P2
- Action: Assigned to Platform Team, monitoring for escalation

IF error rate reaches >10%, will escalate to P1 incident.
```

**Why it's good:**
- Asked clarifying questions
- Gathered data (Grafana check)
- Assessed scope (single cluster)
- Set clear escalation criteria
- Appropriate priority

---

### **Example 2: Build Failures**

#### **BAD Triage**
**Ticket:** INFRA-5678 - "My build failed"

**Bad response:**
> "Please provide more information."

**Why it's bad:**
- Too vague - WHAT information?
- No initial assessment
- User left uncertain
- No timeline for response

---

#### **GOOD Triage**
**Ticket:** INFRA-5678 - "My build failed"

**Good response:**
```
Thanks for reporting! To help diagnose this, please provide:

1. **PipelineRun name** (e.g., my-app-build-abc123)
2. **Namespace** (which tenant?)
3. **Cluster** (prd-01, prd-02, etc.)
4. **Error message** from the failed task
5. **When** did this start? (approximate time)

Meanwhile, I checked:
- No widespread build failures detected (success rate: 96%)
- No recent deployments or incidents

**Initial assessment:** Likely isolated issue. Once you provide the info above, we can investigate further.

**Workaround:** Try re-triggering the build (sometimes transient failures).
```

**Why it's good:**
- Specific information request
- Context: no widespread issue
- Suggested immediate workaround
- Set expectations (need more info)

---

### **Example 3: Feature Request**

#### **BAD Triage**
**Ticket:** INFRA-9999 - "Can we add support for ARM builds?"

**Bad response:**
> "Assigned to Engineering. Low priority."

**Why it's bad:**
- No validation of request
- No context gathering
- No stakeholder alignment
- Just dumped in Engineering backlog

---

#### **GOOD Triage**
**Ticket:** INFRA-9999 - "Can we add support for ARM builds?"

**Good response:**
```
Thanks for the feature request! A few clarifying questions:

1. **Use case:** What would you use ARM builds for?
2. **Urgency:** Is this blocking your team's roadmap?
3. **Volume:** How many ARM builds per day/week?
4. **Alternative:** Are you currently using external CI for ARM?

**Initial assessment:**
- This is a **feature request**, not a bug
- Requires architectural discussion (ARM runner support)
- May have cost implications (ARM infrastructure)

**Next steps:**
1. Product team to evaluate business case
2. Engineering to assess technical feasibility
3. Estimate effort & prioritize in roadmap planning

**Timeline:** Will discuss in next roadmap planning (2 weeks).

Labeled: `enhancement`, `arm-support`, `needs-discussion`
```

**Why it's good:**
- Recognized as feature request (not bug)
- Asked context questions
- Set realistic expectations
- Routed to product + engineering
- Clear next steps and timeline

---

### **Example 4: Duplicate Ticket**

#### **BAD Triage**
**Ticket:** INFRA-7777 - "Tekton UI slow"

**Bad response:**
> "Duplicate. Closed."

**Why it's bad:**
- No link to original ticket
- User left confused
- Missed opportunity to gather more data

---

#### **GOOD Triage**
**Ticket:** INFRA-7777 - "Tekton UI slow"

**Good response:**
```
Thanks for reporting! This appears to be related to ongoing issue INFRA-6666.

**Current status of INFRA-6666:**
- Root cause identified: Database connection pool exhaustion
- Fix in progress (ETA: 2 days)
- Workaround: Refresh page if load time > 10s

**Your report helps:** If you notice specific patterns (e.g., which pages are slowest), please add details to INFRA-6666.

Closing this as duplicate of INFRA-6666. Please follow that ticket for updates.

Link: https://jira.company.com/browse/INFRA-6666
```

**Why it's good:**
- Link to original ticket
- Current status provided
- Workaround offered
- Invited further input
- User knows what to expect

---

## Common Ticket Types & How to Handle

### **Type 1: User Error / Lack of Documentation**

**Example Ticket:**
> "How do I configure GitHub integration? It's not working."

**Triage Decision:**
- **Severity:** Low (not a bug)
- **Priority:** P3 (documentation gap)
- **Action:**
 1. Link to documentation
 2. If docs unclear, create sub-task to improve docs
 3. Close original ticket

**Response Template:**
```
Thanks for reaching out! GitHub integration is documented here: [link]

Steps:
1. Create GitHub App in your org
2. Add app credentials to Platform
3. Configure repository access

If you follow these steps and still have issues, please share:
- Error message
- Which step failed

If the documentation is unclear, let us know so we can improve it!
```

---

### **Type 2: Incident Report (Active Outage)**

**Example Ticket:**
> "URGENT: All pipelines failing right now!"

**Triage Decision:**
- **Severity:** Critical (if verified)
- **Priority:** P1
- **Action:**
 1. Verify immediately (check Grafana)
 2. If confirmed, declare incident
 3. Notify IC/on-call
 4. Link ticket to incident channel

**Response Template:**
```
Investigating immediately.

Grafana check: [current status]
- Pipeline success rate: X%
- Affected clusters: [list]
- Started: [time]

IF CONFIRMED OUTAGE:
→ Declaring Severity 1 incident
→ Creating incident Slack channel: #incident-2026-03-11-pipeline-outage
→ Incident Tracking Tool: [link]
→ Updates every 15 minutes

You can follow real-time updates in the incident channel.
```

---

### **Type 3: Performance Degradation**

**Example Ticket:**
> "Builds are taking 2x longer than normal"

**Triage Decision:**
- **Severity:** Medium (degradation, not outage)
- **Priority:** P2
- **Action:**
 1. Request specifics (which builds, clusters, timeframe)
 2. Check metrics (Grafana, Prometheus)
 3. Assign to observability team

**Response Template:**
```
Thanks for reporting! To investigate, please provide:

1. **PipelineRun examples** (slow build names)
2. **Expected vs actual duration** (e.g., normally 5min, now 10min)
3. **When did this start?** (today, yesterday, last week?)
4. **Which cluster?** (prd-01, prd-02, etc.)

Meanwhile, I'll check:
- Recent deployments (anything changed?)
- Cluster resource usage (CPU/memory spikes?)
- Build queue depth (backlog causing delays?)

Will update within 4 hours with findings.
```

---

### **Type 4: Feature Request**

**Example Ticket:**
> "Can we add Slack notifications for failed builds?"

**Triage Decision:**
- **Severity:** N/A (enhancement)
- **Priority:** P3 (backlog for planning)
- **Action:**
 1. Label as `enhancement`
 2. Validate use case
 3. Route to product team

**Response Template:**
```
Thanks for the suggestion! This is a feature request.

**Use case validation:**
- How would this help your team?
- What Slack channels should receive notifications?
- Should this be tenant-specific or global?
- Any similar tools you've used before?

**Next steps:**
1. Product team evaluates priority
2. Engineering estimates effort
3. Included in roadmap planning

**Interim workaround:** You can use PipelineRun webhooks to build custom Slack integration:
[link to webhook docs]

This will be discussed in next quarterly planning (ETA: 6 weeks).
```

---

### **Type 5: Security Concern**

**Example Ticket:**
> "I found a way to access other tenants' secrets"

**Triage Decision:**
- **Severity:** CRITICAL
- **Priority:** P1
- **Action:**
 1. **DO NOT discuss details publicly in ticket**
 2. Immediately escalate to security team
 3. Create private security incident
 4. Restrict ticket access

**Response Template:**
```
Thank you for reporting this responsibly.

This is a potential security issue. I'm escalating immediately to our security team.

**Next steps:**
1. Security team will contact you privately within 1 hour
2. Please DO NOT share details publicly (Slack, other tickets)
3. We will investigate and patch ASAP

For security issues, please use: security@company.com

Escalating now. Thank you for helping keep Platform secure.

Ticket visibility: RESTRICTED
```

---

## Red Flags: Immediate Action Required

**If you see ANY of these, escalate IMMEDIATELY:**

### **Red Flag #1: "Production Down"**
**Keywords:** production, outage, down, unavailable, cannot access

**Action:**
1. Verify immediately (check Grafana, user reports)
2. Declare incident if confirmed
3. Notify on-call engineer
4. Create incident Slack channel

---

### **Red Flag #2: "All Users"**
**Keywords:** all users, everyone, widespread, global

**Action:**
1. Check scope (really ALL or exaggeration?)
2. If >50% users affected → declare incident
3. Immediate triage call

---

### **Red Flag #3: Security Keywords**
**Keywords:** security, vulnerability, exploit, secrets exposed, unauthorized access

**Action:**
1. Escalate to security team immediately
2. Restrict ticket visibility
3. Do NOT discuss in public channels

---

### **Red Flag #4: Data Loss**
**Keywords:** deleted, lost data, corruption, cannot recover

**Action:**
1. Assess scope (how much data, which users?)
2. Check backups availability
3. Escalate to data team + management
4. Potential incident declaration

---

### **Red Flag #5: Executive/Customer Escalation**
**Keywords:** VP mentioned, customer complaint, executive escalation, urgent from [executive name]

**Action:**
1. Acknowledge immediately (within 15 minutes)
2. Provide status update
3. Loop in team lead/manager
4. Set clear timeline for resolution

---

## Communication Templates

### **Template 1: Need More Information**

```
Thanks for reporting! To help diagnose this, please provide:

1. [Specific question 1]
2. [Specific question 2]
3. [Specific question 3]

Meanwhile, I checked:
- [What you already verified]

**Initial assessment:** [Your current understanding]

**Next steps:** [What happens after they provide info]
```

---

### **Template 2: Assigning to Team**

```
Thanks for reporting! This appears to be related to [component/service].

**Assigned to:** [Team name]
**Priority:** [P1/P2/P3]
**Expected response time:** [Timeline]

**What [Team] will do:**
1. [Step 1]
2. [Step 2]

**You can expect:** [What reporter should expect and when]

You'll receive updates [frequency] in this ticket.
```

---

### **Template 3: Closing as Not a Bug**

```
Thanks for reporting! After investigation, this is actually [expected behavior / user error / documentation issue].

**Explanation:** [Why it works this way]

**How to achieve what you want:**
1. [Step 1]
2. [Step 2]

**Documentation:** [Link to docs]

If this doesn't solve your issue, please reopen and provide [additional info].

Closing as [reason]. Feel free to reopen if needed.
```

---

### **Template 4: Workaround Available**

```
Thanks for reporting! This is a known issue tracked in [JIRA-KEY].

**Root cause:** [Brief explanation]
**Fix ETA:** [Timeline]

**Workaround (immediate):**
1. [Step 1]
2. [Step 2]

This should allow you to continue working while we implement the permanent fix.

Please confirm if the workaround works for you.

Following [original ticket] for resolution.
```

---

### **Template 5: Escalating**

```
Escalating this issue due to [reason].

**Current status:**
- Impact: [severity/scope]
- Users affected: [number/percentage]
- Started: [time]

**Actions taken so far:**
1. [What you've done]
2. [What you've checked]

**Escalation:**
- Notified: [team/person]
- Incident channel: [link]
- Incident Tracking Tool: [link]

**Next update:** [timeframe]
```

---

## Anti-Patterns & Mistakes

### **Anti-Pattern 1: Analysis Paralysis**

**Problem:**
> Spending 30 minutes reading ticket, checking every log, researching before responding.

**Impact:** Other tickets pile up, urgent issues delayed.

**Fix:**
- Initial triage: **2-5 minutes MAX**
- Quick assessment → route to expert
- Deep investigation comes AFTER triage

---

### **Anti-Pattern 2: The Silent Treatment**

**Problem:**
> Ticket sits with no response for hours/days.

**Impact:** User frustrated, escalates to management, reputation damage.

**Fix:**
- Acknowledge **within 15 minutes** (during business hours)
- Even if just: "Investigating, will update in 1 hour"

---

### **Anti-Pattern 3: "Not My Problem"**

**Problem:**
> "This isn't SRE's responsibility. Reassigning." (no helpful context)

**Impact:** Ticket ping-pongs between teams, no one owns it.

**Fix:**
```
This appears to be [Team]'s area. Assigning to them.

@[Team]: This is related to [component]. User is experiencing [issue].

@Reporter: [Team] will take this from here. Expected response: [timeline].
```

---

### **Anti-Pattern 4: Assuming Context**

**Problem:**
> Ticket says "The thing is broken" → You assign to team without clarifying WHAT thing.

**Impact:** Team confused, asks same questions you should have asked.

**Fix:**
- Always ask clarifying questions FIRST
- Don't route until you understand the issue

---

### **Anti-Pattern 5: Priority Inflation**

**Problem:**
> User says "URGENT" → You mark P1 without verifying.

**Impact:** Desensitization to real P1 issues.

**Fix:**
- Verify urgency independently
- Use the Severity + Impact matrix
- Challenge inflated priorities with data

---

### **Anti-Pattern 6: Duplicate Without Context**

**Problem:**
> "Duplicate. Closed." (no link, no explanation)

**Impact:** User feels dismissed, doesn't know where to follow up.

**Fix:**
- Link to original ticket
- Explain current status
- Invite additional input if useful

---

## Triage Decision Tree

```
New Ticket Arrives
 ↓
 Is production down? 
 YES → VERIFY → Declare Incident → P1 
 NO → Continue 
 
 Is this a security issue? 
 YES → Escalate to Security → Restrict Visibility 
 NO → Continue 
 
 Is information complete? 
 NO → Request specific info → Wait for response 
 YES → Continue 
 
 Is this a duplicate? 
 YES → Link original → Provide status → Close 
 NO → Continue 
 
 What type is it? 
 Bug → Assess severity/impact → Assign to team 
 Feature Request → Label → Route to product 
 Question → Answer or link docs → Close 
 Incident → See "production down" above 
 User Error → Provide guidance → Close 
 
 Priority? (Severity + Impact) 
 P1 → Immediate action (< 1 hour) 
 P2 → Next sprint (1-2 weeks) 
 P3 → Backlog (months) 
 P4 → Nice-to-have (when available) 
 
Final: Document decision, set expectations, update ticket 
```

---

## Triage Workflow Checklist

**For every ticket:**

- [ ] **Read carefully** (don't skim - read fully)
- [ ] **Check for red flags** (production down, security, all users, data loss)
- [ ] **Verify with data** (Grafana, logs, metrics - don't just trust reporter)
- [ ] **Assess scope** (1 user vs 10% vs 50% vs all users)
- [ ] **Determine priority** (Severity + Impact matrix)
- [ ] **Ask clarifying questions** if information missing
- [ ] **Assign to correct team** with context
- [ ] **Set expectations** (timeline, next steps)
- [ ] **Acknowledge receipt** (within 15 minutes if urgent)
- [ ] **Document decision** (why you prioritized this way)
- [ ] **Add labels** (component, type, priority)
- [ ] **Link related tickets** (duplicates, related issues)

---

## Summary: The 3 Principles of Good Triage

### **1. Speed + Accuracy**
- Initial assessment: **2-5 minutes**
- Acknowledge: **Within 15 minutes**
- Don't rush → but don't over-analyze

### **2. Context + Communication**
- Understand the issue before routing
- Provide context when assigning
- Set clear expectations

### **3. Empathy + Professionalism**
- Reporter is frustrated (bug/outage affecting them)
- Be helpful, not dismissive
- "Thanks for reporting" goes a long way

---

## Quick Reference: Triage Time Budgets

| Situation | Time Budget | Action |
|-----------|-------------|--------|
| **IC Shift** | 2-3 min/ticket | Quick assessment, route or escalate |
| **Backlog Grooming** | 5-10 min/ticket | Detailed review, prioritize, clean up |
| **Red Flag Detected** | Immediate | Escalate within 5 minutes |
| **Need More Info** | 2 min | Send clarifying questions, move on |
| **Complex Issue** | 5 min triage | Route to expert, don't solve during triage |

---

**Remember:** Triage is about **routing**, not **solving**. Your job is to get the ticket to the right person at the right priority - not to fix it yourself during triage.

Good triage = tickets don't fall through cracks + urgent issues escalated fast + teams have context to act.

 **Happy triaging!**

---

## Appendix A: Real Practice Example

**See detailed walkthrough:** [jira-triage-example-INFRA-456.md](./jira-triage-example-INFRA-456.md)

**Ticket:** INFRA-456 - CrashLoopBackOff in production cluster

**Key Learning:**
> Sometimes the best triage action is NO ACTION. This example shows a ticket that is already properly handled - assigned to the right expert, with complete information, and actively in progress.

**What makes it a good example:**
- Demonstrates the 5-Question Framework in action
- Shows when NOT to intervene
- Explains the difference between infrastructure issues and user impact
- Includes scoring and learning points

**Use this example to:**
- Practice applying the framework
- Learn when to leave tickets alone
- Understand proper escalation criteria

---

**Document Version:** 1.0
**Created:** 2026-03-11
**Author:** Documentation Team
**Last Updated:** 2026-03-11
