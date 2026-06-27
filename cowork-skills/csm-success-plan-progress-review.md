---
name: csm-success-plan-progress-review
description: "Reviews an existing success plan canvas and generates a structured progress review artifact with milestone scorecard, OCV outcome ratings, CSM action list, and optional customer-facing summary and QBR pre-work note."
---

## Company Context

**Company:** AutogenAI — AI-powered proposal and bid writing platform for enterprise teams. Primary value metric: win rate improvement / proposal throughput.

**Role:** Enterprise Customer Success Manager. CS model: hybrid/segmented — high-touch enterprise, tech-touch SMB/mid-market. Accounts per CSM: 25–50 enterprise accounts. Renewal target (GRR): 90%. Expansion target (NRR): 110%.

**Top churn drivers:** Low platform adoption, low bid volume through tool, champion departure, competitive displacement.

**Available integrations:** HubSpot CRM (verified), Microsoft 365 (verified), Glyphic AI call recording (verified). CS Platform (Gainsight/Totango/ChurnZero/Vitally) not connected — milestone and health signals come from CRM, call recordings, and conversation context.

**Health model:** Manual signals. Red = 2+ concurrent churn signals active. Yellow = 1 churn signal active or declining engagement.

**Customer journey stages:** Onboarding / Adoption / Value Realization / Renewal / Expansion.

**Escalation routing:** At-risk renewals → Head of CS (48h). Red health accounts → manager (24h). Executive escalations → manager + AE (same day). Expansion signals (qualified) → AE/AM (48h).

**Source attribution tags:** `[CRM — HubSpot]`, `[Call recording — Glyphic AI]`, `[M365]`, `[Computed]`, `[user provided]`, `[model knowledge]`, `[conversation context]`.

**Shared guardrails:** Health scores are heuristics, not verdicts. Expansion signals tagged `[early signal — not yet qualified]` unless qualifying conversation with economic buyer has occurred. Renewal forecasts: flag language that reads as a revenue commitment. No silent data freshness — state data-as-of timestamp; flag data older than 7 days. Customer-facing deliverables suppress internal narration, health scores, ARR values, and escalation routing details.

---

## Skill Instructions

# /csm:success-plan-progress-review

[PROPOSED]

## Overview

`csm:success-plan-progress-review` generates a structured progress review artifact by reading an upstream success plan canvas and incorporating milestone and OCV updates provided by the CSM.

## Use When

- A CSM needs to generate a structured progress review against an existing success plan canvas
- Reviewing milestone status (On Track / At Risk / Missed) for a customer account
- Assessing OCV outcome progress (Delivered / In Progress / Not Started / Blocked)
- Evaluating success criteria met/not-met status against plan targets
- Preparing for a QBR and need a pre-work note or customer-facing summary
- Capturing a point-in-time progress snapshot for an account

## Do NOT Use For

- Generating or modifying success plan canvases — use `csm:success-plan-canvas [operation=generate]`
- Sending or drafting customer communications — use `csm:customer-comms`
- Health score calculation or portfolio-level reporting — use `rev-ops:portfolio-health-report`
- Automated milestone detection — the CSM provides milestone updates explicitly
- Writing to OCV files — this skill is read-only with respect to canvas and OCV files

## Typical Activation
"/csm:success-plan-progress-review Acme Corp"
"How is [account] tracking against their success plan?"
"Review progress on [customer]'s goals"
"Pull the success plan progress for [account] before my QBR"

**Inter-skill contract:**

```
csm:success-plan-canvas [operation=generate or refresh]
        ↓  emits context/success-plan-[safe_account]-[YYYY-MM-DD].md
csm:success-plan-progress-review [operation=review]
        ↓  reads canvas file → emits context/progress-review-[safe_account]-[YYYY-MM-DD].md
```

This skill is the downstream consumer. The upstream canvas must exist before a review can be generated.

---

## Operations

### operation: review

Reviews an existing success plan canvas and produces a dated progress review document.

**Upstream canvas file resolution:**

The skill locates the canvas at `context/success-plan-[safe_account]-[canvas_date].md`. If `canvas_date` is not provided, the skill scans for the most recent canvas file for the account using the `safe_account`-derived filename prefix.

If no canvas file is found, the skill returns the following error and produces no partial output:

```
Error: No success plan canvas found for [account_name].
Expected file: context/success-plan-[safe_account]-[YYYY-MM-DD].md
Run csm:success-plan-canvas [operation=generate] first.
```

**Inputs:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `operation` | string | Yes | Must be `"review"` |
| `account_name` | string | Yes | Account name — used to locate upstream canvas file and derive `safe_account` |
| `csm_name` | string | Yes | Name of CSM conducting the review — display-only |
| `review_date` | string | No | ISO date of review (defaults to today if not provided) |
| `canvas_date` | string | No | ISO date of canvas to review against (defaults to most recent canvas for account) |
| `milestone_updates` | list | Yes | List of milestone objects: `{milestone_name, status, notes}` where `status` is one of `On Track`, `At Risk`, `Missed` |
| `ocv_updates` | list | No | List of OCV outcome objects: `{outcome_name, status, notes}` where `status` is one of `Delivered`, `In Progress`, `Not Started`, `Blocked` |
| `key_benefits_realized` | list | No | CSM-provided list of concrete benefits the customer has already realized since the plan was created. When provided, rendered as a subsection of the Progress Scorecard and optionally surfaced in the customer-facing summary |
| `success_criteria_status` | list | No | List of success criteria assessment objects: `{criterion, met, notes}` where `met` is boolean. When provided, adds a Success Criteria Evaluation section |
| `include_customer_summary` | boolean | No | Default: `false`. When `true`, generates a customer-facing summary section |
| `include_qbr_note` | boolean | No | Default: `false`. When `true`, generates a QBR pre-work note section |
| `notes` | string | No | CSM freeform notes — appended to the review document as display-only text |

