---
description: Agile and Scrum essential guide - sprints, ceremonies, roles, backlog management, retrospectives, and iterative development workflows.
---

# Agile & Scrum Essential Guide (English)

**Target Audience:** Development Teams, Project Managers, Product Owners, Scrum Masters, Team Leaders
**Reading Time:** 30-45 minutes
**Application Time:** Immediate (framework for iterative development)

---

## What is Agile?

**Agile** is a mindset and set of principles for software development that emphasizes:
- **Iterative development** (work in short cycles)
- **Customer collaboration** (continuous feedback)
- **Adaptability** (embrace change)
- **Working software** (over comprehensive documentation)

### The Agile Manifesto (2001)

**Core Values:**
1. **Individuals and interactions** over processes and tools
2. **Working software** over comprehensive documentation
3. **Customer collaboration** over contract negotiation
4. **Responding to change** over following a plan

**12 Principles (Key Highlights):**
- Satisfy customers through early and continuous delivery
- Welcome changing requirements, even late in development
- Deliver working software frequently (weeks, not months)
- Business people and developers work together daily
- Build projects around motivated individuals
- Face-to-face conversation is the best communication
- Working software is the primary measure of progress
- Sustainable development (maintain constant pace)
- Continuous attention to technical excellence
- Simplicity (maximize work not done)
- Self-organizing teams
- Regular reflection and adjustment

---

## What is Scrum?

**Scrum** is the most popular Agile framework. It's a lightweight process for managing complex projects through:
- **Sprints** (time-boxed iterations)
- **Defined roles** (Product Owner, Scrum Master, Development Team)
- **Ceremonies** (meetings with specific purposes)
- **Artifacts** (tangible work products)

### Scrum Framework Overview

```

PRODUCT BACKLOG 
(Prioritized list of features/requirements) 


↓

SPRINT (1-4 weeks) 

SPRINT PLANNING → Daily Scrum → Development Work 
↓ 
SPRINT BACKLOG (Selected items for this sprint) 



↓

SPRINT REVIEW & RETROSPECTIVE 
(Demo + Team Improvement) 


↓
INCREMENT (Working software)
```

---

## Scrum Roles (The Scrum Team)

### 1. Product Owner (PO)

**Responsibilities:**
- Define and prioritize the Product Backlog
- Maximize value of the product
- Ensure transparency of backlog to all
- Accept or reject work results
- Make decisions about features and releases

**Key Skills:**
- Business domain knowledge
- Stakeholder management
- Decision-making
- Vision and strategy

**Common Mistakes:**
- Being unavailable to the team
- Not prioritizing effectively
- Micromanaging the team
- Acting as a project manager instead of value maximizer

---

### 2. Scrum Master (SM)

**Responsibilities:**
- Facilitate Scrum events
- Remove impediments (blockers)
- Coach the team on Scrum practices
- Protect the team from external distractions
- Foster self-organization
- Shield team from interruptions

**Key Skills:**
- Servant leadership
- Facilitation
- Conflict resolution
- Coaching and mentoring
- Process improvement

**Common Mistakes:**
- Acting as a traditional manager
- Solving all problems for the team (instead of coaching)
- Not addressing impediments quickly
- Enforcing Scrum rigidly without context

---

### 3. Development Team (Developers)

**Responsibilities:**
- Self-organize to accomplish work
- Deliver potentially shippable increments each sprint
- Cross-functional (all skills needed)
- Collaborate daily
- Commit to sprint goals
- Maintain quality standards

**Characteristics:**
- 3-9 members (ideal)
- No sub-teams or hierarchies
- Collective ownership of work
- Accountable as a team, not individuals

**Common Mistakes:**
- Not truly self-organizing
- Siloed work (not collaborating)
- Over-committing in sprint planning
- Poor technical practices (no testing, no CI/CD)

---

## Scrum Artifacts

### 1. Product Backlog

**What:** Ordered list of everything needed in the product (features, bugs, technical work)

**Characteristics:**
- Dynamic (constantly evolving)
- Prioritized by Product Owner
- Estimated by Development Team
- Refined regularly

