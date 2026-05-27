# Claude Code Mastery: Hatékony Workflow Útmutató

**Létrehozva:** 2026-04-21 
**Célközönség:** Infrastructure Team (the company production/OPS Team) 
**Cél:** Maximális produktivitás Claude Code-dal, MCP szerverek kombinált használatával

---

## Miért ez az útmutató?

**Jelenlegi helyzet:**
- MCP szerverek telepítve (LUMINO, Jira, PagerDuty, GitHub, GitLab, Service-C)
- production skills telepítve (5 darab)
- Alapvető workflow működik

**Cél:**
- **70-80% gyorsabb** Jira investigation workflow
- 🧠 **Kontextusban maradás** projektek között
- ⚡ **Automatizált rutinfeladatok** (mint Pete `/agenda` skill-je)
- 🎨 **Custom workflow-k** saját igényekhez

---

## Rendelkezésre Álló Eszközök (Inventory)

### MCP Szerverek (Model Context Protocol)

| Szerver | Eszközök | Használati Terület |
|---------|----------|-------------------|
| **lumino** | 37 tool | Kubernetes/OpenShift/Tekton observability |
| **jira** | 12 tool | Jira ticket management |
| **pagerduty** | ~10 tool | Incident management |
| **github** | ~15 tool | GitHub repos, PRs, issues |
| **gitlab** | ~15 tool | GitLab MRs, pipelines |
| **service-c** | ~12 tool | the company Service-C build system (Pete) |
| **servicenow** | (dev) | ITSM incident management |

### Skills (production CI/CD Tudásbázis)

| Skill | Trigger | Használat |
|-------|---------|-----------|
| `debugging-pipeline-failures` | Tekton pipeline hibák | PipelineRun/TaskRun debugging |
| `navigating-github-to-production-pipelines` | GitHub PR → production | PR checks → kubectl debugging |
| `understanding-production-resources` | production CR-ek | Application, Component, Snapshot, ReleasePlan |
| `working-with-provenance` | Image → build | Konténer provenance követés |
| `component-build-status` | Build trigger | Component build állapot |

### Inspiráció: Pete Custom Skills

Pete **personal skills** példák (`~/.claude/skills/`):
- **`/agenda`** - Reggeli briefing (Kerberos, PagerDuty on-call, Jira, GitLab MRs, GitHub PRs)
- **`/service-c`** - Service-C builder dashboard (host health, channels, failed/running tasks)
- **`/production-board`** - KFLUXSPRT Jira board check (ma megérkezett ticket-ek, unassigned items)

---

## Hatékony Workflow Minták

### 1. Reggeli Rutinautomatizálás (Pete mintája)

**Probléma:** Minden reggel manuálisan ellenőrzöd:
- Kerberos ticket lejárat
- PagerDuty on-call státusz
- Új Jira ticket-ek
- GitHub/GitLab nyitott PR-ek/MR-ek

**Megoldás:** Custom `/morning-briefing` skill