**`safe_account` derivation:**

`account_name` → lowercase → replace all non-alphanumeric characters with `-` → collapse consecutive hyphens to a single `-` → trim to 30 characters.

Examples:
- `"Acme Corp"` → `acme-corp`
- `"TechCo, Inc."` → `techco-inc`
- `"Global Health & Wellness Partners"` → `global-health-wellness-partners`

**Auto-ID generation:**

Format: `REVIEW-[ACCT]-[YYYYMMDD]`

- `ACCT`: first 4 alphabetic characters of `account_name`, uppercased, non-alpha characters stripped before extraction
  - `"Acme Corp"` → `ACME`
  - `"TechCo"` → `TECH`
  - `"123 Systems"` → `SYST`
- `YYYYMMDD`: review date

Example: `REVIEW-ACME-20260517`

**Output artifact structure:**

The review document contains up to 7 structured sections. Three are always present; four are conditional:

| Section | Always Present | Condition |
|---------|---------------|-----------|
| Progress Scorecard | Yes | Always; includes `### Key Benefits Already Realized` subsection when `key_benefits_realized` is provided |
| OCV Outcome Status | No | `ocv_updates` list provided and non-empty |
| Success Criteria Evaluation | No | `success_criteria_status` list provided |
| CSM Action List | Yes | Always (may note "No actions required — all milestones On Track" if applicable) |
| Customer-Facing Summary | No | `include_customer_summary: true`; includes benefits realized when `key_benefits_realized` provided |
| QBR Pre-Work Note | No | `include_qbr_note: true` |
| Notes | Yes | Always (rendered as empty section if no `notes` provided) |

Section assembly order in the output file is fixed: Progress Scorecard → OCV Outcome Status → Success Criteria Evaluation → CSM Action List → Customer-Facing Summary → QBR Pre-Work Note → Notes.

**Console summary returned after successful execution:**

```
Review generated.
  review_id:     REVIEW-[ACCT]-[YYYYMMDD]
  account:       [account_name]
  csm:           [csm_name]
  review_date:   [review_date]
  canvas_ref:    [plan_id from canvas frontmatter]
  at_risk:       [n]
  missed:        [n]
  file:          context/progress-review-[safe_account]-[YYYY-MM-DD].md
```

**Output file naming pattern:**

```
context/progress-review-[safe_account]-[YYYY-MM-DD].md
```

Multiple reviews on the same date for the same account overwrite the prior file (no versioning in v1.0.0).

---

## Output Format

**YAML frontmatter fields in the output file:**

```yaml
---
review_id: REVIEW-ACME-20260517
account_name: Acme Corp
canvas_reference: context/success-plan-acme-corp-2026-05-17.md
plan_id: CANVAS-ACME-20260517
review_date: 2026-05-17
csm_name: Jane Smith
created_at: 2026-05-17T14:00:00Z
created_by: todd@successhacker.co
milestone_summary:
  on_track: 3
  at_risk: 1
  missed: 0
includes_key_benefits_realized: true
includes_success_criteria_evaluation: false
includes_customer_summary: false
includes_qbr_note: true
---
```

**Immutable fields (set at creation; not modified in subsequent operations):**

| Field | Reason |
|-------|--------|
| `review_id` | Identity key |
| `created_at` | Audit timestamp |
| `created_by` | Audit identity |
| `canvas_reference` | Source artifact reference — must match upstream canvas used at review time |
| `account_name` | Account identity |

**Document body header:**

```markdown
# Progress Review: [account_name] — [review_date]

**CSM:** [csm_name]
**Review Date:** [review_date]
**Canvas Reference:** [plan_id]

---
```

**Progress Scorecard section format:**

```markdown
## Progress Scorecard

| Milestone | Status | Notes |
|-----------|--------|-------|
| [milestone_name] | On Track / At Risk / Missed | [notes] |

### Key Benefits Already Realized

(Conditional — present when key_benefits_realized provided)

- [benefit 1]
- [benefit 2]
```

**OCV Outcome Status section format (conditional):**

```markdown
## OCV Outcome Status

| Outcome | Status | Notes |
|---------|--------|-------|
| [outcome_name] | Delivered / In Progress / Not Started / Blocked | [notes] |
```

**Success Criteria Evaluation section format (conditional):**

```markdown
## Success Criteria Evaluation

| Criterion | Met | Notes |
|-----------|-----|-------|
| [criterion] | Yes / No | [notes] |
```

**CSM Action List section format:**

```markdown
## CSM Action List

(Actions generated from At Risk and Missed milestones)

| Milestone | Status | Recommended Action |
|-----------|--------|--------------------|
| [milestone_name] | At Risk / Missed | [action] |

(When all milestones are On Track: "No immediate actions required — continue monitoring.")
```

**Customer-Facing Summary section format (conditional):**

```markdown
## Customer-Facing Summary

(Tone determined by milestone mix — see Template A/B/C below)

[Customer-facing narrative paragraph(s)]

**Key Benefits Realized** (conditional — when key_benefits_realized provided)

[Benefits realized in customer-facing language]
```

**QBR Pre-Work Note section format (conditional):**

```markdown
## QBR Pre-Work Note

**Milestone Summary:** [on_track] on track, [at_risk] at risk, [missed] missed
**Key Wins:** [list]
**Open Risks:** [list]
**Suggested Discussion Agenda:**
- [item]
```

**Notes section format:**

```markdown
## Notes

[CSM freeform notes, or empty]
```

---

## Pre-flight

The company context and plugin configuration are embedded in the Company Context section above. No config files need to be read from disk.

Note from config:
- CS motion — shapes how directive vs. collaborative the progress review framing is
- Health model — provides context for milestone status interpretation and escalation thresholds
- Escalation matrix — required if milestone ratings trigger escalation routing
- Integrations — HubSpot CRM, Glyphic AI, and M365 are verified; CS Platform is not connected

