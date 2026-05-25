# Kanban Essential Guide (English)

**Target Audience:** Development Teams, Operations Teams, Support Teams, Project Managers, Any team wanting to visualize work
**Reading Time:** 25-35 minutes
**Application Time:** Immediate (can start with just a board)

---

## What is Kanban?

**Kanban** is a visual workflow management method that helps teams visualize work, limit work in progress, and maximize efficiency (or flow). Originally developed by Toyota for manufacturing, it has been adapted for knowledge work, especially software development and IT operations.

### Core Concepts

**Kanban in Japanese:** (kan = visual, ban = card or board)

**Key Principles:**
- **Visualize work:** Make all work visible on a board
- **Limit WIP:** Control how much work is in progress
- **Manage flow:** Optimize the movement of work through the system
- **Make policies explicit:** Define and display process rules
- **Implement feedback loops:** Regular reviews and improvements
- **Improve collaboratively:** Evolutionary change, not revolution

---

## Kanban vs Scrum - Quick Comparison

| Aspect | Kanban | Scrum |
|--------|--------|-------|
| **Iterations** | Continuous flow | Fixed sprints (1-4 weeks) |
| **Roles** | No prescribed roles | Product Owner, Scrum Master, Dev Team |
| **Commitment** | Pull items as capacity allows | Commit to sprint backlog |
| **Changes** | Can add items anytime | No changes during sprint |
| **Metrics** | Cycle Time, Lead Time, Throughput | Velocity, Burndown |
| **Board** | Persistent (work flows through) | Reset each sprint |
| **Meetings** | Daily stand-up, Replenishment, Review | Sprint Planning, Daily Scrum, Review, Retro |
| **Best For** | Continuous work, support, ops | Product development, projects |

**Key Insight:** Kanban is more flexible; Scrum is more structured. Many teams use "Scrumban" (hybrid approach).

---

## The Kanban Board

The **Kanban board** is the heart of the system. It visualizes work flowing through different stages.

### Basic Board Structure

```

 KANBAN BOARD 

 BACKLOG TO DO IN PROGRESS REVIEW DONE 
 
 Story A Story C Story E Story G Story I 
 Story B Story D Story F Story H Story J 
 Story E Story K 
 
 WIP Limit: 3 WIP Limit:2 WIP Limit:2 

```

### Columns (States)

**Typical columns for software development:**
1. **Backlog:** All work to be done (prioritized)
2. **To Do:** Work selected and ready to start
3. **In Progress:** Work currently being done
4. **Review/Testing:** Work being validated
5. **Done:** Work completed and delivered

**For IT Operations/Support:**
1. **New:** New requests/incidents
2. **Triage:** Being assessed and prioritized
3. **In Progress:** Being worked on
4. **Waiting:** Blocked or waiting for external input
5. **Resolved:** Completed

**Customize to your workflow!** Your columns should match your actual process.

---

## Work In Progress (WIP) Limits

**WIP limits** are the core mechanism for managing flow in Kanban.

### Why Limit WIP?

**Problem without WIP limits:**
- Everyone working on different tasks
- Nothing gets finished
- Context switching overhead
- No visibility into bottlenecks

**Benefits of WIP limits:**
- Forces focus on finishing work
- Reveals bottlenecks quickly
- Reduces context switching
- Improves flow and throughput
- Encourages collaboration (help finish items before starting new)

### Setting WIP Limits

**Formula (starting point):**
```
WIP Limit = Number of Team Members + 1
```

**Example:**
- Team of 5 people
- WIP limit for "In Progress" = 6 items

**Adjust based on:**
- Team size
- Task complexity
- Number of columns
- Dependencies

### WIP Limit Policy

**What happens when WIP limit is reached?**

```

 IN PROGRESS (WIP: 3/3) - LIMIT REACHED! 
 • Task A 
 • Task B 
 • Task C 

 ↓
 CANNOT PULL NEW WORK
 ↓
 OPTIONS:
 1. Help finish existing tasks
 2. Remove blockers
 3. Swarm on oldest item
 4. Address impediments
```

**Golden Rule:** Stop starting, start finishing!

---

## Kanban Principles (David Anderson's 6 Practices)

### 1. Visualize the Workflow

**Make work visible so everyone understands:**
- What work exists
- What stage it's in
- Who's working on what
- Where bottlenecks are

**Techniques:**
- Physical board (cards on wall)
- Digital board (Jira, Trello, Azure Boards)
- Swimlanes for different work types
- Color coding for priority or type

**Example:**
```

 Swimlanes by Work Type 

 FEATURES 
 Story A Story C 

 BUGS 
 Bug X Bug Y 

 SUPPORT 
 Req 1 Req 2 

```