**Implementáció:**
```bash
mkdir -p ~/.claude/skills/morning-briefing
cat > ~/.claude/skills/morning-briefing/SKILL.md << 'EOF'
---
name: morning-briefing
description: Napi reggeli összefoglaló - Kerberos, PagerDuty, Jira KFLUXSPRT/KFLUXINFRA új ticket-ek, GitLab/GitHub nyitott MR/PR-ek
---

# Morning Briefing Skill

## Automatikus Reggeli Összefoglaló

**Használat:** Minden reggel futtatva kapsz egy gyors dashboard-ot.

### 1. Kerberos Ticket Ellenőrzés

```bash
klist | grep "krbtgt" | awk '{print $1, $2, $3}'
```

**Keresendő:** Ha < 2 óra van hátra, figyelmeztess: `kinit` szükséges!

### 2. PagerDuty On-Call Státusz

MCP tool használat:
- `mcp__pagerduty__get_oncall` - Ki van jelenleg on-call?
- `mcp__pagerduty__get_my_incidents` - Van-e nyitott incidensed?

**Keresendő:**
- On-call vagy-e ma?
- Van-e `triggered` vagy `acknowledged` incidens?

### 3. Jira Új Ticket-ek (Ma érkezett)

MCP tool használat:
- `mcp__jira__search_issues` JQL-lel:
```jql
project IN (KFLUXSPRT, KFLUXINFRA, OPS) 
AND created >= startOfDay() 
ORDER BY created DESC
```

**Formátum:**
```
🆕 Új Jira Ticket-ek (Ma):
- [KFLUXSPRT-1234] Pipeline failure investigation
- [KFLUXINFRA-5678] Upgrade cluster to 4.15
```

### 4. Assigned Ticket-ek (Saját)

MCP tool használat:
- `mcp__jira__get_my_issues` status filter: `In Progress, To Do`

**Formátum:**
```
Saját Feladatok:
- [KFLUXSPRT-9999] (In Progress) - Deploy new feature
- [OPS-1111] (To Do) - Review security patch
```

### 5. GitLab Open MR-ek (gitlab.cee.company.internal)

MCP tool használat:
- `mcp__gitlab__list_merge_requests` scope: `assigned_to_me`, state: `opened`

**Keresendő:**
- Review szükséges?
- Pipeline sikeres?
- Merge conflict?

### 6. GitHub Open PR-ek (github.com/production-ci)

MCP tool használat:
- `mcp__github__list_pull_requests` state: `open`, user: `ops-lead`

**Keresendő:**
- Review comments
- CI status
- Merge readiness

## Output Sablon

```
☀️ REGGELI ÖSSZEFOGLALÓ - 2026-04-21
=====================================

🎫 Kerberos: Érvényes (8h 32m)
🚨 PagerDuty: On-call (ma 08:00-18:00) - 1 acknowledged incident
🆕 Jira (Ma): 3 új ticket (KFLUXSPRT: 2, KFLUXINFRA: 1)
Saját: 2 In Progress, 1 To Do
🔀 GitLab: 1 MR review kell
🔃 GitHub: 2 PR waiting for CI

RÉSZLETEK:
----------
Jira Új:
- [KFLUXSPRT-7890] Build timeout in production-stage
- [KFLUXSPRT-7891] User question: How to trigger manual pipeline?
- [KFLUXINFRA-3456] Plan OCP 4.16 migration

Saját MR/PR:
- GitLab: !456 "Fix Tekton pipeline RBAC" (Approval: 1/2)
- GitHub: #789 "Update lumino-mcp-server docs" (CI passing)
- GitHub: #790 "Add new Prometheus queries" (CI running)
```

## Használat

```
# Claude Code prompt-ban:
/morning-briefing
```

Claude automatikusan:
1. Futtatja az összes query-t párhuzamosan
2. Összegyűjti az eredményeket
3. Strukturált formában mutatja
4. Kiemeli a sürgős teendőket
EOF
```

**Használat:** Begépeled a Claude Code-ban: `/morning-briefing`

---

### 2. Jira Ticket Deep-Dive Workflow

**Probléma:** Új ticket-hez sok kontextus kell (kapcsolódó ticket-ek, korábbi hasonló issue-k, kód repository-k).

**Optimalizált Workflow:**

#### Fázis 1: Ticket Beolvasás (10 mp)

```
# Claude Code prompt:
Retrieve and summarize KFLUXINFRA-1234 including:
- Description, priority, labels
- Comments (latest 5)
- Linked issues (blocks/is blocked by)
```

Claude használni fogja: `mcp__jira__get_issue`, `mcp__jira__get_issue_comments`

#### Fázis 2: Kapcsolódó Kontextus (30 mp)

```
Search Jira for similar issues:
- Same component
- Similar error messages from description
- Same assignee's previous work
- Last 30 days
```

Claude használni fogja: `mcp__jira__search_issues` JQL generálással

#### Fázis 3: Kubernetes Kontextus (Ha releváns) (1 perc)

```
If this is a cluster issue, check:
- Namespace: <namespace-from-jira>
- Recent PipelineRuns (last 24h)
- Failed pods
- Events with errors
```

