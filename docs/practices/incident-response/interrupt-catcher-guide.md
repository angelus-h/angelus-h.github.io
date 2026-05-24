# SRE Team Interrupt Catcher (IC) Role Guide

## What is the Interrupt Catcher (IC)?

The **Interrupt Catcher** is a rotation role within the SRE Team/SRE team designed to have a dedicated engineer handle incoming incidents, alerts, and tenant requests. This reduces interruptions for the rest of the team and provides a clear escalation point.

**IC Schedule:**
- **Follow The Sun (FTS)**: Business hours coverage (NASA and EMEA shifts)
- **Primary Oncall**: 24/7 weekly rotation (30-minute response time for critical alerts)
- **PagerDuty Schedules**:
 - FTS: https://redhat.pagerduty.com/schedules#PQ022DV
 - Primary: https://redhat.pagerduty.com/schedules#PHS3079

**Slack Handles:**
- `@sre-team-ic` - Interrupt Catcher
- `@sre-team-oncall` - 24/7 Oncall
- `@sre-team-onboarding-ic` - Onboarding support (separate rotation)

---

## IC Responsibilities - Priority Order

### 1. Critical Alerts (Highest Priority)

**What is this?**
- `critical` or `critical-fts` severity alerts via PagerDuty
- Slack channel: `#sd-sre-team-oncall`

**What to do:**
1. **Respond immediately** - This is the highest priority, everything else can wait!
2. **Assess the impact:**
 - Service completely down? → Incident declaration
 - Significant degradation? → Incident declaration
 - If uncertain → Escalate!
3. **Follow the incident process:** `/docs/sre-team/incident-process.md`
4. **Ask for help** if:
 - You're in a meeting and can't respond
 - You're overwhelmed with other tasks
 - You're not confident about the solution

**SOP Location:**
- **Runbook** link in the alert → Click it!
- Follow the steps described there

---

### 2. Production User-Reported Issues

**Where?**
- Slack channel: `#sd-sre-team`

**Types of issues:**
- User reports production problem but there's no alert
- User requests urgent MR merge because they're investigating a production incident
 - **This is the ONLY time it's OK to ping IC for an MR!**

**What to do:**
1. **Assess the impact:**
 - False positive (client-side issue)?
 - Real production problem?
2. **If no alert but real problem:**
 - Escalate to tenant team for impact assessment
 - Review incident criteria (`/docs/sre-team/incident-process.md`)
 - Decide: Is an incident needed?
3. **If MR merge needed during production incident:**
 - Prioritize the MR
 - Merge if approved
 - Notify the tenant

---

### 3. App-Interface Merge Request Reviews