---

### 2. Limit Work in Progress (WIP)

**Already covered above** - Core mechanism for flow.

---

### 3. Manage Flow

**Flow** = How work moves through your system

**Goal:** Smooth, predictable flow with minimal delays

**Flow Metrics:**
- **Throughput:** Items completed per time period (week/month)
- **Cycle Time:** Time from "In Progress" to "Done"
- **Lead Time:** Time from "Requested" to "Done"

**Optimizing Flow:**
- Identify and remove bottlenecks
- Balance work across stages
- Reduce wait times
- Standardize similar work
- Improve handoffs

**Cumulative Flow Diagram (CFD):**
```
Items
 ↑
 Done
 
 In Review
 In Progress
 To Do
 
 → Time

Width of each band = WIP in that state
Slope = Throughput
```

---

### 4. Make Process Policies Explicit

**Document and display your rules:**

**Examples of policies:**
- **Definition of Ready:** When can work move from Backlog to To Do?
- **Definition of Done:** When is work truly complete?
- **WIP Limits:** How many items in each column?
- **Pull Criteria:** When can team members pull new work?
- **Priority Rules:** How is work prioritized?

**Example Policy Board Section:**
```

 POLICIES 

 • WIP Limit "In Progress" = 5 
 • Critical bugs take priority 
 • Definition of Done: 
 - Code reviewed 
 - Tests pass 
 - Deployed to staging 
 • Daily standup at 9:00 AM 

```

---

### 5. Implement Feedback Loops

**Regular meetings to inspect and adapt:**

#### Daily Standup (15 min)
- Walk the board right to left (Done → To Do)
- Focus on work, not people
- Identify blockers
- Ensure WIP limits respected

**Questions:**
- What items are blocked?
- What's close to being done?
- Where do we need to swarm?

#### Replenishment Meeting (weekly/biweekly)
- Review backlog
- Prioritize upcoming work
- Ensure enough ready work in backlog

#### Delivery Planning (monthly/quarterly)
- Forecast future capacity
- Plan releases
- Coordinate with stakeholders

#### Operations Review (monthly)
- Review metrics (throughput, cycle time, lead time)
- Identify trends
- Discuss service delivery

#### Risk Review (as needed)
- Identify risks and blockers
- Mitigation strategies

---

### 6. Improve Collaboratively, Evolve Experimentally

**Kanban embraces evolutionary change:**

- **Start with what you do now:** Don't redesign everything
- **Pursue incremental improvements:** Small, safe changes
- **Use scientific method:** Hypothesis → Experiment → Measure → Learn
- **Respect current roles and responsibilities:** Change happens gradually

**Improvement Process:**
1. **Identify improvement opportunity** (from metrics or observation)
2. **Form hypothesis:** "If we do X, Y will improve"
3. **Run small experiment:** Try it for 2 weeks
4. **Measure results:** Did Y improve?
5. **Keep or revert:** Adopt if successful, rollback if not

**Example:**
> Hypothesis: "If we add a 'Code Review' column with WIP limit of 2, our cycle time will decrease because reviews won't pile up."
>
> Experiment: Add column for 2 weeks, measure cycle time
>
> Result: Cycle time reduced by 20% → Keep the change

---

## Kanban Metrics

### 1. Lead Time

**Definition:** Total time from work item creation to completion

**Measures:** Customer perspective (how long they wait)

**Example:**
```
User requests feature → 15 days later → Feature deployed
Lead Time = 15 days
```

**Improvement strategies:**
- Reduce backlog size
- Improve prioritization
- Optimize entire workflow

---

### 2. Cycle Time

**Definition:** Time from when work starts to when it's done

**Measures:** Team efficiency

**Example:**
```
Team starts working on item → 5 days later → Item completed
Cycle Time = 5 days
```

**Improvement strategies:**
- Reduce WIP
- Remove blockers
- Improve team skills
- Better tooling/automation

**Lead Time vs Cycle Time:**
```
Lead Time

 Backlog Cycle Time 
 (wait) (active work) 
 
Request Start Done
```

---

### 3. Throughput

**Definition:** Number of items completed in a time period

**Measures:** Team capacity

**Example:**
- Completed 20 items in 2 weeks
- Throughput = 10 items/week

**Use for:**
- Forecasting (how much can we deliver?)
- Capacity planning
- Trend analysis

**Throughput Chart:**
```
Items/Week
 ↑
15 
 
10 
 
 5
 → Weeks
 1 2 3 4 5 6
```

---

### 4. Work Item Age

