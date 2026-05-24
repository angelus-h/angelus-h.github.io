# ITIL 4 Essential Guide (English)

**Target Audience:** IT Service Management professionals, DevOps Engineers, SREs, IT Managers, Support Teams
**Reading Time:** 40-50 minutes
**Application Time:** Organizational (requires process implementation)

---

## What is ITIL?

**ITIL (Information Technology Infrastructure Library)** is a framework of best practices for IT Service Management (ITSM). It provides a systematic approach to delivering IT services that align with business needs.

### Key Points

- **Not prescriptive:** ITIL is a framework, not a rigid methodology
- **Best practices:** Collected from global IT organizations
- **Business-focused:** Aligns IT with business objectives
- **Continual improvement:** Built-in focus on optimization
- **Vendor-neutral:** Applicable to any organization

### ITIL Versions

- **ITIL v1** (1989): Initial 31 books
- **ITIL v2** (2000): Consolidated to 8 books
- **ITIL v3** (2007): 5 core books (Service Strategy, Design, Transition, Operation, CSI)
- **ITIL 4** (2019): **Current version** - Modernized for DevOps, Agile, cloud

---

## ITIL 4 Overview

**ITIL 4 focuses on:**
- Value co-creation with customers
- Service Value System (holistic approach)
- Four Dimensions Model (comprehensive view)
- Flexibility and integration with modern practices (Agile, DevOps, Lean)

### Core Publications

1. **ITIL Foundation** - Entry-level concepts
2. **ITIL Managing Professional (MP)** - Practitioner stream
3. **ITIL Strategic Leader (SL)** - Leadership stream
4. **ITIL Master** - Highest certification level

---

## ITIL 4 Service Value System (SVS)

The **SVS** describes how all components and activities of an organization work together to facilitate value creation through IT-enabled services.

```

 GOVERNANCE 
 (Oversight & Direction) 

 

 
 
 Opportunity/ 
 Demand 
 
 ↓ 
 
 SERVICE VALUE CHAIN 
 (6 Key Activities) 
 
 Plan → Improve → Engage 
 Design & Transition 
 Obtain/Build → Deliver 
 
 
 ↓ 
 
 VALUE 
 (for customer) 
 
 
 Supported by: 
 • Guiding Principles (7) 
 • Practices (34) 
 • Continual Improvement 
 

```

---

## The 7 Guiding Principles

ITIL 4 defines **7 universal guiding principles** that should inform all decisions and actions:

### 1. Focus on Value

**Everything the organization does should link back to value for stakeholders.**

- Understand who the service consumer is
- Define value from customer perspective
- Measure outcomes, not just outputs

**Example:**
> Instead of measuring "tickets resolved," measure "user productivity restored" or "business service availability."

---

### 2. Start Where You Are

**Don't start from scratch - assess current state and reuse what works.**

- Assess existing processes and services
- Leverage what's working
- Avoid "rip and replace"

**Example:**
> Before implementing a new incident management tool, assess current ticket system - maybe it just needs configuration changes, not replacement.

---

### 3. Progress Iteratively with Feedback

**Work in incremental improvements with continuous feedback.**

- Break large initiatives into smaller iterations
- Gather feedback at each stage
- Adjust based on learning

**Example:**
> Roll out new change management process to one team first, gather feedback, refine, then expand to other teams.

---

### 4. Collaborate and Promote Visibility

**Work together across organizational boundaries and make work transparent.**

- Break down silos
- Share information openly
- Involve right people at right time

**Example:**
> Include developers, operations, and security in incident post-mortems (blameless retrospectives).

---

### 5. Think and Work Holistically

**No service or element stands alone - consider the whole system.**

- Understand dependencies
- Four Dimensions Model (see below)
- End-to-end service view

**Example:**
> When improving deployment process, consider not just tools but also people skills, organizational culture, and information flows.

---

### 6. Keep it Simple and Practical

**Use minimum number of steps to accomplish objectives.**

- Eliminate unnecessary complexity
- Practical over theoretical
- Outcome-focused

**Example:**
> Don't create 10-page change request forms for routine updates. Automate or simplify standard changes.

---

### 7. Optimize and Automate

**Maximize value of human work by automating repetitive tasks.**

- Automate routine work
- Human judgment for complex decisions
- Continual optimization

**Example:**
> Automate password resets, server provisioning, backup verification. Let humans handle complex troubleshooting and innovation.

---

## The Four Dimensions Model

