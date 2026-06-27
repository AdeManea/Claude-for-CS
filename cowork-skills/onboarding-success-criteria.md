---
name: onboarding-success-criteria
description: >
  Define, refine, and track onboarding success criteria with the customer. Produces
  a structured success criteria document — typically 3–5 measurable outcomes the
  customer commits to achieving by the end of onboarding. Reads your criteria format,
  review cadence, and methodology from your onboarding profile. Use --define (default)
  to facilitate a criteria definition session from scratch, --refine to adjust existing
  criteria after a scope change, --review to assess current progress against confirmed
  criteria, or --export to produce a clean customer-facing criteria summary without
  internal notes.
argument-hint: "[<account-name-or-ID>] [--define | --refine | --review | --export]"
version: "1.0.0"
---

## Company Context

**Company:** AutogenAI — AI-powered proposal and bid writing platform for enterprise teams.

**Primary value metric:** Win rate improvement and proposal throughput. Success is measured via the Triple Metric framework:
- Corporate level: Win rate, Revenue, ROS/ROA
- Business unit level: Technical score, Operating margins, Revenue per transaction
- Project level: Time saved, User adoption, Headcount efficiency

**Segments:** Enterprise (primary, white-glove), Mid-Market and SME (lighter model).

**CSM role:** Enterprise CSM, supported by Onboarding PM and Implementation Consultant (IC). IC remains on the account permanently post-onboarding.

**Success criteria model:** Jointly defined during the Value Alignment Session in Month 1. Reviewed quarterly. Criteria are customer-defined per account — no fixed universal KPIs.

**Success criteria format:** Outcome-based (default). Template: Value Realisation Deck (PowerPoint).

**Key milestones (Enterprise):**
- M1: Kickoff + Discovery complete (Month 1) — Value Alignment Session complete, Triple Metric agreed
- M2: Configuration complete (Month 1)
- M3: Adoption underway (Months 2–4)
- M4: First Business Review (Month 3–4) — first Triple Metric tracking conversation
- M5: Handoff ready (Month 5) — Onboarding PM exits, CSM takes ownership

**Graduation criteria:** First Business Review complete, joint roadmap agreed, monthly value reporting running.

**Top churn signals:** Low platform adoption, low bid volume through tool, champion departure, competitive displacement.

**Tools in use:** HubSpot (CRM), Planhat (CS platform, transitioning from Asana), SharePoint (document storage), Slack and Outlook (comms).

---

## Skill Instructions

# /onboarding:success-criteria

Define and track onboarding success criteria.

---

## Trigger Precision

**Use when:**
- Defining 3–5 success criteria for a new account at or before kickoff (`--define`)
- Refining existing success criteria after a kickoff call reveals different priorities (`--refine`)
- Reviewing success criteria achievement status at a milestone review or QBR (`--review`)
- Producing a customer-facing success criteria summary for sharing or sign-off (`--export`)

**Downstream dependency:** After success criteria are defined, build a formal success plan incorporating these criteria as the plan's foundation (if the `csm` plugin is installed, run `/csm:success-plan-builder`).

**Do NOT use for:**
- Tracking milestone completion — success criteria and milestones are distinct (use `/onboarding:milestone-tracker`)
- Generating the full onboarding plan (use `/onboarding:onboarding-plan`)
- Handoff graduation readiness checks (use `/onboarding:handoff-doc --readiness`)

## Typical Activation
- "Define success criteria for [Account]"
- "We need to refine the success criteria after the kickoff call"
- "Review success criteria achievement for [Account]'s QBR"
- "Export the success criteria for [Account] to share with the customer"
- CSM runs `/onboarding:success-criteria [account] --define` to facilitate a criteria definition session
- CSM runs `/onboarding:success-criteria [account] --refine` to adjust criteria after a scope or stakeholder change
- CSM runs `/onboarding:success-criteria [account] --review` to assess progress at a milestone checkpoint
- CSM runs `/onboarding:success-criteria [account] --export` to produce a clean customer-facing criteria summary

---

## Reasoning Protocol

Before generating output, apply these primers:

1. **CLASSIFY**: What type of success criteria request is this?
   - **Greenfield Definition**: No existing criteria; starting from scratch with sales context and customer goals. Optimize for customer-anchored discovery and the 3-5 ceiling.
   - **Scope-Triggered Refinement**: Existing criteria need revision due to scope change, stakeholder shift, product capability adjustment, or date shift. Isolate affected criteria; preserve confirmed ones.
   - **Progress Review**: Criteria are confirmed; CSM needs a progress assessment against a milestone checkpoint. Diagnose at-risk criteria, don't just report status.
   - **Customer-Facing Export**: Criteria are confirmed and need clean formatting for the customer. Zero internal labels, flags, or reviewer notes in output.

2. **CONSTRAINTS**: What limits the solution space?
   - G2: Criteria must be confirmed with the customer before driving milestone tracking — CSM-only criteria are hypotheses, not commitments.
   - G4: Each criterion must have a specific observable measure — vague criteria ("team is comfortable") cannot be confirmed as achieved.
   - G5: `--export` is quiet mode — no internal labels, confidence signals, pending-confirmation flags, or reviewer notes reach the customer.
   - G7: Flag any CRM or account data used that is stale relative to the configured staleness threshold — never silently present outdated context as current.
   - Criteria anchor to milestones (M2/M3/M4/M5) — orphaned criteria without a milestone anchor cannot drive review conversations.

3. **EXPERT CHECK**: What would a veteran onboarding CSM verify first?
   - Is the M4 anchor criterion named and concrete? If the single most important Month 3–4 outcome is missing or vague, the criteria set is misaligned regardless of how polished the other criteria are.
   - Are criteria anchored to what the customer said they wanted (sales echo), or are they CSM projections of what the product can do? Customer words first, CSM judgment second.
   - Is there a confirmation gap? How many criteria are confirmed with the customer vs. pending? Unconfirmed criteria must be flagged, never treated as final.

4. **ANTI-PATTERNS**: Common mistakes to avoid:
   - Drafting criteria from product capabilities instead of customer-stated goals — produces criteria the customer never asked for and won't prioritize.
   - Accepting more than 5 criteria without forcing prioritization — dilutes focus and creates tracking overhead that outlasts onboarding.
   - Reporting at-risk criteria without diagnosing the block type (customer / technical / CSM) — status without diagnosis is not actionable.
   - Revising criteria after a scope change without marking them `[Requires re-confirmation]` — the CSM treats revised criteria as agreed when they are not.
   - Running `--export` on criteria that include unconfirmed items — the customer receives hypotheses presented as commitments.
   - Writing an M4 anchor criterion that is aspirational rather than observable ("customer sees value" instead of "zero manual exports in the last two weeks").

**After execution**, verify:
- Does the output match the mode requested (--define / --refine / --review / --export)?
- Are all criteria anchored to a milestone and confirmable with an observable measure?
- Is the confirmation status explicit for every criterion (confirmed vs. pending)?
- Confidence: [High] if criteria are customer-confirmed with observable measures and milestone anchors / [Medium] if CSM-confirmed but pending customer validation / [Low] if inferred from sales notes without direct customer input — state which.

## Mode

`--define` (default): Facilitate a structured success criteria definition session.
Asks the CSM a sequence of discovery questions designed to elicit 3–5 measurable
outcomes from the customer. Produces a draft criteria document for CSM review before
sharing with the customer.

`--refine`: Revise existing criteria after a scope change, stakeholder change, or
product capability adjustment. Ask what changed and which criteria are affected.
Preserves confirmed criteria and flags revised ones for re-confirmation with the
customer.

`--review`: Assess current progress against previously confirmed criteria. Asks the
CSM for status on each criterion (on track / at risk / achieved). Produces a progress
assessment with confidence signals and recommended actions for at-risk criteria.

`--export`: Customer-facing criteria summary — clean format, no internal labels,
no confidence signals, no at-risk flags. Produces the criteria document ready for
sharing in the customer's preferred format.

---

## Account identification

Ask: "Which account are we defining success criteria for?"

If CRM connector available (HubSpot), pull:
- Account name, segment, and ARR
- Product tier (higher tiers typically unlock capabilities that anchor criteria)
- Industry vertical (calibrates which outcome types are most relevant)
- Sales notes / use cases captured during discovery