**`context/` path resolution:** Inter-skill artifacts (`context/success-plan-*` and `context/progress-review-*`) resolve relative to the session working directory at runtime. Both this skill and `csm:success-plan-canvas` must operate from the same working directory for the inter-skill contract to hold.

---

## Reasoning Protocol

Before generating output, apply these primers:

1. **CLASSIFY** — Determine operation validity and data completeness before proceeding:
   - Is `operation` present and equal to `"review"`? If absent or any other value → return an error and halt before any output.
   - Is `account_name` non-empty? If not → reject immediately with a clear error; do not attempt canvas resolution.
   - Does an upstream canvas file exist for this account (located via `canvas_date` or most-recent scan)? If not → return the canonical canvas-not-found error and halt; produce no partial output.
   - Are `milestone_updates` provided and non-empty? If absent → halt with a missing-required-input error; the progress scorecard cannot be generated without milestone data.
   - CLASSIFY is complete when: operation confirmed as `"review"`, `account_name` validated, canvas file located and readable, `milestone_updates` present.

2. **CONSTRAINTS** — Apply before generating any output (blocking before non-blocking):
   - **C-1 BLOCKING**: `account_name` must be non-empty — reject all operations if absent; `safe_account` derivation cannot proceed.
   - **C-2 BLOCKING**: Canvas file must exist for the target account before any review output is generated — return the canonical error message exactly as specified; do not generate partial output.
   - **C-3 BLOCKING**: `milestone_updates` must be provided and non-empty — the Progress Scorecard is always-present and cannot be built without milestone data.
   - **C-4 BLOCKING**: Each `milestone_updates[].status` value must be one of `On Track`, `At Risk`, `Missed` — halt with a validation error on any invalid value; do not process the remaining list.
   - **C-5 BLOCKING**: Each `ocv_updates[].status` value (when provided) must be one of `Delivered`, `In Progress`, `Not Started`, `Blocked` — halt with a validation error on any invalid value.
   - **C-6 Non-blocking**: `safe_account` derivation must be applied before any file path construction — never use raw `account_name` in a path.
   - **C-7 Non-blocking**: Canvas content is read-only; this skill never writes to canvas files or OCV files under any condition — write scope is `context/progress-review-*` only.
   - G5: Internal data (health scores, ARR, expansion signals) must never appear in customer-facing output
   - G7: Flag any data older than 30 days with source date and staleness indicator

3. **EXPERT CHECK** — What a veteran CSM verifies before generating a progress review:
   - Do the `milestone_updates` statuses reflect the current state rather than the planned state? A CSM who copies milestone names from the canvas without providing actual status assessments produces a review that adds no signal. Flag if all milestones are listed as `On Track` with no notes — this pattern often signals incomplete input rather than genuine account health.
   - For `At Risk` milestones: does each entry include a `notes` field with a specific reason? A bare `At Risk` status without a root cause observation produces a CSM Action List with generic recommendations — flag for input enrichment.
   - For `Missed` milestones: has the escalation threshold check been applied? A missed milestone on a high-ARR account or near-renewal account may trigger escalation — this check must not be skipped.
   - If `include_customer_summary: true`: does the milestone mix support the tone selected from the template? A cautionary summary generated from an all-`On Track` milestone set is misaligned — verify tone selection against the actual milestone_summary counts.
   - If `include_qbr_note: true`: are `key_benefits_realized` entries present? A QBR Pre-Work Note without Key Wins forces the template to fall back to `On Track` milestones — this produces weaker pre-work material; prompt the CSM to provide explicit benefits if absent.

4. **ANTI-PATTERNS** — Mistakes to catch before generating output:
   - **AP-1 Partial output on canvas-not-found**: beginning any section generation before the canvas file has been confirmed readable — the error path must halt all output, not produce a partial review with a warning appended.
   - **AP-2 Invalid status values silently coerced**: accepting a `milestone_updates[].status` of `"Delayed"` or `"Complete"` and mapping it to a valid enum value — invalid values must surface as validation errors, not be silently normalized.
   - **AP-3 Customer-facing summary with internal language**: allowing health tier labels (`Red`, `Yellow`, `Green`), escalation routing details, ARR values, or internal action items to appear in the Customer-Facing Summary section — customer output is firewalled from internal language.
   - **AP-4 Notes field overwrite**: if a prior review file exists for the same account and date, overwriting prior notes without surfacing the conflict — v1.0.0 behavior is overwrite, but the CSM should be aware.
   - **AP-5 QBR note without risk acknowledgment**: generating a QBR Pre-Work Note that lists only Key Wins when At Risk or Missed milestones are present — the Open Risks section must reflect the milestone mix, not be omitted for tone reasons.

**After execution**, verify:
- Does the output file contain all required sections in fixed order (Progress Scorecard → OCV Outcome Status → Success Criteria Evaluation → CSM Action List → Customer-Facing Summary → QBR Pre-Work Note → Notes)? Are conditional sections present only when their conditions were met?
- Is the YAML frontmatter complete? Are `review_id`, `canvas_reference`, `plan_id`, `milestone_summary` counts, and all `includes_*` boolean flags correctly populated?
- For any `At Risk` or `Missed` milestones: does the CSM Action List contain specific actions (not generic "monitor closely" entries)?
- Is the `safe_account` derivation applied correctly in the output file path? Does `canvas_reference` point to the exact canvas file that was read?
- Is the customer-facing language firewall intact? No health scores, escalation identifiers, ARR values, or internal routing language in the Customer-Facing Summary or QBR Pre-Work Note.
- Confidence: [High] if canvas file confirmed readable with complete frontmatter, all milestone statuses are valid enum values, and `milestone_updates` entries include notes / [Medium] if canvas file located but some frontmatter fields missing, or milestone entries lack notes for At Risk/Missed statuses / [Low] if canvas file not found and operating on CSM-provided description only, or if `milestone_updates` statuses are unvalidated — state which.

