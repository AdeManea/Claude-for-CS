---
name: onboarding-blocker-review
description: >
  Diagnose and resolve onboarding blockers — anything preventing a customer from
  reaching their next milestone on time. Classifies blockers by type and severity,
  routes to the correct resolution path per your escalation matrix, and produces
  a clear action plan with named owners and deadlines. Reads escalation thresholds
  and escalation contacts from your onboarding profile. Use --diagnose (default) to
  work through a current blocker with a guided diagnostic, --escalate to produce a
  formatted escalation brief for a specific contact, or --log to record a resolved
  blocker for the account history.

---

## Company Context

**AutogenAI** sells an AI-powered proposal and bid writing platform to enterprise teams. Primary value metric: win rate improvement and proposal throughput.

**CSM role:** Enterprise CSM. Enterprise accounts receive white-glove onboarding with a team of CSM + Onboarding PM + Implementation Consultant (IC) + Bid Consultant. Mid-Market and SME are IC-led with handoff back to CSM.

**Milestone framework (Enterprise):**
- M1: Kickoff + Discovery complete (Month 1) — stakeholders confirmed, Value Alignment Session done, Triple Metric agreed
- M2: Configuration complete (Month 1) — platform live, document library set up, Academy access granted
- M3: Adoption underway (Months 2–4) — training delivered, drop-in sessions running, monthly value reports started
- M4: First Business Review (Month 3–4) — progress reviewed, joint roadmap co-created
- M5: Handoff ready (Month 5) — Onboarding PM exits, CSM takes full ownership, IC remains

**Escalation matrix (from config):**

| Situation | Escalate to | Method |
|-----------|-------------|--------|
| M1 missed | Manager of CS + Head of Professional Services | Slack |
| Technical blocker >5 days | IC (may escalate to Head of PS or Build team) | IC-led |
| Executive sponsor unresponsive | CCO | Slack + email |
| SLA breach risk | CCO | TBD |
| Customer wants to cancel | CSM → Manager of CS + Legal | TBD |

⚠️ Escalation matrix SLAs and named contacts are [PLACEHOLDER]. If unconfigured, describe the resolution path without naming specific contacts and prompt the CSM to complete the matrix.

**Key tools:** HubSpot (CRM), Planhat (CS platform), SharePoint (docs), Slack + Outlook (comms).

**Top churn signals:** Low platform adoption, low bid volume through tool, champion departure, competitive displacement.

**Triple Metric (success framework):** Corporate level (win rate, revenue, ROS/ROA), Business unit level (technical score, operating margins, revenue per transaction), Project level (time saved, user adoption, headcount efficiency).

---

## Skill Instructions

<!-- Status: [PROPOSED] -->

# /onboarding:blocker-review

Blocker diagnosis, escalation, and resolution tracking.

---

## Pre-flight

