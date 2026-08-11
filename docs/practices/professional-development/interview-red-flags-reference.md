# Interview Red Flags: Investigation Guide

**Purpose:** Detailed reference for evaluating warning signs during technical interviews  
**Usage:** Investigation framework, not rejection checklist  
**Last Updated:** 2026-06-17

---

## How to Use This Guide

Each entry follows this structure:

1. **Observation** - What you see or hear during the interview
2. **Possible Benign Explanation** - Legitimate reasons this might occur
3. **Possible Problematic Explanation** - Warning signs this might indicate
4. **Follow-Up Questions** - How to investigate further
5. **Additional Information Needed** - Context that helps evaluate
6. **Suggested Professional Response** - How to address if problematic

**Critical:** These are signals requiring clarification, not reasons to immediately reject an opportunity.

---

## Red Flag 1: "We Wear Many Hats"

### Observation
The hiring manager or job description uses this phrase to describe the role.

### Possible Benign Explanation
- Small team or early-stage startup with genuinely broad responsibilities
- Explicit cross-functional role with defined boundaries (e.g., DevOps + Security)
- Opportunity for skill development in adjacent areas
- Company culture values versatility and learning

### Possible Problematic Explanation
- Budget for one person to do three jobs
- Undefined role - company will "figure it out" after hiring
- Chronic understaffing masked as "flexibility"
- Scope creep normalized as job requirement
- Company doesn't understand what skills the role needs

### Follow-Up Questions
1. "Can you walk me through a typical week in this role? What are the different types of work, and roughly how much time goes to each?"
2. "What's the most unexpected responsibility someone in this role has handled in the past six months?"
3. "How does the team protect against scope creep when wearing multiple hats?"
4. "Are there clear boundaries for what this role does versus doesn't own?"

### Additional Information Needed
- Team size and structure
- Whether role responsibilities are documented
- How priorities are set when competing demands arise
- Examples of recent projects across the "different hats"

### Suggested Professional Response

**If benign:**
Accept that small teams require flexibility. Confirm boundaries are clear even if broad.

**If problematic:**
"Based on what we've discussed, it sounds like the scope is still being defined. I'm comfortable with broad roles, but I need clarity on core responsibilities and how priorities get set when demands conflict."

**If unresolvable:**
"I appreciate the transparency. The scope seems broader than what I'm looking for at this stage of my career."

---

## Red Flag 2: Vague or Generic Job Description

### Observation
Job posting contains:
- Generic responsibilities ("work with cross-functional teams")
- Every technology in the ecosystem listed as requirements
- No specific deliverables or success criteria
- Multiple unrelated expert-level skills (graphic design + advanced accounting + customer service)

### Possible Benign Explanation
- HR department wrote the posting without deep technical input
- Template-based job description that wasn't customized
- Comprehensive skill list meant as "nice-to-haves" not requirements
- Company is exploring what the role should be

### Possible Problematic Explanation
- Company doesn't understand what they need
- Multiple positions combined due to budget constraints
- Role expectations are genuinely unclear
- Hiring manager hasn't thought through daily work
- Position exists on paper but not in practice

### Follow-Up Questions
1. "I noticed the job description mentions [long list of technologies]. Which of these are day-to-day requirements versus nice-to-haves?"
2. "Can you describe a recent project someone in a similar role completed from start to finish?"
3. "What does success look like for this role in the first 60 days? First six months?"
4. "How was this job description created? Who provided the technical requirements?"

### Additional Information Needed
- Whether anyone currently holds this role or similar
- Recent examples of actual work performed
- Technology stack actually used daily versus occasionally
- Decision-making process that led to creating this position

### Suggested Professional Response

**If benign:**
"I understand job descriptions are often comprehensive. Let's focus on what the daily work actually looks like."

**If problematic:**
"I'm having trouble understanding what this role would focus on day-to-day. Can we schedule a follow-up conversation with someone currently doing similar work?"

**If unresolvable:**
"The role seems to combine responsibilities that typically require different specializations. I'm not confident I can deliver the expertise needed across all these areas."

---

## Red Flag 3: Toil Exceeds 50% Without Acknowledgment

### Observation
During discussion of daily work, it becomes clear the team spends most time on:
- Manual, repetitive operational tasks
- Firefighting and incident response
- Ticket processing with little engineering work
- "Keeping the lights on" activities