**Dashboards:**
- [Merge Queue](https://gitlab.cee.redhat.com/service/app-interface-output/-/blob/master/app-interface-merge-queue.md)
- [Review Queue](https://gitlab.cee.redhat.com/service/app-interface-output/-/blob/master/app-interface-review-queue.md)
- [InScope Review Queue](https://inscope.corp.redhat.com/app-interface/review-queue)
- [InScope Merge Queue](https://inscope.corp.redhat.com/app-interface/merge-queue)

**SLO:**
- **3 hours** review time target

**What to do:**
1. Check the review queue regularly
2. Review MRs on a FIFO (First In First Out) basis
 - Exception: `bot/` labeled MRs get priority
3. Mark approved MRs with the `lgtm` label
4. If new onboarding is needed → See: `/docs/sre-team/re-onboarding-checklist.md`

**Detailed review guide:** `/docs/sre-team/sops/general/app-interface-review-process.md`

**Important rules:**
- Self-serviceable MRs do NOT require SRE Team review
- If tenant pings IC for MR:
 - Only OK during production incident
 - Otherwise: "Please wait in the queue, we review in FIFO order"

---

### 4a. ASIC Tickets Review & Triage

**What is ASIC?**
- JIRA project: https://jira.company.com/projects/ASIC/issues/
- Tenant requests and issues tracking
- Collaborative effort - context is in JIRA

**Rules:**
1. **ASIC tickets are NOT owned by anyone**
 - If you feel ownership is needed → Move to APPSRE board, assign to yourself
 - If not → Add context and unassign when shift ends
2. **Don't let tickets rot:**
 - If solution provided to tenant → Close it (tenant can reopen if needed)
3. **Not IC responsibility:**
 - Project work (e.g., install Jenkins plugin, new operator request)
 - These → Move to APPSRE board and notify grooming masters

**What to do:**
1. Review open ASIC tickets
2. Start investigation or resolve
3. Add findings to ticket
4. If resolved → Close
5. If project work → Move to APPSRE

---

### 4b. User Questions (#sd-sre-team)

**Types of questions:**
- How do I do X in app-interface?
- General app-interface questions

**Important exceptions:**
1. **If app status is `InProgress`:**
 - Redirect → `#sd-sre-team-onboarding` channel
2. **If OSD cluster question:**
 - If not app-interface managed → Redirect to `#forum-rosa-support`

**What to do:**
- Answer if you can
- Use documentation:
 - [Developer Guidelines](https://gitlab.cee.redhat.com/service/dev-guidelines)
 - [App-Interface FAQ](https://gitlab.cee.redhat.com/service/app-interface/-/blob/master/FAQ.md)
- If you can't answer → Escalate or create ASIC ticket

**Community Support:**
- Encourage users to help each other!
- If you see someone else knows the answer → Let them respond

---

### 5. High Alerts (#sd-sre-team-alert)

**Slack Channel:** `#sd-sre-team-alert`

**Not urgent**, but keep an eye on them!

**Process:**
1. Click on the alert's **Runbook** link
2. Read the SOP
3. Try to resolve
4. Can't resolve? → Escalate to tenant team
5. **If resolved:**
 - Check: Is this a recurring alert?
 - SOP incomplete or incorrect?
 - If yes → Create ASIC ticket

**IMPORTANT - Channel Maintenance:**
- **The IC is responsible for maintaining the `#sd-sre-team-alert` channel!**
- If you detect a flappy alert:
 - Downgrade severity → `medium`
 - Notify tenant to fix it
 - If the channel is overloaded with flappy alerts → Nobody can use it!

---

### 6. Clean Up Looping Integrations

**What is this?**
- Integration processes generate repetitive log messages
- Visible in CI output during MR review

**Examples:**

#### Unmerged Template MRs
```
[INFO] [DRY-RUN] [renderer.py:process_template:301] - diff for template xxx...
```
- Check open MRs: [Template MR Filter](https://gitlab.cee.redhat.com/service/app-interface/-/merge_requests/?label_name%5B%5D=template-output)
- If uncertain → Ask the person who could merge it

#### Unused Resources in SaaS Files
- SaaS files declare managed resource kinds
- Templates change → Tenants forget to remove old kinds
- **Remove** the orphaned resource kinds

**If you see any unexpected repeating messages in MRs:**
- Probably stale or missing config
- Fix it!

---

## Web-RCA Service Handling

### What is Web-RCA?

**Web-RCA** is an incident tracking tool that allows anyone at Company to create **RCAs (Root Cause Analysis)** / incident reports.

**Service Info:**
- **Owner:** SD OPS Dev team
- **Email:** sd-ops-services-dev@redhat.com
- **Slack Escalation:** `@status-board`

**Environments:**
- **Stage:** https://web-rca.stage.devshift.net
- **Production:** https://web-rca.devshift.net

**Architecture Doc:** https://gitlab.cee.redhat.com/service/web-rca/-/blob/main/docs/architecture.md

**Grafana Dashboard:** https://grafana.sre-team.devshift.net/d/_7_Ul_07k/web-rca?orgId=1

---

### Service Level Objectives (SLOs)

| SLO | Target | Measurement |
|-----|--------|-------------|
| **Availability** | 90% | Service uptime |
| **Latency** | 99% of requests < 1 second | p99 latency |

---

### Web-RCA Incident Handling

#### 1. Web-RCA Production Down

**Severity:** High

**Impact:**
- WebRCA UI down: https://web-rca.devshift.net/

**Access Required:**
- View access to prod cluster
- Namespace: `web-rca-production`

**Steps:**
1. **Confirm pod health:**
 ```bash
 oc get pods -n web-rca-production
 ```
 Console: https://console-openshift-console.apps.rosa.appsrep11ue1.tgem.p3.openshiftapps.com/k8s/ns/web-rca-production/pods

2. **Check service status:**
 ```bash
 oc get svc -n web-rca-production
 ```

3. **Optionally try to restart pods:**
 ```bash
 oc delete pod <pod-name> -n web-rca-production
 ```
 - Pod will be re-created from deployment

4. **Manually trigger deployment pipeline** (if necessary)

**Escalation:**
- Ping `@status-board` on Slack
- Email: sd-ops-services-dev@redhat.com

**Escalation Policy:**
- File: `/teams/sd-ops-dev/escalation-policies/web-rca.yml`
- **Note:** "This service is in stage only. Escalate only via the slack channel and e-mail."
- **Escalate after:** 1 hour of down-time
- **Do NOT start manager escalation**

---

#### 2. Web-RCA Low Availability (High 5xx Errors)

**Severity:** High

**Impact:**
- Users getting API errors
- Abnormally high 5xx error rate

**Access Required:**
- View access to cluster + namespace

**Steps:**

1. **Login to console and verify pods:**
 - Stage: https://console-openshift-console.apps.sre-team-stage-0.k3s7.p1.openshiftapps.com/k8s/ns/web-rca-stage/pods
 - Production: https://console-openshift-console.apps.rosa.appsrep11ue1.tgem.p3.openshiftapps.com/k8s/ns/web-rca-production/pods

2. **Check pod status:**
 ```bash
 oc get pods -n web-rca-production
 oc describe pod <pod-name> -n web-rca-production
 ```

3. **Check pod logs:**
 ```bash
 oc logs <pod-name> -n web-rca-production --tail=100
 ```

4. **Check for OOM (Out of Memory):**
 ```bash
 oc describe pod <pod-name> -n web-rca-production | grep -i oom
 ```

5. **Optionally:**
 - Delete pod → Re-creation
 - Increase memory limit → Redeploy
 - Trigger deployment pipeline manually

**Escalation:**
- Ping `@status-board` on Slack

---

#### 3. Web-RCA High Latency (p99 > 1 second)

**Severity:** Medium

**Impact:**
- Slow API responses
- User experience degradation

**Mitigation:**
1. Check Grafana dashboard for latency trends
2. Check database performance (RDS)
3. Check pod resource usage:
 ```bash
 oc top pods -n web-rca-production
 ```
4. Scale horizontally if needed
5. Escalate to SD OPS Dev if persistent

---

### Web-RCA Additional Resources

**Repository Locations:**
- **API:** https://gitlab.cee.redhat.com/service/web-rca
- **UI:** https://gitlab.cee.redhat.com/service/web-rca-ui
- **Ansible Integration:** https://gitlab.cee.redhat.com/sd-ops-services-dev/web-rca-ansible
- **Incident Summarizer:** https://github.com/RedHatInsights/web-rca-incident-summarizer

**SOP Documentation:**
- **Location:** `/home/mgreczi/repos/app-interface/docs/tenant-services/web-rca/sops/`
- Files:
 - `web-rca-production-down.md`
 - `web-rca-stage-down.md`
 - `web-rca-availability.md`
 - `web-rca-latency.md`
 - `disaster-recovery.md`
 - `load-testing.md`

---

## RCA (Root Cause Analysis) Process

### What is RCA?

**RCA = Root Cause Analysis**
- Document created after an incident
- Purpose: What happened? Why? How do we fix it?

### Pipeline RCA Process

**Documentation Location:** https://spaces.redhat.com/display/SP/Pipeline+RCA+Process (SAML protected)

**RCA Template in JIRA:**
- Location: `/home/mgreczi/repos/catalog/templates/jira/recent-incident-rca-description.j2`

**Sections:**
1. **Summary** - What problems occurred?
2. **Incident URLs** - PagerDuty incident links
3. **Guidelines & Help** - Follow Pipeline RCA Process
4. **Investigation Process** - SP Availability Async Investigation Process

**PagerDuty Access:**
- ServiceNOW self-service ticket
- Service: PagerDuty (Event Management)
- Request type: Access Request
- Team: Software Production
- Role: "I need to be able to acknowledge alerts"

---

## Handover Process

**Slack Channel:** `#sd-sre-team-handover`

**Timing:** End of shift (Slack reminder configured)

**Should include:**
1. **Active issues status:**
 - "Working on X incident, next IC should monitor Y"
2. **Helpful information:**
 - "Many MRs in queue, didn't have time to review"
 - "Open tenant request in #sd-sre-team about Z"
3. **If nothing:**
 - "There are no issues to handover."

**Always write a handover!** Even if it's empty.

---

## Schedule Conflicts

**Rules:**
- NO ONE should IC for both Fedramp AND Commercial environments
- NO ONE should be both onboarding-ic AND regular IC

**If there's a conflict:**
1. Review upcoming shift regularly
2. Reach out for swaps in teamchat or email
3. Last minute? → Still ask! Usually find coverage

---

## Resources & Quick Links

### Slack Channels
- `#sd-sre-team` - Main support channel
- `#sd-sre-team-oncall` - Critical alerts
- `#sd-sre-team-alert` - High severity alerts
- `#sd-sre-team-teamchat` - Team chat (ask for help here!)
- `#sd-sre-team-handover` - IC handover
- `#sd-sre-team-onboarding` - Onboarding support

### Dashboards
- [Merge Queue](https://inscope.corp.redhat.com/app-interface/merge-queue)
- [Review Queue](https://inscope.corp.redhat.com/app-interface/review-queue)
- [Self-Service Review Queue](https://inscope.corp.redhat.com/app-interface/self-serviceable)
- [ASIC JIRA Board](https://jira.company.com/projects/ASIC/issues/)
- [Onboarding JIRA Board](https://jira.company.com/secure/Dashboard.jspa?selectPageId=12341197)

### Documentation
- [Incident Process](/docs/sre-team/incident-process.md)
- [App-Interface FAQ](https://gitlab.cee.redhat.com/service/app-interface/-/blob/master/FAQ.md)
- [Developer Guidelines](https://gitlab.cee.redhat.com/service/dev-guidelines)
- [AAA - Anthology of App-SRE Axioms](https://gitlab.cee.redhat.com/service/app-interface/blob/master/docs/sre-team/AAA.md)

### PagerDuty
- [Follow The Sun Schedule](https://redhat.pagerduty.com/schedules#PQ022DV)
- [Primary Oncall Schedule](https://redhat.pagerduty.com/schedules#PHS3079)

---

## Pro Tips

### 1. Ask for Help!
- IC work is intense and requires context switching
- If overwhelmed → Ask in `#sd-sre-team-teamchat`
- Team members can jump in for MRs, alerts, etc.

### 2. Prioritize by SLO
- MR reviews: 3 hours
- Critical alerts: Immediate
- High alerts: Best effort
- ASIC tickets: During shift, collaborative

### 3. Escalate When in Doubt
- **Never play hero!**
- Uncertain about incident? → Escalate
- Can't resolve in SOP? → Escalate to tenant team
- Production impact unclear? → Escalate

### 4. Maintain Alert Channels
- Flappy alerts degrade trust
- Downgrade severity if needed
- Work with tenants to fix root causes

### 5. Document Everything
- ASIC tickets: Add context for next IC
- Handover: Be specific
- Incidents: Follow process, document decisions

---

## Quick Reference - Escalation Flow

```
1. Critical Alert
 ↓
 Check Runbook → Follow SOP
 ↓
 Can't resolve? → Escalate to tenant team
 ↓
 Major impact? → Declare incident
 ↓
 Need help? → Ask in #sd-sre-team-teamchat
 ↓
 Still blocked? → Manager escalation (PagerDuty)

2. Web-RCA Down
 ↓
 Check pods → oc get pods -n web-rca-production
 ↓
 Restart pod if needed
 ↓
 Still down after 1 hour? → Ping @status-board
 ↓
 NO manager escalation for Web-RCA!
```

---

## Summary - IC Checklist

**Shift Start:**
- [ ] Check PagerDuty schedule
- [ ] Join Slack channels
- [ ] Read previous handover
- [ ] Check merge/review queues
- [ ] Scan for critical alerts

**During Shift:**
- [ ] Respond to critical alerts immediately
- [ ] Monitor #sd-sre-team for production issues
- [ ] Review MRs (target 3 hours)
- [ ] Triage ASIC tickets
- [ ] Answer user questions
- [ ] Monitor high alerts in #sd-sre-team-alert
- [ ] Clean up looping integrations

**Shift End:**
- [ ] Write handover in #sd-sre-team-handover
- [ ] Unassign ASIC tickets
- [ ] Notify next IC of any ongoing issues

**Emergency:**
- [ ] Critical alert → Immediate response
- [ ] Uncertain → Escalate
- [ ] Overwhelmed → Ask for help
- [ ] Major incident → Follow incident process

---

Good luck with your IC shift! 
