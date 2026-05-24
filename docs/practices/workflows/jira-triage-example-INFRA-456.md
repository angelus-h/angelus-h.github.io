# Jira Triage Practice Example: PLATFORM-11591 

**Practice Date:** 2026-03-11
**Ticket:** PLATFORM-11591
**URL:** https://jira.company.com/browse/PLATFORM-11591
**Triage Method:** 5-Question Framework

---

## Ticket Overview

**Summary:** CrashLoopBackOff: 7 instances of POD: 'network-controller' in namespace: 'kube-network-system' in cluster: 'production-cluster-01'

**Quick Facts:**
- **Status:** In Progress 
- **Assignee:** [Infrastructure Engineer]
- **Reporter:** [Performance Team Engineer]
- **Created:** 2025-12-19
- **Priority:** Normal
- **Type:** Bug
- **Components:** Infrastructure

---

## Triage Analysis Using 5-Question Framework

### **Question 1: What is actually broken?**

**Analysis:**
- **Type:** Infrastructure issue (networking pods crashing)
- **Component:** `network-controller` pods (Kubernetes OVN Kubernetes networking)
- **Symptom:** CrashLoopBackOff (pods repeatedly crashing and restarting)
- **Scope:** 7 pods affected in single cluster (production-cluster-01)
- **Detection:** Automated monitoring tool (proactive, not user-reported)

**Assessment:**
- This is a **bug** (pods should not crash)
- Infrastructure-level (networking component)
- Potentially serious (networking is critical infrastructure)

---

### **Question 2: Who is affected?**

**Analysis:**
- **Cluster:** production-cluster-01 (PRODUCTION cluster) 
- **Namespace:** kube-network-system (system namespace)
- **Component:** network-controller (runs on worker nodes for pod networking)
- **7 pods affected** - likely 7 worker nodes having networking issues

**Impact Assessment:**
- **Production environment** (serious)
- **Networking component** (can affect user workloads)
- **Unknown user impact** - are users experiencing networking issues?

**Questions I would ask (if this needed action):**
1. Are users reporting networking problems?
2. Are pods on these affected nodes unable to communicate?
3. Is this affecting workload availability?

---

### **Question 3: What is the business impact?**

**Current Evidence:**
- **No mention of user impact** in ticket
- **Detected by monitoring tool**, not user reports
- **Production environment** (high risk if degrades)
- **Networking component** (critical infrastructure)

**Business Impact Matrix:**

| Factor | Assessment |
|--------|------------|
| **User-facing?** | Unknown - likely yes if networking broken |
| **Blocking work?** | Unknown - no user reports mentioned |
| **Workaround available?** | Unknown |
| **Data loss risk?** | No |
| **Severity** | Medium-High (production networking) |
| **Impact** | Unknown (no confirmed user complaints) |

**Estimated Impact:**
- **High potential risk** (production networking component)
- **Low current confirmed impact** (no user complaints mentioned)
- **Priority: Watch closely** - could escalate if user impact emerges

---

### **Question 4: What information is missing?**

**What we HAVE:** 
- Affected pods (7 specific pod names with full details)
- Cluster identification (production-cluster-01)
- Namespace (kube-network-system)
- Timestamps (when crashes occurred - 2025-12-18)
- Logs attached (pod logs, descriptions, CSV)
- Detection method (automated perfscale tool)
- Tool details (GitHub link to monitoring tool)

**What we're MISSING:** 
- **User impact** - are workloads affected?
- **Root cause** - WHY are pods crashing?
- **Current status** - are pods still crashing NOW? (ticket mentions "last 24 hours")
- **Mitigation attempted** - what has been tried already?
- **Related alerts** - any PagerDuty, Grafana alerts fired?
- **Update since creation** - ticket created Dec 19, updated Mar 10 - what's the current state?

**Should I request more info?**
- **NO** - ticket already assigned and in progress
- Assignee ([Infrastructure Engineer]) should investigate root cause
- Information is sufficient for initial investigation
- Detailed logs and CSV attached for deep dive

---

### **Question 5: What is the right next action?**

**Current State:**
- Already assigned to: [Infrastructure Engineer] (Infrastructure team)
- Status: In Progress (actively being worked on)
- Has detailed information (logs, CSV, pod names, timestamps)
- Last updated: 2026-03-10 (recent activity)