**Definition:** How long an item has been in progress (not yet done)

**Purpose:** Identify stale work

**Example:**
- Item started 30 days ago, still in progress
- Age = 30 days (RED FLAG - should investigate)

**Action:** Items with high age should be:
- Prioritized for completion
- Investigated for blockers
- Potentially broken down smaller

---

### 5. Blocked Time

**Definition:** How long items spend in "Blocked" state

**Purpose:** Quantify impact of impediments

**Improvement:** Focus on reducing blocked time through:
- Better dependencies management
- Faster escalation
- Root cause analysis

---

## Types of Kanban Boards

### 1. Team Kanban Board

**Purpose:** Manage team's daily work

**Example (Development Team):**
```

 BACKLOG READY DEVELOP REVIEW DONE 
 (WIP: 5) (WIP: 3) (WIP: 2) 

 Story 10 Story 4 Story 1 Story 2 Story 3
 Story 11 Story 5 
 Story 12 

```

---

### 2. Portfolio Kanban Board

**Purpose:** Manage strategic initiatives/projects

**Example (Leadership/PM):**
```

 IDEATION ANALYSIS EXECUTING COMPLETE 

Project A Project D Project E Project G 
Project B Project F Project H 
Project C 

```

---

### 3. Support/Incident Kanban Board

**Purpose:** Manage support tickets, incidents, requests

**Example (IT Support):**
```

 NEW TRIAGE IN PROGRESS WAITING RESOLVED 

INC-1 INC-5 INC-10 INC-15 INC-20 
INC-2 INC-6 INC-11 INC-16 INC-21 
INC-3 
INC-4 

```

**Swimlanes by Priority:**
- P1 (Critical)
- P2 (High)
- P3 (Normal)
- P4 (Low)

---

## Kanban Cadences (Meetings)

### Daily Standup (15 min)

**Purpose:** Synchronize and identify blockers

**Format:**
- Walk the board from right to left (Done → To Do)
- Focus on work items, not individuals
- Identify blockers and help needed

**Questions:**
- What's blocked?
- What's aging (getting old)?
- Where can we collaborate?

---

### Replenishment Meeting (Weekly)

**Purpose:** Fill the pipeline with work

**Activities:**
- Review new work requests
- Prioritize backlog
- Ensure sufficient "Ready" work
- Break down large items

**Outcome:** Backlog is prioritized and sized appropriately

---

### Kanban Review (Biweekly/Monthly)

**Purpose:** Review delivered value with stakeholders

**Activities:**
- Demo completed work
- Gather feedback
- Discuss upcoming priorities
- Adjust roadmap

**Similar to:** Scrum Sprint Review

---

### Retrospective (Monthly/Quarterly)

**Purpose:** Improve processes and collaboration

**Activities:**
- Review metrics (cycle time, throughput, etc.)
- Discuss what's working / not working
- Identify improvement experiments
- Review previous experiments

**Similar to:** Scrum Sprint Retrospective

---

### Service Delivery Review (Monthly/Quarterly)

**Purpose:** Operational health check

**Review:**
- Delivery metrics (throughput, cycle time)
- Quality metrics (defect rate, rework)
- Service levels (SLA compliance)
- Customer satisfaction

**Attendees:** Team + Management

---

## Classes of Service

**Classes of Service** define different types of work with different policies.

### Common Classes

**1. Expedite (Red/Urgent)**
- Critical issues, production down
- Highest priority
- No WIP limit (can exceed)
- Example: P1 incidents, security vulnerabilities

**2. Fixed Delivery Date (Orange)**
- Hard deadline (regulatory, contractual)
- Scheduled with buffer
- Example: Compliance reports, contracted features

**3. Standard (Blue)**
- Normal work
- Follows WIP limits
- Example: Features, improvements

**4. Intangible (Green)**
- Technical debt, refactoring, learning
- Background work
- Lower priority but necessary
- Example: Code cleanup, documentation, training

### Visualizing Classes on Board

```

 EXPEDITE (no limit) 
 [INC-123: DB Down] 

 FIXED DATE 
 [Feature X - Due: March 30] 

 STANDARD (WIP: 5) 
 [Feature A] [Feature B] [Bug C] 

 INTANGIBLE 
 [Refactor module Y] 

```

---

## Scrumban (Scrum + Kanban Hybrid)

Many teams combine Scrum and Kanban for the best of both worlds.

### Scrumban Characteristics

**From Scrum:**
- Sprint cadence (1-2 weeks)
- Sprint Planning (select work)
- Sprint Review (demo)
- Sprint Retrospective