Claude használni fogja: LUMINO MCP tools (`list_namespaces`, `list_recent_pipeline_runs`, `smart_get_namespace_events`)

#### Fázis 4: Investigation Plan (20 mp)

```
Based on the above context, create:
1. Investigation plan (3-5 steps)
2. Potential root causes (ranked by likelihood)
3. Required access/permissions
4. Estimated time to resolve
```

**Teljes idő:** ~2 perc (vs. manuális 20-30 perc)

---

### 3. Incident Response Workflow (PagerDuty → Jira → K8s)

**Szcenárió:** PagerDuty alert érkezik → Investigation → Jira ticket → Fix

#### Lépés 1: Incident Elemzés

```
Get latest PagerDuty incident and analyze:
- Incident title, urgency, service
- Timeline of alerts
- Related monitoring data
```

MCP: `mcp__pagerduty__get_my_incidents`, `mcp__pagerduty__get_incident_alerts`

#### Lépés 2: Kubernetes Diagnosztika

```
Based on PagerDuty alert (e.g., "High pod restart rate in production-stage"):
- List pods in namespace with restart count > 5
- Get recent events
- Analyze pod logs for crash patterns
```

MCP: LUMINO `list_pods_in_namespace`, `smart_get_namespace_events`, `smart_summarize_pod_logs`

#### Lépés 3: Automated Jira Ticket Draft

```
Create Jira ticket draft for KFLUXSPRT with:
- Title from PagerDuty alert
- Description with:
- PagerDuty incident link
- Kubernetes namespace/pod info
- Error summary from logs
- Proposed investigation steps
```

MCP: `mcp__jira__create_issue` (REVIEW BEFORE POSTING!)

#### Lépés 4: Resolution & Documentation

```
After fix:
1. Update Jira with resolution steps
2. Add PagerDuty incident note
3. Resolve PagerDuty incident
```

MCP: `mcp__jira__add_comment`, `mcp__pagerduty__add_incident_note`, `mcp__pagerduty__resolve_incident`

---

### 4. Code Review Optimization (GitHub/GitLab)

**Probléma:** PR/MR review-hoz kell:
- Jira ticket kontextus
- Kapcsolódó pipeline-ok állapota
- Build sikeresség

**Workflow:**

```
Review PR/MR with full context:
1. Fetch PR details (GitHub/GitLab MCP)
2. Find linked Jira ticket from PR title/description
3. Check CI pipeline status
4. If production build - get PipelineRun logs
5. Summarize changes + context for review
```

**Példa prompt:**

```
Review GitHub PR production-ci/integration-service#456:
- Fetch PR description and changed files
- Find linked Jira ticket
- Check CI status
- If failed, analyze logs
- Provide review summary with context
```

---

## 🛠️ Custom Skill-ek Létrehozása

### Sablon: Egyszerű Info Query Skill

```bash
mkdir -p ~/.claude/skills/service-c-dashboard
cat > ~/.claude/skills/service-c-dashboard/SKILL.md << 'EOF'
---
name: service-c-dashboard
description: the company Service-C builder health dashboard - host status by architecture, failed builds, running tasks
---

# Service-C Builder Dashboard

## Célja

Gyors áttekintés a Service-C build rendszer állapotáról.

## Ellenőrzendő

### 1. Host Health by Architecture

```bash
service-c list-hosts | awk '{print $2}' | sort | uniq -c
```

**Keresendő:** Elérhető host-ok száma arch-onként (x86_64, aarch64, ppc64le, s390x)

### 2. Failed Builds (Last 24h)

MCP tool (Pete service-c MCP-je):
```
mcp__service-c__list_tasks(state='FAILED', since='1 day ago')
```

**Formátum:**
```
Failed Builds (24h): 12
Top failures:
- rust-toolchain: 5 failures (timeout)
- gcc-cross-compiler: 3 failures (missing dep)
```

### 3. Running Tasks

```
mcp__service-c__list_tasks(state='OPEN')
```

## Output

```
🏗️ BREW DASHBOARD
================
🖥️ Hosts: x86_64(45), aarch64(12), ppc64le(8), s390x(6)
Failed (24h): 12
⏳ Running: 34
```
EOF
```

