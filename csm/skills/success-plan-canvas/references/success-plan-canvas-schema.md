# Success Plan Canvas Schema

**Reference for:** `csm:success-plan-canvas`  
**Version:** 1.0.0  
**Governs:** Canvas record format, YAML frontmatter fields, section structure, field validation, auto-ID generation, immutable field enforcement

---

## 1. File Naming Convention

```
context/success-plan-[safe_account]-[YYYY-MM-DD].md
```

### safe_account Derivation

The `safe_account` value is derived from `account_name` at generation time and is used exclusively for filesystem path construction. It is never displayed to the user in place of `account_name`.

**Derivation algorithm:**
1. Lowercase the full `account_name` string
2. Replace every non-alphanumeric character (including spaces, punctuation, special characters) with a hyphen `-`
3. Collapse consecutive hyphens into a single hyphen
4. Trim the result to a maximum of 30 characters

**Examples:**

| account_name | safe_account |
|-------------|-------------|
| `Acme Corp` | `acme-corp` |
| `123 Widgets Inc.` | `123-widgets-inc-` |
| `TechCo Solutions LLC` | `techco-solutions-llc` |
| `Müller & Associates` | `m-ller-associates` |
| `A Very Long Company Name That Exceeds Limits` | `a-very-long-company-name-t` (30 chars) |

**Validation rules:**
- Result must be non-empty after derivation
- Result must not exceed 30 characters
- Result must match pattern `[a-z0-9-]+`
- Leading or trailing hyphens are permitted (do not strip — they are a product of the algorithm)

---

## 2. YAML Frontmatter Fields

Every canvas file begins with a YAML frontmatter block containing the following fields:

```yaml
---
plan_id: CANVAS-ACME-20260517
account_name: Acme Corp
plan_type: initial
csm_name: Jane Smith
account_stage: Onboarding
created_at: 2026-05-17T09:00:00Z
created_by: todd@successhacker.co
refreshed_at: null
---
```

### Field Definitions

| Field | Type | Required | Mutable | Description |
|-------|------|----------|---------|-------------|
| `plan_id` | string | Yes | No | Auto-generated canvas identifier. Format: `CANVAS-[ACCT]-[YYYYMMDD]` |
| `account_name` | string | Yes | No | Original account name as provided by CSM. Used in document display only. |
| `plan_type` | string | Yes | No | Enum: `initial`, `expansion`, `renewal-refresh` |
| `csm_name` | string | Yes | Yes | CSM who owns this plan. Display-only. |
| `account_stage` | string | No | Yes | Customer lifecycle stage. Display-only. |
| `created_at` | string | Yes | No | ISO 8601 datetime of initial generation. Set by system. |
| `created_by` | string | Yes | No | Session user identity at time of generation. Set by system. |
| `refreshed_at` | string | No | Yes | ISO 8601 datetime of most recent refresh. Null if never refreshed. Updated by `refresh` operation. |

### Field Validation Rules

| Field | Validation |
|-------|-----------|
| `plan_id` | Must match pattern `CANVAS-[A-Z]{4}-[0-9]{8}`. Set at generate; reject any attempt to set via refresh. |
| `account_name` | Non-empty string. Reject immutable field error if included in refresh payload. |
| `plan_type` | Enum: exactly one of `initial`, `expansion`, `renewal-refresh`. Reject other values. |
| `csm_name` | Non-empty string. Display-only; no path construction use. |
| `account_stage` | Optional string. No enum constraint. Display-only. |
| `created_at` | ISO 8601 format. Set by system at generate. Immutable. |
| `created_by` | Non-empty string. Set by system from session user. Immutable. |
| `refreshed_at` | ISO 8601 format or null. Updated by system at refresh. Never set by CSM input. |

---

## 3. Auto-ID Generation

**Format:** `CANVAS-[ACCT]-[YYYYMMDD]`

### ACCT Derivation

`ACCT` = first 4 alphabetic characters extracted from `account_name`, uppercased, with all non-alpha characters stripped.

**Algorithm:**
1. Strip all non-alpha characters from `account_name`
2. Take the first 4 characters of the resulting string
3. Uppercase the result
4. If fewer than 4 alpha characters exist, use all available (minimum 1)

