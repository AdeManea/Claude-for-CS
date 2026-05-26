# Progress Review Schema

**File:** `reference/progress-review-schema.md`  
**Skill:** `csm:success-plan-progress-review`  
**Version:** 1.0.0  
**Purpose:** Canonical review record format — YAML frontmatter field definitions, section assembly order, field validation rules, auto-ID generation rules, and output artifact structure.

---

## 1. YAML Frontmatter Field Definitions

All progress review documents begin with a YAML frontmatter block. Fields are listed in canonical order.

### Required Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `review_id` | string | Yes | Auto-generated. Format: `REVIEW-[ACCT]-[YYYYMMDD]`. Immutable after creation. |
| `account_name` | string | Yes | Display name of the account. Set from `account_name` input. Immutable after creation. |
| `canvas_reference` | string | Yes | Path of the upstream canvas file used as the review baseline. Format: `context/success-plan-[safe_account]-[YYYY-MM-DD].md`. Immutable after creation. |
| `plan_id` | string | Yes | The `plan_id` value read from the upstream canvas frontmatter. |
| `review_date` | string | Yes | ISO date of the review. From `review_date` input or defaults to today. Format: `YYYY-MM-DD`. |
| `csm_name` | string | Yes | Name of CSM conducting the review. Display-only — never used in path construction. |
| `created_at` | string | Yes | ISO 8601 UTC timestamp of document creation. Auto-set. Format: `YYYY-MM-DDTHH:MM:SSZ`. Immutable after creation. |
| `created_by` | string | Yes | Identity of the skill operator (plugin user identity). Immutable after creation. |
| `milestone_summary` | object | Yes | Aggregate counts of milestone statuses. See sub-fields below. |
| `milestone_summary.on_track` | integer | Yes | Count of milestones with status `On Track`. |
| `milestone_summary.at_risk` | integer | Yes | Count of milestones with status `At Risk`. |
| `milestone_summary.missed` | integer | Yes | Count of milestones with status `Missed`. |

### Conditional Boolean Flags

These flags record which optional sections were generated. Always present in frontmatter regardless of value.

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `includes_key_benefits_realized` | boolean | `false` | `true` when `key_benefits_realized` input was provided and non-empty. |
| `includes_success_criteria_evaluation` | boolean | `false` | `true` when `success_criteria_status` input was provided. |
| `includes_customer_summary` | boolean | `false` | `true` when `include_customer_summary: true` was passed. |
| `includes_qbr_note` | boolean | `false` | `true` when `include_qbr_note: true` was passed. |

### Complete Frontmatter Example

```yaml
---
review_id: REVIEW-ACME-20260517
account_name: Acme Corp
canvas_reference: context/success-plan-acme-corp-2026-03-01.md
plan_id: CANVAS-ACME-20260301
review_date: 2026-05-17
csm_name: Jane Smith
created_at: 2026-05-17T14:00:00Z
created_by: todd@successhacker.co
milestone_summary:
  on_track: 2
  at_risk: 1
  missed: 0
includes_key_benefits_realized: true
includes_success_criteria_evaluation: true
includes_customer_summary: false
includes_qbr_note: true
---
```

---

## 2. Field Validation Rules

### `account_name`
- Must be non-empty string
- Accepted as provided; displayed verbatim in document output
- `safe_account` is derived from this value — see Section 4

### `review_date`
- Must be a valid ISO date string: `YYYY-MM-DD`
- If not provided, defaults to the current date at time of skill execution
- Cannot be in the future relative to the current date (validation warning — not a hard block)

### `canvas_date`
- Must be a valid ISO date string: `YYYY-MM-DD` if provided
- Used to locate a specific upstream canvas file; if omitted, the most recent canvas for the account is used

### `milestone_updates`
- Must be a non-empty list
- Each item must include: `milestone_name` (string, non-empty), `status` (one of `On Track`, `At Risk`, `Missed`), `notes` (string, may be empty)
- Status values are case-sensitive — use exact capitalization
- `milestone_summary` counts in frontmatter are derived from this list

### `ocv_updates` (optional)
- Each item must include: `outcome_name` (string, non-empty), `status` (one of `Delivered`, `In Progress`, `Not Started`, `Blocked`), `notes` (string, may be empty)
- If provided but empty list, OCV Outcome Status section is omitted
- Status values are case-sensitive

### `key_benefits_realized` (optional)
- Each item must be a non-empty string describing a concrete, customer-acknowledged benefit
- If provided and non-empty, `includes_key_benefits_realized` flag is set to `true`

### `success_criteria_status` (optional)
- Each item must include: `criterion` (string, non-empty), `met` (boolean — `true` or `false`), `notes` (string, may be empty)
- If provided, `includes_success_criteria_evaluation` flag is set to `true`

### `notes`
- Freetext string — no validation constraints
- Rendered verbatim into Notes section; never interpreted or executed

---

## 3. Section Assembly Order