Execute the following steps in order for every `review` operation. Do not skip steps or reorder them.

**Step 1 — Resolve `safe_account`**

Derive `safe_account` from `account_name`: lowercase → replace all non-alphanumeric characters with `-` → collapse consecutive hyphens → trim leading/trailing hyphens → truncate to 30 characters. Store as the path-construction token. The original `account_name` is `display_account` — used only in document content.

**Step 2 — Locate upstream canvas file**

If `canvas_date` is provided, attempt to read `context/success-plan-[safe_account]-[canvas_date].md`. If `canvas_date` is not provided, scan `context/success-plan-[safe_account]-*.md` for the most recent file by date suffix. If no matching canvas file is found, return the error message exactly as specified and halt — produce no partial output.

**Step 3 — Read canvas frontmatter**

From the located canvas file, extract: `plan_id`, `plan_type`, `csm_name` (if `csm_name` input is not provided), and `account_stage`. These values populate the review document frontmatter and inform section structure expectations based on `plan_type` (initial vs expansion vs renewal-refresh).

**Step 4 — Generate `review_id`**

Format: `REVIEW-[ACCT]-[YYYYMMDD]`. Derive `ACCT`: strip all non-alphabetic characters from `account_name`, take the first 4 characters, uppercase. Derive `YYYYMMDD` from `review_date` (or today if not provided) with hyphens removed.

**Step 5 — Build Progress Scorecard**

Iterate `milestone_updates`. Validate each `status` value against the allowed enum (`On Track`, `At Risk`, `Missed`); halt with validation error on any invalid value. Count on_track, at_risk, and missed totals for `milestone_summary`. Render the milestone status table. If `key_benefits_realized` is provided and non-empty, append the `### Key Benefits Already Realized` subsection and set `includes_key_benefits_realized: true`.

**Step 6 — Build OCV Outcome Status section (conditional)**

If `ocv_updates` is provided and non-empty, validate each `status` value against the OCV enum (`Delivered`, `In Progress`, `Not Started`, `Blocked`). Render the OCV outcome status table. If `ocv_updates` is absent or an empty list, omit this section entirely.

**Step 7 — Build Success Criteria Evaluation section (conditional)**

If `success_criteria_status` is provided, render the success criteria table with `met` displayed as `Yes` / `No`. Set `includes_success_criteria_evaluation: true`. Apply evaluation logic from the Milestone Rating Guide (Section 4) — flag unmet criteria with no corresponding At Risk or Missed milestone for additional CSM action items.

**Step 8 — Build CSM Action List**

Apply action generation logic from the Milestone Rating Guide (Section 3). For each `At Risk` milestone, generate investigation and intervention actions. For each `Missed` milestone, generate recovery and escalation actions. Apply escalation threshold logic from Section 2. If all milestones are `On Track`, render: "No immediate actions required — continue standard monitoring cadence."

**Step 9 — Build Customer-Facing Summary (conditional)**

If `include_customer_summary: true`, select the appropriate tone template based on the milestone mix (positive / cautionary / escalation — see Customer-Facing Summary Templates in Reference Material below). Incorporate `key_benefits_realized` using customer-facing language guidance. Set `includes_customer_summary: true`.

**Step 10 — Build QBR Pre-Work Note (conditional)**

If `include_qbr_note: true`, generate the QBR Pre-Work Note using the QBR Pre-Work Note template (see Reference Material below). Populate Key Wins from `key_benefits_realized` (or On Track milestones if key_benefits_realized is absent). Populate Open Risks from At Risk and Missed milestones. Set `includes_qbr_note: true`.

**Step 11 — Assemble and write output file**

Construct the YAML frontmatter block with all required fields. Assemble document sections in fixed order: Progress Scorecard → OCV Outcome Status → Success Criteria Evaluation → CSM Action List → Customer-Facing Summary → QBR Pre-Work Note → Notes. Write the complete document to `context/progress-review-[safe_account]-[review_date].md`. Return the console summary.

---

## Reviewer note

> **⚠️ Reviewer note**
> - **Sources:** [CS Platform — not connected | CRM ✓ HubSpot verified | Glyphic AI ✓ verified | M365 ✓ verified | canvas from [date] | user provided]
> - **Data as of:** [timestamp per source]
> - **Flagged for your judgment:** [N items marked `[review]` inline | none]
> - **Before sending:** Confirm progress data is current (data >30 days old is directional only). Do not share internal health score components in customer-facing progress summary.

---

## Security & Permissions

- **Network access:** none
- **Filesystem read scope:** `context/success-plan-*` (upstream canvas files — read-only); `context/progress-review-*` (own output files — read for conflict detection)
- **Filesystem write scope:** `context/progress-review-*` only
- **Subprocess execution:** none
- **Dynamic code execution:** none

This skill does not read, write, or modify any file outside the `context/` directory. It does not write to canvas files or OCV files under any condition.

---

## Trust & Verification

- `account_name` is sanitized via the `safe_account` function (alphanumeric + hyphen, max 30 chars) for all filesystem path construction. The unsanitized value (`display_account`) is used only in document content — never in path construction.
- `milestone_updates` and `ocv_updates` list items (including `milestone_name`, `outcome_name`, and `notes` fields) are treated as display-only. They are never used in filesystem path construction, logic branching, or code execution.
- `notes` input is treated as display-only freetext. It is rendered into the Notes section of the output document without interpretation.
- `csm_name` is treated as display-only. It is never used in filesystem path construction.
- Canvas content loaded from the upstream file is rendered into the review document as display content. It is never executed, evaluated, or interpolated into skill logic.
- All path construction uses only `safe_account` (derived from `account_name`) and `review_date` (ISO date). No other user input reaches path construction.

---

## Examples

### Example 1 — Basic progress review