Every service should be designed considering **four dimensions** to ensure holistic, balanced approach:

### 1. Organizations and People

**People, roles, responsibilities, culture, competencies**

- Organizational structure
- Roles and responsibilities
- Culture and mindset
- Skills and competencies
- Communication

**Key Questions:**
- Do we have the right skills?
- Is our culture supportive of our goals?
- Are roles and responsibilities clear?

**Example:**
> Transitioning to DevOps requires not just new tools but cultural shift (collaboration, shared ownership), new skills (infrastructure as code), and potentially new organizational structure (cross-functional teams).

---

### 2. Information and Technology

**Information, knowledge, technologies, and relationships**

- Information and data management
- Technologies and tools
- Relationships between components
- Architecture and infrastructure

**Key Questions:**
- What information do we need?
- What technologies support our services?
- How do components interact?

**Example:**
> Implementing monitoring solution requires choosing right tools (Prometheus, Grafana), defining what metrics to collect, how to store data, and how to present information to different audiences.

---

### 3. Partners and Suppliers

**Relationships with external organizations**

- Vendor relationships
- Outsourcing arrangements
- Strategic partnerships
- Contracts and SLAs

**Key Questions:**
- Which services should we outsource?
- How do we manage vendor relationships?
- What are our contractual obligations?

**Example:**
> Using cloud providers (AWS, Azure, GCP) requires vendor management, understanding SLAs, cost optimization, and integration strategies.

---

### 4. Value Streams and Processes

**How work is organized and executed**

- Service value streams
- Processes and procedures
- Workflows and activities
- Performance management

**Key Questions:**
- How does value flow through our organization?
- Are our processes efficient?
- Where are bottlenecks?

**Example:**
> Software delivery value stream: Code → Build → Test → Deploy → Monitor. Each stage has processes, tools, and handoffs.

---

## Service Value Chain

The **Service Value Chain** is a flexible operating model with **6 key activities** that transform inputs (opportunity/demand) into outputs (value).

### The 6 Activities

```
 
 PLAN → IMPROVE → ENGAGE →DESIGN & →OBTAIN/ → DELIVER 
 TRANSITION BUILD & SUPPORT
 
```

### 1. Plan

**Purpose:** Ensure shared understanding of vision, current status, and improvement direction

**Key Activities:**
- Strategic planning
- Portfolio management
- Architecture and policies
- Regulatory and compliance planning

**Example:**
> Annual IT planning: Define objectives, allocate budget, prioritize initiatives, align with business strategy.

---

### 2. Improve

**Purpose:** Continual improvement of services and practices

**Key Activities:**
- Identify improvement opportunities
- Assess current state
- Prioritize and plan improvements
- Measure and evaluate

**Example:**
> Post-incident review identifies manual deployment as risk → Improvement initiative to automate CI/CD pipeline.

---

### 3. Engage

**Purpose:** Understand stakeholder needs and manage relationships

**Key Activities:**
- Customer relationship management
- User engagement
- Service request management
- Demand management

**Example:**
> Regular meetings with business stakeholders to understand upcoming needs, gather feedback on services, manage expectations.

---

### 4. Design & Transition

**Purpose:** Ensure services meet stakeholder expectations for quality, cost, and time-to-market

**Key Activities:**
- Service design
- Testing and validation
- Change management
- Release management

**Example:**
> Designing new cloud-based application: Architecture design → Development → Testing → Staged rollout to production.

---

### 5. Obtain/Build

**Purpose:** Ensure service components are available when needed

**Key Activities:**
- Procurement
- Development
- Integration
- Contract negotiation

**Example:**
> Procuring new monitoring solution: Vendor evaluation → Proof of concept → Contract negotiation → Deployment.

---

### 6. Deliver & Support

**Purpose:** Ensure services are delivered and supported according to agreed specifications

**Key Activities:**
- Incident management
- Service desk operations
- Service performance monitoring
- User support

**Example:**
> 24/7 support team handling incidents, service requests, monitoring alerts, and ensuring SLA compliance.

---

## ITIL 4 Management Practices (34 Total)

ITIL 4 defines **34 management practices** (previously called "processes" in ITIL v3). Here are the most important ones:

### General Management Practices (14)

#### Continual Improvement

**Purpose:** Align organization's practices and services with changing business needs

**Key Activities:**
- Improvement identification
- Improvement logging and tracking
- Assessment and prioritization
- Implementation and monitoring