**Triage Decision Matrix:**

| Action | Should I do it? | Reasoning |
|--------|----------------|-----------|
| **Escalate to incident?** | NO | No confirmed user impact, no outage reported |
| **Reassign to different team?** | NO | Infrastructure is correct team for OVN networking |
| **Request more info from reporter?** | NO | Reporter provided comprehensive details with logs |
| **Close/redirect?** | NO | Valid infrastructure bug in production |
| **Change priority?** | MAYBE | Consider upgrading IF user impact discovered |
| **Add to incident watch list?** | YES | Monitor for potential escalation |
| **Just monitor?** | YES | Watch for updates, verify resolution |
| **Take immediate action?** | NO | Already being handled appropriately |

**Recommended Action:** **NO TRIAGE ACTION NEEDED**

**Reasoning:**
- Ticket is already properly assigned to the right team
- Assignee is actively working on it (In Progress status)
- Comprehensive information provided (logs, CSV, affected pods)
- No immediate escalation needed (no user impact reported)

---

## Triage Summary Card

```

 TRIAGE ASSESSMENT: PLATFORM-11591 
 CrashLoopBackOff: network-controller pods 


Issue Type: Infrastructure Bug (Networking)
Severity: Medium-High (production networking pods crashing)
Impact: Unknown (no confirmed user reports)
Priority: Normal → APPROPRIATE 

Cluster: production-cluster-01 (PRODUCTION) 
Component: network-controller (OVN Kubernetes networking)
Affected: 7 pods / 7 worker nodes

Current State: In Progress
Assigned to: [Infrastructure Engineer] (Infrastructure)
Information: Complete (logs, timestamps, pod details)
Last Updated: 2026-03-10 (recent)



TRIAGE DECISION: NO ACTION REQUIRED

Reasoning:
1. Already assigned to correct team (Infrastructure)
2. Sufficient information provided (logs, CSV, pod details)
3. No confirmed user impact (monitoring detected, not users)
4. Assignee actively working on it (In Progress)
5. Priority appropriate (Normal without user impact)



WATCH FOR:
 User reports of networking issues in production-cluster-01
 Escalation to Severity 1 if user-facing impact emerges
 Reassignment if Manish requests additional expertise
 Ticket stale for >3 days without update



IF I WERE THE ASSIGNEE, INVESTIGATION STEPS:
1. Check if pods are STILL crashing (ticket mentions "last 24 hours")
2. Analyze attached logs for root cause (OOM? Config error? Node issue?)
3. Verify if user workloads affected (check pod communication)
4. Investigate OVN networking health on affected nodes
5. Check for recent cluster/networking changes
6. Update ticket with findings and root cause
```

---

## Learning Points from This Triage

### **What Was EXCELLENT About This Ticket:**

1. **Comprehensive Details**
 - Exact pod names provided
 - Cluster and namespace specified
 - Timestamps of crashes included
 - Logs and CSV attached

2. **Automated Detection**
 - Proactive monitoring tool found the issue
 - Not waiting for user complaints
 - Shows mature observability

3. **Proper Initial Routing**
 - Reporter correctly identified as Infrastructure issue
 - Assigned to domain expert ([Infrastructure Engineer])
 - Component tagged appropriately

4. **Clear Scope**
 - 7 specific pods listed
 - Single cluster affected (production-cluster-01)
 - Easy to reproduce/investigate

5. **Tool Transparency**
 - GitHub link to monitoring tool provided
 - Command used for detection shared
 - Reproducible findings

---

### **What Could Be IMPROVED:**

1. **Missing User Impact Assessment**
 - No mention if users experiencing issues
 - No check if workloads affected
 - **Recommended:** Add "User Impact: None reported" or "Investigating user impact"

2. **No Current Status Update**
 - Ticket says "last 24 hours" but created Dec 19
 - Are pods STILL crashing on Mar 10?
 - **Recommended:** Regular status updates

3. **No Mitigation Attempted Section**
 - What was tried before creating ticket?
 - Were pods restarted? Did it help?
 - **Recommended:** Add "Attempted fixes: None yet, needs investigation"

4. **Priority Might Be Too Low**
 - If production users affected, should be higher
 - Networking in production = potential P1
 - **Recommended:** Verify user impact before confirming priority