**From Kanban:**
- Kanban board with WIP limits
- Continuous flow within sprint
- Pull system (take new work when capacity)
- Focus on cycle time

**Example Scrumban Board:**
```

 SPRINT TO DO DOING REVIEW DONE 
 BACKLOG (WIP: 5) (WIP: 3) (WIP: 2) 

 Story A Story D Story F Story H Story 
 Story B Story E Story G I 
 Story C 

 ↑ ↓
 Sprint Planning Sprint Review
```

**When to Use Scrumban:**
- Team is comfortable with Scrum but wants more flexibility
- Work has unpredictable arrival (support + development)
- Want sprint commitment but continuous flow

---

## Kanban for Different Contexts

### Software Development

**Board:**
```
Backlog → Ready → Dev → Code Review → Testing → Deployed
```

**Key Practices:**
- Feature flags for continuous deployment
- Automated testing
- Small batch sizes
- Frequent releases

---

### IT Operations / SRE

**Board:**
```
New → Triage → Assigned → In Progress → Waiting → Resolved
```

**Key Practices:**
- Priority-based swimlanes (P1, P2, P3, P4)
- SLA tracking
- Automated triage where possible
- Escalation policies

---

### DevOps / Platform Engineering

**Board:**
```
Requests → Backlog → Design → Build → Test → Deploy → Done
```

**Key Practices:**
- Infrastructure as Code
- Automated pipelines
- Self-service where possible
- Platform metrics

---

### Support Team

**Board:**
```
New Tickets → L1 Triage → L2 Support → L3 Engineering → Resolved
```

**Key Practices:**
- Knowledge base integration
- Automatic routing rules
- Customer communication tracking
- SLA compliance monitoring

---

## Getting Started with Kanban

### Step 1: Map Your Current Workflow

**Ask:**
- What are the stages work goes through?
- Where does work come from?
- When is work "done"?

**Example for dev team:**
1. Request comes in
2. Gets prioritized
3. Developer picks it up
4. Code is written
5. Code review happens
6. Testing
7. Deployment
8. Done

**Create columns for each stage.**

---

### Step 2: Visualize Existing Work

**Put all current work on the board:**
- One card per work item
- Place in appropriate column
- Include all work (not just "official" work)

**This often reveals:**
- Too much WIP
- Invisible work
- Bottlenecks

---

### Step 3: Set Initial WIP Limits

**Start conservatively:**
- Count current WIP in each column
- Set limit slightly below current WIP
- Adjust based on learning

**Example:**
- Currently 8 items "In Progress"
- Set WIP limit to 6
- Forces team to finish more before starting new

---

### Step 4: Hold Daily Standups

**Walk the board:**
- Right to left (Done → To Do)
- Focus on blockers
- Respect WIP limits

---

### Step 5: Measure and Improve

**Track basic metrics:**
- Throughput (items/week)
- Cycle time (days from start to done)
- Lead time (days from request to done)

**Review monthly:**
- Are metrics improving?
- Where are bottlenecks?
- What experiments should we try?

---

## Common Kanban Anti-Patterns

### 1. No WIP Limits

**Problem:** Everyone working on different things, nothing finishing

**Solution:** Set and enforce WIP limits

---

### 2. WIP Limits Too High

**Problem:** Limits don't constrain behavior

**Solution:** Lower limits gradually until you feel the constraint

---

### 3. Not Pulling Work

**Problem:** Work is pushed to people instead of pulled

**Solution:** Team members pull work when they have capacity

---

### 4. Ignoring Blockers

**Problem:** Blocked items sit indefinitely

**Solution:** Make blockers highly visible, swarm to resolve

---

### 5. No Metrics

**Problem:** Can't tell if improving or declining

**Solution:** Track at least throughput and cycle time

---

### 6. Board Doesn't Match Reality

**Problem:** Board shows one thing, reality is different

**Solution:** Daily discipline to update board, make it source of truth

---

## Tools for Kanban

### Physical Boards

**Pros:**
- Highly visible
- Tactile (sticky notes)
- Low barrier to start
- Great for co-located teams

**Cons:**
- Not accessible remotely
- Hard to track metrics
- No history/audit trail

**Best for:** Small, co-located teams

---

### Digital Tools

#### Jira

**Pros:**
- Powerful, highly customizable
- Advanced reporting
- Integrations

**Cons:**
- Complex, steep learning curve
- Can be slow
- Expensive

**Best for:** Large organizations, need for extensive reporting

---

#### Trello

**Pros:**
- Simple, intuitive
- Free tier available
- Quick to set up

**Cons:**
- Limited reporting
- Fewer advanced features