### Sablon: Kombinált MCP Skill (Multi-Source)

```bash
mkdir -p ~/.claude/skills/production-health-check
cat > ~/.claude/skills/production-health-check/SKILL.md << 'EOF'
---
name: production-health-check
description: production platform health - staging/production cluster status, recent failures, Jira critical tickets
---

# production Health Check

## Multi-Source Status Dashboard

### 1. Staging Cluster Status (LUMINO)

```
Check namespace: production-stage
- List pods with status != Running
- Recent failed PipelineRuns (6h)
- Events with type=Warning (1h)
```

MCP: LUMINO `conservative_namespace_overview`, `list_recent_pipeline_runs`

### 2. Production Cluster Status (LUMINO)

```
Check namespace: production-prod
- Critical pod failures
- PipelineRun success rate (24h)
```

### 3. Critical Jira Tickets

```
Search Jira:
project = KFLUXINFRA 
AND priority = Highest 
AND status != Done
```

MCP: `mcp__jira__search_issues`

### 4. Recent PagerDuty Incidents

```
Get incidents (24h):
- Triggered
- Acknowledged
- Resolved
```

MCP: `mcp__pagerduty__list_incidents`

## Output Format

```
🏥 KONFLUX HEALTH CHECK
=======================

🔵 Staging (production-stage):
Pods: 45/45 Running
PipelineRuns: 3/120 failed (6h)
Events: 2 warnings (ImagePullBackOff)

Production (production-prod):
Pods: 120/120 Running
PipelineRuns: 100% success (24h)

🎫 Critical Jira: 2
- [KFLUXINFRA-9999] Database migration stuck
- [KFLUXINFRA-8888] API rate limit exceeded

🚨 PagerDuty (24h):
Triggered: 1 (CPU spike - under investigation)
Resolved: 4
```
EOF
```

---

## Pete & GG Inspiráció Összefoglalás

### Pete Workflow Highlights

**Amit Pete használ:**
- **Napi rutinok automatizálása:** `/agenda` skill
- **System health dashboards:** `/service-c`, `/production-board`
- **MCP szerverek:** AAP, Service-C, Grafana, ServiceNow, Atlassian, GitHub, GitLab, Lumino, PagerDuty

**Pete skill pattern:**
```
Skill = Concrete daily task automation
- Input: None (just invoke)
- Process: Multi-source data aggregation
- Output: Actionable dashboard
```

### GG Lumino SRE Assistant

**Repository:** https://gitlab.cee.company.internal/ggeorgie/lumino-sre-assistant

**Jellemzők:**
- **Agentic Claude Code:** Full workflow orchestration
- **Skills:** production-specific troubleshooting patterns
- **Configs:** Pre-tuned hooks and automation
- **Processes:** End-to-end incident response

**Tanulság:** Teljes workflow automatizálás lehetséges!

---

## Következő Lépések (Action Plan)

### Fázis 1: Alapok Optimalizálása (1-2 nap)

- [ ] **Morning briefing skill** létrehozása (Pete `/agenda` mintájára)
- [ ] **Service-C dashboard skill** implementálása (ha használod a Service-C-t)
- [ ] **production health check** skill (staging/prod gyors áttekintés)

### Fázis 2: Workflow Finomhangolás (3-5 nap)

- [ ] **Jira investigation template:** Automatizált context gathering workflow
- [ ] **Incident response playbook:** PagerDuty → K8s → Jira integráció
- [ ] **Code review assistant:** GitHub/GitLab PR/MR + Jira context

### Fázis 3: Haladó Automatizálás (1-2 hét)

- [ ] **GG Lumino SRE Assistant** tanulmányozása és adaptálás
- [ ] **Custom hooks** beállítása (pl. auto-refresh minden 30 percben)
- [ ] **Workflow dokumentálás:** Saját best practice-ek rögzítése

### Fázis 4: Megosztás & Optimalizálás (folyamatos)

