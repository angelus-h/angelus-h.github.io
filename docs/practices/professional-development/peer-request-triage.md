# Peer Request Triage Guide

**Version:** 1.0  
**Purpose:** Framework for evaluating colleague help requests BEFORE committing time  
**Target:** Engineers who struggle with boundary-setting when peers ask for help

---

## The Problem

**Scenario:** A colleague asks for help. You want to be a good team player, so you say "yes" immediately.

**Result:** 4 hours later, you've solved their problem but made zero progress on your own work.

**Pattern:**
- Peer requests feel urgent (even when they're not)
- You feel obligated to help (compliance pressure)
- You don't clarify scope/timeline before starting
- Your own work gets deprioritized
- You feel resentful but don't know how to say "no"

**This guide teaches you to TRIAGE peer requests, not reject them.**

---

## The 30-Second Decision Framework

**When a colleague asks for help, spend 30 seconds answering these questions:**

```
┌─────────────────────────────────────┐
│ Is production down RIGHT NOW?      │
└─────────────────────────────────────┘
         │                    │
       YES                   NO
         │                    │
         ▼                    ▼
   Help immediately    ┌──────────────────────────┐
                       │ Am I the primary owner?  │
                       └──────────────────────────┘
                              │              │
                            YES             NO
                              │              │
                              ▼              ▼
                        Prioritize high   Clarify scope
                              │              │
                              ▼              ▼
                       ┌──────────────────────────┐
                       │ Do I have capacity NOW?  │
                       └──────────────────────────┘
                              │              │
                            YES             NO
                              │              │
                              ▼              ▼
                         Time-box it   Negotiate timeline
```

**Key insight:** Most peer requests are NOT production emergencies, yet we treat them like they are.

---

## Part 1: Classification (What Kind Of Request?)

### Type A: Production Emergency

**Characteristics:**
- Services are down NOW
- Users/customers are impacted
- Revenue loss or SLA breach
- All hands on deck situation

**Response Time:** Immediate (drop everything)

**Example:**
> "The Jenkins job is failing and blocking all RHEL module builds"

**Action:** Help immediately, clarify scope after stabilization.

---

### Type B: Urgent Investigation (Not Yet Emergency)

**Characteristics:**
- Something is broken but not yet impacting users
- Could become emergency if not addressed
- Blocking colleague's work TODAY
- Time-sensitive deadline (hours, not days)

**Response Time:** Within 1-2 hours

**Example:**
> "ursa-major-brew is failing, we need to fix before next UMB message arrives"

**Action:** Clarify scope and time-box BEFORE starting.

---

### Type C: Non-Urgent Help Request

**Characteristics:**
- No immediate production impact
- Not blocking urgent work
- "Would be nice to understand" or "Can you help when you have time"
- Exploratory or educational

**Response Time:** When you have capacity (hours to days)

**Example:**
> "Do you know if we have any way to test if the jenkins job is receiving UMB messages now?"

**Action:** Negotiate timeline based on your current workload.

---

### Type D: Misrouted Request (Not Your Domain)

**Characteristics:**
- You're being asked because you're helpful, not because it's your responsibility
- Someone else is the actual owner/expert
- You would need to learn the domain to help effectively

**Response Time:** Redirect immediately

**Example:**
> "Can you debug this OpenShift networking issue?" (when you're a CI/CD specialist)

**Action:** Point to correct owner, offer triage only if truly blocking.

---

## Part 2: Scope Clarification Questions

**BEFORE you start helping, ask these questions:**

### Essential Questions (Always Ask)

**1. What's the deadline?**
- "When do you need this resolved by?"
- "Is this blocking work today, this week, or just nice-to-have?"

**2. What's the impact?**
- "Is production affected?"
- "How many people are blocked?"
- "What's the business impact if we wait until tomorrow?"

**3. How much time should I allocate?**
- "Are we doing quick triage (30 min) or deep debugging (hours)?"
- "Do you need a full solution or just direction?"

**4. Am I the right person?**
- "Is this my area of ownership?"
- "Should [Primary Owner] be involved?"

### Optional Questions (Depending on Type)

**5. What have you tried already?**
- Prevents duplicate work
- Shows their effort level
- Helps you start from their current state

**6. Is there documentation?**
- "Have you checked [wiki/runbook/previous tickets]?"
- Teaches them to self-serve first

**7. Who else knows about this?**
- "Have you asked [Domain Expert]?"
- "Is [Manager] aware this is blocking you?"

---

## Part 3: Response Templates

### Production Emergency (Type A)

**Immediate Help:**
> "On it. Investigating now, will update in 30 minutes with findings."

**After Stabilization:**
> "Immediate issue resolved. For root cause analysis, I need [X hours] tomorrow when I'm not heads-down on [your priority work]. Sound good?"

---

### Urgent Investigation (Type B)

**Has Capacity:**
> "I can help. Let me time-box this to 1 hour. If we don't find root cause by then, we should loop in [Domain Expert] or escalate to [Manager]."

**No Capacity (But Can Help Soon):**
> "I'm heads-down on [X] until [time]. Can you wait until [afternoon/tomorrow], or is this blocking you RIGHT NOW?"

**No Capacity (Blocking Peer):**
> "I'm at capacity with [X] and [Y]. Let's escalate to [Manager] together so they can reprioritize or assign someone else."

---

### Non-Urgent Request (Type C)

**Has Capacity (With Time-Box):**
> "Happy to help! Quick clarification first:
> - Deadline: When do you need this?
> - Scope: Quick triage (30min) or full debug (hours)?
> - Impact: Is this blocking other work?
>
> Based on your answers, I can allocate [X time] [today/tomorrow]."

**No Capacity (Defer):**
> "I don't have capacity today (committed to [X]). I can look at this [tomorrow/next week]. Is that timeline okay, or should we find someone else?"

**No Capacity (Redirect):**
> "I'm swamped this week. Have you tried:
> - Checking [documentation/wiki]
> - Asking [Domain Expert]
> - Posting in [team channel]
>
> If it's urgent, let's escalate to [Manager] to reprioritize."

---

### Misrouted Request (Type D)

**Not Your Domain:**
> "This is [Domain Expert]'s area. I can do initial triage (30 min max), but they should be primary owner. Want me to loop them in?"

**Role Confusion:**
> "Just to clarify: is this my responsibility, or [Owner]'s? I'm happy to help, but want to make sure we're not duplicating work."

**Capability Mismatch:**
> "I'm not the best person for this (I don't have deep [domain] knowledge). [Expert] would solve this faster. Should we reach out to them?"

---

## Part 4: Time-Boxing Strategies

**Once you've agreed to help, ALWAYS time-box:**

### The 30-Minute Triage

**What you CAN do in 30 minutes:**
- Read error logs
- Identify obvious root cause
- Point to likely culprit
- Suggest next investigation steps
- Decide if this needs escalation

**What you CANNOT do in 30 minutes:**
- Full root cause analysis
- Fix the problem
- Write comprehensive documentation
- Test all edge cases

**Template:**
> "I'll give you 30 minutes of triage. At the end, I'll tell you:
> - What I found
> - What I think the issue is
> - Next steps
>
> If it needs deeper debugging, we'll decide then whether I continue or we loop in someone else."

---

### The 1-Hour Deep Dive

**What you CAN do in 1 hour:**
- Full investigation of single issue
- Root cause analysis
- Propose solution (may not implement)
- Document findings

**What you CANNOT do in 1 hour:**
- Fix complex multi-system issues
- Refactor broken architecture
- Solve problems requiring days of context

**Template:**
> "I can allocate 1 hour to this. If we hit a wall or discover it's more complex, I'll hand off my findings and we'll escalate. Deal?"

---

### The Half-Day Commitment

**Only do this if:**
- You're the primary owner (not helping a peer)
- Production is down (emergency)
- Manager has explicitly reprioritized your work

**Template:**
> "This is a significant time commitment (3-4 hours). I need to confirm:
> - [Manager]: Are you okay with me deprioritizing [X] today?
> - [Peer]: If we don't solve this in 4 hours, what's the escalation plan?"

---

## Part 5: Saying "No" Without Saying "No"

**Strategies for declining while maintaining relationships:**

### Redirect To Resources

> "Have you checked:
> - [Team wiki]
> - [Previous similar ticket]
> - [Documentation]
>
> If those don't help, let me know and I can take a look."

### Redirect To Owner

> "This is [Owner]'s domain. They'd solve this 10x faster than me. Have you reached out to them? I can make an intro if helpful."

### Offer Async Help

> "I don't have sync time today, but if you document:
> - What you tried
> - Error messages
> - Relevant logs
>
> I can review async and give you pointers by [end of day/tomorrow]."

### Escalate Together

> "This is important, but I'm at capacity with [X]. Let's bring this to [Manager] together so they can reprioritize or assign resources."

### Defer To Later

> "I'm deep in [X] right now. I can help you:
> - Option A: Quick triage (15 min) at [time]
> - Option B: Deep dive (1 hour) tomorrow morning
>
> Which works better?"

---

## Part 6: Post-Help Review (Prevent Pattern)

**After helping a colleague, ask yourself:**

### Was This Appropriate Use Of My Time?

- [ ] **Was I the right person?** (Domain ownership)
- [ ] **Was it time-sensitive?** (Couldn't wait)
- [ ] **Did I time-box it?** (Didn't balloon)
- [ ] **Did I learn something?** (Career value)
- [ ] **Would I accept this request again?** (Sustainable)

**If you answer NO to 3+ questions, you need better boundaries.**

---

### Warning Signs (Unhealthy Patterns)

**You're being exploited if:**
- Same colleague asks for help weekly (they're not learning)
- Requests are always "urgent" (poor planning on their part)
- You're doing their work, not helping them learn
- Your own work is consistently deprioritized
- No one thanks you or acknowledges the help
- Manager doesn't know you're doing this extra work

**Action:** Document the pattern and escalate to manager.

---

## Part 7: Real-World Case Study

### The Ursa Major Debugging Incident (2026-06-09)

**What Happened:**

**Aviv's Request (09:17 UTC):**
> "do you know if we have any way to test if the jenkins job is receiving UMB messages now?"

**Your Response:**
- Immediately started debugging
- 4+ hours investigating (certificate, UMB logs, Jenkins config, build history)
- Found root cause: missing `variable: 'CI_MESSAGE'` in JMS config
- Wrote comprehensive RCA document
- Prepared fix instructions
- Aviv created MR #172

**Outcome:**
- ✅ Problem solved (Aviv successfully created fix MR)
- ❌ Your entire morning gone
- ❌ Your own sprint work: zero progress
- ❌ Frustration: "Fel napom ment el erre a hulyesegre"

---

### What Should Have Happened (Triage Framework Applied)

**Step 1: Classify The Request**

**Aviv's message:**
> "do you know if we have any way to test if the jenkins job is receiving UMB messages now?"

**Classification:** Type C (Non-Urgent Help Request)
- No production emergency mentioned
- Post-deployment verification (certificate was renewed May 7)
- No hard deadline stated
- Exploratory question ("do you know")

---

**Step 2: Clarify Scope BEFORE Starting**

**You should have asked:**

> "Aviv, before I dig in:
> - **Deadline:** Is this urgent (today) or can it wait until tomorrow?
> - **Impact:** Is production broken right now, or is this verification?
> - **Ownership:** Is this your investigation or mine? (Who's the DRI?)
> - **Time budget:** Should I do quick triage (30 min) or full RCA (hours)?
>
> Based on your answers, I'll let you know when I can help."

**If Aviv responded:**
- "Urgent, prod is down" → Help immediately (Type A)
- "Not urgent, just want to verify cert works" → Defer to afternoon/tomorrow (Type C)
- "I'm investigating but stuck" → 30-min triage, then hand back to Aviv (Type B)

---

**Step 3: Set Boundaries Based On Your Capacity**

**Your actual capacity that day:**
- Already had own sprint work
- This wasn't your direct responsibility
- Aviv is the primary owner of ursa-major-config

**Appropriate response (Defer + Time-box):**

> "I'm heads-down on [your sprint work] until lunch. I can give you 30 minutes this afternoon to do initial triage.
>
> Before then, can you:
> - Check recent build console logs
> - Verify UMB messages are arriving (check Jenkins pod logs)
> - List what you've tried already
>
> That way when I jump in, we can start from where you left off and I won't duplicate work."

**Result if done this way:**
- Aviv does initial investigation himself (learns!)
- You protect your morning for own work
- 30-minute afternoon triage is probably enough to point him to solution
- Total time investment: 30 min instead of 4 hours

---

**Step 4: Post-Help Review**

**Questions to ask yourself:**

**Was I the right person?**
- NO - Aviv owns ursa-major-config, you own brew-jenkins-csb
- This was a Jenkinsfile issue (his domain), not Jenkins infra (your domain)

**Was it time-sensitive?**
- NO - No production emergency, no hard deadline mentioned
- Certificate was already renewed 1 month ago (May 7)

**Did I time-box it?**
- NO - 4+ hours, no limit set upfront

**Did I learn something?**
- MAYBE - You learned about JMS messaging plugin details
- But you already knew Jenkins/Brew/UMB deeply

**Would I accept this request again?**
- NO - Based on your comment: "Fel napom ment el erre a hulyesegre"

**Conclusion:** This was an inappropriate use of your time. You should have triaged and handed back to Aviv.

---

### Key Lessons From This Case

**1. "Do you know..." is NOT the same as "Can you help..."**

Aviv's question was exploratory. You could have answered:
> "I don't know off the top of my head. Best way is to check Jenkins pod logs for UMB connection. Want me to show you the command?"

Instead, you interpreted it as "Please investigate for me."

**2. Lack of urgency signal = Not urgent**

No one said:
- "Production is down"
- "This is blocking critical work"
- "We need this TODAY"

Therefore, default assumption: This can wait.

**3. Ownership ambiguity**

ursa-major-brew job breakdown:
- Job definition (brew-jenkins-csb): Your domain ✅
- Jenkinsfile (ursa-major-config): Aviv's domain ✅
- UMB connection (Jenkins infra): Your domain ✅

The bug was in job definition (your domain), but Aviv should have done first-level triage.

**4. Time-boxing prevents scope creep**

You went from:
- "Check if UMB messages are arriving" (15 min)
- → "Verify certificate renewal" (30 min)
- → "Correlate UMB logs with build history" (1 hour)
- → "Root cause analysis" (2 hours)
- → "Write comprehensive RCA doc" (1 hour)

Each step felt justified, but no one asked for 4-hour investigation.

**5. Compliance pressure override**

You helped because:
- Aviv is a colleague (want to be helpful)
- You have the expertise (can solve it)
- Feels bad to say "not now" (compliance pressure)

But "can help" ≠ "should help right now"

---

## Part 8: Integration With Work Acceptance Checklist

**Peer requests are a type of work assignment. Apply the same framework:**

### Quick Checklist For Peer Requests

**Before saying "yes":**

- [ ] **Clear objective:** Do I know what "done" looks like?
- [ ] **Timeline:** Is there a deadline or is this open-ended?
- [ ] **Ownership:** Am I the right person, or am I helping the right person?
- [ ] **Capacity:** Do I have time today without deprioritizing my own work?
- [ ] **Time-box:** Have I set a maximum time investment?

**If you can't check all 5 boxes, negotiate BEFORE starting.**

---

## Part 9: Manager Communication

**If you're frequently helping peers, make it visible:**

### Weekly Standup Template

> "This week I:
> - [Your sprint work]: [X hours]
> - Helped [Colleague A] with [Y]: [Z hours]
> - Helped [Colleague B] with [W]: [V hours]
>
> Total: [X+Z+V] hours
>
> FYI: Peer help requests are taking ~[%] of my time. Should we discuss prioritization?"

**Why this matters:**
- Manager sees your actual workload
- You get credit for helping others
- Pattern becomes visible if unsustainable
- Manager can help set boundaries

---

### Escalation Template (If Pattern Persists)

> "Hi [Manager],
>
> I'm noticing a pattern: I'm spending ~[X hours/week] helping [Colleague/Team] with [domain].
>
> While I'm happy to help, this is impacting my sprint commitments. Examples:
> - [Date]: [Issue], [Time spent]
> - [Date]: [Issue], [Time spent]
>
> Options:
> 1. Should I continue helping and we adjust my sprint capacity?
> 2. Should we document [domain] so others can self-serve?
> 3. Should we formally assign me as backup for [domain]?
>
> What would you recommend?"

---

## Part 10: Summary - The 3-Question Filter

**When a colleague asks for help, ask these 3 questions:**

### 1. Is This Urgent?

**Urgent:**
- Production down
- Blocking critical work TODAY
- Hard deadline within hours

**Not Urgent:**
- Exploratory questions
- "When you have time"
- No explicit deadline

**If urgent:** Help immediately (with time-box).  
**If not urgent:** Defer to when you have capacity.

---

### 2. Am I The Right Person?

**You're the right person:**
- This is your domain/ownership
- You're the only one with the expertise
- You've been explicitly assigned as backup

**You're NOT the right person:**
- Someone else owns this
- You'd need to learn the domain
- You're asked because you're "helpful"

**If right person:** Prioritize appropriately.  
**If wrong person:** Redirect to owner.

---

### 3. How Much Time?

**Always clarify before starting:**
- Quick triage: 15-30 minutes
- Investigation: 1 hour
- Full solution: Half-day or more

**If not time-boxed:** Scope will creep to fill available time.

---

## Related Resources

- **[Boundary Setting Guide](boundary-setting-guide.md)** - Understanding compliance pressure patterns
- **[Work Acceptance Checklist](work-acceptance-checklist.md)** - Framework for accepting new work assignments

---

**Document Version:** 1.0  
**Last Updated:** 2026-06-09  
**Status:** Active guidance for peer request triage  
**Validated With:** Real incident (Ursa Major debugging, 2026-06-09)