**Best for:** Small teams, simple workflows

---

#### Azure DevOps Boards

**Pros:**
- Integrated with Azure DevOps suite
- Good for Microsoft shops
- Decent reporting

**Cons:**
- Tied to Microsoft ecosystem
- Can be complex

**Best for:** Teams already using Azure DevOps

---

#### GitHub Projects

**Pros:**
- Integrated with GitHub
- Free for GitHub users
- Simple Kanban boards

**Cons:**
- Limited features
- Basic reporting

**Best for:** Open source projects, dev teams on GitHub

---

#### Linear

**Pros:**
- Modern, fast UI
- Great keyboard shortcuts
- Good for engineering teams

**Cons:**
- Newer tool, fewer integrations
- Paid only

**Best for:** Fast-moving engineering teams

---

## Kanban in Company/Platform Context

### Operations Team Board

**Columns:**
```
New Alerts → Triage → Investigating → Implementing Fix → Monitoring → Closed
```

**Classes of Service:**
- P1 (Prod down)
- P2 (Degraded)
- P3 (Normal)
- Tech Debt

**WIP Limits:**
- Investigating: 3
- Implementing Fix: 2

---

### Platform Team Board

**Columns:**
```
Requests → Backlog → Design → Development → Testing → Deployed
```

**Swimlanes:**
- Features
- Bug Fixes
- Infrastructure
- Security

**Metrics:**
- Lead Time: Request to Deployed
- Cycle Time: Development to Deployed
- Throughput: Deployments per week

---

### Integration with Jira

**Jira Kanban Board for INFRA:**
```
TO DO → IN PROGRESS → CODE REVIEW → TESTING → DONE
```

**Automation:**
- Auto-move to "Code Review" when PR opened
- Auto-move to "Testing" when PR merged
- Auto-move to "Done" when deployed to prod

---

## Learning Resources

### Books

- **"Kanban: Successful Evolutionary Change for Your Technology Business"** - David J. Anderson
 - THE definitive Kanban book
- **"Kanban from the Inside"** - Mike Burrows
 - Values and principles focus
- **"The Phoenix Project"** - Gene Kim
 - Novel demonstrating Kanban/DevOps concepts
- **"Making Work Visible"** - Dominica DeGrandis
 - Time theft and how Kanban helps

---

### Online Resources

- **Kanban University:** [https://kanban.university/](https://kanban.university/)
- **LeanKit Blog:** [https://leankit.com/learn/](https://leankit.com/learn/)
- **Kanban Tool Resources:** Various tool-specific guides

---

### Certifications

**Kanban University Certifications:**
- **TKP (Team Kanban Practitioner):** 2-day course, entry level
- **KMP I (Kanban Management Professional I):** Advanced
- **KMP II:** Expert level

**Note:** Certification is optional - Kanban is lightweight and can be learned without formal training.

---

## Quick Reference: Kanban Cheat Sheet

### Core Practices
1. Visualize the workflow
2. Limit WIP
3. Manage flow
4. Make policies explicit
5. Implement feedback loops
6. Improve collaboratively

### Key Metrics
- **Lead Time:** Request to Done
- **Cycle Time:** Start to Done
- **Throughput:** Items completed per period
- **WIP:** Work in progress count

### Cadences
- **Daily Standup:** 15 min
- **Replenishment:** Weekly
- **Review:** Biweekly/Monthly
- **Retrospective:** Monthly/Quarterly

### WIP Limits
- Start with: Team Size + 1
- Adjust based on flow
- Enforce strictly

---

## Action Items

### For Individuals
1. [ ] Read "Kanban" by David Anderson (or watch YouTube intro)
2. [ ] Create personal Kanban board (Trello or paper)
3. [ ] Track your tasks for 2 weeks
4. [ ] Measure your cycle time

### For Teams
1. [ ] Map current workflow (whiteboard session)
2. [ ] Create initial Kanban board
3. [ ] Set conservative WIP limits
4. [ ] Start daily standups
5. [ ] Measure throughput and cycle time for 1 month
6. [ ] Retrospective: What to improve?

### For Organizations
1. [ ] Pilot Kanban with 1-2 teams
2. [ ] Provide training and coaching
3. [ ] Select appropriate tooling
4. [ ] Establish metrics and reporting
5. [ ] Share learnings across teams
6. [ ] Scale successful practices

---

**Last Updated:** 2026-03-16
**Maintainer:** Documentation Team
**Related:**
- See `Agile_Scrum_Learning_Plan_EN.md` for Scrum comparison
- See `ITIL_Learning_Plan_EN.md` for ITIL integration