```
csm:success-plan-progress-review
  operation: review
  account_name: Acme Corp
  csm_name: Jane Smith
  milestone_updates:
    - milestone_name: "Complete onboarding for 3 admin users"
      status: On Track
      notes: "All 3 admins completed training last week"
    - milestone_name: "Achieve 80% DAU adoption in core module"
      status: At Risk
      notes: "Currently at 52% DAU; adoption stalled in ops team"
    - milestone_name: "Complete integration with Salesforce"
      status: Missed
      notes: "IT resourcing delayed; now targeting Q3"
```

**What this produces:**
- Reads `context/success-plan-acme-corp-[most-recent-date].md`
- Creates `context/progress-review-acme-corp-2026-05-17.md`
- Output includes: Progress Scorecard, CSM Action List, Notes
- Console summary: 1 At Risk, 1 Missed

---

### Example 2 — Full review with OCV updates, success criteria, customer summary, and QBR note

```
csm:success-plan-progress-review
  operation: review
  account_name: Acme Corp
  csm_name: Jane Smith
  review_date: 2026-05-17
  canvas_date: 2026-03-01
  milestone_updates:
    - milestone_name: "Complete onboarding for 3 admin users"
      status: On Track
      notes: "All 3 admins certified"
    - milestone_name: "Achieve 80% DAU adoption in core module"
      status: At Risk
      notes: "52% DAU — ops team lagging"
  ocv_updates:
    - outcome_name: "Reduce manual reporting time by 40%"
      status: In Progress
      notes: "Reporting automation deployed; measuring impact"
    - outcome_name: "Enable real-time pipeline visibility"
      status: Delivered
      notes: "Live dashboards confirmed by VP Sales"
  key_benefits_realized:
    - "Admin team onboarded and self-sufficient — no support tickets in 30 days"
    - "Real-time pipeline dashboard live — VP Sales confirmed visibility improvement"
  success_criteria_status:
    - criterion: "3 admin users certified"
      met: true
      notes: "All 3 completed certification 2026-04-15"
    - criterion: "80% DAU in core module"
      met: false
      notes: "Currently 52%; ops team adoption lagging"
  include_customer_summary: true
  include_qbr_note: true
  notes: "QBR scheduled for 2026-06-01. Ops team adoption is the key risk to address."
```

**What this produces:**
- Reads `context/success-plan-acme-corp-2026-03-01.md` (explicit canvas_date)
- Creates `context/progress-review-acme-corp-2026-05-17.md`
- Output includes all 7 sections: Progress Scorecard (with Key Benefits Already Realized subsection), OCV Outcome Status, Success Criteria Evaluation, CSM Action List, Customer-Facing Summary (with benefits realized), QBR Pre-Work Note, Notes
- Console summary: 1 At Risk, 0 Missed

---

## Reference Material

### Progress Review Schema

**YAML Frontmatter Field Definitions**

Required fields (canonical order):

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `review_id` | string | Yes | Auto-generated. Format: `REVIEW-[ACCT]-[YYYYMMDD]`. Immutable after creation. |
| `account_name` | string | Yes | Display name of the account. Immutable after creation. |
| `canvas_reference` | string | Yes | Path of the upstream canvas file. Format: `context/success-plan-[safe_account]-[YYYY-MM-DD].md`. Immutable. |
| `plan_id` | string | Yes | The `plan_id` value read from the upstream canvas frontmatter. |
| `review_date` | string | Yes | ISO date of the review. Format: `YYYY-MM-DD`. |
| `csm_name` | string | Yes | Name of CSM conducting the review. Display-only. |
| `created_at` | string | Yes | ISO 8601 UTC timestamp of document creation. Auto-set. Immutable. |
| `created_by` | string | Yes | Identity of the skill operator. Immutable. |
| `milestone_summary.on_track` | integer | Yes | Count of On Track milestones. |
| `milestone_summary.at_risk` | integer | Yes | Count of At Risk milestones. |
| `milestone_summary.missed` | integer | Yes | Count of Missed milestones. |

Conditional boolean flags (always present in frontmatter):

| Field | Default | Description |
|-------|---------|-------------|
| `includes_key_benefits_realized` | `false` | `true` when `key_benefits_realized` input was provided and non-empty |
| `includes_success_criteria_evaluation` | `false` | `true` when `success_criteria_status` input was provided |
| `includes_customer_summary` | `false` | `true` when `include_customer_summary: true` was passed |
| `includes_qbr_note` | `false` | `true` when `include_qbr_note: true` was passed |

**Auto-ID Generation Rules**

Format: `REVIEW-[ACCT]-[YYYYMMDD]`

ACCT derivation: strip all non-alphabetic characters from `account_name` → take first 4 characters → uppercase.

| `account_name` | `ACCT` |
|----------------|--------|
| `Acme Corp` | `ACME` |
| `TechCo` | `TECH` |
| `123 Systems Inc` | `SYST` |
| `A1 Solutions` | `ASOL` |

**`safe_account` Derivation**

1. Convert `account_name` to lowercase
2. Replace all non-alphanumeric characters with `-`
3. Collapse consecutive hyphens into a single `-`
4. Trim leading and trailing hyphens
5. Truncate to maximum 30 characters

| `account_name` | `safe_account` |
|----------------|----------------|
| `Acme Corp` | `acme-corp` |
| `TechCo, Inc.` | `techco-inc` |
| `Global Health & Wellness Partners` | `global-health-wellness-par` |

**Error Conditions**

| Condition | Error Message | Behavior |
|-----------|--------------|----------|
| Canvas file not found | `Error: No success plan canvas found for [account_name]. Expected file: context/success-plan-[safe_account]-[YYYY-MM-DD].md. Run csm:success-plan-canvas [operation=generate] first.` | Halt — no partial output |
| `milestone_updates` missing or empty | `Error: milestone_updates is required and must contain at least one item.` | Halt |
| Invalid milestone status value | `Error: Invalid milestone status "[value]" for milestone "[milestone_name]". Valid values: On Track, At Risk, Missed.` | Halt |
| Invalid OCV outcome status value | `Error: Invalid OCV status "[value]" for outcome "[outcome_name]". Valid values: Delivered, In Progress, Not Started, Blocked.` | Halt |