Sections appear in the output document in this fixed order. Omitted conditional sections do not leave blank space — the next present section follows immediately.

```
1. Document Header (always)
   ├── H1 title: # Progress Review: [account_name] — [review_date]
   ├── CSM, Review Date, Canvas Reference metadata
   └── Horizontal rule

2. Progress Scorecard (always)
   ├── Milestone status table
   └── ### Key Benefits Already Realized (conditional — when key_benefits_realized provided)

3. OCV Outcome Status (conditional — when ocv_updates provided and non-empty)
   └── OCV outcome status table

4. Success Criteria Evaluation (conditional — when success_criteria_status provided)
   └── Success criteria table with met/not met

5. CSM Action List (always)
   └── Actions derived from At Risk and Missed milestones per milestone-rating-guide.md

6. Customer-Facing Summary (conditional — when include_customer_summary: true)
   ├── Customer-facing narrative
   └── Key Benefits Realized subsection (conditional — when key_benefits_realized provided)

7. QBR Pre-Work Note (conditional — when include_qbr_note: true)
   ├── Milestone summary line
   ├── Key Wins
   ├── Open Risks
   └── Suggested Discussion Agenda

8. Notes (always)
   └── CSM freeform notes, or empty section
```

---

## 4. Auto-ID Generation Rules

**Format:** `REVIEW-[ACCT]-[YYYYMMDD]`

**`ACCT` derivation from `account_name`:**
1. Strip all non-alphabetic characters from `account_name`
2. Take the first 4 characters of the resulting string
3. Uppercase

| `account_name` | Strip non-alpha | First 4 | Uppercased `ACCT` |
|----------------|-----------------|---------|-------------------|
| `Acme Corp` | `AcmeCorp` | `Acme` | `ACME` |
| `TechCo` | `TechCo` | `Tech` | `TECH` |
| `123 Systems Inc` | `SystemsInc` | `Syst` | `SYST` |
| `A1 Solutions` | `ASolutions` | `ASol` | `ASOL` |
| `XY` | `XY` | `XY` | `XY` (fewer than 4 chars — use as-is) |

**`YYYYMMDD` derivation:** ISO review date with hyphens removed.

**Full example:** Account `Acme Corp`, review date `2026-05-17` → `REVIEW-ACME-20260517`

---

## 5. `safe_account` Derivation Rules

Used exclusively for filesystem path construction.

**Algorithm (applied in order):**
1. Convert `account_name` to lowercase
2. Replace all characters that are not alphanumeric (`a-z`, `0-9`) with a hyphen (`-`)
3. Collapse any sequence of consecutive hyphens into a single `-`
4. Trim leading and trailing hyphens
5. Truncate to maximum 30 characters (truncate at character boundary — do not split mid-word if avoidable)

| `account_name` | `safe_account` |
|----------------|----------------|
| `Acme Corp` | `acme-corp` |
| `TechCo, Inc.` | `techco-inc` |
| `Global Health & Wellness Partners` | `global-health-wellness-par` (30 chars) |
| `123 Systems` | `123-systems` |

**`safe_account` is used only in:**
- Upstream canvas file path lookup: `context/success-plan-[safe_account]-[canvas_date].md`
- Output file path construction: `context/progress-review-[safe_account]-[review_date].md`

**`display_account` (the original `account_name` value) is used only in:**
- Document title: `# Progress Review: [display_account] — [review_date]`
- YAML frontmatter `account_name` field
- Console summary output

---

## 6. Immutable Fields Reference

These fields are set at document creation and must not be modified by any subsequent operation:

| Field | Set From | Rationale |
|-------|----------|-----------|
| `review_id` | Auto-generated at creation | Identity key — uniquely identifies this review artifact |
| `created_at` | System timestamp at creation | Audit timestamp — must reflect true creation time |
| `created_by` | Plugin user identity | Audit identity — who initiated the review |
| `canvas_reference` | Upstream canvas path at time of review | Source reference — must match the actual canvas read |
| `account_name` | Input `account_name` | Account identity — changing this would corrupt the audit trail |

---

## 7. Error Conditions

| Condition | Error Message | Behavior |
|-----------|--------------|----------|
| Canvas file not found | `Error: No success plan canvas found for [account_name]. Expected file: context/success-plan-[safe_account]-[YYYY-MM-DD].md. Run csm:success-plan-canvas [operation=generate] first.` | Halt — no partial output produced |
| `milestone_updates` missing or empty | `Error: milestone_updates is required and must contain at least one item.` | Halt — no partial output produced |
| Invalid milestone status value | `Error: Invalid milestone status "[value]" for milestone "[milestone_name]". Valid values: On Track, At Risk, Missed.` | Halt — no partial output produced |
| Invalid OCV outcome status value | `Error: Invalid OCV status "[value]" for outcome "[outcome_name]". Valid values: Delivered, In Progress, Not Started, Blocked.` | Halt — no partial output produced |