**Example Items:**
```
Priority | Story | Estimate | Status
---------|--------------------------------------------|---------|---------
1 | As a user, I want to reset my password | 5 points| Ready
2 | As admin, I want to export user reports | 8 points| Needs refinement
3 | Fix: Login timeout error | 3 points| Ready
4 | Technical: Upgrade database to v12 | 13 points| Spike needed
```

---

### 2. Sprint Backlog

**What:** The subset of Product Backlog items selected for the current sprint + plan to deliver them

**Characteristics:**
- Created during Sprint Planning
- Owned by Development Team
- Can be adjusted during sprint (how, not what)
- Visible to all (task board)

**Example Sprint Backlog (Task Board):**
```

TO DO IN PROGRESS REVIEW DONE 

Story A Story B Story C Story D 
- Task 1 - Task 3 - Task 5 
- Task 2 - Task 4 

```

---

### 3. Increment (Product Increment)

**What:** The sum of all completed Product Backlog items at the end of a sprint

**Characteristics:**
- Must be in "Done" state (meets Definition of Done)
- Potentially shippable (deployable quality)
- Cumulative (includes previous increments)
- Inspected during Sprint Review

**Definition of Done (Example):**
- Code written and peer-reviewed
- Unit tests passed (>80% coverage)
- Integration tests passed
- Documentation updated
- Deployed to staging environment
- Product Owner acceptance

---

## Scrum Events (Ceremonies)

### 1. Sprint Planning

**Purpose:** Plan the work for the upcoming sprint

**Timeboxed:** Max 8 hours for 4-week sprint (shorter for shorter sprints)

**Attendees:** Entire Scrum Team

**Agenda:**
1. **What can be done?** (Product Owner presents top priorities)
2. **How will it be done?** (Team breaks down work into tasks)

**Outputs:**
- Sprint Goal (objective statement)
- Sprint Backlog (selected items + tasks)

**Example Sprint Goal:**
> "Enable users to manage their account settings, focusing on password reset and email preferences."

---

### 2. Daily Scrum (Stand-up)

**Purpose:** Synchronize work and plan for the next 24 hours

**Timeboxed:** 15 minutes