---

### Milestone Rating Guide

**Milestone Status Definitions**

**On Track:** The milestone is progressing as planned. No active blockers or meaningful deviation from the plan timeline. Skill interpretation: no action item generated; contributes to `on_track` count; positive summary tone if all/most milestones On Track.

**At Risk:** The milestone is in jeopardy. A credible threat to on-time completion exists, but the milestone has not yet been formally missed. Detection signals: completion rate materially below target; specific blocker identified; customer engagement declined or silent 2+ weeks; key sponsor/champion changed; dependency risk. Skill interpretation: generates action items; contributes to `at_risk` count; cautionary summary tone if 1-2 milestones At Risk.

**Missed:** The milestone has not been completed by its planned date, or deliverable formally deferred without a committed recovery date. Skill interpretation: generates escalated action items; contributes to `missed` count; escalation summary tone if any milestone Missed.

**Escalation Thresholds**

| Threshold | Condition | Action List Response | Summary Tone |
|-----------|-----------|---------------------|--------------|
| 1 — Standard monitoring | All On Track | "No immediate actions required — continue standard monitoring cadence." | Positive |
| 2 — Active management | 1 At Risk; no Missed | Investigate + intervention plan | Cautionary |
| 3 — Elevated risk | 2+ At Risk; no Missed | Investigate + intervention for each; add coordination note | Cautionary with urgency; recommend alignment call |
| 4 — Recovery planning | 1+ Missed; any At Risk | Recovery plan for Missed; intervention for At Risk; internal escalation | Escalation — honest, calm, outcome-focused |
| 5 — Executive escalation | 3+ Missed, OR any Missed on critical path | All of Threshold 4 + executive sponsor outreach; flag for internal QBR | Escalation — signal need for leadership alignment |

**CSM Action Generation Logic**

On Track → No action item.

At Risk → For each At Risk milestone, generate:
- Investigate: Review notes and history for [milestone_name] — identify root cause (resourcing, blocker, engagement, dependencies).
- Diagnose: Schedule a focused conversation with the customer to confirm the blocker and assess their commitment.
- Intervention plan: Draft revised timeline, escalation path if customer-side blocker, or support engagement if product/delivery-side.
- Internal flag: Flag [milestone_name] in the next internal account review.

Missed → For each Missed milestone, generate:
- Root cause: Document root cause — customer-side failure, CSM-side delivery gap, or external dependency.
- Recovery plan: Define revised target date, adjusted scope (if needed), and owner confirmation from customer.
- Customer communication: Communicate the miss and recovery plan to the customer sponsor using escalation tone.
- Internal escalation: Escalate missed status to CSM manager / account team lead. Add to account risk log.
- Sponsor outreach (Threshold 5 only): Engage executive sponsor to reaffirm commitment to plan objectives and revised delivery path.

**Success Criteria Evaluation Logic**

`met: true` → Display "Yes"; count toward criteria met tally; display notes as context.

`met: false` → Display "No"; if associated milestone is At Risk or Missed, criterion failure reinforces those action items. If criterion is `met: false` but no associated milestone is At Risk or Missed, add CSM Action item: "Review [criterion] — success criterion not yet met despite active milestone. Identify gap and corrective action."

No `partial` value — field is boolean. For "almost met," set `met: false` and use the `notes` field to describe the gap.

**Measures of Success Assessment**

When OCV outcomes or success criteria have quantitative targets defined in the canvas:
1. Identify the target from the upstream canvas.
2. Assess current state from `notes` in `ocv_updates` or `success_criteria_status`.
3. Rate trajectory: on trajectory (note measurement and expected completion) / off trajectory (note gap; flag in CSM Action List) / target met (note achievement date and evidence).

When off trajectory, the CSM Action item should be specific: "DAU adoption is at 52% against an 80% target. With [n] weeks remaining, required adoption rate increase is [x]% per week. Schedule focused adoption sprint or revise target with sponsor alignment."

**Key Benefits Already Realized Guidance**

Include: benefits the customer has explicitly acknowledged; quantitative outcomes where available; qualitative improvements the customer has articulated; early-stage value signals even if not fully measured.

Do not include: milestone completions (already in Progress Scorecard); features enabled but not yet used; benefits the CSM believes exist but customer has not confirmed; internal CSM activities.

Framing: outcome-focused ("Ops team has eliminated weekly manual data export — estimated 3 hours/week saved"), not feature-focused ("Customer has the reporting dashboard enabled"). Attribute to customer's team where possible.

---

### Customer-Facing Summary Templates

**Language Guidelines**

Always: Write in the customer's perspective. Use plain business language — avoid OCV, MoS, safe_account, progress review artifact. Name the value. Be specific where data is available. Maintain collaborative, partnership tone. Lead with progress and wins before introducing risks. Short paragraphs (3-4 sentences max).

Never: Minimize genuine risks. Use blame language. Over-promise recovery timelines. Include health scores, scoring, or risk flags. Reference plan mechanics the customer doesn't need ("your account is At Risk in our system"). Use jargon: OCV, MoS, CSAT score, health score, milestone_updates, or internal tool names.

**Template A — Positive Tone (All or Most Milestones On Track)**

Use when: All milestones On Track, or On Track with at least one OCV outcome Delivered or In Progress.

Template A1 — All On Track, Key Benefits Realized:
```
Hi [account_name] team,

We wanted to share a quick progress update on your success plan.

Things are moving well. [benefit_1]. [benefit_2]. These are the kinds of outcomes we set out to achieve together, and it's great to see them taking shape.

Your team is on track across all active milestones. [Optional: call out one milestone by name if particularly strong.]

Next up: [brief summary of what the next milestone or phase involves and what the customer needs to do or prepare]. We're here to support you every step of the way.

Warm regards,
[csm_name]
```