### Possible Benign Explanation
- Team is in temporary high-load period due to specific project or incident
- Acknowledged technical debt with concrete plan to reduce toil
- Operational load is seasonal (e.g., end-of-quarter peak)
- Recent team member departure created temporary gap

### Possible Problematic Explanation
- Operational work is systemic and sustainable state
- No engineering capacity to automate toil
- Organization doesn't distinguish between operations and engineering
- "SRE" or "Platform" role is actually traditional operations
- Team is trapped in reactive mode with no path to proactive work

### Follow-Up Questions
1. "What percentage of the team's time goes to manual, repetitive work versus long-term engineering projects?"
2. "How do you protect engineering time when operational demand increases?"
3. "Can you walk me through a recent automation project the team completed?"
4. "What's the biggest source of toil right now, and what's the plan to address it?"
5. "How is on-call load distributed? What's the average number of pages per week?"

### Additional Information Needed
- Actual toil percentage (ask team members if possible, not just manager)
- Whether team tracks toil explicitly
- Recent engineering projects completed (not just operational work)
- On-call rotation structure and page volume
- Roadmap showing engineering work, not just operations

### Suggested Professional Response

**If benign (acknowledged with plan):**
"I understand you're in a high-toil period. Can you share more details about the roadmap for reducing it? What authority would I have to drive automation?"

**If problematic (normalized without plan):**
"Based on what we've discussed, it sounds like the majority of work is operational. I'm looking for a role with more engineering focus. Is there a realistic timeline for rebalancing toward 50/50 or better?"

**If unresolvable:**
"I appreciate the honesty about the operational load. That's heavier than what I'm looking for in my next role."

---

## Red Flag 4: Missing or Chaotic Onboarding

### Observation
When asked about onboarding, the hiring manager:
- Describes informal "shadow someone" approach with no structure
- Says "we'll figure it out" or "everyone learns differently"
- Can't articulate what the first task would be
- Mentions "hitting the ground running" on day one
- Describes being thrown into production incidents immediately

### Possible Benign Explanation
- Role is genuinely senior and expects self-direction
- Small team where structured programs aren't practical
- Recent organizational change disrupted previous onboarding process
- Team has good tribal knowledge transfer even without formal process