Confirm:
> "[CRM]: [account name] · [segment] · [industry] · data as of [timestamp]"

If no CRM: "Tell me the account name, their industry, and the primary use cases they
described during the sales process — that context helps anchor criteria to real
outcomes."

---

## Criteria format reference

### Outcome-based (default for AutogenAI)

Each criterion is a stated business outcome the customer will achieve:

```
Criterion: [Customer role/team] will [accomplish X] by [M4/M5 date].
Measure: [How they'll know it happened — observable behavior or artifact]
Owner: [Customer name or role]
```

Example:
```
Criterion: The bid team will produce first drafts using AutogenAI without
           manual rework by M4 (First Business Review).
Measure: X% of bids in the last two weeks of the M4 period initiated in AutogenAI.
Owner: [Bid Team Lead]
```

### Metric-based

Each criterion is tied to a quantified target:

```
Criterion: [Metric] will reach [target value] by [date].
Baseline: [Current value if known]
Owner: [Customer name or role]
Data source: [Where this metric lives]
```

### Milestone-based

Each criterion is the completion of a defined deliverable:

```
Criterion: [Deliverable] will be complete by [milestone] ([date]).
Definition of complete: [Specific observable state]
Owner: [Customer name or role]
```

If the CSM has not indicated a preference, use outcome-based format. If multiple formats fit the customer's context, note the primary format used.

---

## `--define` mode: Discovery sequence

Facilitate this sequence in order. Each question produces a draft criterion or
eliminates a direction. The goal is 3–5 confirmed criteria — not more.

### Step 1: Anchor to the sales conversation

Ask the CSM:
> "What problem was the customer solving when they bought AutogenAI? What did they say
> they wanted to accomplish in the first 30–60 days?"

Probe for: named use cases, explicit commitments made during sales, any ROI or
win-rate claims the AE made.

Draft 1–2 candidate criteria from this input. Present them and ask: "Do these match
what the customer told you they wanted? Anything to add or adjust?"

### Step 2: Identify the first measurable outcome

Ask:
> "What's the single most important thing that needs to be true at the First Business Review
> (Month 3–4) for this customer to feel like onboarding was worth it?"

This becomes M4 (First value) — the anchor criterion. Every other criterion either
leads to this one or follows from it. For AutogenAI accounts, this is typically a
win rate signal, adoption metric, or time-saving measure tied to the Triple Metric.

Draft the M4 criterion. Apply the outcome-based format unless the CSM specifies otherwise.

### Step 3: Identify the handoff-ready outcome

Ask:
> "What would 'fully onboarded' look like at Month 5? What does the customer need to
> be doing independently before the Onboarding PM exits and you take full ownership?"

This becomes the M5 graduation criterion. It must align with: First Business Review complete, joint roadmap agreed, monthly value reporting running.

Draft the M5 criterion.

### Step 4: Fill in the gap criteria

Ask:
> "What needs to happen between kickoff and the First Business Review for that outcome
> to be achievable? Think about platform configuration, document library setup, Academy
> access, and Foundations training completion."

These intermediate criteria map to M2 (configuration complete) and M3 (adoption underway).
Typically 1–2 criteria per intermediate milestone. Keep them specific and ownable.

Draft M2 and M3 criteria.

### Step 5: Confirm the criteria set

Present all drafted criteria as a numbered list:

> "Here's the draft criteria set for [account name]. Before we finalize:
> 1. Can each criterion be confirmed as met — is there a clear observable measure?
> 2. Is each criterion realistic given their team size and bandwidth?
> 3. Are any criteria outside what AutogenAI can deliver in the onboarding window?
> 4. Does the customer know about these criteria, or do we need to confirm them on
>    the next call?"

Revise based on CSM input. Flag any criterion the CSM marks as unconfirmed with the
customer as `[Pending customer confirmation]`.

---

## `--refine` mode

Ask: "What changed? (a) Scope change, (b) stakeholder change, (c) product capability
adjustment, (d) date shift."

For each change type:

**Scope change:** Identify which criteria are affected. Revise the criterion text and
re-evaluate the measure. If the scope expands, add a candidate criterion — don't
stretch an existing one. Mark revised criteria `[Requires re-confirmation with customer]`.

**Stakeholder change:** Update the Owner field. If the new owner was not part of the
original criteria conversation, flag: "New owner [name] may not be aligned on this
criterion — confirm at the next touchpoint."

**Product capability adjustment:** If a criterion references a capability that is
unavailable or delayed, flag: "This criterion may not be achievable within the
onboarding window — recommend revising or replacing it before the next review."

**Date shift:** Recalculate which milestone a criterion maps to based on updated dates.
If a criterion mapped to M4 but M4 shifted past a contractual renewal event, escalate
to the CSM for judgment.

---

## `--review` mode

Ask: "What milestone are we reviewing against? (M2 / M3 / M4 / M5)"

For each confirmed criterion, ask the CSM for status:
- On track: Customer is progressing; no intervention needed
- At risk: Progress exists but the target is in doubt
- Achieved: Criterion is met — provide evidence (artifact, observation, customer statement)
- Blocked: No progress; root cause needed

**At-risk response protocol:**

For each at-risk criterion:
1. Ask: "What's blocking progress?" — surface the root cause
2. Classify the block: customer-side (adoption, bandwidth, priority) / technical
   (integration, configuration) / CSM-side (unclear ask, missing resource)
3. Recommend action:
   - Customer-side: escalation sequence (champion → exec sponsor for Enterprise accounts),
     or simplification of the criterion's scope
   - Technical: route to IC (who remains on the account permanently)
   - CSM-side: name the specific action the CSM should take before the next touchpoint
4. Flag if the at-risk criterion is the M4 or M5 anchor — those are highest priority

Output format for `--review`:

```
Success Criteria Review — [Account Name]
Milestone: [M#] · Target date: [date] · Review date: [today]

[Criterion 1] ✓ Achieved / ⚠ At risk / ● On track / ✗ Blocked
  Evidence / Note: [brief]
  Recommended action: [if at risk or blocked]

[Criterion 2] ...

Summary: [X] of [Y] criteria on track or achieved.
[If any at risk:] Recommended immediate action: [highest-priority action]
```

---

## Output format

### `--define` and `--refine` — internal review version

```
**Success Criteria — [Account Name]**
*Draft — CSM review required before sharing*
*Criteria format: [configured format]*
*Review cadence: Quarterly*

---

[Criterion 1]
  Milestone: [M#] · Target date: [date]
  Measure: [observable confirmation]
  Owner: [name/role]
  Status: [Confirmed with customer | Pending customer confirmation]

[Criteria 2–5 in same format]

---

⚠️ Internal notes:
- Unconfirmed criteria: [list or "none"]
- Criteria outside M5 window: [flag or "none"]
- Capability concerns: [any criteria that may exceed product scope]

[Reviewer note]
```

### `--export` output (quiet mode — customer-facing)

```
**[Account Name] — Onboarding Success Criteria**
*Defined: [date] · Owner: [CSM name]*

By the end of onboarding ([M5 date]), we're working toward:

1. [Criterion 1 — outcome statement only, no internal labels]
   How we'll know: [measure]
   Owner: [customer name/role]

2-5. [Same format]

We'll review progress against these criteria quarterly.
Questions? [CSM name] · [contact]
```

---

## Reviewer note (internal — `--define`, `--refine`, `--review` only)

> ⚠️ Reviewer note
> - **Sources:** [CRM ✓ | manual input | sales notes]
> - **Config fields used:** criteria format ([value]), review cadence (Quarterly),
>   graduation criteria (First Business Review complete, joint roadmap agreed, monthly value reporting running)
> - **Criteria count:** [N] defined; [N] confirmed with customer; [N] pending
> - **M4 anchor criterion:** [brief description]
> - **M5 graduation alignment:** [confirmed matches graduation criteria | gap noted]
> - **Capability concerns:** [criteria flagged against product capability | none]
> - **Flagged for your judgment:** [unconfirmed criteria / scope concerns | none]
> - **Before sharing:** Remove internal notes section and this reviewer note.
>   Confirm all criteria have been discussed with the customer, not just the CSM.
>   Replace `[Pending customer confirmation]` markers before the export.