Template A2 — All On Track, No Key Benefits to Surface Yet:
```
Hi [account_name] team,

We're excited to share an update on where things stand with your success plan.

Your team is making solid progress across all active milestones. [Briefly name 1–2 specific milestones and their current state.]

We're on a good path toward [plan_objective]. The next milestone we'll be working through together is [milestone_name], and here's what that involves: [one sentence on what the customer should expect or prepare].

Looking forward to continuing this momentum with you.

Warm regards,
[csm_name]
```

Template A3 — OCV Outcome Delivered (insert into A1 or A2):
```
A quick note on one specific win we want to make sure doesn't go unnoticed: [OCV outcome name or benefit description]. This is a direct result of the work your team put into [brief context].
```

**Template B — Cautionary Tone (Mixed Status: 1–2 At Risk)**

Use when: 1–2 milestones At Risk; no milestones Missed. Progress is real but intervention is indicated.

Template B1 — 1 At Risk Milestone:
```
Hi [account_name] team,

We wanted to share a progress update and flag one area that needs our attention.

First, the good news: [milestone_name] and [other milestones] are on track, and [benefit_1 if available]. Your team is making real progress toward [plan_objective].

That said, we want to be transparent about [at-risk milestone_name]. [Brief, non-blaming description of current state.] This is something we want to get ahead of together before it affects your timeline.

Here's what we'd like to do: [brief description of proposed next step.] [csm_name] will be in touch this week to get that scheduled.

Warm regards,
[csm_name]
```

Template B2 — 2 At Risk Milestones, Recommending Alignment Call:
```
Hi [account_name] team,

We wanted to take a moment to share an honest progress update.

There are areas of real momentum: [milestone_name] is on track, and [benefit_1 if available]. At the same time, we're tracking two milestones where we see elevated risk: [at-risk milestone 1] and [at-risk milestone 2].

For [at-risk milestone 1]: [one sentence on current state and the specific concern].
For [at-risk milestone 2]: [one sentence on current state and the specific concern].

Given the combination of these two items, we'd like to recommend a short alignment call with your team. [csm_name] will reach out to schedule something in the next few days.

Warm regards,
[csm_name]
```

**Template C — Escalation Tone (Missed Milestone or Multiple At Risk)**

Use when: Any milestone Missed, or 3+ milestones At Risk. Tone: clear, direct, outcome-focused. Acknowledge honestly, take shared ownership, focus on recovery and next steps.

Template C1 — Single Missed Milestone, Recovery Plan in Place:
```
Hi [account_name] team,

We wanted to reach out with a straightforward update on your success plan progress, including an honest conversation about one area where we've fallen behind.

[milestone_name] has not been completed by the planned date. [Brief explanation of what happened.] We want to be transparent about this and share what we're doing to address it.

Here's our proposed path forward: [brief recovery plan — revised target, what the customer needs to do, what the CSM team will support]. We're aiming to have this back on track by [target_date].

On the positive side, [On Track milestones] remain on track[, and [benefit_1 if available]].

We'd like to schedule time to walk through the recovery plan together. [csm_name] will follow up to arrange that.

Warm regards,
[csm_name]
```

Template C2 — Multiple Missed or At Risk, Requesting Executive Alignment:
```
Hi [account_name] team,

We want to be straightforward with you about where your success plan stands and what we believe is the right next step.

We are tracking [n] milestone(s) as missed or at risk: [list milestone names briefly with one-line status each]. Together, these represent a meaningful deviation from the plan timeline.

This is not the trajectory we planned for, and we take our part in getting it back on course seriously.

Given the scope of the situation, we'd like to request a focused alignment conversation with [your sponsor / your leadership team] to review the full picture, agree on a revised path, and confirm continued commitment to [plan_objective].

[csm_name] will reach out directly to schedule this conversation.

Warm regards,
[csm_name]
```

Template C — Key Benefits Realized Insert (escalation context): Insert after opening context, before recovery plan:
```
It's also important to acknowledge what your team has already achieved: [benefit_1]. [benefit_2 if available]. These are real outcomes — and they are part of why getting the full plan back on track matters.
```

**Key Benefits Realized — Customer-Facing Language Guide**

Framing principles: Lead with the outcome the customer experienced, not the feature or activity. Use approximate quantification. Attribute to the customer's team. Keep each benefit to 1–2 sentences in the customer summary.

Transformation examples:

| CSM internal note | Customer-facing language |
|-------------------|--------------------------|
| "Reporting dashboard deployed" | "Your operations team no longer needs to manually compile weekly reports — the data is now available in real time." |
| "VP Sales confirmed pipeline visibility as a win" | "Your VP Sales has already seen the impact of real-time pipeline visibility — a direct result of the work your team put in to configure the dashboard." |
| "3 admins certified, no support tickets in 30 days" | "Your three admin users are fully self-sufficient. In the past 30 days, your team has resolved onboarding questions independently — a sign the training has taken hold." |

**QBR Pre-Work Note Template**

```markdown
## QBR Pre-Work Note

**Account:** [account_name]
**Review Date:** [review_date]
**CSM:** [csm_name]
**QBR Objective:** [One sentence on what the QBR is meant to accomplish.]

---

### Milestone Summary

| Status | Count |
|--------|-------|
| On Track | [n] |
| At Risk | [n] |
| Missed | [n] |

[If any milestones are At Risk or Missed, include a one-line summary of the most significant item(s).]

---

### Key Wins to Highlight

[List 2–4 concrete wins to lead with in the QBR. Drawn from key_benefits_realized and On Track milestones. Frame in customer value terms.]

- [Win 1 — specific and customer-outcome-oriented]
- [Win 2]
- [Win 3 if available]

---

### Open Risks and Discussion Items

[For each At Risk or Missed milestone: note the risk, why it matters, and what decision or action is needed from the customer.]

- **[Risk/item name]:** [Current state and why it matters]. Needed from customer: [specific ask.]

---

### Suggested Discussion Agenda

1. Celebrate progress and wins — [estimated time: 5 min]
2. Milestone status review — [estimated time: 10 min]
3. [Risk/open item 1] — discussion and decision — [estimated time: 10 min]
4. [Risk/open item 2 if applicable] — [estimated time: 5–10 min]
5. Revised plan / next 90 days — alignment and commitment — [estimated time: 10 min]

---

### Preparation Notes

[Internal context the CSM or account team needs before the QBR. Do not share with the customer.]
```

