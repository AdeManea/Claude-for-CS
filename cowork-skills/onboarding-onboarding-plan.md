---
name: onboarding-onboarding-plan
description: >
  Generate, update, or summarize the customer-facing onboarding plan document.
  The plan is the primary shared artifact between the CSM and the customer team
  — it defines the milestone timeline, ownership at each stage, first priorities,
  communication cadence, and success criteria placeholder. Reads onboarding model,
  milestone framework, duration targets, and plan format from your onboarding profile.
  Pulls contract start date, segment, and stakeholders from CRM if available. Use
  --draft (default) to generate a complete plan after kickoff, --update to revise
  the plan after a milestone completes or scope changes, or --summary to produce
  an abbreviated current-status view for async stakeholder updates.

---

## Company Context

**Company:** AutogenAI — AI-powered proposal and bid writing platform for enterprise teams.

**Role:** Enterprise CSM. Team structure for Enterprise accounts: CSM + Onboarding Project Manager + Implementation Consultant + Bid Consultant. For Mid-Market and SME, the model is Implementation Consultant-led, then handoff back to CSM.

**Segments:** Enterprise (primary), Mid-Market, SME. Onboarding model: white-glove for Enterprise; implementation-plus-handoff for Mid-Market and SME.

**Milestone framework (Enterprise — month-based, supersedes day-target defaults):**
- M1 — Kickoff + Discovery complete: Month 1 (stakeholders confirmed, bid discovery done, Value Alignment Session complete, Triple Metric agreed)
- M2 — Configuration complete: Month 1 (platform live, document library set up, Academy access granted)
- M3 — Adoption underway: Months 2–4 (foundations training delivered, drop-in sessions running, monthly value reports started)
- M4 — First Value / First Business Review: Month 3–4 (progress reviewed, joint roadmap co-created, Triple Metric tracking confirmed)
- M5 — Handoff ready: Month 5 (Onboarding PM exits, CSM takes ownership, IC remains permanently)

**Duration targets:** Enterprise ~4 months; Mid-Market ~3 months; SME ~2–3 months. TtV targets not yet defined.

**Success framework:** Triple Metric (corporate: win rate, revenue, ROS/ROA; BU: technical score, margins, revenue per transaction; project: time saved, user adoption, headcount efficiency). Template: Value Realisation Deck (PowerPoint).

**Tools:** CRM = HubSpot; CS/PM = Planhat; Docs = SharePoint; Comms = Slack and Outlook. Customer-facing plan format: PowerPoint deck. Internal status: Planhat.

**Graduation criteria:** First Business Review complete; joint roadmap agreed; monthly value reporting running. Handoff format: handoff call (Onboarding PM to CSM); IC remains on account permanently.

---

## Skill Instructions

<!-- Status: [PROPOSED] -->

# /onboarding:onboarding-plan

Onboarding plan document — generate, update, or summarize.

---

## Pre-flight

Fields used from onboarding config (embedded above in Company Context):
- Onboarding model (white-glove / guided-self-serve / implementation-plus-handoff / partner-led — shapes ownership columns and section structure)
- Milestone framework (M1–M5 targets, completion criteria, at-risk signals)
- Duration targets by segment (Enterprise / Mid-Market / SMB — anchors the plan timeline)
- Customer-facing plan format (PowerPoint — sets the header note for where the plan lives)
- CS methodology (none formal — affects play references in footnotes)
- Graduation criteria (what signals readiness for the post-onboarding handoff)
- TtV targets by segment (internal reference; always labeled as planning targets, never as customer commitments — currently [PLACEHOLDER])

Fields used from company profile:
- Company brand name: AutogenAI
- Default communication cadence (consultative and direct style)
- Success criteria review timing: quarterly

If milestone framework fields contain `[PLACEHOLDER]`:
> "Milestone targets aren't fully configured. TtV day targets are not yet defined — the output will use month-based Enterprise milestone targets from config or generic timelines for other segments."

Proceed with the month-based Enterprise milestones from Company Context above. For non-Enterprise segments, surface the gap and offer to continue with generic timelines.

**G-code dependency:** All G-code guardrails referenced in this skill (G1–G9) are defined in context above. If any critical config is missing, G-codes may be undefined — do not proceed with partial config silently.