**Example:**
> Quarterly service reviews identify areas for improvement → Prioritized backlog → Improvement projects → Measure impact.

---

#### Information Security Management

**Purpose:** Protect information needed by the organization

**Key Activities:**
- Security policies and controls
- Risk assessment
- Incident response
- Compliance

**Example:**
> Implement zero-trust architecture, regular security audits, incident response procedures, compliance with GDPR/SOC2.

---

#### Knowledge Management

**Purpose:** Maintain and improve effective use of knowledge

**Key Activities:**
- Knowledge capture
- Knowledge sharing
- Knowledge maintenance
- Knowledge retirement

**Example:**
> Confluence wiki for runbooks, Slack channels for knowledge sharing, regular documentation reviews.

---

### Service Management Practices (17)

#### Incident Management

**Purpose:** Minimize negative impact of incidents by restoring normal service operation as quickly as possible

**Key Concepts:**
- **Incident:** Unplanned interruption or reduction in quality
- **Severity:** Impact on business
- **Priority:** Urgency + Impact
- **Major Incident:** Highest impact/urgency requiring special handling

**Incident Lifecycle:**
```
Incident Logged → Categorized → Prioritized → Diagnosed → Resolved → Closed
 ↓
 (Escalation if needed)
```

**Example:**
> Production database down → P1 Major Incident → War room → DBA investigates → Failover to replica → Service restored → Post-incident review.

**Metrics:**
- Mean Time to Detect (MTTD)
- Mean Time to Resolve (MTTR)
- Incident volume by category
- First-call resolution rate

---

#### Problem Management

**Purpose:** Reduce likelihood and impact of incidents by identifying actual and potential causes

**Key Concepts:**
- **Problem:** Cause of one or more incidents
- **Known Error:** Problem with documented root cause and workaround
- **Root Cause Analysis:** Investigative process

**Problem vs Incident:**
- **Incident:** "Database is down" (symptom)
- **Problem:** "Database runs out of memory due to connection leak" (root cause)

**Example:**
> Multiple incidents of application crashes → Problem investigation → Root cause: memory leak in code → Fix deployed → Problem closed.

**Techniques:**
- 5 Whys
- Fishbone Diagram (Ishikawa)
- Fault Tree Analysis
- Kepner-Tregoe

---

#### Change Control (Change Enablement)

**Purpose:** Maximize number of successful changes by ensuring risks assessed and authorized

**Change Types:**

1. **Standard Change:**
 - Pre-authorized
 - Low risk
 - Well-documented procedure
 - Example: Password reset, routine patching

2. **Normal Change:**
 - Requires assessment and authorization
 - Change Advisory Board (CAB) review (if needed)
 - Example: Application deployment, infrastructure upgrade

3. **Emergency Change:**
 - Urgent, can't wait for normal process
 - Expedited authorization
 - Example: Hotfix for critical security vulnerability

**Change Process:**
```
Request → Assessment → Authorization → Implementation → Review
```

**Example:**
> Developer requests to deploy new feature → Change request created → Risk assessment → CAB approval → Scheduled deployment → Post-implementation review.

**In DevOps Context:**
- Standard changes can be fully automated (CI/CD)
- Normal changes may use automated approval gates
- Emergency changes still need accountability

---

#### Release Management

**Purpose:** Make new and changed services available for use

**Key Activities:**
- Release planning
- Release build and test
- Deployment
- Release review

**Release Strategies:**
- **Big Bang:** All at once (risky)
- **Phased:** Gradual rollout (safer)
- **Blue-Green:** Two environments, instant switch
- **Canary:** Small percentage first, then expand

**Example:**
> New application version: Build release → Test in staging → Deploy to 10% users (canary) → Monitor → Full rollout → Post-release review.

---

#### Service Desk

**Purpose:** Capture demand for incident resolution and service requests

**Functions:**
- Single point of contact (SPOC)
- Incident logging and categorization
- First-line resolution
- Request fulfillment
- Communication hub

**Service Desk Models:**
- **Local:** On-site support
- **Centralized:** One location for all
- **Virtual:** Distributed team appearing as one
- **Follow-the-Sun:** 24/7 coverage across time zones

**Example:**
> User calls/emails/Slack messages service desk → Ticket created → L1 attempts resolution → If unresolved, escalate to L2/L3 specialists.

**Metrics:**
- First Contact Resolution (FCR)
- Average Handle Time (AHT)
- Customer Satisfaction (CSAT)
- Ticket volume and backlog