Before running any blocker review, note the following from the embedded Company Context above:
- Escalation matrix (who gets notified at each severity tier; named escalation contacts)
- Onboarding model (Enterprise = white-glove; escalations may reach executive sponsor; Mid-Market/SME = IC-led)
- Milestone framework (at-risk signals per milestone — used to classify the blocker's milestone impact)
- CS methodology: None formal

If escalation matrix contacts are `[PLACEHOLDER]`:
> "Escalation contacts aren't configured. Run `/onboarding:cold-start-interview --section escalation` to define your escalation matrix before routing blockers. I'll describe the resolution path without naming specific contacts."

Proceed with a generic escalation path description if the user confirms.

**G-code dependency:** The following guardrails apply throughout this skill:
- **G4:** Escalation recommendations must route through the configured escalation matrix with a named owner, channel, and SLA — no generic "escalate to your manager." If the matrix is `[PLACEHOLDER]`, flag before proceeding.
- **G5:** Confidentiality check required before any output containing account details leaves the CSM's view — especially escalation briefs sent to external parties.
- **G7:** Flag stale CRM or usage data with source date and staleness indicator — never present undated data as current. (CRM >7 days, CS platform >3 days, call data >14 days.)

---

## Trigger Precision

**Use when:**
- Diagnosing an active blocker preventing a customer from reaching their next milestone on time
- Producing a formatted escalation brief for a specific escalation contact (AE, manager, exec sponsor, implementation engineer, partner, support)
- Logging a resolved blocker to the account history for pattern recognition

**Do NOT use for:**
- General onboarding status checks (use `/onboarding:milestone-tracker`)
- Proactive risk flagging before a blocker is confirmed (use `/onboarding:milestone-tracker --flag`)
- Reviewing overall onboarding health across a portfolio

## Typical Activation
- "We have a blocker on [Account] — the integration isn't working"
- "I need to escalate this to the AE — generate me a brief"
- "That blocker is resolved — log it"
- CSM runs `/onboarding:blocker-review [account] --diagnose` to work through an active blocker
- CSM runs `/onboarding:blocker-review [account] --escalate` to produce a formatted escalation brief
- CSM runs `/onboarding:blocker-review [account] --log` to record a resolved blocker

## Reasoning Protocol

Before generating output, apply these primers:

1. **CLASSIFY**: What type of blocker review request is this?
   - **Active Technical Impediment**: Something is broken or blocked in the product/environment — integration failure, provisioning gap, data quality, or product bug. Customer wants to proceed but cannot.
   - **Customer Engagement Stall**: Customer has stopped progressing — low activity, missed meetings, champion unavailable, internal reprioritization. No technical impediment exists.
   - **CSM Execution Gap**: Blocker traces back to something the CSM or vendor team failed to deliver — unclear instructions, missing resources, dropped follow-through.
   - **Vendor/Product Constraint**: Bug, feature gap, or support queue delay outside the CSM's direct control. Resolution depends on internal product or engineering teams.
   - **Multi-Party Coordination Failure**: Partner-led or multi-stakeholder blocker where ownership is ambiguous. Multiple parties each believe another owns the next step.

2. **CONSTRAINTS**: What limits the solution space?
   - G4: Escalation recommendations must route through the configured escalation matrix with a named owner, channel, and SLA — no generic "escalate to your manager." If the matrix is `[PLACEHOLDER]`, flag before proceeding.
   - G5: Confidentiality check required before any output containing account details leaves the CSM's view — especially escalation briefs sent to external parties.
   - G7: Flag stale CRM or usage data with source date and staleness indicator — never present undated data as current.
   - Mode constraint: Match output depth to the actual need — `--diagnose` for uncharacterized problems, `--escalate` only after diagnosis, `--log` only after resolution.
   - Partner-led model constraint: In partner-led accounts, blockers route through the partner before reaching the customer directly.

3. **EXPERT CHECK**: What would a veteran onboarding CSM verify first?
   - Is this actually a blocker, or is the customer simply not engaged? Ask whether the customer has attempted the blocked step before classifying it as technical.
   - Does the milestone math support the stated severity? Calculate days remaining minus estimated resolution time — trust the math over the CSM's emotional urgency.
   - Has the CSM already tried the obvious resolution paths? Never recommend actions already attempted — ask what's been tried before generating the action plan.

4. **ANTI-PATTERNS**: Common blocker review mistakes to avoid:
   - Accepting the first symptom description as the root cause — the presenting symptom is almost never the actual blocker. Run the full diagnostic sequence before classifying.
   - Classifying an engagement problem as a technical blocker because the CSM framed it that way — probe for actual customer activity before accepting the label.
   - Generating an escalation brief with a vague ask ("please help with this account") instead of a specific action, owner, and deadline.
   - Recommending outreach the CSM already attempted — repeating failed actions destroys credibility with the customer.
   - Routing directly to the customer in a partner-led model without confirming partner awareness first.
   - Logging a vendor blocker with a ticket number but no escalation trigger date — passive waiting is not a resolution path.

**After execution**, verify:
- Does the action plan address the root cause, not just the presenting symptom?
- Is every escalation path routed through the configured matrix with named contacts (or flagged if unconfigured)?
- Does the severity rating match the milestone impact math, not the CSM's emotional framing?
- Are all data sources timestamped and staleness-flagged per G7? (CRM >7 days, CS platform >3 days, call data >14 days.)
- Does the output mode (`--diagnose` / `--escalate` / `--log`) match the actual need — not a mode the CSM defaulted to?
- Confidence: [High] if CRM data + CSM confirmation corroborate the classification / [Medium] if single-source or partially stale data / [Low] if working from CSM description alone — state which.

## Mode

`--diagnose` (default): Guided diagnostic session. Asks structured questions to
classify the blocker, assess its milestone impact, and produce a ranked action plan
with owners and deadlines. Use when the CSM knows something is wrong but hasn't
fully characterized the problem.

`--escalate`: Produce a formatted escalation brief for a specific escalation contact
(AE, manager, exec sponsor, implementation engineer, or support). The brief includes
account context, blocker description, milestone impact, actions already taken, and
the specific ask. Use when the CSM has already diagnosed the blocker and needs to
involve another party.

`--log`: Record a resolved blocker for the account history. Captures blocker type,
root cause, resolution action, days lost (if any), and preventability assessment.
Use after a blocker is resolved to build institutional knowledge for future accounts
in the same segment or model.

---

## Account identification

Ask: "Which account is experiencing the blocker?"

If CRM connector available, pull: account name, segment, current milestone, contract
start date, CSM/AE names, and any prior escalation history on this account.

Confirm: "[CRM]: [account name] · [segment] · currently in M[#] · CSM: [name]
· data as of [timestamp]"

If no CRM: "Tell me the account name and which milestone you're currently trying to
complete — I'll work from there."

---

## Blocker classification

Every blocker is classified on two axes: **type** and **severity**.

### Type

**Customer-side blockers:**
- `adoption` — Customer team is not using the product as expected; low login activity,
  skipped training, low engagement
- `bandwidth` — Customer team lacks time or headcount to complete required actions
- `champion-absent` — The primary champion is unavailable (travel, leave, departure)
- `priority-shift` — Internal customer initiative deprioritized onboarding; competing
  internal project
- `stakeholder-misalignment` — Disagreement within the customer team about goals,
  scope, or ownership

**Technical blockers:**
- `integration` — Required integration is incomplete, broken, or waiting on IT approval
- `data-quality` — Customer data needed for product setup is missing, dirty, or
  inaccessible
- `provisioning` — User accounts, permissions, or SSO not configured
- `environment` — Firewall, VPN, or security policy blocking product access

**CSM-side blockers:**
- `unclear-ask` — Customer is uncertain what to do next; the CSM's instructions were
  unclear or misunderstood
- `missing-resource` — Required documentation, training, or configuration guide not
  provided
- `follow-through` — CSM committed to an action that hasn't been completed

**Vendor/product blockers:**
- `bug` — Product defect preventing a required workflow
- `feature-gap` — Customer needs a capability not yet in the product
- `support-queue` — Open support ticket blocking progress; resolution not yet received

**Partner blockers** (partner-led model only):
- `partner-bandwidth` — Partner is delayed on their implementation deliverables
- `partner-alignment` — Partner and CSM are misaligned on scope, timeline, or ownership

### Severity

| Severity | Definition | Example |
|----------|-----------|---------|
| P1 — Critical | Current milestone will be missed without immediate action | Integration broken 2 days before M2 |
| P2 — High | Current milestone is at risk if not resolved within 3 days | Champion absent for a week; no backup named |
| P3 — Medium | Next milestone is at risk; current milestone is still achievable | Data quality issue slowing M3 setup |
| P4 — Low | Non-blocking but needs tracking | Minor training gap; user onboarding delayed by 1 day |

---

## `--diagnose` mode: Diagnostic sequence

### Step 1: Describe the symptom

Ask: "What specifically is not happening that should be? What did you expect to
see by now that you haven't seen?"

Record the symptom verbatim. This anchors the diagnosis to observable behavior,
not interpretation.

### Step 2: Identify the milestone impact

Ask: "Which milestone does this affect — and by how much? Will this cause the
milestone to be missed, or is it a risk that could still be managed?"

Calculate: if the current milestone target date is [date] and the blocker is
estimated to take [N] days to resolve, the milestone is [on track / at risk / will
be missed]. Show the math transparently.

### Step 3: Classify the blocker

Use the type taxonomy above. Ask probing questions to narrow the classification:

- "Is the customer actively trying to complete this step but unable to?" → Technical
  or provisioning blocker
- "Has the customer engaged at all in the last 7 days?" → Adoption, bandwidth, or
  champion-absent
- "Did the customer agree on what needed to happen next?" → May be unclear-ask
  or stakeholder-misalignment
- "Is there an open support ticket?" → Support-queue blocker
- "Did we commit to something and not deliver?" → CSM-side follow-through

Present the classification:
> "Based on your description, this looks like a [type] blocker at [severity] — is
> that consistent with what you're seeing?"

Adjust based on CSM input.

### Step 4: Determine prior actions taken

Ask: "What have you already tried? Have you reached out to the champion, the AE,
or opened a support ticket?"

Record prior actions. Do not recommend actions already taken.

### Step 5: Action plan

Generate a ranked action plan. Each action includes:
- **Action:** [specific step]
- **Owner:** [CSM / customer champion / AE / support / implementation engineer]
- **Deadline:** [date — typically 24h for P1, 48–72h for P2, this week for P3]
- **Escalation trigger:** [if this action doesn't resolve the blocker by [date],
  escalate to [contact from config]]

Maximum 3 ranked actions. The first action should be the fastest path to unblocking
the milestone. Do not list every possible action — list the right ones in priority order.

If the action plan requires escalation, offer to run `--escalate` immediately:
> "This P1 blocker likely needs [AE / exec sponsor / implementation engineer] involvement.
> Run `/onboarding:blocker-review --escalate` to generate the escalation brief now."

---

## `--escalate` mode

Ask: "Who is this escalation going to?" Options from config escalation matrix:
- AE (account executive)
- Manager or team lead
- Implementation engineer
- Executive sponsor (white-glove model)
- Partner contact (partner-led model)
- Support / technical escalation

Generate the escalation brief:

```
**Escalation Brief — [Account Name]**
*To: [escalation contact name and role]*
*From: [CSM name]*
*Date: [today] · Severity: [P1/P2/P3]*

**Account context:**
[Account name] · [Segment] · Contract start: [date]
Current milestone: M[#] ([label]) · Target: [date] · [N] days [remaining / overdue]

**Blocker:**
[Blocker type: [classification]] — [1–2 sentence description of what is not happening
and what the symptom looks like]

**Milestone impact:**
[If unresolved by [date], M[#] will be missed. Current delay estimate: [N] days.]

**Actions already taken:**
- [Action 1] — [date taken] — [outcome]
- [Action 2] — [date taken] — [outcome]

**Your ask:**
[Specific, scoped request — not "help me fix this." E.g., "Reach out to [exec] to
confirm this is a priority," or "Unblock the support ticket [#] by EOD [date]," or
"Join the call on [date] to reset the integration scope."]

**If not resolved by [date]:**
[Next escalation step from config]
```

---

## `--log` mode

Ask: "What was the blocker, how was it resolved, and how many days did it cost?"

Produce a structured log entry for the account record:

```
Blocker Log — [Account Name]
Date opened: [date] · Date resolved: [date] · Days lost: [N]

Type: [classification]
Severity: [P1/P2/P3/P4]
Milestone affected: M[#]
Root cause: [1–2 sentences]
Resolution action: [what actually fixed it]
Owner of resolution: [CSM / AE / customer / partner / support]
Preventable: [Yes — how | No — why not]
Recommendation for future accounts in this segment/model: [brief]
```

If a PM connector is configured (Planhat), offer to add this log entry to the account's
project as a closed task.

---

## Reviewer note (internal — `--diagnose` only)

> ⚠️ Reviewer note
> - **Sources:** [CRM ✓ | manual input]
> - **Data as of:** [timestamp]
> - **Config fields read:** escalation matrix ([contacts]), milestone at-risk signals
>   ([M# signals relevant to this blocker])
> - **Blocker classification:** [type] at [severity] — confidence: [High / Moderate
>   based on symptom description]
> - **Milestone impact assessment:** [on track / at risk / will be missed — based on
>   [N] days to target and [N] days estimated to resolve]
> - **Prior actions recorded:** [list or "none provided"]
> - **Escalation recommendation:** [escalate now / monitor / no escalation needed]
> - **Flagged for your judgment:** [any ambiguous classification or conflicting signals]

---

## Output

Blocker review output — format driven by flag (`--diagnose`, `--escalate`, `--log`).
Diagnose mode: structured diagnostic report with blocker inventory, severity tiers,
root cause analysis, and recommended actions. Escalate mode: escalation brief.
Log mode: CRM-ready note. See mode-specific sections for field-level structure.

> [review before sending]

---

## Security & Permissions

This skill operates read-only against configuration files and connected MCP data sources.
No filesystem writes, no subprocess execution, no dynamic code execution.
All data access is through explicitly connected MCP connectors; no outbound network calls are made directly.

## Trust & Verification

All output is labeled with data source and timestamp per reviewer note format.
Escalation briefs contain account ARR and contract context — confirm receiving party is authorized before sharing.
Blocker severity ratings are calculated from milestone math, not CSM emotional framing — skill presents the calculation transparently.
CSM judgment is required for escalation decisions and action plan execution — skill presents options, never directives.

## Guardrails

**Classify before acting.** A blocker misclassified as technical when it is actually
adoption-related will produce the wrong action plan. Run the full diagnostic sequence
before generating actions — do not skip to solutions based on the first symptom
described.

**Severity drives urgency, not the CSM's emotional state.** A CSM who says "this is
urgent" on a P3 blocker is reacting, not assessing. Use the milestone impact calculation
to determine severity — not the description of how the CSM feels about it.

**`--escalate` requires a specific ask.** An escalation brief without a clear,
scoped request creates confusion and delays resolution. Do not produce an escalation
that says "please help" — name exactly what action the escalation contact must take
and by when.

**Don't recommend actions already taken.** Before generating the action plan, ask
what's already been tried. Recommending the same outreach that failed last week wastes
the CSM's credibility with the customer.

**Partner-led blockers route through the partner first.** For accounts on the
partner-led model, a blocker that appears customer-side may actually be in the
partner's lane. Ask: "Is the partner aware of this? Has the partner reached out to
the customer about it?" before routing directly to the customer.

**`--log` builds institutional knowledge.** Blocker logs are not just housekeeping —
they are the data source for pattern recognition across accounts. Log every significant
blocker (P1 and P2 at minimum) after resolution. Unlogged blockers cannot inform
future proactive interventions.

**Escalation thresholds are minimums — not maximums.** The config escalation thresholds
define when escalation is required. A CSM may judge that an earlier escalation is
warranted given account context (strategic relationship, exec attention, partner
complexity). The config threshold is the floor; the CSM's judgment sets the ceiling.

---

## Reference Material

### Reasoning Blueprint: Onboarding Blocker Review

Load this blueprint when Tier 3 reasoning is activated for blocker diagnosis,
escalation, and resolution tracking. It provides the domain-specific taxonomy,
heuristics, and expert judgment patterns that shape expert-level blocker triage.

---

#### Problem Classification Taxonomy

**Type A: Active Technical Impediment**
Characteristics: Something is broken or blocked in the product/environment — integration failure, provisioning gap, data quality issue, or product bug. The customer wants to proceed but cannot.
Primary Risk: Misclassifying an adoption problem as technical — the CSM chases engineering when the real issue is engagement.
Expert Focus: Verify the customer has actually attempted the blocked step. No attempt + "technical blocker" = likely adoption or bandwidth issue.

**Type B: Customer Engagement Stall**
Characteristics: The customer has stopped progressing — low activity, missed meetings, champion unavailable, or internal reprioritization. No technical impediment exists.
Primary Risk: Treating silence as low priority when it signals stakeholder misalignment or champion loss.
Expert Focus: Distinguish temporary bandwidth constraints (recoverable with a nudge) from structural disengagement (requires escalation or champion replacement).

**Type C: CSM Execution Gap**
Characteristics: The blocker traces back to something the CSM or vendor team failed to deliver — unclear instructions, missing resources, dropped follow-through.
Primary Risk: Defensive misclassification — labeling a CSM-side gap as customer-side to avoid accountability.
Expert Focus: Ask "did we deliver everything we committed to?" before classifying customer-side.

**Type D: Vendor/Product Constraint**
Characteristics: A bug, feature gap, or support queue delay outside the CSM's direct control. Resolution depends on internal product or engineering teams.
Primary Risk: Using "waiting on product" as a holding pattern without an escalation deadline or workaround plan.
Expert Focus: Every vendor blocker needs a workaround assessment and an escalation trigger date — not just a ticket number.

**Type E: Multi-Party Coordination Failure**
Characteristics: Partner-led or multi-stakeholder blocker where ownership is ambiguous. The partner, customer, and CSM each believe another party owns the next step.
Primary Risk: Routing directly to the customer when the partner-led model requires partner-first engagement.
Expert Focus: Map the ownership chain before acting. In partner-led models, always confirm partner awareness first.

---

#### Domain Heuristics

1. **The Symptom-vs-Cause Rule**: The first blocker the CSM describes is almost always a symptom. Ask "what is preventing the customer from completing the next milestone step?" to surface the root cause. One probing question saves a misclassified action plan.

2. **The 7-Day Silence Rule**: If a customer has had zero engagement (no logins, no replies, no meeting attendance) for 7+ days during active onboarding, the blocker is engagement-related until proven otherwise — regardless of what the CSM labels it.

3. **The Milestone Math Rule**: Severity is determined by math, not feeling. Days remaining to milestone target minus estimated resolution time equals the risk window. Negative = P1. Under 3 days = P2. Everything else is P3/P4.

4. **The Already-Tried Filter**: Never recommend an action the CSM has already attempted. Repeating failed outreach destroys credibility. Always ask what's been tried before generating the action plan.

5. **The Partner-First Gate**: In partner-led models, every blocker routes through the partner before reaching the customer. Skipping this step undermines the partner relationship and creates confusion about ownership.

6. **The Escalation Specificity Rule**: An escalation without a specific ask, a named owner, and a deadline is not an escalation — it's a complaint. Every escalation brief must answer: who does what by when.

7. **The Single-Thread Danger Rule**: If the blocker resolution depends on exactly one person (customer champion, partner contact, support engineer), flag it as fragile. Identify a backup contact or parallel path before committing the action plan.

---

#### Common Failure Modes by Blocker Type

**Active Technical Impediment Failures**
- Phantom technical blocker: CSM reports "integration is broken" but customer hasn't attempted the integration step yet. Fix: Ask "has the customer attempted this step?" before classifying as technical.
- Missing reproduction context: Escalation to engineering without specific error details or environment context. Fix: Collect error messages, timestamps, and environment details before generating the escalation brief.

**Customer Engagement Stall Failures**
- Bandwidth excuse accepted at face value: CSM accepts "we're too busy" without probing for underlying disengagement. Fix: Ask about competing priorities, stakeholder changes, and whether the customer's internal sponsor still supports the initiative.
- Single-channel follow-up: CSM sends repeated emails to an unresponsive champion without trying alternative contacts or channels. Fix: After 2 unanswered outreach attempts, escalate to a different stakeholder or engage the AE for a warm introduction.

**CSM Execution Gap Failures**
- Defensive misclassification: Blocker is labeled customer-side when the root cause is a missed CSM deliverable. Fix: The diagnostic sequence explicitly asks "did we deliver everything we committed to?" — do not skip this step.

**Vendor/Product Constraint Failures**
- Passive waiting: CSM logs a support ticket and waits indefinitely without setting an escalation deadline. Fix: Every vendor blocker gets a "if not resolved by [date], escalate to [contact]" trigger in the action plan.

**Multi-Party Coordination Failures**
- Partner bypass: CSM routes directly to the customer in a partner-led model, creating ownership confusion. Fix: Apply the Partner-First Gate heuristic. Confirm partner awareness before any direct customer action.

---

#### Expert Judgment Patterns

**Severity Calibration Decisions**
- When the CSM's emotional urgency exceeds the milestone math, trust the math — present the calculated severity and let the CSM override with stated reasoning.
- When multiple blockers exist simultaneously, severity is driven by the highest-impact single blocker, not aggregated — but note compound risk in the reviewer note.

**Scope Decisions**
- If diagnosis reveals the blocker is actually two distinct blockers (e.g., technical + engagement), split into separate entries with independent action plans rather than conflating into one.
- If the blocker affects multiple milestones, anchor the action plan to the nearest milestone at risk — don't try to solve everything at once.

**Escalation Timing Decisions**
- Escalate early on P1 blockers even if the CSM wants to "try one more thing" — the cost of late escalation on a critical blocker exceeds the cost of a premature one.
- For P2 blockers, the CSM's judgment on timing is respected — the config threshold is the floor, not the mandate.

**Classification Confidence Decisions**
- When the blocker sits between two types (e.g., technical vs. adoption), present both classifications with the distinguishing question that would confirm one over the other.
- When the CSM disagrees with the suggested classification, accept their override — they have context you don't. Note the override in the reviewer note.

Use the Write tool to create this file. Return "done" when complete.