---

## Security & Permissions

This skill operates read-only against configuration files and connected MCP data sources.
No filesystem writes, no subprocess execution, no dynamic code execution.
All data access is through explicitly connected MCP connectors; no outbound network calls are made directly.

## Trust & Verification

Customer-facing outputs (`--export` mode) apply quiet mode — internal confidence signals, reviewer notes, and CSM assessment flags are suppressed.
Success criteria achievement status is based on customer-confirmed evidence only — CSM assumption is explicitly flagged as unverified.
The 3–5 criteria ceiling is enforced — skill will not define more than 5 criteria regardless of input.
CSM review is required before sharing any exported success criteria with the customer.

## Guardrails

**Criteria require customer confirmation.** Success criteria defined only in a CSM
internal session are CSM hypotheses, not confirmed criteria. Every criterion must be
confirmed with the customer contact before it drives milestone tracking. Flag
unconfirmed criteria explicitly — do not treat them as final.

**3–5 criteria maximum.** More than 5 criteria dilutes focus and creates tracking
overhead that outlasts the onboarding period. If the CSM has 8 candidate criteria,
facilitate prioritization — don't output all 8. The 3–5 most important outcomes
are more valuable than a comprehensive list.

**Each criterion must be confirmable.** Vague criteria ("the team is more confident,"
"adoption improves") cannot be confirmed as achieved. Every criterion must have a
specific observable measure. If the CSM cannot name how they'll know a criterion is
met, the criterion needs revision before it's added to the set.

**Criteria anchor to milestones.** Every criterion maps to a specific milestone (M2,
M3, M4, or M5). Criteria that float without a milestone anchor cannot drive review
conversations. If a criterion doesn't fit any milestone, it may belong in the
post-onboarding relationship, not the onboarding plan.

**`--export` is quiet mode.** The customer-facing export contains no internal labels,
confidence signals, "pending confirmation" flags, or reviewer notes. Run `--export`
only after all criteria are confirmed with the customer.

**`--review` is diagnostic — not a report.** The review output is for the CSM's
internal assessment, not for sharing with the customer as-is. At-risk signals and
root cause analysis are internal planning tools. Format for customer communication
separately.

**Capability concerns are a guardrail, not a blocker.** If a criterion references
a capability that is unavailable or unclear, flag it for CSM judgment. Do not silently
remove the criterion — the CSM may know something about product roadmap or workarounds
that makes it valid. Raise the concern; don't resolve it unilaterally.

---

## Reference Material

### Reasoning Blueprint: Onboarding Success Criteria

Load this blueprint when advanced reasoning is activated for success criteria work.
It provides the domain-specific taxonomy, heuristics, and expert judgment patterns
that shape expert-level criteria definition, refinement, and review.

---

#### Problem Classification Taxonomy

**Type A: Greenfield Definition**
Characteristics: No existing criteria; CSM is starting from scratch with sales context and customer goals.
Primary Risk: Criteria are CSM hypotheses disconnected from what the customer actually said they wanted.
Expert Focus: Anchor every criterion to a named customer statement or sales artifact before drafting.

**Type B: Scope-Triggered Refinement**
Characteristics: Existing criteria need revision due to a scope change, stakeholder shift, product capability adjustment, or date shift.
Primary Risk: Revising criteria without re-confirming with the customer — the CSM adjusts internally and treats revised criteria as agreed.
Expert Focus: Distinguish which criteria are genuinely affected vs. reflexively rewritten; preserve confirmed criteria that still hold.

**Type C: Progress Review**
Characteristics: Criteria are confirmed; CSM needs a progress assessment against a milestone checkpoint.
Primary Risk: Review becomes a status report instead of a diagnostic — at-risk criteria get reported but not root-caused.
Expert Focus: For every at-risk criterion, classify the block type (customer / technical / CSM) before recommending action.

**Type D: Customer-Facing Export**
Characteristics: Criteria are confirmed and need to be formatted for customer consumption.
Primary Risk: Internal labels, confidence signals, or unconfirmed flags leak into the customer-facing document.
Expert Focus: Verify every criterion is confirmed with the customer before exporting — unconfirmed criteria must not appear.