---

## Trigger Precision

**Use when:**
- Generating a new onboarding plan for a customer account (`--draft`)
- Revising an existing onboarding plan after scope, timeline, or milestone changes (`--update`)
- Producing a customer-facing summary of the onboarding plan for sharing or review (`--summary`)

**Do NOT use for:**
- Milestone status tracking against an existing plan (use `/onboarding:milestone-tracker`)
- Success criteria definition — criteria must be defined before or during plan generation, not after
- Handoff document generation (use `/onboarding:handoff-doc`)

## Typical Activation
- "Draft the onboarding plan for [Account]"
- "The timeline changed — update the onboarding plan for [Account]"
- "Give me a summary of the [Account] onboarding plan to share with the customer"
- CSM runs `/onboarding:onboarding-plan [account] --draft` to generate a new post-kickoff plan
- CSM runs `/onboarding:onboarding-plan [account] --update` after a milestone completion or scope change
- CSM runs `/onboarding:onboarding-plan [account] --summary` to produce an async stakeholder update

---

## Reasoning Protocol

Before generating output, apply these primers:

1. **CLASSIFY**: What type of onboarding plan request is this?
   - **New Plan Draft**: Post-kickoff, first plan generation. CSM has contract start date, stakeholders, and initial priorities. Optimize for completeness and model-correct structure.
   - **Plan Update**: Existing plan needs revision — milestone completed, date shifted, scope changed, or stakeholder changed. Preserve change log; update only affected sections.
   - **Status Summary**: Abbreviated current-state view for async communication (Slack, email, stakeholder update). Quiet mode — zero internal labels, no plan modifications.
   - **Incomplete Config / Recovery**: Config files missing or contain placeholders. Surface every gap explicitly; do not silently generate with defaults.

2. **CONSTRAINTS**: What limits the solution space?
   - G1 (dates anchor to contract start): All milestone dates derive from contract start date + config day targets. If contract start is unavailable, placeholder every date — never estimate.
   - G2 (TtV is internal): TtV targets appear only in Section 7 with `[review — internal planning target]` label. Zero TtV references in Sections 1-6 or `--summary` output.
   - G4 (model adaptation is structural): The onboarding model changes section structure, ownership columns, and prose tone materially. If model is `[PLACEHOLDER]`, default to white-glove and flag — never produce a generic plan silently.
   - G5 (confidentiality): Customer-facing export contains no internal labels, TtV references, at-risk signals, reviewer notes, or escalation paths. Audit before delivery.
   - G7 (success criteria is a placeholder until confirmed): Section 6 is always a placeholder on first `--draft` unless success-criteria skill has already run. Never populate from assumptions or sales notes.

3. **EXPERT CHECK**: What would a veteran onboarding CSM verify first?
   - Is the onboarding model configured and applied, or did the plan silently use a default structure? A white-glove plan shared with a guided-self-serve account damages the relationship framing from day one.
   - Are all milestone dates calculated from contract start date, not from today's date or kickoff date? One wrong anchor cascades every date in the plan.
   - For `--update` requests: is the change log preserved and are downstream milestone dates recalculated if a date shift cascades?

4. **ANTI-PATTERNS**: Common mistakes to avoid:
   - Calculating milestone dates from today's date or kickoff date instead of contract start date — creates scheduling drift from the anchor.
   - Producing a plan without applying the configured model adaptation — a partner-led plan missing the three-party header and communication structure looks like a template, not a plan.
   - Regenerating the full plan on an `--update` request instead of updating affected sections and appending to the change log — destroys milestone history.
   - Including TtV references, at-risk signals, or escalation paths in `--summary` or customer-facing output — leaks internal planning assumptions.
   - Populating Section 6 (success criteria) from sales notes or kickoff conversation when the success-criteria skill has not been run — these require explicit customer agreement.
   - Silently generating with default milestone day targets when config contains `[PLACEHOLDER]` values — surface the gap and offer the cold-start-interview path.

**After execution**, verify:
- Does the plan match the correct onboarding model with all model-specific adaptations applied?
- Are all milestone dates anchored to contract start date and internally consistent?
- Is the output mode (draft/update/summary) matched to the actual request, with internal content suppressed in customer-facing output?
- Confidence: [High] if config is complete + CRM data verified / [Medium] if config partial or CRM unavailable / [Low] if user-provided context only — state which.