---

### Reasoning Blueprint

**Problem Classification Taxonomy**

**Type A — Routine Progress Review, Green Trajectory**
Characteristics: Milestones On Track; OCV outcomes In Progress or Delivered; success criteria trending toward met.
Primary Risk: Shallow review — a Green trajectory doesn't mean no action items; milestones running on schedule without substance still fail at the success criteria check.
Expert Focus: Even On Track reviews need a value story; confirm success criteria progress, not just milestone cadence.

**Type B — At-Risk Milestone Review**
Characteristics: One or more milestones At Risk; OCV outcomes Blocked or Not Started; success criteria status uncertain.
Primary Risk: Treating each risk independently — multiple At Risk milestones may share a root cause; addressing symptoms individually misses the systemic issue.
Expert Focus: Identify whether risks are independent or connected; the action list should address root causes, not just milestone-level symptoms.

**Type C — QBR Pre-Work Review**
Characteristics: Review generated in preparation for a QBR; customer-facing summary and QBR pre-work note required.
Primary Risk: Customer-facing summary is too internal — CSM action items, risk flags, and internal-only context should not appear in the customer summary.
Expert Focus: Hard separation between internal scorecard and customer-facing summary; QBR pre-work note should be executive-ready, not operational.

**Type D — Post-Missed-Milestone Review**
Characteristics: One or more milestones marked Missed; account may be at risk.
Primary Risk: Documenting the miss without actioning it — a Missed milestone note that only records what didn't happen provides no path forward.
Expert Focus: Every Missed milestone requires a clear action item: recovery plan, timeline adjustment, or escalation routing.

**Domain Heuristics**

H1 — Canvas Is the Authoritative Source: The upstream success plan canvas is the authoritative source for plan structure, success criteria, and milestone definitions. If the canvas is incomplete or outdated, the progress review inherits those gaps. Flag canvas quality issues before generating the review.

H2 — Milestone Scorecard Is Not the Final Product: The milestone scorecard is a component of the review, not the deliverable. The deliverable is the action list — what the CSM will do next based on the scorecard state.

H3 — OCV Outcome Ratings Drive the Value Narrative: OCV outcome ratings are the primary evidence for the value story at renewal. A review without OCV ratings is missing its strongest retention argument.

H4 — Customer-Facing Summary Has a Different Audience: The customer-facing summary is for executive stakeholders, not operational reviewers. It should lead with value delivered, not with process status. CSM internal notes and action items never appear in the customer-facing summary.

H5 — Point-in-Time Snapshot: The progress review is a snapshot artifact. It captures state at the time of generation and is not updated retroactively. If milestones change after generation, a new review must be generated.

H6 — Milestone Rating Guide Is Authoritative: Use the Milestone Rating Guide definitions and rating criteria. Do not apply subjective or informal rating criteria.

**Common Failure Modes**

Type A (Green Trajectory):
1. No action items for Green accounts — Review completed with no actions because "everything is fine." Fix: Even Green reviews should confirm executive sponsor engagement and surface the next value touchpoint.
2. Milestone status without substance — Scorecard shows On Track but no evidence of what's been accomplished. Fix: Each On Track milestone should have a progress note, not just a status.

Type B (At-Risk):
1. Independent treatment of connected risks — Three At Risk milestones attributed to three different causes when the root cause is one shared dependency gap. Fix: Assess risks for shared root causes before assigning action items.
2. Action items without owners — Risk action list has tasks but no assigned owner or timeline. Fix: Each action item requires a clear owner (CSM, AE, customer, CS Lead) and a target date.

Type C (QBR Pre-Work):
1. Internal content in customer summary — CSM notes, escalation flags, or internal ARR discussions appear in customer-facing output. Fix: Generate the internal scorecard first; strip all internal content before producing the customer summary.
2. QBR pre-work note is operational, not executive — Pre-work note lists tactical milestones rather than strategic value. Fix: QBR pre-work note leads with business impact and outcome delivery, not process status.

Type D (Missed Milestone):
1. Documentation without action — Missed milestone recorded with no recovery plan. Fix: Every Missed milestone requires an action item: recovery timeline, adjusted target, or escalation.
2. Missed milestone understated — Framed as a minor delay rather than a review signal. Fix: Missed milestones trigger a review of whether the success criteria timeline is still achievable; document that assessment.

**Expert Judgment Patterns**

Scope Decisions:
- If the upstream canvas is incomplete (missing milestones, absent success criteria), flag and request an updated canvas before generating the review.
- If all milestones are On Track but OCV outcomes are Not Started, flag the disconnect — schedule performance and outcome delivery are not the same thing.

Output Component Decisions:
- Customer-facing summary: generate only when explicitly requested or for QBR pre-work.
- QBR pre-work note: generate only for Type C reviews; lead with strategic value narrative.
- Internal scorecard and action list: always generated.

Risk Escalation Routing:
- At Risk + executive sponsor disengaged: route to CSM Lead.
- Missed milestone + ARR above threshold: route to AE.
- Multiple Missed milestones: trigger renewal readiness assessment.

Confidence Decisions:
- Milestone status from CSM-provided updates: moderate confidence — CSM self-report; verify against CS platform data where available.
- OCV outcome ratings from live catalog: high confidence.
- Success criteria met/not-met assessment from CSM judgment only: moderate confidence — document basis for the assessment.