---

#### Service Level Management

**Purpose:** Set clear business-based targets for service levels

**Key Artifacts:**

**1. Service Level Agreement (SLA):**
- Documented agreement between service provider and customer
- Defines expected service levels
- Example: "99.9% uptime, <5 min response for P1 incidents"

**2. Operational Level Agreement (OLA):**
- Agreement between internal teams
- Supports delivery of SLAs
- Example: "Database team provides <15 min response to critical issues"

**3. Underpinning Contract (UC):**
- Agreement with external supplier
- Example: "Cloud provider guarantees 99.95% availability"

**Metrics:**
- Availability (uptime %)
- Performance (response time, throughput)
- Service quality (error rates)
- SLA compliance %

**Example:**
```
SLA: Web application available 99.9% during business hours (8am-6pm)
 = Max 43 minutes downtime per month

Measurement: Track actual uptime, report monthly, review quarterly
```

---

#### Monitoring and Event Management

**Purpose:** Systematically observe services and service components

**Event Types:**
- **Informational:** Normal operation (logged for reference)
- **Warning:** Threshold approaching (alert)
- **Exception:** Abnormal operation (alert + action)

**Event Flow:**
```
Event Occurs → Detected → Filtered → Correlated → Trigger → Response
```

**Example:**
> CPU usage >80% → Warning event → Alert to operations → If sustained >90%, auto-scale → Log for capacity planning.

**Tools in Modern Context:**
- Prometheus (metrics collection)
- Grafana (visualization)
- Alertmanager (alert routing)
- PagerDuty (on-call management)

---

#### Service Request Management

**Purpose:** Support agreed quality of service by handling service requests

**Service Request Examples:**
- Access requests (permissions, accounts)
- Information requests (reports, documentation)
- Standard changes (software installation)

**Request Fulfillment:**
```
Request → Approval (if needed) → Fulfillment → Closure
```

**Example:**
> User requests access to production environment → Manager approval → Access granted via automated workflow → User notified → Request closed.

**Automation Opportunities:**
- Password resets
- Software provisioning
- Access requests (with approval workflows)
- VM/container provisioning

---

#### Service Configuration Management

**Purpose:** Ensure accurate and reliable information about configuration of services

**Key Concepts:**

**Configuration Item (CI):**
- Any component that needs to be managed to deliver service
- Examples: Servers, applications, documentation, licenses

**Configuration Management Database (CMDB):**
- Repository storing CI information and relationships
- Foundation for many ITIL practices

**Example CMDB Structure:**
```
Application "E-commerce Website"
 Frontend Server (VM)
 Hosted on: AWS EC2
 Depends on: Backend API
 Monitored by: Prometheus
 Backend API (Container)
 Hosted on: Kubernetes Cluster
 Depends on: Database
 Code repository: GitHub
 Database (RDS Instance)
 Hosted on: AWS RDS
 Backed up: Daily
```