## Mode

`--draft` (default): Generate a complete onboarding plan from kickoff inputs. Use immediately after the kickoff call when the CSM has the contract start date, stakeholder names, and first priorities. Produces the full plan document ready to share with the customer after CSM review.

`--update`: Revise an existing plan after a milestone is completed, a scope change occurs, or a key date shifts. Ask the CSM which milestone was completed or what changed. Regenerate affected sections (milestone table, next priorities, cadence) while preserving confirmed historical entries. Append a change log entry at the bottom of the plan.

`--summary`: Abbreviated current-state view — current milestone status, what's done, what's next, and the next milestone target date. Formatted for pasting into a Slack message, email, or async stakeholder update. Suppresses the full milestone table and historical context. Quiet mode (no internal labels, no reviewer note).

---

## Account identification and data pull

Ask: "Which account is this plan for? I need the account name and contract start date to calculate milestone dates."

If a CRM connector is available (HubSpot), pull:
- Account name, segment, and tier
- Contract start date (required for all date calculations)
- Assigned CSM and AE
- Account ARR (calibrates plan depth — strategic accounts get more detailed ownership)
- Known stakeholders: executive sponsor, champion, technical lead, billing contact
- Sales handoff notes (populates the "context coming into onboarding" section)

Confirm the pull:
> "[CRM]: [account name] · [segment] · contract start [date] · CSM: [name] · AE: [name] · data as of [timestamp]"

If no CRM connector is available:
> "No CRM connector configured. Provide the account name, segment, contract start date, and stakeholder names — I'll build the plan from what you give me."

---

## Milestone date calculation

All milestone dates are calculated from the contract start date using targets from the onboarding config.

**Enterprise milestone framework (month-based — use these for Enterprise accounts):**

| Milestone | Label | Target | Completion Criteria |
|-----------|-------|--------|---------------------|
| M1 | Kickoff + Discovery complete | Month 1 | Stakeholders confirmed, end users finalised, bid discovery done, Value Alignment Session complete, Triple Metric agreed |
| M2 | Configuration complete | Month 1 | Platform live, document library set up, Academy access granted |
| M3 | Adoption underway | Months 2–4 | Foundations training delivered, drop-in sessions running, monthly value reports started |
| M4 | First Value (First Business Review) | Month 3–4 | Progress reviewed, joint roadmap co-created, Triple Metric tracking confirmed |
| M5 | Handoff ready | Month 5 | Implementation wrap-up complete, Onboarding PM exits, CSM takes ownership |