5. **Missing Related Context**
 - Any recent cluster changes?
 - Related to recent deployments?
 - **Recommended:** Check cluster change log

---

### **If I Were Commenting (Hypothetical):**

```markdown
Thanks for the detailed report and proactive detection!

**Quick questions to help prioritize:**
1. Are these pods currently still crashing (as of March 10)?
2. Are any user workloads experiencing networking issues on affected nodes?
3. Have we checked Grafana/PagerDuty for related user-impact alerts?

**Initial investigation plan:**
1. Analyze attached logs for root cause (checking now)
2. Verify current pod status
3. Check if user pods on these nodes can communicate
4. Investigate OVN networking health

**Will update within 4 hours with findings.**

If this is affecting users, please let me know immediately so we can escalate priority.
```

---

## **Key Triage Learnings**

### **Learning #1: Not Every Ticket Needs Action**

This ticket demonstrates a critical triage principle:

> **Sometimes the best triage action is NO ACTION.**

**Why?**
- Already assigned to the right expert
- Comprehensive information provided
- Active work in progress
- Priority appropriate for current evidence

**My job as triager:** Verify everything is handled correctly, then focus on tickets that need intervention.

---

### **Learning #2: Infrastructure vs User Impact**

**Important distinction:**
- **Infrastructure issue detected** ≠ **User-facing problem**
- Monitoring tools find issues early (before users notice)
- Priority should reflect ACTUAL impact, not POTENTIAL impact

**This ticket:**
- Infrastructure problem: YES (pods crashing)
- User impact: UNKNOWN (not mentioned)
- Priority: Normal (appropriate until user impact confirmed)

**If user impact emerges → immediately escalate to P1/P2**

---

### **Learning #3: Trust the Assignment**

**When to LEAVE a ticket alone:**
- Already assigned to domain expert
- Status shows active work
- Information is complete
- Recent updates visible

**When to INTERVENE:**
- Assigned to wrong team
- Missing critical information
- Priority misaligned with impact
- Stale (no updates for days)
- User impact escalating

---

### **Learning #4: Watch vs Act**

**Add to "Watch List" for:**
- Production infrastructure issues
- Potential for user impact
- Critical components (networking)

**Escalate immediately if:**
- User reports appear
- Error rate increases
- Ticket stale for >3 days

---

## **Triage Practice Score**

| Criteria | Score | Notes |
|----------|-------|-------|
| **Speed** | ⏱ 3 minutes | Quick assessment without deep diving |
| **Accuracy** | 95% | Correctly identified as properly handled |
| **Information Gathering** | 100% | Used all provided information |
| **Communication** | N/A | No comment needed (already assigned) |
| **Prioritization** | Correct | Normal priority appropriate without user impact |
| **Escalation Decision** | Correct | No escalation needed, monitoring recommended |
| **Assignment** | Correct | Left with Infrastructure expert |

**Overall Triage Quality:** **EXCELLENT**

**Reasoning:**
- Fast triage (3 minutes)
- Correct decision (no action needed)
- Identified what to watch for (user impact)
- Respected existing assignment

---

## **When to Re-Triage This Ticket**

**Trigger for Re-Assessment:**

1. **User reports networking issues in production-cluster-01**
 - Action: Escalate to P1/P2, declare potential incident

2. **Ticket stale for >3 days**
 - Action: Ping assignee for status update

3. **Crash rate increases (monitoring shows more pods)**
 - Action: Check if spreading, escalate if worsening

4. **Assignee requests help**
 - Action: Loop in additional teams (Networking, SRE)

---

## **Related Triage Guide Sections**

- **5-Question Framework:** See main guide, page 1
- **Severity vs Priority Matrix:** See main guide, page 3
- **Common Ticket Types:** Infrastructure bugs, page 7
- **Red Flags:** Production keywords, page 12
- **Decision Tree:** See main guide, page 20

---

## **Practice Summary**

**Ticket:** PLATFORM-11591
**Triage Time:** 3 minutes
**Decision:** No action needed (already properly handled)
**Key Lesson:** Not every ticket needs intervention during triage

**Practice Quality:** This is a realistic example of a well-triaged ticket that requires no additional action from the triager.

---

**End of Practice Example**

**Next Steps:**
- Practice on more tickets
- Compare your triage decisions with examples
- Build muscle memory for 5-Question Framework