**Benefits:**
- Impact analysis (what's affected by a change?)
- Dependency mapping
- Incident troubleshooting
- Audit and compliance

---

### Technical Management Practices (3)

#### Deployment Management

**Purpose:** Move new or changed components to live environments

**Key Activities:**
- Deployment planning
- Deployment execution
- Verification and testing
- Transfer to operations

**In DevOps:**
- CI/CD pipelines (Jenkins, GitLab CI, Tekton)
- Infrastructure as Code (Terraform, Ansible)
- Automated testing
- Progressive delivery (canary, blue-green)

**Example:**
> Git push → CI builds container → Automated tests → Deploy to staging → QA approval → Deploy to production (canary) → Monitor → Full rollout.

---

## ITIL and DevOps/Agile Integration

**Common Misconception:** "ITIL is waterfall, incompatible with Agile/DevOps"

**Reality:** ITIL 4 is designed to work with Agile and DevOps

### ITIL + Agile

| ITIL Practice | Agile Equivalent |
|---------------|------------------|
| Continual Improvement | Sprint Retrospectives |
| Change Control | Sprint Review / Acceptance |
| Release Management | Sprint Delivery |
| Service Level Management | Product Backlog Prioritization |

### ITIL + DevOps

| ITIL Practice | DevOps Practice |
|---------------|-----------------|
| Change Control | Automated deployment gates, peer review |
| Release Management | CI/CD pipelines |
| Incident Management | On-call rotation, runbooks |
| Monitoring & Event Mgmt | Observability (metrics, logs, traces) |
| Problem Management | Blameless post-mortems |

**Key Integration Points:**
- **Standard Changes:** Fully automated via CI/CD (pre-approved)
- **Normal Changes:** Automated with approval gates
- **Incidents:** Integrated with monitoring/alerting tools
- **Knowledge:** Runbooks in version control, blameless post-mortems
- **CMDB:** Infrastructure as Code repositories, service catalogs

---

## ITIL in Company/Platform Context

**Practical Application:**

### Incident Management
- **Tool:** Jira (KFLUXINFRA tickets), PagerDuty alerts
- **Process:** Alert → Investigate → Mitigate → Resolve → Post-mortem
- **Roles:** On-call SRE, incident commander, subject matter experts

### Change Control
- **Standard Changes:** Automated deployments via Tekton pipelines (pre-approved)
- **Normal Changes:** GitLab MR → Peer review → Automated tests → Deployment
- **Emergency Changes:** Hotfix process with expedited review

### Problem Management
- **Post-Incident Reviews:** Blameless retrospectives after major incidents
- **RCA Tools:** 5 Whys, timeline reconstruction
- **Tracking:** Jira Problem tickets linked to Incidents

### Service Level Management
- **SLOs:** Service Level Objectives defined per service
- **Monitoring:** Prometheus metrics, Grafana dashboards
- **Reporting:** Monthly SLA reports to stakeholders

### Release Management
- **Pipeline:** Code → Build → Test → Staging → Production
- **Strategy:** Canary deployments, feature flags
- **Cadence:** Continuous deployment (multiple times per day)

### Knowledge Management
- **Runbooks:** Confluence wiki, inline documentation
- **Code Documentation:** README files, architecture docs
- **Lessons Learned:** Post-mortem database

---

## ITIL Certifications

### ITIL 4 Certification Scheme

```

 ITIL MASTER 
 (Highest Level) 

 
 
 
 
 ITIL Managing ITIL Strategic 
 Professional Leader 
 (Transition) 
 
 
 
 →Create, Deliver, Support 
 
 
 
 →Drive Stakeholder Value 
 
 
 
 →High Velocity IT →Direct, Plan, 
 Improve 
 
 
 
 ITIL 4 FOUNDATION 
 (Entry Level) 
 
```

### ITIL 4 Foundation

**Target Audience:** Anyone involved in IT services

**Topics:**
- Service management key concepts
- 7 Guiding Principles
- 4 Dimensions
- Service Value System
- Service Value Chain
- Key practices (high-level)

**Exam:**
- 40 multiple-choice questions
- 65% pass mark (26/40)
- 60 minutes
- Closed book

**Recommended Prep:**
- Official ITIL 4 Foundation book
- Accredited training course (optional but recommended)
- Practice exams

---

### ITIL Managing Professional Stream

**For IT Practitioners:**

1. **ITIL Specialist: Create, Deliver and Support**
 - Service desk, incident management, service requests
 - Relevant for operations and support teams

2. **ITIL Specialist: Drive Stakeholder Value**
 - Customer journey, user experience
 - Relevant for service managers

3. **ITIL Specialist: High Velocity IT**
 - DevOps, Agile, Lean integration
 - **Most relevant for DevOps/SRE roles**

4. **ITIL Strategist: Direct, Plan and Improve**
 - Governance, continual improvement
 - Relevant for managers

---

### ITIL Strategic Leader Stream

**For IT Leaders:**

1. **ITIL Strategist: Direct, Plan and Improve** (same as MP)
2. **ITIL Leader: Digital and IT Strategy**
 - Digital transformation, IT strategy
 - C-level and senior management

---

## Quick Reference: ITIL 4 Cheat Sheet

### 7 Guiding Principles
1. Focus on Value
2. Start Where You Are
3. Progress Iteratively with Feedback
4. Collaborate and Promote Visibility
5. Think and Work Holistically
6. Keep it Simple and Practical
7. Optimize and Automate

### 4 Dimensions
1. Organizations and People
2. Information and Technology
3. Partners and Suppliers
4. Value Streams and Processes

### Service Value Chain (6 Activities)
1. Plan
2. Improve
3. Engage
4. Design & Transition
5. Obtain/Build
6. Deliver & Support

### Key Practices to Know
- Incident Management
- Problem Management
- Change Control
- Release Management
- Service Level Management
- Service Desk
- Monitoring and Event Management
- Continual Improvement

---

## Common ITIL Terms - Glossary

| Term | Definition | Example |
|------|------------|---------|
| **Service** | Means of enabling value co-creation | Email system, cloud platform |
| **Incident** | Unplanned interruption or reduction | Website down, slow performance |
| **Problem** | Cause of one or more incidents | Memory leak, misconfiguration |
| **Change** | Addition, modification, or removal | Deploy new version, config change |
| **Release** | Version of service made available | v2.0 release, quarterly update |
| **Event** | Any detectable occurrence | CPU alert, user login, backup complete |
| **Service Request** | Request from user for something | Access request, info request |
| **SLA** | Service Level Agreement | 99.9% uptime commitment |
| **CI** | Configuration Item | Server, app, document, contract |
| **CMDB** | Configuration Management Database | Central repository of CIs |

---

## Resources

### Official
- [ITIL Official Website](https://www.axelos.com/certifications/itil-service-management)
- [ITIL 4 Foundation Book](https://www.axelos.com/certifications/itil-service-management/itil-4-foundation)

### Books
- "ITIL Foundation, ITIL 4 Edition" - AXELOS (official)
- "ITIL 4 Essentials: Your essential guide..." - Claire Agutter
- "The Phoenix Project" - Gene Kim (IT/DevOps novel demonstrating ITIL concepts)
- "The DevOps Handbook" - Gene Kim (ITIL + DevOps integration)

### Online Training
- [Pluralsight ITIL Courses](https://www.pluralsight.com/)
- [Udemy ITIL 4 Foundation](https://www.udemy.com/)
- Accredited training organizations (search AXELOS approved)

### Communities
- [ITIL subreddit](https://www.reddit.com/r/ITIL/)
- [IT Service Management Forum](https://www.itsmf.org/)
- LinkedIn ITIL groups

---

## Practical Tips for Implementing ITIL

### Start Small
- Don't try to implement all 34 practices at once
- Focus on what adds value
- Common starting points: Incident Management, Change Control, Service Desk

### Adapt to Your Context
- ITIL is a framework, not a prescription
- Tailor practices to your organization size and needs
- Combine with Agile/DevOps where appropriate

### Measure What Matters
- Don't track metrics just because ITIL mentions them
- Focus on business outcomes
- Use data to drive improvement

### Tool Selection
- Tools should support processes, not dictate them
- Evaluate: Jira Service Management, ServiceNow, Freshservice, etc.
- Integration with existing tools is critical

### Cultural Change
- ITIL success requires culture shift, not just process
- Leadership support essential
- Training and communication are key

---

## ITIL vs Other Frameworks

| Framework | Focus | Best For |
|-----------|-------|----------|
| **ITIL** | IT Service Management | Service delivery, operations |
| **Agile/Scrum** | Software Development | Development teams, product delivery |
| **DevOps** | Development + Operations | Fast delivery, automation |
| **COBIT** | IT Governance | Compliance, risk management |
| **Lean IT** | Waste Elimination | Process optimization |
| **Six Sigma** | Quality Improvement | Reducing defects and variation |

**Key Point:** These frameworks complement each other. Modern organizations often combine:
- **ITIL** for service management structure
- **Agile** for development methodology
- **DevOps** for automation and culture
- **Lean** for continuous improvement

---

## Action Items

### For Individuals
1. [ ] Obtain ITIL 4 Foundation certification
2. [ ] Read official ITIL 4 Foundation book
3. [ ] Identify which ITIL practices apply to your role
4. [ ] Join ITIL community (Reddit, LinkedIn)

### For Teams
1. [ ] Assess current service management maturity
2. [ ] Identify gaps and improvement opportunities
3. [ ] Prioritize 2-3 practices to improve
4. [ ] Define processes and train team
5. [ ] Implement tooling to support processes
6. [ ] Measure and iterate

### For Organizations
1. [ ] Align IT strategy with business objectives
2. [ ] Establish service management framework
3. [ ] Invest in training and certification
4. [ ] Select and implement ITSM tooling
5. [ ] Define service catalog and SLAs
6. [ ] Create continual improvement culture

---

**Last Updated:** 2026-03-16
**Maintainer:** Documentation Team
**Related:**
- See `Agile_Scrum_Learning_Plan_EN.md` for Agile/Scrum methodology
- See `Kanban_Learning_Plan_EN.md` for Kanban workflow