- [ ] **Collegákkal egyeztetés:** Pete-tel és GG-vel skill/workflow megosztás
- [ ] **Új use-case-ek:** Folyamatosan új automatizálási lehetőségek keresése
- [ ] **Feedback loop:** Hatékonyság mérése, finomhangolás

---

## 🔗 Hasznos Linkek

- **production Skills:** https://github.com/production-ci/skills
- **GG Lumino SRE Assistant:** https://gitlab.cee.company.internal/ggeorgie/lumino-sre-assistant
- **Pete skills:** Kérdezd meg Slack-en megosztásra
- **Claude Code docs:** https://docs.anthropic.com/claude-code
- **MCP Protocol:** https://modelcontextprotocol.io

---

## Tippek & Trükkök

### 1. Párhuzamos MCP Hívások

**Hatékony:**
```
In parallel, fetch:
1. Jira ticket KFLUXINFRA-1234
2. GitHub PR production-ci/repo#456
3. Kubernetes namespace events (production-stage, 1h)
```

Claude egyszerre hívja a 3 MCP tool-t → **3x gyorsabb!**

### 2. Context Preservation

**Probléma:** Hosszú investigation-nél elveszíted a kontextust.

**Megoldás:** Interim summary-k
```
After each investigation phase, summarize:
- What we found
- What we ruled out
- Next steps

Keep this summary in the conversation for context.
```

### 3. Skill Chain-elés

**Hasznos pattern:**
```
1. Run /morning-briefing
2. If critical Jira found → Auto-trigger deep-dive workflow
3. If PagerDuty incident → Auto-trigger incident response
```

Ezt hook-okkal automatizálhatod!

### 4. Template-ek Skill-ekbe

**Jira comment template skill:**
```bash
cat > ~/.claude/skills/jira-update-template/SKILL.md << 'EOF'
---
name: jira-update-template
description: Standard Jira update comment format for production investigations
---

# Jira Update Template

## Investigation Update Format

### Summary
- **Status:** [In Progress / Blocked / Resolved]
- **Root Cause:** [Brief description]
- **Impact:** [User-facing impact]

### Findings
1. [Finding 1 with evidence]
2. [Finding 2 with evidence]

### Next Steps
- [ ] Action 1
- [ ] Action 2

### Timeline
- Started: YYYY-MM-DD HH:MM
- Updated: YYYY-MM-DD HH:MM
- ETA: [If known]

### Links
- Kubernetes logs: [URL]
- PipelineRun: [URL]
- GitHub PR: [URL]
EOF
```

---

## 🚦 Gyakori Hibák & Megoldások

### Hiba: "Túl sok információ, nem látom át"

**Ok:** Minden adatot egyszerre kérsz.

**Megoldás:** Progressive disclosure
```
1. High-level summary first
2. Ask: "Which area to deep-dive?"
3. Detailed analysis only on selected area
```

### Hiba: "Claude hallucinálja a kontextust"

**Ok:** Nem használod az MCP tool-okat, Claude a memóriájából "talál ki" dolgokat.

**Megoldás:** Explicit MCP query
```
"What's the status of KFLUXINFRA-1234?"
"Use mcp__jira__get_issue to fetch KFLUXINFRA-1234 status"
```

### Hiba: "Skill nem triggeredik automatikusan"

**Ok:** A skill description nem elég specifikus.

**Megoldás:** Pontos trigger leírás
```yaml
---
description: Use when investigating Tekton PipelineRun failures, stuck TaskRuns, or build timeouts in production
---
```

---

## 📈 Hatékonyság Mérése

### Metrikák (Opcionális)

Track ezeket hetente:
- **Átlagos Jira ticket investigation idő:** (Cél: < 30 perc)
- **MCP query használat:** (Tracking: Mely tool-okat használod legtöbbet?)
- **Skill invocation rate:** (Mely skill-ek a leghasznosabbak?)
- **Manual intervention rate:** (Hány %-ban kell manuálisan beavatkozni?)

---

**Verzió:** 1.0 
**Utolsó frissítés:** 2026-04-21 
**Következő review:** 2026-05-01 (hatékonyság értékelés)