**Attendees:** Development Team (Scrum Master and PO can attend but don't participate)

**Format (Traditional 3 Questions):**
1. What did I do yesterday?
2. What will I do today?
3. Are there any impediments blocking me?

**Alternative Format (Focus on Sprint Goal):**
- What progress did we make toward the Sprint Goal?
- What's our plan for today?
- What obstacles need to be addressed?

**Common Mistakes:**
- Reporting to Scrum Master instead of team collaboration
- Too long (>15 minutes)
- Problem-solving during stand-up (take offline)
- Not daily or inconsistent timing

---

### 3. Sprint Review (Demo)

**Purpose:** Inspect the increment and adapt the Product Backlog

**Timeboxed:** Max 4 hours for 4-week sprint

**Attendees:** Scrum Team + Stakeholders

**Agenda:**
1. Product Owner explains what was "Done" vs not done
2. Development Team demonstrates working software
3. Product Owner discusses Product Backlog status
4. Stakeholders provide feedback
5. Group collaborates on what to do next

**Key Point:** Informal demonstration, not a formal presentation

---

### 4. Sprint Retrospective

**Purpose:** Team reflects on the past sprint and identifies improvements

**Timeboxed:** Max 3 hours for 4-week sprint

**Attendees:** Scrum Team only

**Format (Many variations exist):**
- **Start-Stop-Continue:** What should we start, stop, continue doing?
- **Glad-Sad-Mad:** What made us glad, sad, or mad?
- **4 Ls:** Liked, Learned, Lacked, Longed For

**Output:** Action items for the next sprint (process improvements)

**Example Retrospective Outcomes:**
```
What went well:
Good collaboration on complex story
Automated deployment pipeline

What didn't go well:
Too many production bugs
Unclear acceptance criteria

Action Items:
→ Implement automated testing for critical paths
→ PO to review stories with team before sprint
```

---

## Scrum Cadence (Example: 2-Week Sprint)

```
Week 1:
Monday: Sprint Planning (4 hours)
Tue-Fri: Daily Scrum (15 min each morning)
Development Work

Week 2:
Mon-Thu: Daily Scrum + Development Work
Friday: Sprint Review (2 hours)
Sprint Retrospective (1.5 hours)
[Next Sprint Planning can start immediately]
```

---

## Key Scrum Concepts

### Sprint

- **Fixed-length iteration** (1-4 weeks, commonly 2 weeks)
- **No changes to Sprint Goal** once started
- Can be cancelled (rare, only by Product Owner)
- Sustainable pace (no death marches)

### Sprint Goal

- **Objective** that guides the team during the sprint
- Provides **flexibility** in how to achieve it
- Focuses team on **why** not just **what**

**Example:**
> "Improve user onboarding experience by reducing signup friction"

### Velocity

- **Measure of work completed** per sprint (story points or # of stories)
- Used for **forecasting** future capacity
- Team-specific (don't compare across teams)
- Trend matters more than absolute number

**Example Velocity Chart:**
```
Story Points Completed
↑
40| 
35| 
30| 
25| 
→ Sprints
1 2 3 4 5 6
```

### Definition of Ready (DoR)

Checklist for backlog items to be ready for sprint planning:
- User story format followed
- Acceptance criteria defined
- Dependencies identified
- Estimated by team
- Testable

### Definition of Done (DoD)

Checklist for work to be considered complete:
- Code complete and reviewed
- Tests written and passing
- Documentation updated
- Deployed to staging
- Accepted by Product Owner

---

## User Stories

**Format:**
```
As a [role]
I want [feature]
So that [benefit]
```

**Example:**
```
As a customer
I want to track my order status
So that I know when to expect delivery
```

**Acceptance Criteria (Given-When-Then):**
```
Given I have placed an order
When I view the order details page
Then I should see the current delivery status
And I should see estimated delivery date
```

**INVEST Criteria (Good User Stories):**
- **I**ndependent (can be developed separately)
- **N**egotiable (details can be discussed)
- **V**aluable (delivers value to users)
- **E**stimable (team can estimate effort)
- **S**mall (fits in a sprint)
- **T**estable (clear acceptance criteria)

---

## Estimation Techniques

### 1. Story Points

**Relative sizing** using Fibonacci sequence: 1, 2, 3, 5, 8, 13, 21

**What Story Points Represent:**
- Complexity
- Effort
- Uncertainty/Risk

**Example:**
- 1 point: Simple config change
- 3 points: New API endpoint with basic logic
- 8 points: Complex feature with DB changes
- 13 points: Major refactoring or new integration

### 2. Planning Poker

**Process:**
1. Product Owner reads user story
2. Team discusses and asks questions
3. Each member selects a card (story points) privately
4. All reveal simultaneously
5. Discuss discrepancies (high/low estimators explain)
6. Re-vote until consensus

### 3. T-Shirt Sizing

Quick estimation: XS, S, M, L, XL, XXL

Useful for initial backlog refinement before detailed estimation.

---

## Scaling Scrum

### Multiple Teams

**Scrum of Scrums:**
- Representatives from each team meet regularly
- Discuss dependencies and integration
- Coordinate work across teams

### Frameworks for Scale:

**SAFe (Scaled Agile Framework):**
- Large enterprises
- Portfolio, Program, Team levels
- Release trains

**LeSS (Large-Scale Scrum):**
- Minimal changes to Scrum
- One Product Backlog, multiple teams
- Prefer descaling over scaling

**Nexus:**
- 3-9 Scrum teams on one product
- Nexus Integration Team
- Nexus Sprint events

---

## Common Anti-Patterns

### Scrum But...

"We do Scrum, but..."
- We don't do retrospectives
- We have 6-month sprints
- Product Owner isn't available
- We skip sprint planning

**Reality:** Not doing Scrum properly

### Zombie Scrum

Going through Scrum motions without agile mindset:
- No real stakeholder engagement
- No working software delivered
- Meetings feel like status reports
- No continuous improvement

### Waterfall in Sprints

- Big design upfront
- Testing phase at the end
- No working software mid-sprint
- Handoffs between roles

---

## Agile vs Traditional (Waterfall)

| Aspect | Agile/Scrum | Waterfall |
|--------|-------------|-----------|
| **Approach** | Iterative, incremental | Sequential, linear |
| **Requirements** | Evolving, flexible | Fixed upfront |
| **Delivery** | Continuous (every sprint) | Single release at end |
| **Customer Involvement** | High (continuous) | Low (beginning and end) |
| **Change** | Welcomed anytime | Costly and discouraged |
| **Team Structure** | Cross-functional, self-organizing | Specialized, hierarchical |
| **Documentation** | Lightweight, as needed | Comprehensive upfront |
| **Risk** | Early detection and mitigation | Late discovery |
| **Best For** | Uncertain requirements, innovation | Clear requirements, regulated |

---

## When to Use Scrum

**Scrum Works Well For:**
- Complex product development
- Uncertain or changing requirements
- Need for frequent feedback
- Cross-functional teams
- Innovation and exploration

**Scrum May Not Fit:**
- Well-defined, predictable work
- Highly regulated environments (strict documentation)
- Small, simple projects
- Teams not co-located and can't communicate frequently
- Organization unwilling to embrace change

---

## Transitioning to Scrum

### Phase 1: Foundation (Month 1-2)
- Scrum Master training/certification
- Team education on Agile principles
- Define initial Product Backlog
- Establish Definition of Done

### Phase 2: First Sprints (Month 3-4)
- Start with 2-week sprints
- Focus on cadence and ceremonies
- Learn estimation techniques
- Build trust and collaboration

### Phase 3: Optimization (Month 5-6)
- Refine processes based on retrospectives
- Improve engineering practices
- Increase velocity and predictability
- Stakeholder engagement

### Phase 4: Maturity (Ongoing)
- Continuous improvement culture
- Self-organizing team
- Consistent delivery
- Metrics-driven decisions

---

## Metrics in Scrum

### Team-Level Metrics

**1. Velocity**
- Story points completed per sprint
- Trend over time

**2. Sprint Burndown**
- Remaining work per day in sprint
- Visual progress tracking

**3. Velocity Trend**
- Increasing = improving efficiency or better estimation
- Stable = predictable delivery
- Decreasing = impediments or overcommitment

**4. Defect Rate**
- Bugs found in production
- Quality indicator

### Product-Level Metrics

**1. Release Burnup**
- Features completed vs total scope
- Progress toward release goal

**2. Cycle Time**
- Time from "start" to "done"
- Efficiency indicator

**3. Lead Time**
- Time from request to delivery
- Customer perspective

---

## Engineering Practices (Technical Agility)

**Scrum defines the framework, but technical practices ensure quality:**

### Continuous Integration (CI)
- Integrate code multiple times per day
- Automated builds and tests
- Fast feedback

### Test-Driven Development (TDD)
- Write tests before code
- Refactor with confidence
- Better design

### Pair Programming
- Two developers, one workstation
- Knowledge sharing
- Fewer defects

### Code Review
- Peer review before merge
- Knowledge transfer
- Quality gate

### Refactoring
- Continuous code improvement
- Reduce technical debt
- Maintain velocity

### Automated Testing
- Unit, integration, end-to-end tests
- Fast regression detection
- Confidence in changes

---

## Scrum Master Facilitation Tips

### Sprint Planning
- Timebox discussions
- Use planning poker for estimation
- Ensure Sprint Goal is clear
- Prevent over-commitment

### Daily Scrum
- Start on time, end on time
- Keep it focused on the Sprint Goal
- Take detailed discussions offline
- Update the board visibly

### Sprint Review
- Make it interactive, not just a demo
- Encourage stakeholder feedback
- Capture new backlog items
- Celebrate successes

### Retrospective
- Create safe environment
- Vary the format to keep fresh
- Focus on actionable improvements
- Track action items from previous retros

---

## Product Owner Best Practices

### Backlog Management
- Refine backlog regularly (10% of sprint capacity)
- Keep top items "Ready"
- Ruthlessly prioritize
- Say "no" to low-value features

### Stakeholder Engagement
- Regular communication
- Manage expectations
- Gather feedback continuously
- Transparent roadmap

### Vision and Strategy
- Clear product vision
- Communicate the "why"
- Align backlog with business goals
- Measure value delivered

---

## Scrum in Platform/Company Context

**Real-World Application:**

**Team Structure:**
- Product Owner: Product Manager or Lead
- Scrum Master: Engineering Manager or dedicated SM
- Dev Team: Engineers, QE, Docs (5-7 people)

**Sprint Cadence:**
- 2-week sprints (common in software teams)
- Sprint Planning: Monday morning
- Daily Scrum: 9:00 AM daily
- Sprint Review: Friday afternoon (demo to stakeholders)
- Retrospective: Friday after review

**Backlog:**
- Jira as backlog management tool
- Epics for larger initiatives (INFRA epics)
- Stories for user-facing features
- Tasks for technical work
- Bugs prioritized with stories

**Integration with ITIL:**
- Incident Management: High-priority bugs in sprint
- Change Management: Sprint Review as change approval
- Release Management: Sprint increment as release candidate
- Problem Management: Root cause analysis in retros

---

## Quick Reference: Scrum Cheat Sheet

### Roles
- **Product Owner:** Maximize value, manage backlog
- **Scrum Master:** Facilitate, remove impediments
- **Development Team:** Build product, self-organize

### Events (2-week sprint)
- **Sprint Planning:** 4 hours
- **Daily Scrum:** 15 minutes
- **Sprint Review:** 2 hours
- **Sprint Retrospective:** 1.5 hours

### Artifacts
- **Product Backlog:** All work for product
- **Sprint Backlog:** Work for current sprint
- **Increment:** Potentially shippable product

### Key Principles
- Transparency, Inspection, Adaptation
- Commitment, Courage, Focus, Openness, Respect
- Empiricism: Decisions based on observation and experience

---

## Resources

### Official
- [Scrum Guide](https://scrumguides.org/) - Official definition (17 pages)
- [Scrum.org](https://www.scrum.org/) - Training and certification
- [Scrum Alliance](https://www.scrumalliance.org/) - Community and certification

### Books
- "Scrum: The Art of Doing Twice the Work in Half the Time" - Jeff Sutherland
- "Essential Scrum" - Kenneth Rubin
- "User Story Mapping" - Jeff Patton
- "The Agile Samurai" - Jonathan Rasmusson

### Certifications
- **CSM (Certified Scrum Master)** - Scrum Alliance
- **PSM (Professional Scrum Master)** - Scrum.org
- **CSPO (Certified Scrum Product Owner)**
- **CSD (Certified Scrum Developer)**

### Tools
- **Jira:** Most popular Agile project management
- **Azure DevOps:** Microsoft's offering
- **Trello:** Simple Kanban boards
- **Miro/Mural:** Virtual collaboration for remote teams

---

## Action Items for Your Team

### Getting Started
1. [ ] Educate team on Agile Manifesto and Scrum Guide
2. [ ] Define roles (who will be PO, SM, Developers)
3. [ ] Create initial Product Backlog (prioritized)
4. [ ] Establish Definition of Done
5. [ ] Set up sprint cadence and calendar events

### First Sprint
1. [ ] Hold Sprint Planning (set Sprint Goal)
2. [ ] Daily Scrum at same time/place
3. [ ] Track work on physical or digital board
4. [ ] Sprint Review (demo working software)
5. [ ] Sprint Retrospective (identify 1-2 improvements)

### Continuous Improvement
1. [ ] Track velocity over 3-5 sprints
2. [ ] Refine backlog regularly
3. [ ] Act on retrospective items
4. [ ] Celebrate small wins
5. [ ] Share knowledge across team

---

**Last Updated:** 2026-03-16
**Maintainer:** Documentation Team
**Related:** See `Kanban_Learning_Plan_EN.md` for comparison with Kanban methodology