---

#### Domain Heuristics

1. **The Sales Echo Rule**: The strongest criteria come from echoing what the customer said during sales, not from what the CSM thinks the product can do. If no sales context exists, the first discovery question must surface the customer's own words.

2. **The 3-5 Ceiling**: More than 5 criteria always indicates a prioritization failure, not a complex customer. Force-rank and cut. Three sharp criteria outperform seven vague ones.

3. **The Confirmation Gap**: A criterion defined in a CSM-only session is a hypothesis until the customer confirms it. Track the gap explicitly — unconfirmed criteria cannot drive milestone tracking.

4. **The M4 Anchor Test**: If the single most important First Business Review outcome is not named as a criterion, the set is misaligned. The M4 anchor criterion is the one the customer would notice is missing.

5. **The Measurability Gate**: If the CSM cannot name how they will know a criterion is met, the criterion is not ready. "The team is comfortable" fails; "zero manual exports in the last two weeks" passes.

6. **The Milestone Orphan Check**: A criterion that does not map to M2, M3, M4, or M5 belongs in the post-onboarding relationship, not the onboarding plan. Orphaned criteria dilute focus.

7. **The Graduation Alignment Rule**: The M5 criterion must align with the configured graduation criteria. If they diverge, either the criterion or the config is wrong — surface the gap.

---

#### Common Failure Modes by Type

**Greenfield Definition Failures**
- CSM Projection: CSM drafts criteria from product knowledge rather than customer input. Fix: Start with Step 1 (sales echo) and draft only from customer-stated goals before adding CSM perspective.
- Scope Creep Past 5: CSM lists 7-8 candidate criteria and expects all to be included. Fix: Facilitate prioritization explicitly — present the 3-5 ceiling as a design constraint, not a suggestion.
- Vague Anchor Criterion: M4 criterion is aspirational rather than observable ("customer sees value"). Fix: Apply the measurability gate — ask "how will you know?" and rewrite until the answer is concrete.

**Scope-Triggered Refinement Failures**
- Silent Re-Confirmation Skip: CSM revises criteria internally without flagging re-confirmation need. Fix: Every revised criterion gets `[Requires re-confirmation with customer]` — no exceptions.
- Wholesale Rewrite: A single scope change triggers rewriting all criteria instead of isolating the affected ones. Fix: Ask which specific criteria are affected before revising; preserve unaffected confirmed criteria.

**Progress Review Failures**
- Status Without Diagnosis: At-risk criteria are reported but not root-caused — "at risk" with no block type or action. Fix: For every at-risk criterion, require block classification (customer / technical / CSM) and one recommended action.
- Anchor Deprioritization: M4 or M5 anchor criterion is at risk but treated with the same urgency as intermediate criteria. Fix: Flag anchor criteria at risk with explicit priority escalation.

**Customer-Facing Export Failures**
- Internal Label Leak: Confidence signals, pending-confirmation flags, or reviewer notes appear in export. Fix: Run export only after confirming all criteria; strip all internal markers before output.

---

#### Expert Judgment Patterns

**Scope Decisions**
- When a scope change affects more than 2 criteria, treat it as a re-definition session (Type A), not a refinement.
- When a stakeholder change affects the owner of the M4 anchor criterion, escalate to a confirmation call before revising.

**Prioritization Decisions**
- When the CSM has 6+ candidate criteria, ask: "If the customer could only achieve three of these, which three would they pick?" The customer's priority order, not the CSM's, drives the cut.
- When criteria span multiple product areas, verify each area is achievable within the onboarding window before including it.

**Confidence Decisions**
- High confidence when criteria are confirmed with the customer and map to observable measures with milestone anchors.
- Medium confidence when criteria are CSM-confirmed but pending customer validation.
- Low confidence when criteria are inferred from sales notes without direct customer input.

**Timing Decisions**
- Run --review at each milestone checkpoint, not only when problems surface — proactive review catches drift before it becomes risk.
- Run --refine immediately after a scope change is identified, not at the next scheduled review — delayed refinement creates phantom criteria the customer has already abandoned.