**Examples:**

| account_name | Alpha-only extraction | ACCT |
|-------------|----------------------|------|
| `Acme Corp` | `AcmeCorp` | `ACME` |
| `123 Widgets` | `Widgets` | `WIDG` |
| `TechCo` | `TechCo` | `TECH` |
| `AB Systems` | `ABSystems` | `ABSY` |
| `XY` | `XY` | `XY` (fewer than 4 available) |

### YYYYMMDD

Generation date formatted as 8-digit integer: `YYYYMMDD`.

- Set at `generate` time
- Preserved on `refresh` — the date in the ID always reflects the initial generation date

### Uniqueness

One canvas per account per date. If `generate` is called for the same account on the same date, the existing file is overwritten (not versioned). See Open Question OQ-1 in the design specification regarding a future `force: true` gate.

---

## 4. Section Structure by Plan Type

The sections present in a canvas document depend on `plan_type`. Section order is fixed as defined below.

### 4.1 `initial` — Initial Success Plan

Document header: `# Initial Success Plan: [account_name]`

Required sections (in order):

1. **Goals** — CSM-provided `key_objectives`; inferred from `account_stage` if not provided
2. **Onboarding Milestones** — Key onboarding milestones with target dates, owners, and status
3. **Responsibilities** — RACI or responsibility matrix for CSM, customer, and other stakeholders
4. **Success Metrics** — Quantifiable metrics aligned to committed OCV outcomes (if provided)
5. **Timelines** — High-level timeline or phased schedule
6. **Risks and Assumptions** — Known risks and assumptions made at plan creation
7. **Communication Strategy** — Cadence, channels, and escalation paths
8. **Next Steps** — Immediate action items with owners and dates

Optional sections (appended when inputs present):

- **OCV Outcomes** — Rendered if `ocv_snapshot` provided
- **Notes** — Rendered if `notes` provided; append-only on refresh

All 7 CCSM-104 components are present in `initial` plans. See `reference/plan-type-guide.md` for component-to-section mapping and placeholder guidance.

---

### 4.2 `expansion` — Expansion Canvas

Document header: `# Expansion Canvas: [account_name]`

The expansion canvas is a distinct artifact from the initial success plan. Its header branding, framing language, and section structure explicitly reflect an expansion motion rather than onboarding.

Required sections (in order):

1. **Expansion Rationale** — Why this expansion is being proposed; business case summary
2. **Current Outcomes** — OCV outcomes already delivered or in progress mapped to current state
3. **Expansion Opportunity** — Specific product areas, use cases, or seats being proposed
4. **Proposed Outcomes** — New OCV outcomes or expanded outcome commitments tied to expansion
5. **Responsibilities** — Stakeholder assignments for the expansion motion
6. **Success Metrics** — How expansion success will be measured
7. **Risks and Assumptions** — Risks specific to expansion (adoption, change management, budget)
8. **Communication Strategy** — Expansion-specific communication and alignment plan
9. **Next Steps** — Specific expansion deal progression steps with owners and dates

Optional sections:

- **OCV Outcomes** — Full OCV snapshot rendered if `ocv_snapshot` provided
- **Notes** — Rendered if `notes` provided; append-only on refresh

---

### 4.3 `renewal-refresh` — Renewal-Refresh Plan

Document header: `# Renewal-Refresh Plan: [account_name]`

Required sections (in order):

1. **Renewal Context** — Account renewal date, ARR, relationship summary
2. **OCV Gap Analysis** — Outcomes with `committed` or `in_progress` status and no `delivered` resolution; these are renewal-risk gaps (rendered when `ocv_snapshot` provided; omitted with note if not provided)
3. **Delivered Outcomes** — Outcomes with `delivered` status from OCV snapshot
4. **At-Risk Outcomes** — Gaps from OCV Gap Analysis surfaced as at-risk items with remediation notes
5. **Renewal Objectives** — CSM-provided `key_objectives` for the renewal motion
6. **Responsibilities** — Renewal stakeholder assignments
7. **Communication Strategy** — Renewal conversation plan, executive alignment, timing
8. **Next Steps** — Pre-renewal action items with owners and dates

Optional sections:

- **Notes** — Rendered if `notes` provided; append-only on refresh

Note: The `OCV Gap Analysis` and `Delivered Outcomes` sections are powered by `ocv_snapshot`. If no OCV snapshot is provided, these sections render a placeholder: `No OCV snapshot provided. Supply ocv_snapshot data to enable gap analysis.`

---

## 5. Immutable Field Enforcement

The following fields are set at `generate` time and cannot be modified via `refresh`.

| Field | Immutability Reason |
|-------|-------------------|
| `plan_id` | Identity key — must remain stable for cross-skill reference by `csm:success-plan-progress-review` |
| `created_at` | Audit timestamp — must reflect original creation time |
| `created_by` | Audit identity — must reflect original creator |
| `plan_type` | Determines document structure; changing post-create would corrupt section layout and downstream consumer behavior |
| `account_name` | Account identity — changing corrupts downstream file discovery via `safe_account` path derivation |

**Enforcement behavior:**

If a `refresh` payload contains any of these fields, the operation must respond with the error message for each offending field before processing any valid updates:

```
Immutable field error: [field_name] cannot be modified after generation.
```

If multiple immutable fields are present, report each on a separate line. After reporting errors, the operation does NOT proceed — it stops and returns only the error messages.

---

## 6. Complete Example Record

The following is a complete, valid canvas record illustrating all schema elements for an `initial` plan type.

```markdown
---
plan_id: CANVAS-ACME-20260517
account_name: Acme Corp
plan_type: initial
csm_name: Jane Smith
account_stage: Onboarding
created_at: 2026-05-17T09:00:00Z
created_by: todd@successhacker.co
refreshed_at: null
---

# Initial Success Plan: Acme Corp

**CSM:** Jane Smith
**Date:** 2026-05-17
**Stage:** Onboarding

---

## Goals

- Complete technical onboarding within 30 days
- Achieve first value milestone by day 45
- Establish executive sponsor alignment

## Onboarding Milestones

| Milestone | Target Date | Owner | Status |
|-----------|-------------|-------|--------|
| Technical onboarding complete | 2026-06-17 | Jane Smith | Not Started |
| First value milestone | 2026-07-01 | Acme Champion | Not Started |
| Executive sponsor alignment meeting | 2026-05-30 | Jane Smith | Not Started |

## Responsibilities

| Role | Name | Responsibility |
|------|------|---------------|
| CSM | Jane Smith | Plan coordination, milestone tracking |
| Champion | Sarah Lee | Internal adoption, change management |
| Executive Sponsor | Mark Torres | Strategic alignment, renewal authority |

## Success Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Onboarding completion | Day 30 | Technical checklist complete |
| First value milestone | Day 45 | Feature adoption threshold met |

## Timelines

| Phase | Start | End | Owner |
|-------|-------|-----|-------|
| Technical Onboarding | 2026-05-17 | 2026-06-17 | Jane Smith |
| Adoption Phase | 2026-06-17 | 2026-08-17 | Acme Champion |

## Risks and Assumptions

| Risk / Assumption | Type | Mitigation |
|-------------------|------|-----------|
| IT resource availability for onboarding | Risk | Confirm IT lead by 2026-05-20 |
| Champion availability assumed stable | Assumption | Identify backup champion |

## Communication Strategy

| Cadence | Format | Participants |
|---------|--------|-------------|
| Weekly | Sync call | Jane Smith, Sarah Lee |
| Monthly | Executive review | Jane Smith, Mark Torres |
| Ad hoc | Slack / Email | Jane Smith, Sarah Lee |

## Next Steps

| Action | Owner | Due Date |
|--------|-------|----------|
| Confirm IT lead for technical onboarding | Sarah Lee | 2026-05-20 |
| Schedule executive sponsor alignment meeting | Jane Smith | 2026-05-24 |

## OCV Outcomes

| Outcome | Status | Owner |
|---------|--------|-------|
| Reduce manual reporting time | committed | Jane Smith |
| Improve team visibility into pipeline | not_started | Jane Smith |

## Notes

Champion is Sarah Lee (VP Operations). Executive sponsor is Mark Torres (CFO). Kickoff completed 2026-05-15.
```