**Fallback day-target framework (use for Mid-Market/SME or when month targets don't apply):**

| Milestone | Label | Day Target | Completion Criteria |
|-----------|-------|------------|---------------------|
| M1 | Kickoff complete | Day 5 | Kickoff call held; plan shared; first priorities confirmed |
| M2 | Technical setup | Day 14 | Required integrations active; users provisioned |
| M3 | First use | Day 21 | Product used for at least one real workflow |
| M4 | First value | Day 30 | Measurable outcome against at least one success criterion |
| M5 | Handoff ready | Day 60 | All success criteria met or tracked; CSM graduation checklist complete |

Calculate each milestone date as: contract start date + milestone day/month target.

If a milestone date falls on a weekend, note it:
> "M2 falls on [day] — suggest confirming with the customer whether [adjusted date] works better."

**TtV note:** TtV targets are currently [PLACEHOLDER] for AutogenAI. Every reference to TtV in the output is labeled `[review — internal planning target]` and appears only in the internal version of the plan (Section 7).

---

## Plan builder

### Section 1 — Header (customer-facing)

```
AutogenAI × [Account Name]
Onboarding Plan

Contract start:     [date]
Plan owner:         [CSM name]
Last updated:       [today's date]
Plan lives in:      PowerPoint deck / SharePoint
```

### Section 2 — Your onboarding timeline

Milestone table — customer-facing format:

| Milestone | Target date | What it means | Status |
|-----------|-------------|---------------|--------|
| M1: Kickoff + Discovery complete | [date] | Stakeholders confirmed, bid discovery done, Triple Metric agreed | Scheduled |
| M2: Configuration complete | [date] | Platform live, document library set up, Academy access granted | Upcoming |
| M3: Adoption underway | [date] | Foundations training delivered, drop-in sessions running | Upcoming |
| M4: First Business Review | [date] | Progress reviewed, joint roadmap co-created | Upcoming |
| M5: Handoff ready | [date] | Onboarding PM exits, CSM takes ownership | Upcoming |

Status values: Upcoming / In progress / Complete / At risk — updated on `--update` calls.

### Section 3 — What happens at each stage

Brief prose description of each milestone phase — what the AutogenAI team does, what the customer team does, and what signals completion.

Keep this section brief (2–3 sentences per milestone). The goal is a shared mental model, not a process document. For white-glove and implementation-plus-handoff models, this section is slightly longer — see model adaptations below.

### Section 4 — First priorities

"Here's what we need to accomplish before [M2 date]:"

Populate from kickoff notes or ask: "What are the 2–3 things that must happen before the next milestone? Who owns each?"

Present as a named list with owner and due date:
- [Priority 1] — Owner: [name] · Due: [date]
- [Priority 2] — Owner: [name] · Due: [date]
- [Priority 3 if applicable] — Owner: [name] · Due: [date]

### Section 5 — How we'll work together

Communication cadence:
> "We'll connect [weekly / biweekly / async] via [format]. You can always reach [CSM name] at [contact] between sessions."

Add preferred communication channel (Slack / email / Outlook) if known from CRM notes or kickoff.

### Section 6 — Success criteria

This section is a placeholder on the first `--draft` output:

> **Success criteria** will be defined together during our Value Alignment Session. We'll document what "successful onboarding" looks like for [account name] using the Triple Metric framework — typically covering corporate-level metrics (win rate, revenue), business unit metrics, and project-level metrics (time saved, user adoption). Criteria will be confirmed by [M1 date].

If the `/onboarding:success-criteria` skill has already been run for this account, replace the placeholder with the confirmed criteria list. Ask: "Have you already run the `/onboarding:success-criteria` skill for this account? If so, paste or describe the agreed criteria and I'll populate this section."

### Section 7 — Internal section (suppressed in customer-facing output)

Visible only in `--draft` internal version:

**TtV context [review — internal planning target]:**
- Segment TtV target: [PLACEHOLDER — not yet defined for AutogenAI]
- Current projection: [M5 date — contract start date] → [X months]
- Gap/surplus vs. target: [±X — cannot calculate until TtV target is defined]

**At-risk signals to watch:**
- M1: Stakeholders not confirmed; exec sponsor unresponsive; bid discovery session not scheduled
- M2: Technical blockers >5 days (escalate to IC); platform configuration delayed
- M3: Low training attendance; adoption metrics flat; champion departure

**Escalation path:**
- M1 missed: Manager of CS + Head of Professional Services via Slack
- Technical blocker >5 days: IC (may escalate to Head of PS or Build team directly)
- Executive sponsor unresponsive: CCO via Slack + email
- Customer wants to cancel: CSM → Manager of CS + Legal team

---

## Model adaptations

**white-glove (Enterprise default):**
- Section 3 is expanded — each milestone phase includes explicit AutogenAI-team-owned actions and customer-owned actions as separate bullet lists
- Add an "Executive relationship" note under Section 5: who is the exec sponsor, how often they receive updates, and in what format
- Section 2 milestone table includes an "Owner" column naming the CSM/Onboarding PM and the customer champion for each milestone

**guided-self-serve:**
- Section 3 is compressed — focus on customer-owned actions; CSM is "available to support" rather than leading
- Add a "Resources available to you" subsection under Section 5: documentation link, training portal, support channel
- Section 4 First priorities emphasizes what the customer's team needs to complete independently before the next CSM touchpoint

**implementation-plus-handoff (Mid-Market and SME):**
- Add a "Phase structure" note at the top of Section 2: Implementation phase (M1–M3, Implementation Consultant lead) and Adoption phase (M3–M5, CSM lead)
- Name the Implementation Consultant in the plan header alongside the CSM
- Section 3 distinguishes between "Implementation phase" milestones and "Adoption phase" milestones with separate prose blocks
- Section 7 internal section notes the handoff milestone date and pre-handoff checklist items

**partner-led:**
- Plan header includes the partner name: "AutogenAI × [Partner] × [Account]"
- Section 5 communication cadence includes the three-party structure: who the customer reaches for which type of issue (partner for day-to-day; CSM for escalation; AutogenAI support for technical)
- Section 3 milestone descriptions name the partner's role at each stage
- Internal Section 7 notes the partner contact, their scope of delivery, and the escalation trigger that routes to the CSM directly

---

## `--update` mode

Ask: "What changed? Options: (a) milestone completed, (b) date shift, (c) scope change, (d) stakeholder change."

For each change type:

**Milestone completed:**
- Update the Status column in the milestone table to "Complete" with the actual completion date
- Advance the First priorities section to reflect the next milestone's priorities
- Append a change log entry:
  `[date] — [CSM name]: M[X] marked complete. Actual date: [date]. Next: M[X+1] target [date].`

**Date shift:**
- Recalculate downstream milestone dates if the shift cascades
- Flag if the shift puts M5 past the TtV target [internal, labeled] — note that TtV target is currently [PLACEHOLDER] for AutogenAI
- Append change log: `[date] — [CSM name]: [milestone] rescheduled to [new date]. Reason: [brief]`

**Scope change:**
- Revise the affected sections (First priorities, Section 3 descriptions if needed)
- If the scope change affects graduation criteria, flag it: "This change may affect your agreed Triple Metric criteria — run `/onboarding:success-criteria --update` to confirm."
- Append change log: `[date] — [CSM name]: Scope change — [brief description].`

**Stakeholder change:**
- Update the plan header and the relevant milestone ownership entries
- Append change log: `[date] — [CSM name]: [Role] changed from [old name] to [new name].`

---

## Output format

### `--draft` output (CSM review version)

```
[Plan header — customer-facing content]

[Sections 1–6 — customer-facing content]

---

⚠️ Internal — do not share with customer

[Section 7 — TtV context, at-risk signals, escalation path]

[Reviewer note]
```

### Customer-facing export (--draft with quiet flag or after CSM review)

```
[Plan header]

[Sections 1–6 only]

No internal labels, no reviewer notes, no TtV references, no at-risk signals.
```

### `--update` output

```
[Updated plan — full document with revised sections]

[Change log — appended at bottom]

[Reviewer note — internal only]
```

### `--summary` output (quiet mode)

```
**[Account Name] — Onboarding Status**
*As of [date]*

**Current milestone:** [M# — label] — [Complete / In progress / At risk]
**Completed:** [list of complete milestones with dates]
**Next milestone:** [M# — label] by [date]
**Current priorities:**
  - [priority 1]
  - [priority 2]

Questions? [CSM name] · [contact]
```

---

## Reviewer note (internal — `--draft` and `--update` only)

> ⚠️ Reviewer note
> - **Sources:** [HubSpot CRM ✓ verified | manual input]
> - **Data as of:** [timestamp]
> - **Config fields read:** onboarding model ([value]), milestone framework ([M1–M5 targets]), duration target ([segment: X months]), plan format (PowerPoint / SharePoint)
> - **Milestone dates calculated from:** contract start [date] + config targets
> - **TtV projection:** [PLACEHOLDER — TtV targets not yet defined for AutogenAI]
> - **Success criteria section:** [placeholder — run /onboarding:success-criteria | populated from prior run]
> - **Flagged for your judgment:** [weekend milestone dates / missing stakeholders / scope not yet confirmed | none]
> - **Before sharing:** Remove the internal section (Section 7) and this reviewer note. Confirm all milestone dates are accurate. Replace the success criteria placeholder if Triple Metric criteria have been established.

---

## Security & Permissions

This skill operates read-only against configuration files and connected MCP data sources. No filesystem writes, no subprocess execution, no dynamic code execution. All data access is through explicitly connected MCP connectors (HubSpot, SharePoint, Planhat); no outbound network calls are made directly.

## Trust & Verification

Customer-facing outputs (`--summary` mode) apply quiet mode — TtV targets, internal health assessments, and reviewer notes are suppressed. All CRM and PM data is timestamped and staleness-flagged. TtV figures in Section 7 (internal planning view) are always tagged `[review — internal planning target]` and never appear in `--summary` output. CSM review is required before sharing any customer-facing plan.

## Guardrails

**Dates anchor to contract start — never estimate.** If the contract start date is unavailable, leave all milestone dates as `[confirm date]`. A milestone date that drifts from the actual contract start creates scheduling confusion from day one.

**TtV is internal.** The labels `[review — internal planning target]` and TtV references appear only in the internal section of the plan and the reviewer note. The customer-facing plan contains milestone dates and completion criteria — not time-to-value framing. Note: TtV targets are currently [PLACEHOLDER] for AutogenAI.

**The plan is a living document.** The `--draft` output is the baseline. Every milestone completion, date shift, or scope change must go through `--update` to keep the plan current. Do not regenerate the plan from scratch when an update is needed — use `--update` to preserve the change log and milestone history.

**Success criteria is a placeholder until confirmed.** Section 6 always displays a placeholder on the first `--draft` run unless the `/onboarding:success-criteria` skill has already been completed for this account. Do not populate success criteria from assumptions or sales notes — they require explicit agreement with the customer using the Triple Metric framework.

**Model adaptation changes the ownership structure materially.** The plan for an Enterprise white-glove account (CSM + Onboarding PM + IC + Bid Consultant) looks substantially different from a Mid-Market implementation-plus-handoff plan. If the model is ambiguous, default to white-glove for Enterprise and implementation-plus-handoff for Mid-Market/SME — and flag it.

**Quiet mode for customer-facing output.** The customer-facing export contains no internal labels, TtV references, at-risk signals, reviewer notes, or escalation paths. The customer receives a clean, professional document that reflects shared commitments — not internal planning assumptions.

**`--summary` is for async communication — not plan management.** The summary output is formatted for Slack, email, or stakeholder updates. It does not replace the full plan and should not be treated as a plan revision. Direct any plan changes to `--update`.

---

## Reference Material

### Reasoning Blueprint: Onboarding Plan

*For use when Tier 3 reasoning is activated for onboarding plan work. Provides domain-specific taxonomy, heuristics, and expert judgment patterns.*

---

#### Problem Classification Taxonomy

**Type A: New Plan Draft (Post-Kickoff)**
Characteristics: First plan generation after kickoff call. CSM has contract start date, stakeholder names, and initial priorities. No prior plan artifact exists.
Primary Risk: Generating a plan with unconfigured milestone targets or missing model adaptation — produces a generic document that undermines credibility at first share.
Expert Focus: Verify onboarding model and milestone framework are configured before building; a white-glove plan structure shared with a guided-self-serve account damages the relationship framing.

**Type B: Plan Update (Milestone or Scope Change)**
Characteristics: Existing plan requires revision — milestone completed, date shifted, scope changed, or stakeholder changed. Change log history must be preserved.
Primary Risk: Regenerating sections that should be preserved, losing change log continuity, or failing to cascade date shifts to downstream milestones.
Expert Focus: Identify which change type triggers which section updates; a scope change that affects graduation criteria requires a cross-skill flag to success-criteria.

**Type C: Status Summary (Async Communication)**
Characteristics: Abbreviated current-state view for Slack, email, or stakeholder update. No plan modifications. Quiet mode — no internal labels.
Primary Risk: Including internal-only content (TtV targets, at-risk signals, escalation paths) in output destined for customer or external stakeholders.
Expert Focus: Verify the summary contains zero internal labels before delivery; a single leaked TtV reference reframes the relationship as vendor-metric-driven.

**Type D: Incomplete Config / Recovery**
Characteristics: Config files missing, contain placeholders, or contract start date unavailable. Plan cannot be fully calculated.
Primary Risk: Silently generating a plan with invented dates or default assumptions the CSM treats as real.
Expert Focus: Surface every gap explicitly and offer the cold-start-interview path; a plan with fabricated dates is worse than no plan.

---

#### Domain Heuristics

1. **The Contract Anchor Rule**: Every date in the plan traces to contract start + config day/month target. If the contract start date is missing, no dates are calculated — use `[confirm date]` placeholders. Fabricated dates create downstream scheduling failures.

2. **The Model Shapes Everything Rule**: The onboarding model (white-glove, guided-self-serve, implementation-plus-handoff, partner-led) changes section structure, ownership columns, and prose tone. If model is placeholder, default to white-glove for Enterprise and flag it — never produce a model-agnostic plan silently.

3. **The TtV Firewall Rule**: TtV targets are internal planning benchmarks. Every TtV reference carries `[review — internal planning target]` and appears only in Section 7. If you find TtV language in Sections 1-6, it is a defect.

4. **The Living Document Rule**: Plans are updated, never regenerated. `--update` preserves the change log and milestone history. If a CSM asks to "redo the plan," clarify whether they mean `--update` (preserve history) or a genuine fresh `--draft` (rare — usually means the original was never shared).

5. **The Success Criteria Placeholder Rule**: Section 6 is always a placeholder on first draft unless success-criteria skill has already run. Never populate success criteria from assumptions, sales notes, or kickoff conversations — they require explicit customer agreement using the Triple Metric framework.

6. **The Weekend Milestone Rule**: When a calculated milestone date falls on a weekend, flag it with a suggested adjusted date. Do not silently shift the date — the CSM decides.

---

#### Common Failure Modes by Classification Type

**New Plan Draft Failures**
- Generic plan despite configured model: Produced a plan without applying model adaptations (e.g., missing Onboarding PM in Enterprise white-glove header, missing IC in implementation-plus-handoff).
  Fix: Check onboarding model value before building any section; apply the matching model adaptation block completely.
- Dates calculated from wrong anchor: Used today's date or kickoff date instead of contract start date for milestone calculation.
  Fix: Confirm contract start date explicitly before any date math. If unavailable, placeholder all dates.

**Plan Update Failures**
- Change log dropped: Regenerated the plan from scratch instead of updating affected sections and appending to the change log.
  Fix: Always use `--update` logic — identify which sections are affected, update only those, append change log entry.
- Downstream dates not cascaded: Updated one milestone date but left downstream milestones unchanged, creating impossible timelines.
  Fix: When a date shifts, recalculate all downstream milestone dates and flag if M5 exceeds TtV target (internal only — note currently [PLACEHOLDER] for AutogenAI).

**Status Summary Failures**
- Internal content leaked: Summary included TtV references, at-risk signals, or reviewer notes — content meant for internal section only.
  Fix: Summary mode suppresses Section 7, reviewer note, and all internal labels. Audit output for any `[review` tags before delivery.
- Summary treated as plan revision: CSM used summary output to communicate a scope change rather than running `--update`.
  Fix: If summary content implies changes, prompt: "This looks like a plan change — run `--update` to revise the plan and preserve the change log."

**Incomplete Config Failures**
- Silent default assumptions: Generated a plan using default milestone day targets without disclosing that config values were missing.
  Fix: If any config field is placeholder, surface the warning and offer the cold-start-interview path before proceeding.

---

#### Expert Judgment Patterns

**Scope Decisions**
- When model is ambiguous, default to white-glove for Enterprise (most complete structure) and flag — never silently produce a stripped-down plan.
- When success criteria are available from a prior skill run, populate Section 6 fully using the Triple Metric framework; never leave the placeholder when data exists.

**Sequencing Decisions**
- Pull CRM data (HubSpot) before building any section — the account segment calibrates plan depth (strategic Enterprise accounts get more detailed ownership columns including Onboarding PM and Bid Consultant).
- Calculate all milestone dates in one pass before writing prose — date inconsistencies between sections are the most common CSM complaint.

**Depth Decisions**
- `--draft` always produces both the customer-facing plan (Sections 1-6) and the internal section (Section 7) in a single output — the CSM strips Section 7 before sharing.
- `--summary` is minimal by design — if the CSM asks for "more detail in the summary," redirect to `--draft` with a quiet flag rather than inflating the summary format.

**Confidentiality Decisions**
- Any output containing TtV, at-risk signals, or escalation paths is internal-only. The customer-facing export is Sections 1-6 with zero internal metadata.
- Portfolio-level summaries (multiple accounts) require extra confidentiality review — account-specific ARR and health signals must not cross account boundaries.

Use the Write tool to create this file. Return "done" when complete.
