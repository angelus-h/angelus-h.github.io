# Incident Manager Handbook

**Version:** 1.0
**Date:** 2026-03-10
**Author:** Documentation Team
**Purpose:** Complete guide to Incident Management, RCA facilitation, and Incident Tracking Tool usage

---

## Table of Contents

1. [What is an Incident Manager?](#what-is-an-incident-manager)
2. [Incident Manager vs Interrupt Catcher](#incident-manager-vs-interrupt-catcher)
3. [The Incident Lifecycle](#the-incident-lifecycle)
4. [Slack Incident Coordination](#slack-incident-coordination)
5. [RCA Meeting Facilitation](#rca-meeting-facilitation)
6. [Incident Tracking Tool Tool Usage](#incident-tracker-tool-usage)
7. [Communication Templates](#communication-templates)
8. [Common Mistakes](#common-mistakes)
9. [Best Practices](#best-practices)
10. [Quick Reference](#quick-reference)

---

## What is an Incident Manager?

### The Core Concept

An **Incident Manager** (also called **Incident Commander** or IC) is **not the technical expert** who fixes the problem. Instead, they are the **coordinator** who brings structure to chaos.

**Mental Model:**
```
Chaos → Structure → Resolution
```

### Key Responsibilities

The Incident Manager:

1. **Provides structure** to chaotic incident communication
2. **Ensures all relevant teams** participate
3. **Summarizes the situation** regularly
4. **Maintains focus** on resolution
5. **Documents key information** for post-incident analysis

**What the IM does NOT do:**
- Solve the technical problem (that's the engineering team's job)
- Know everything immediately
- Write code or deploy fixes

**What the IM DOES do:**
- Ask the right questions
- Coordinate multiple teams
- Keep stakeholders informed
- Document timeline and decisions

---

### The Real Skill: Coordination, Not Expertise

> "A good Incident Manager doesn't necessarily know how to fix the database, but they know who to ask, how to coordinate the response, and how to keep everyone aligned."

This is **technical leadership**, not just technical expertise.

---

## Incident Manager vs Interrupt Catcher

Many engineers confuse these two roles. They are **different but related**.

### Comparison Table

| Aspect | Interrupt Catcher (IC) | Incident Manager |
|--------|------------------------|------------------|
| **Scope** | All incoming interrupts | Specific critical incident |
| **Duration** | Shift-based (FTS/24h rotation) | Duration of incident |
| **Primary Focus** | Triage alerts, review MRs, answer questions | Coordinate incident response |
| **Communication** | Multiple channels (#alerts, #support, Jira) | Focused incident Slack channel |
| **Outcome** | Distribute work, escalate issues | Resolve incident, document learnings |
| **Post-Incident** | Handover to next IC | Facilitate RCA meeting |

### How They Relate

**Common Pattern:**

```
Interrupt Catcher receives critical alert
 ↓
Escalates to Incident (based on severity/impact)
 ↓
Interrupt Catcher becomes Incident Manager
 ↓
Coordinates response until resolution
 ↓
Facilitates RCA meeting
 ↓
Returns to IC duties
```

**Alternative Pattern (Larger Organizations):**

```
IC detects critical incident
 ↓
Declares incident and creates Slack channel
 ↓
Dedicated Incident Manager takes over coordination
 ↓
IC continues other duties (alerts, MRs)
```

---

## The Incident Lifecycle

Understanding the full lifecycle helps you know what to do at each stage.

### Complete Flow

```
1. Detection
 Alert triggered
 User report
 Manual discovery

2. Declaration
 IC evaluates severity
 Creates incident Slack channel
 Invites relevant teams

3. Coordination (Incident Manager takes over)
 Acknowledges ownership
 Gathers initial context
 Summarizes situation
 Assigns investigation threads
 Provides regular updates

4. Investigation
 Teams investigate in parallel
 IM keeps everyone aligned
 IM documents key findings

5. Mitigation
 Fix identified
 Fix deployed
 Service recovery confirmed

6. Resolution
 Incident declared resolved
 Slack channel archived
 Transition to RCA

7. Post-Incident (RCA)
 IM facilitates RCA meeting
 Incident Tracking Tool document created
 Action items tracked
 Learnings shared
```

---

## Slack Incident Coordination

This is where most engineers feel lost. Here's the complete playbook.

### Phase 1: Joining the Incident Channel

**When:** You're notified of a critical incident and join the Slack channel.

**Your First Message (Template):**

```
SRE here, joining as incident manager.

Give me a few minutes to review the current context and timeline.

In the meantime, please share any relevant logs, alerts, or recent changes here.

I'll summarize the situation shortly.
```

**Why this works:**
- Signals **ownership** (someone is in charge)
- Buys you **time** to understand the situation
- **Activates the team** (starts gathering information)
- **Promises structure** (summary coming)

**What NOT to say:**
- "What's going on?" (passive, looks lost)
- "Can someone explain the issue?" (no ownership)
- "Hi everyone" (too casual, no authority)

---

### Phase 2: Initial Situation Summary (5-10 minutes)

**Template:**

```
Current summary (please correct if needed):

• Impact: <what is broken/affected>
• Start time: <when it started>
• Systems involved: <service/cluster/component>
• Current observations: <logs/alerts/symptoms>

Investigation ongoing.
```

**Example:**

```
Current summary (please correct if needed):

• Impact: Platform Dataplane API returning 500 errors
• Start time: 10:05 UTC (approx 15 minutes ago)
• Systems involved: platform-dataplane service, AWS platform-prd-es01 cluster
• Current observations: PagerDuty alert triggered, error rate 45%

Investigation ongoing.
```

**Why this matters:**
- Creates **shared understanding**
- Prevents **duplicate work**
- Gives management **visibility**
- Establishes **baseline** for future updates

---

### Phase 3: Assign Investigation Threads

**Template:**

```
Proposed investigation threads:

1⃣ Recent deployments/changes
2⃣ Service health and logs
3⃣ Infrastructure/cluster status

If someone is already looking into one of these, please confirm here.
```

**Why this works:**
- **Parallelizes work** across teams
- **Prevents chaos** (everyone knows their task)
- **Avoids duplicate effort**

---

### Phase 4: Regular Status Updates (Every 10-15 minutes)

**Template:**

```
Quick update:

• Deployment check — no suspicious changes in last 2 hours
• Logs review — investigating error pattern in pod xyz-123
• Cluster health — CPU/memory normal

Continuing analysis.
```

**Critical Rule:**

> Even if you have **no new information**, post an update every 10-15 minutes.

**Example of "no progress" update:**

```
Quick update:

No obvious root cause identified yet.

Team continues investigating logs and recent configuration changes.

Next update in 10 minutes.
```

**Why regular updates matter:**
- Management sees **control** (even without resolution)
- Team stays **aligned**
- Prevents **duplicate questions**

---

### Phase 5: Mitigation/Resolution

**When a fix is identified:**

```
Update: potential fix identified.

Team is deploying <describe mitigation>.

Monitoring for recovery.
```

**When service recovers:**

```
Update: service appears to be stable.

Error rate returned to normal (<1%).

We will continue root cause analysis and document findings in Incident Tracking Tool.
```

**Transition to RCA:**

```
Incident resolved.

Thank you everyone for the quick response.

I'll schedule an RCA meeting for <day/time> to review timeline and identify improvements.

Incident Tracking Tool document will be created at <link>.
```

---

### The Three Questions Framework

During any incident, the IM always knows the answer to:

1. **What is broken?**
2. **Who is investigating?**
3. **When is the next update?**

If these are visible in the Slack channel, the incident is **under control**.

---

## RCA Meeting Facilitation

The RCA meeting is **separate from the incident**. It happens **after resolution**, often 1-3 days later.

### Meeting Purpose

**Goal:** Learn from the incident and improve systems.

**NOT the goal:**
- Find who to blame
- Punish individuals
- Debate technical details endlessly

**Culture:** **Blameless Postmortem**

---

### Meeting Structure (30-60 minutes)

```
1. Opening (5 min)
 Set blameless tone

2. Timeline Reconstruction (10 min)
 Walk through what happened when

3. Technical Explanation (10 min)
 What actually failed

4. Root Cause Analysis (10 min)
 Why did it happen (5 Whys method)

5. What Worked Well (5 min)
 Positive reinforcement

6. Improvements (10 min)
 What should we change

7. Action Items (5 min)
 Owners and deadlines

8. Summary (5 min)
 Next steps
```

---

### Opening the Meeting

**Your first words set the tone.**

**Template:**

```
Thanks everyone for joining.

The goal of this meeting is to understand what happened during the incident,
learn from it, and identify improvements to prevent similar issues in the future.

This is a blameless discussion — we're focusing on systems and processes,
not individuals.

Let's get started.
```

**Why this matters:**
- Reduces **anxiety**
- Sets **psychological safety**
- Focuses on **learning**

---

### Timeline Reconstruction

**This is the most important part of the RCA.**

**Questions to ask:**

```
Let's reconstruct the timeline.

When was the first alert triggered?
When did the impact start?
When did we detect the incident?
When did investigation begin?
When was root cause identified?
When was mitigation deployed?
When did service recover?
```

**Document as you go:**

```
Timeline Summary:
10:02 UTC - Alert triggered (CPU spike)
10:05 UTC - Investigation started
10:18 UTC - Root cause identified (config error)
10:24 UTC - Mitigation deployed
10:31 UTC - Service recovered
```

**Why timeline is critical:**
- **Objective facts** (not opinions)
- Reveals **gaps in detection**
- Shows **response efficiency**
- Foundation for **process improvements**

---

### Root Cause Analysis (5 Whys Method)

**Don't stop at the surface-level cause.**

**Example:**

```
Why did the service fail?
→ Because the configuration was invalid.

Why was the configuration invalid?
→ Because validation was missing in the deployment process.

Why was validation missing?
→ Because the CI pipeline didn't check configuration syntax.

Why didn't the CI pipeline check it?
→ Because configuration validation was not part of the deployment checklist.

Why wasn't it in the checklist?
→ Because we didn't have a process for updating deployment requirements.
```

**Root Cause:** Missing process for deployment checklist updates.

**This leads to:** Process improvement, not individual blame.

---

### What Worked Well

**This is often skipped but very important.**

**Ask:**

```
What helped us detect or mitigate the incident quickly?

What processes or tools worked as expected?
```

**Example responses:**
- Monitoring alerts worked correctly
- Rollback process was smooth
- Team responded quickly
- On-call documentation was clear

**Why this matters:**
- **Reinforces good practices**
- **Boosts team morale**
- **Identifies what NOT to change**

---

### Improvements and Action Items

**Ask:**

```
What concrete changes could prevent this issue in the future?
```

**Categories:**
- Monitoring (better alerts)
- Automation (reduce manual steps)
- Documentation (clearer runbooks)
- Process (deployment checks)
- Architecture (redundancy)

**Action Item Format:**

Every action must have:

```
Action: <specific task>
Owner: <person or team>
Due date: <realistic deadline>
Tracking: <Jira ticket ID>
```

**Example:**

```
Action: Add configuration validation to CI pipeline
Owner: Platform Team (@john)
Due date: Next sprint (2026-03-24)
Tracking: INFRA-XXXX
```

---

### Closing the Meeting

**Template:**

```
Thanks everyone for the input.

We'll document the findings in Incident Tracking Tool and track the action items in Jira.

The goal is to ensure we learn from this incident and improve the system.

I'll share the Incident Tracking Tool document in #announce-platform-incidents when it's ready.
```

---

### Facilitator's Role

**You are NOT:**
- The technical expert
- The problem solver
- The decision maker

**You ARE:**
- The **timekeeper**
- The **question asker**
- The **note taker** (or you assign someone)
- The **focus maintainer**

**When the meeting goes off-track:**

```
Let's return to the timeline for a moment.

or

Let's focus on the root cause rather than implementation details.
```

---

## Incident Tracking Tool Tool Usage

**Incident Tracking Tool is the documentation tool**, not the process itself.

### When to Create Incident Tracking Tool Document

**Timing:** After incident resolution, before RCA meeting.

**Who creates it:** The Incident Manager.

### Incident Tracking Tool Structure

Typical sections:

```
1. Incident Summary
 - Impact
 - Start/end time
 - Affected services

2. Timeline
 - Key events with timestamps

3. Root Cause
 - What failed
 - Why it failed

4. Resolution
 - How it was fixed

5. What Worked Well
 - Positive observations

6. Action Items
 - Improvements with owners

7. Supporting Data
 - Logs, metrics, screenshots
```

---

### How to Use Incident Tracking Tool

**Step 1: Create RCA**

Go to: https://incident-tracker.company.com/

Click: "Create New RCA"

**Step 2: Fill Basic Info**

```
Title: [Date] [Service] [Brief Description]
Example: 2026-03-10 Platform Dataplane API 500 Errors

Status: Draft

Severity: Critical / High / Medium / Low

Affected Services: platform-dataplane, platform-prd-es01
```

**Step 3: Timeline Section**

Copy from your Slack incident channel:

```
10:02 UTC - PagerDuty alert triggered
10:05 UTC - Incident Manager joined
10:08 UTC - Initial summary posted
10:18 UTC - Root cause identified
10:24 UTC - Mitigation deployed
10:31 UTC - Service recovered
```

**Step 4: Root Cause**

```
Configuration error in dataplane deployment caused API to reject requests.

Validation was missing in CI pipeline, allowing invalid config to be deployed.
```

**Step 5: Action Items**

```
1. Add config validation to CI (Owner: Platform Team, Due: 2026-03-24)
2. Update deployment runbook (Owner: SRE Team, Due: 2026-03-17)
3. Improve alert description (Owner: Observability, Due: 2026-03-20)
```

**Step 6: Share**

- Post link in incident Slack channel
- Share in #announce-platform-incidents
- Link in RCA meeting invite

---

## Communication Templates

### Quick Copy-Paste Templates

#### 1. Joining Incident

```
SRE here, joining as incident manager.

Give me a few minutes to review the current context and timeline.

In the meantime, please share any relevant logs, alerts, or recent changes here.

I'll summarize the situation shortly.
```

---

#### 2. Initial Summary

```
Current summary (please correct if needed):

• Impact: <describe>
• Start time: <time>
• Systems involved: <systems>
• Current observations: <symptoms>

Investigation ongoing.
```

---

#### 3. Investigation Threads

```
Proposed investigation threads:

1⃣ Recent deployments/changes
2⃣ Service health and logs
3⃣ Infrastructure status

If someone is already looking into one of these, please confirm here.
```

---

#### 4. Status Update

```
Quick update:

• <Thread 1> — <status>
• <Thread 2> — <status>
• <Thread 3> — <status>

Continuing analysis. Next update in 10 minutes.
```

---

#### 5. Resolution

```
Update: service appears to be stable.

<Metric> returned to normal.

We will continue root cause analysis and document findings in Incident Tracking Tool.
```

---

#### 6. RCA Meeting Opening

```
Thanks everyone for joining.

The goal of this meeting is to understand what happened, learn from it,
and identify improvements.

This is a blameless discussion focusing on systems and processes.
```

---

#### 7. Timeline Reconstruction

```
Let's reconstruct the timeline.

When was the first alert triggered?
When did the impact start?
When did investigation begin?
When was mitigation deployed?
```

---

#### 8. Root Cause Questions

```
What technically failed?

Why didn't our safeguards prevent it?

What conditions allowed this to happen?
```

---

#### 9. Meeting Refocus

```
Let's return to the timeline for a moment.

or

Let's focus on the root cause rather than implementation details.
```

---

## Common Mistakes

### Mistake 1: Trying to Solve the Problem Yourself

**Wrong approach:**
> "Let me debug this..."

**Incident Manager role:**
> "Who can investigate the logs? Who can check recent deployments?"

**Why:** Your job is coordination, not debugging.

---

### Mistake 2: No Regular Updates

**Problem:** Silence for 30 minutes during incident.

**Impact:** Management thinks incident is out of control.

**Solution:** Update every 10-15 minutes, even if no progress.

**Template for "no news":**
```
Quick update: No root cause identified yet. Investigation continues.
```

---

### Mistake 3: Too Many Threads

**Problem:** Assigning 10 different investigation tasks.

**Impact:** Confusion, duplicate work, no focus.

**Solution:** Start with 2-3 clear threads. Add more only if needed.

---

### Mistake 4: Blame Culture in RCA

**Problem:** "Why did John deploy the broken config?"

**Impact:** Team becomes defensive, no learning happens.

**Solution:** "Why didn't our deployment process catch the invalid config?"

**Focus on:** Systems and processes, not individuals.

---

### Mistake 5: No Timeline Documentation

**Problem:** RCA meeting with no clear timeline.

**Impact:** Cannot identify gaps in detection or response.

**Solution:** Document timeline during incident in real-time.

---

### Mistake 6: No Action Items

**Problem:** RCA ends with "we should do better monitoring."

**Impact:** Nothing changes.

**Solution:** Specific actions with owners and deadlines.

```
 "Improve monitoring"
 "Add alert for invalid config deployment (Owner: SRE, Due: 2026-03-20)"
```

---

## Best Practices

### Practice 1: Acknowledge Immediately

**Even if you don't understand the incident yet**, post your joining message.

**This alone** reduces management anxiety by 80%.

---

### Practice 2: Use Threads for Deep Technical Discussion

**Main channel:** Status updates and decisions

**Thread:** Detailed technical debugging

**Why:** Keeps main channel readable for leadership.

---

### Practice 3: Document in Real-Time

Keep a scratchpad during the incident:

```
10:05 - IM joined
10:08 - Summary posted
10:15 - Platform team investigating logs
10:18 - Found error in config
10:24 - Rollback deployed
```

This becomes your Incident Tracking Tool timeline.

---

### Practice 4: Schedule RCA Meeting Quickly

**Don't wait weeks.** Schedule within 1-3 days while memory is fresh.

**Invite:**
- Incident participants
- Team leads
- Relevant stakeholders

---

### Practice 5: Follow Up on Action Items

Create Jira tickets **during the RCA meeting**.

Track them in team standups.

---

### Practice 6: Share Learnings Widely

Post Incident Tracking Tool summary in:
- #announce-platform-incidents
- Team Slack channels
- Wiki/Confluence

**Why:** Other teams learn from your incident.

---

## Quick Reference

### Incident Manager Cheat Sheet

```
1. Acknowledge (join incident channel, claim ownership)
2. Gather context (read logs, alerts, recent changes)
3. Summarize situation (post initial summary)
4. Assign investigation threads (2-3 parallel tracks)
5. Provide regular updates (every 10-15 min)
6. Document timeline (real-time notes)
7. Confirm mitigation (verify service recovery)
8. Transition to RCA (schedule meeting, create Incident Tracking Tool)
```

---

### RCA Facilitator Cheat Sheet

```
1. Open with blameless statement
2. Reconstruct timeline
3. Explain what failed technically
4. Identify root cause (5 Whys)
5. Discuss what worked well
6. Define improvements
7. Create action items (owner + deadline)
8. Document in Incident Tracking Tool
```

---

### Three Questions to Always Answer

During any incident:

1. **What is broken?**
2. **Who is investigating?**
3. **When is the next update?**

If everyone knows the answer to these, you're doing well.

---

### When to Escalate

**Escalate when:**
- Impact is severe and growing
- You need more engineering resources
- Multiple teams need coordination
- Incident duration > 1 hour with no progress
- You're overwhelmed with other IC duties

**How to escalate:**
```
@team-lead Escalating: incident impact increasing, need additional engineering support.
```

---

## Appendix: Related Documents

### Internal Documentation

- **Interrupt Catcher Guide:** `/learning-plans/spre-interrupt-catcher-guide.md`
- **App-SRE Incident Process:** `/repos/app-interface/docs/sre-team/incident-process.md`
- **App-SRE IC Process:** `/repos/app-interface/docs/sre-team/interrupt-catching.md`

### External Resources

- **Incident Tracking Tool Tool:** https://incident-tracker.company.com/
- **PagerDuty:** https://company.pagerduty.com/
- **Slack Channels:**
 - `#announce-platform-incidents`
 - `#sd-sre-team-oncall`
 - `#incident-tracker-status-board-adoption`

---

## Document History

| Date | Version | Changes | Author |
|------|---------|---------|--------|
| 2026-03-10 | 1.0 | Initial creation based on real-world experience and ChatGPT discussion | Documentation Team |

---

## Feedback

This is a living document. If you have suggestions or encounter situations not covered here:

1. Create a Jira ticket or
2. Reach out in #platform-sre-team or
3. Submit a pull request to update this guide

**The goal:** Every SRE engineer should feel confident managing incidents and facilitating RCAs.

---

**Remember:** You don't need to know how to fix every technical problem. You need to know how to coordinate people who do, keep everyone informed, and help the team learn from the experience.

**Good luck, and may your incidents be few and short!** 