### Possible Problematic Explanation
- No documentation exists
- Knowledge is siloed in individuals (bus factor risk)
- Organization underestimates onboarding complexity
- Team is too busy firefighting to invest in new hire success
- Previous hires failed due to poor onboarding (but lesson wasn't learned)

### Follow-Up Questions
1. "How do you ramp up new engineers? What does the first task typically look like?"
2. "How is technical context—architecture decisions, coding standards, service ownership—documented or transferred?"
3. "Who would be my primary mentor or go-to person for questions in the first few months?"
4. "What does the average ramp-up period look like before someone is fully productive?"
5. "Can you share an example of a recent new hire's onboarding experience?"

### Additional Information Needed
- Documentation quality (ask to see examples if possible)
- Recent new hire experiences (talk to them if possible)
- Whether onboarding has dedicated time budget or is "squeezed in"
- Team size (smaller teams may legitimately lack formal programs)

### Suggested Professional Response

**If benign:**
"I'm comfortable with self-directed onboarding, but I'd want to make sure there's documentation for architecture context and someone designated as a resource for questions."

**If problematic:**
"It sounds like onboarding is fairly informal. Given the complexity of the system, what support would I have in the first few months to get up to speed?"

**If unresolvable:**
"I'm concerned about the lack of structured onboarding given the system's complexity. Can we discuss what resources would be available to help me ramp up?"

---

## Red Flag 5: Zero-Barrier Alert Patterns

### Observation
When discussing on-call or operational support:
- End users can page directly without incident management tools
- No triage or severity classification process
- Alerts come through Slack DMs, email, or informal channels
- Team is "always on" rather than rotating on-call
- No distinction between urgent and non-urgent work

### Possible Benign Explanation
- Small organization where formal incident management is overkill
- High-trust environment with responsible users who don't abuse access
- Temporary state during incident management tool migration
- Team size is small enough that informal coordination works

### Possible Problematic Explanation
- Team is treated as dumping ground for any infrastructure question
- No protection from low-priority noise during on-call
- Users bypass proper channels because they know direct contact gets faster response
- Organizational culture doesn't respect engineering focus time
- Management doesn't understand cost of constant interruptions

### Follow-Up Questions
1. "How are infrastructure tasks prioritized? Is there a defined backlog managed by a Product Owner?"
2. "What percentage of on-call pages require immediate action versus things that could wait until business hours?"
3. "How does the team protect focus time for engineering work when operational requests come in?"
4. "What's the process for someone requesting infrastructure support? Walk me through a typical request."
5. "How is severity classified? Who determines what's an actual emergency?"

### Additional Information Needed
- Whether incident management tools are used
- On-call page volume and breakdown by severity
- Examples of recent pages (what were they, how urgent were they really)
- Team's ability to push back on low-priority requests
- Management support for establishing boundaries

### Suggested Professional Response

**If benign:**
"I understand direct communication works for your team size. As you grow, have you thought about how to scale this without creating alert fatigue?"

**If problematic:**
"It sounds like the team gets a high volume of requests through informal channels. How much autonomy would I have to establish triage processes and protect engineering focus time?"

**If unresolvable:**
"Based on what we've discussed, it sounds like the alert volume and lack of filtering would make it difficult to do sustained engineering work. That's not the environment I'm looking for."

---

## Red Flag 6: "Fast-Paced Environment"

### Observation
Job description or hiring manager emphasizes "fast-paced," "dynamic," "rapidly changing."

### Possible Benign Explanation
- Company ships frequently and iterates based on feedback
- Competitive market requires quick responses
- Startup or growth-stage energy is genuinely exciting
- Team values velocity and continuous improvement

### Possible Problematic Explanation
- Chronic urgency due to poor planning
- Unrealistic deadlines are normalized
- No time for technical debt reduction
- "Move fast" is used to justify cutting corners
- Burnout is expected and normalized

### Follow-Up Questions
1. "When you say fast-paced, what does that look like day-to-day?"
2. "How does the team balance moving quickly with sustainable workload?"
3. "What's a recent example of something that moved faster than expected? What enabled that?"
4. "How much time does the team allocate to technical debt versus new features?"
5. "What does work-life balance look like on the team?"

### Additional Information Needed
- Actual deployment frequency and change success rate
- Incident rate (frequent incidents suggest "fast" means "reckless")
- Team tenure (high turnover suggests unsustainable pace)
- Whether "fast-paced" means "effective" or "chaotic"

### Suggested Professional Response

**If benign:**
"I appreciate environments that ship quickly. How do you maintain quality and avoid burning out the team?"

**If problematic:**
"It sounds like there's a lot of urgency. Can you help me understand whether that's driven by business opportunity or operational challenges?"

**If unresolvable:**
"I'm looking for a team that values sustainable pace over constant urgency. It sounds like there might be a mismatch in expectations."

---

## Red Flag 7: Defensive Response to Direct Questions

### Observation
When you ask about organizational challenges, frustrations, or weaknesses:
- Hiring manager deflects or says "nothing really"
- Answers become vague or evasive
- They react defensively or suggest you're being negative
- They pivot to talking only about positives

### Possible Benign Explanation
- Hiring manager is inexperienced at interviewing
- They're being careful about legal liability (some HR policies discourage negativity)
- Company culture genuinely is very positive (rare but possible)
- They're testing whether you handle pushback professionally

### Possible Problematic Explanation
- Lack of psychological safety - people can't admit problems
- Hiring manager is disconnected from team reality
- Organization doesn't acknowledge or address issues
- Culture of toxic positivity where complaints aren't allowed
- Serious problems exist that they're hiding

### Follow-Up Questions
1. "I appreciate the positive perspective. In the spirit of understanding fit, can you share what challenges the team is actively working to improve?"
2. "Every team has areas they're working on. What would you change if you could wave a magic wand?"
3. "What's been the team's biggest learning experience in the past year?"
4. "Can I speak with someone currently on the team to hear their perspective?"

### Additional Information Needed
- Whether you can talk to current team members (if not, that's telling)
- Glassdoor or Blind reviews about the organization
- Your instinct about whether evasiveness is policy or hiding problems

### Suggested Professional Response

**If benign:**
"I understand you want to present the team positively. I'm looking for honest assessment of both strengths and areas for improvement to understand fit."

**If problematic:**
"I'm having trouble getting a clear picture of the challenges the team faces. Would it be possible to speak with a current team member to hear their perspective?"

**If unresolvable:**
"I appreciate your time, but I'm not getting the level of candor I need to evaluate this opportunity properly."

---

## Red Flag 8: Title Inflation Without Scope

### Observation
The job title is "Senior," "Lead," or "Principal," but:
- Responsibilities described are mid-level
- No mention of leadership, mentorship, or architecture ownership
- Scope is narrow individual contributor work
- Compensation doesn't match title level

### Possible Benign Explanation
- Company uses inflated titles to stay competitive in hiring market
- Title reflects deep expertise in narrow area
- Organization has non-standard leveling system
- Role will grow into title over time

### Possible Problematic Explanation
- Title is used instead of competitive compensation
- Company doesn't understand what senior roles entail
- Role is actually mid-level but titled senior to attract candidates
- If you leave, similar titles elsewhere will require qualifications you won't gain here

### Follow-Up Questions
1. "Can you describe what 'Senior' means at this organization? What distinguishes it from mid-level?"
2. "What leadership or mentorship responsibilities come with this role?"
3. "What architectural or strategic decisions would I own?"
4. "How does the leveling framework work here?"
5. "What would my scope of impact be—team level, department level, organization level?"

### Additional Information Needed
- Compensation range (does it match market for the actual scope?)
- Comparison to mid-level roles at the company
- Whether progression exists beyond this level
- Examples of recent senior-level work (not just task execution)

### Suggested Professional Response

**If benign:**
"I understand titles are used differently here. Can we focus on the actual scope and impact of the role to make sure we're aligned?"

**If problematic:**
"Based on what we've discussed, the scope sounds more mid-level than senior. Can you help me understand what senior responsibilities look like in practice?"

**If unresolvable:**
"I'm looking for a role with senior-level scope—architectural ownership, technical leadership, strategic impact. It sounds like this position may be focused more narrowly than that."

---

## Red Flag 9: Consolidated Team / "Dumping Ground" Pattern

### Observation
When discussing team structure:
- Multiple specialized teams were merged into one
- Promised "cross-skilling" and "knowledge sharing"
- Team now supports many disparate products or services
- Scope expanded significantly without corresponding team growth

### Possible Benign Explanation
- Organizational restructuring for legitimate business reasons
- Consolidation came with proper documentation and knowledge transfer
- Team has clear boundaries and can push back on out-of-scope work
- Consolidation is recent and leadership is actively managing the transition

### Possible Problematic Explanation
- Cost-cutting masked as "efficiency"
- Team became dumping ground for unwanted work
- No one understands the full scope anymore (cognitive overload)
- Documentation doesn't exist across the merged domains
- Original specialized knowledge is being lost

### Follow-Up Questions
1. "What prompted the team consolidation?"
2. "How was knowledge transferred when the teams merged?"
3. "What boundaries exist for what the team does versus doesn't support?"
4. "How do you manage cognitive load when the scope is so broad?"
5. "What happens when a request comes in that's outside the team's core expertise?"

### Additional Information Needed
- Timeline of consolidation (recent vs. stable state)
- Whether team size increased with scope increase
- Documentation quality across the merged domains
- Team members' perspectives (do they feel overwhelmed?)

### Suggested Professional Response

**If benign:**
"I understand consolidation happened. Can you walk me through how the team maintained quality and prevented scope creep during the transition?"

**If problematic:**
"It sounds like the team's scope expanded significantly without corresponding support. How much autonomy would I have to establish boundaries and push back on out-of-scope requests?"

**If unresolvable:**
"Based on what we've discussed, the cognitive load from supporting so many disparate areas concerns me. That doesn't sound like an environment where I could be effective."

---

## Red Flag 10: Future Promises Instead of Current Solutions

### Observation
When you raise concerns about current problems:
- Hiring manager responds with future technology promises
- "We're planning to..." or "We'll eventually..." without concrete timelines
- Buzzword solutions: "Agentic AI will handle alerts," "LLMs will write runbooks"
- Presentation-layer fixes for architectural problems

### Possible Benign Explanation
- Organization has realistic roadmap and is honest about current state
- Future vision is exciting and you'd help build it
- Short-term pain is acknowledged with credible mitigation plan
- Leadership understands root causes and is investing properly

### Possible Problematic Explanation
- Future promises are used to avoid fixing current problems
- Organization doesn't understand root causes
- Technology hype is masking systemic dysfunction
- No actual timeline or ownership for promised improvements
- You'd be stuck with current problems indefinitely

### Follow-Up Questions
1. "That future direction sounds interesting. What's the plan for managing the current situation while that's being developed?"
2. "Who owns the roadmap for that initiative? What's the timeline?"
3. "Are there architectural changes in progress to address the underlying issues?"
4. "What would my role be in implementing those improvements versus managing the current state?"
5. "If that future solution doesn't materialize in the expected timeline, what's the backup plan?"

### Additional Information Needed
- Actual roadmap with dates and ownership
- Budget and resources allocated to promised improvements
- Track record of past promises (did they deliver?)
- Whether "future solution" addresses root cause or just symptoms

### Suggested Professional Response

**If benign:**
"I'm excited about the future vision. Can we discuss what percentage of my time would go toward building that versus managing the current operational reality?"

**If problematic:**
"I appreciate the vision, but I'm concerned about the current operational load. Without concrete timelines and ownership, I need to evaluate the role based on current state, not future promises."

**If unresolvable:**
"It sounds like the improvements are aspirational rather than actively in progress. I need a role where the foundational problems are being addressed now, not deferred to future initiatives."

---

## Red Flag 11: No Clear Success Criteria

### Observation
When asked "What does success look like?" the hiring manager:
- Can't articulate concrete milestones
- Describes vague aspirations ("make things better")
- Says "we'll figure it out together"
- Provides contradictory success criteria

### Possible Benign Explanation
- Role is genuinely exploratory and requires co-creating clarity
- Organization values autonomy and self-direction
- Success criteria will be defined collaboratively after hire
- Startup environment with appropriate ambiguity

### Possible Problematic Explanation
- Organization doesn't know what they need
- Success is undefined so expectations will shift constantly
- You'll be blamed for not delivering against unclear goals
- Manager is inexperienced or ineffective
- Position exists to "solve problems" without understanding what problems

### Follow-Up Questions
1. "Let's try to make this concrete. In an ideal world, what would be different six months after I join?"
2. "What are the top three priorities for this role in the first quarter?"
3. "How will my performance be evaluated? What metrics or outcomes matter?"
4. "Can you share an example of a recent success from someone in a similar role?"
5. "Who decides what this role should focus on when competing priorities arise?"

### Additional Information Needed
- Whether role is genuinely exploratory or just undefined
- How priorities are set and communicated
- Examples of how similar roles have been evaluated
- Your comfort with ambiguity (some people thrive here)

### Suggested Professional Response

**If benign:**
"I'm comfortable with ambiguity, but I need to understand the boundaries. What authority would I have to define success criteria, and how often would we revisit them?"

**If problematic:**
"I'm having trouble understanding what you're optimizing for. Can we schedule a follow-up to define concrete goals for the first six months before moving forward?"

**If unresolvable:**
"Without clear success criteria, I'm concerned about being set up to fail. I need more definition before I can commit to this role."

---

## Summary: Using This Guide

**Key Principles:**

1. **Investigate, don't reject.** Red flags are signals, not disqualifications.

2. **Context matters.** The same observation can be benign or problematic depending on organizational stage, size, and acknowledgment.

3. **Look for honesty, not perfection.** Every organization has problems. The question is whether they acknowledge them.

4. **Ask follow-up questions.** Your questions demonstrate seniority and help both parties understand fit.

5. **Trust your instinct.** If answers are evasive, defensive, or don't add up, that's telling.

6. **Walk away professionally.** When red flags confirm problems, decline respectfully.

**When Multiple Red Flags Appear:**

A single red flag warrants investigation. Multiple red flags in the same interview suggest a pattern:

- 2-3 red flags: Investigate thoroughly, talk to current team members if possible
- 4+ red flags: Consider whether this is the right opportunity
- Defensiveness when investigating: Likely not a good fit

**Remember:**

The goal isn't to find a perfect organization. It's to find one where:
- Expectations are clear
- Problems are acknowledged
- You have agency to address issues
- Leadership supports sustainable work

The interview is your opportunity to assess this. Use it.

---

**Document Version:** 1.0  
**Last Updated:** 2026-06-17  
**Purpose:** Detailed investigation framework for common interview red flags  
**Related Documents:** [When_Job_Titles_and_Expectations_Do_Not_Match.md](When_Job_Titles_and_Expectations_Do_Not_Match.md)
