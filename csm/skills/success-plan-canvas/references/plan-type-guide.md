# Plan Type Guide

**Reference for:** `csm:success-plan-canvas`  
**Version:** 1.0.0  
**Governs:** CCSM-104 7-component framework, per-plan-type section templates, OCV integration rules by plan type, expansion canvas distinction, renewal-refresh gap detection logic, component presence rules

---

## 1. CCSM-104 7-Component Framework

The CCSM-104 framework defines the seven components that constitute a complete Customer Success Plan. These components are the structural foundation of all plan types in `csm:success-plan-canvas`, though their presence and framing varies by plan type.

| # | Component | Purpose |
|---|-----------|---------|
| 1 | **Goal Alignment** | Connects customer business objectives to the CS engagement; anchors all other components |
| 2 | **Milestones** | Time-bound checkpoints marking progress toward goals; enables progress tracking |
| 3 | **Responsibilities** | Defines who owns what; prevents accountability gaps across CSM, customer, and stakeholders |
| 4 | **Success Metrics** | Quantifiable measures of success; linked to OCV committed outcomes where applicable |
| 5 | **Timelines** | Phased schedule; connects milestones to calendar reality |
| 6 | **Risks and Assumptions** | Surfaces known risks and unstated assumptions at plan creation; enables proactive mitigation |
| 7 | **Communication Strategy** | Defines cadence, channels, escalation paths, and executive alignment mechanisms |

All 7 components are **required** in `initial` plans. `expansion` and `renewal-refresh` plans adapt component presence and framing to their specific motion.

---

## 2. Plan Type: `initial` — Initial Success Plan

### Purpose

The initial success plan establishes the foundational CS engagement for a new customer. It aligns stakeholders on goals, milestones, and responsibilities during the onboarding phase. It is the canonical "start of record" artifact for the customer's CS journey.

### Document Header

```
# Initial Success Plan: [account_name]
```

### Component Presence

| Component | Present | Section Name | Required |
|-----------|---------|-------------|----------|
| Goal Alignment | Yes | Goals | Required |
| Milestones | Yes | Onboarding Milestones | Required |
| Responsibilities | Yes | Responsibilities | Required |
| Success Metrics | Yes | Success Metrics | Required |
| Timelines | Yes | Timelines | Required |
| Risks and Assumptions | Yes | Risks and Assumptions | Required |
| Communication Strategy | Yes | Communication Strategy | Required |
| — | Yes | Next Steps | Required |
| OCV Outcomes | Conditional | OCV Outcomes | Present if `ocv_snapshot` provided |
| Notes | Conditional | Notes | Present if `notes` provided |

### Section Templates

**## Goals**
List the CSM-provided `key_objectives`. If none provided, prompt CSM to define objectives aligned to `account_stage`. Do not fabricate objectives.

```markdown
## Goals

- [Objective 1 from key_objectives]
- [Objective 2 from key_objectives]
- [Add additional objectives as needed]
```

**## Onboarding Milestones**
Structured table of milestones, target dates, owners, and status. Pre-populate with stage-appropriate milestone templates if no specifics are provided, noting they are placeholders.

```markdown
## Onboarding Milestones

| Milestone | Target Date | Owner | Status |
|-----------|-------------|-------|--------|
| Technical onboarding complete | [Date] | [Owner] | Not Started |
| First value milestone | [Date] | [Owner] | Not Started |
| Champion/sponsor alignment | [Date] | [Owner] | Not Started |
```

**## Responsibilities**
RACI-style or responsibility matrix. Always include CSM row. Add Champion and Executive Sponsor rows when names are available from context.

```markdown
## Responsibilities

| Role | Name | Responsibility |
|------|------|---------------|
| CSM | [csm_name] | Plan coordination, milestone tracking |
| Champion | [Name or TBD] | Internal adoption, change management |
| Executive Sponsor | [Name or TBD] | Strategic alignment, renewal authority |
```

**## Success Metrics**
Metrics aligned to committed OCV outcomes when OCV snapshot is provided. Otherwise, stage-appropriate generic metrics.

```markdown
## Success Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| [Metric name] | [Target value or date] | [How measured] |
```

**## Timelines**
High-level phased schedule.

```markdown
## Timelines

| Phase | Start | End | Owner |
|-------|-------|-----|-------|
| Technical Onboarding | [Date] | [Date] | [Owner] |
| Initial Adoption | [Date] | [Date] | [Owner] |
```

**## Risks and Assumptions**

```markdown
## Risks and Assumptions

| Risk / Assumption | Type | Mitigation |
|-------------------|------|-----------|
| [Risk description] | Risk | [Mitigation approach] |
| [Assumption description] | Assumption | [Validation step] |
```

**## Communication Strategy**

```markdown
## Communication Strategy

| Cadence | Format | Participants |
|---------|--------|-------------|
| Weekly | Sync call | [CSM], [Champion] |
| Monthly | Executive review | [CSM], [Exec Sponsor] |
| Ad hoc | Slack / Email | [CSM], [Champion] |
```

**## Next Steps**
Immediate action items with owners and dates. Always include at least one CSM-owned action item.

```markdown
## Next Steps

| Action | Owner | Due Date |
|--------|-------|----------|
| [Action item] | [Owner] | [Date] |
```

### OCV Integration for `initial`

OCV outcomes are informational in the initial plan. They document what outcomes the customer has committed to, setting a baseline for future progress review.

- List all outcomes from `ocv_snapshot` in the **OCV Outcomes** section
- Use status indicators from the OCV snapshot (`committed`, `in_progress`, `delivered`, `not_started`)
- Do not perform gap analysis in `initial` plans — that is reserved for `renewal-refresh`
- Reference committed outcomes in **Success Metrics** to show alignment

---

## 3. Plan Type: `expansion` — Expansion Canvas

### Purpose

The expansion canvas is a distinct artifact from the initial success plan. It is used for pre-expansion motions — when a CSM is building the business case and plan for expanding the customer's investment. The document framing is explicitly expansion-oriented, not onboarding-oriented.

### Expansion Canvas Distinction Rules

The expansion canvas must be visually and structurally distinct from the initial success plan:

1. **Header branding:** The document title MUST use "Expansion Canvas" — not "Success Plan" or "Initial Plan"
2. **Framing language:** All section content should frame the current engagement as established context and the expansion as the goal
3. **Separate artifact:** Expansion canvas files use the same naming convention (`context/success-plan-[safe_account]-[YYYY-MM-DD].md`) but are identified by `plan_type: expansion` in frontmatter
4. **No onboarding language:** Onboarding-specific language (e.g., "getting started", "first 30 days") is inappropriate in expansion canvases

### Document Header

```
# Expansion Canvas: [account_name]
```

### Component Presence

| Component | Present | Section Name | Required |
|-----------|---------|-------------|----------|
| Goal Alignment | Yes (as Expansion Rationale) | Expansion Rationale | Required |
| Milestones | Implicit (in Proposed Outcomes) | — | Embedded |
| Responsibilities | Yes | Responsibilities | Required |
| Success Metrics | Yes | Success Metrics | Required |
| Timelines | Implicit (in Next Steps) | — | Embedded |
| Risks and Assumptions | Yes | Risks and Assumptions | Required |
| Communication Strategy | Yes | Communication Strategy | Required |
| — | Yes | Expansion Opportunity | Required |
| — | Yes | Current Outcomes | Required |
| — | Yes | Proposed Outcomes | Required |
| — | Yes | Next Steps | Required |
| OCV Outcomes | Conditional | OCV Outcomes | Present if `ocv_snapshot` provided |
| Notes | Conditional | Notes | Present if `notes` provided |

Note: All 7 CCSM-104 components are represented, though some are embedded within expansion-specific sections rather than appearing as standalone sections.

### Section Templates

**## Expansion Rationale**
Why this expansion is being proposed. Connect to business outcomes already achieved (use OCV delivered outcomes if available) and the customer's next goal horizon.

```markdown
## Expansion Rationale

[Summary of business case for expansion. Reference delivered outcomes, growth signals,
and customer-expressed needs that motivate the expansion.]
```

**## Current Outcomes**
OCV outcomes already delivered or in progress. Maps the established baseline.

```markdown
## Current Outcomes

| Outcome | Status | Owner |
|---------|--------|-------|
| [outcome_name] | delivered / in_progress | [owner] |
```

If no OCV snapshot provided, render:
```markdown
## Current Outcomes

No OCV snapshot provided. Supply ocv_snapshot data to populate current outcome status.
```

**## Expansion Opportunity**
Specific product areas, use cases, seats, or capabilities being proposed.

```markdown
## Expansion Opportunity

[Description of what is being expanded — product features, seats, departments,
use cases, or service tiers. Be specific.]
```

**## Proposed Outcomes**
New OCV outcomes or expanded commitments tied to the expansion.

```markdown
## Proposed Outcomes

| Proposed Outcome | Success Measure | Target Date |
|------------------|----------------|-------------|
| [Outcome name] | [Measurable indicator] | [Date] |
```

**## Responsibilities**

```markdown
## Responsibilities

| Role | Name | Responsibility |
|------|------|---------------|
| CSM | [csm_name] | Expansion plan coordination |
| Champion | [Name or TBD] | Internal expansion advocacy |
| Executive Sponsor | [Name or TBD] | Budget and strategic approval |
| Sales | [Name or TBD] | Commercial terms and close |
```

**## Success Metrics**
How expansion success will be measured at 30/60/90 days post-expansion.

```markdown
## Success Metrics

| Metric | Baseline | Target | Timeline |
|--------|----------|--------|----------|
| [Metric] | [Current value] | [Target value] | [Date] |
```

**## Risks and Assumptions**
Expansion-specific risks: adoption, change management, budget, competitive.

```markdown
## Risks and Assumptions

| Risk / Assumption | Type | Mitigation |
|-------------------|------|-----------|
| Budget approval timeline | Risk | Confirm exec sponsor authority by [date] |
| Department adoption readiness | Risk | Change management plan required |
```

**## Communication Strategy**

```markdown
## Communication Strategy

| Cadence | Format | Participants | Purpose |
|---------|--------|-------------|---------|
| [Frequency] | [Format] | [Participants] | Expansion alignment |
| Pre-close | Executive briefing | [Exec Sponsor], [CSM] | Business case sign-off |
```

**## Next Steps**
Expansion deal progression steps.

```markdown
## Next Steps

| Action | Owner | Due Date |
|--------|-------|----------|
| Present expansion business case | [csm_name] | [Date] |
| Executive sponsor alignment | [Name] | [Date] |
| Proposal / commercial terms | Sales | [Date] |
```

### OCV Integration for `expansion`

OCV outcomes are central to the expansion narrative:

- **Current Outcomes section:** Render all `delivered` and `in_progress` outcomes from `ocv_snapshot`
- **Proposed Outcomes section:** If CSM provides `key_objectives`, map them as proposed outcomes; OCV outcomes with `not_started` status are candidates for proposed outcomes
- **Expansion Rationale:** Reference the `delivered` outcomes by name to establish credibility for the expansion
- Do not surface gaps in `expansion` plans — gap analysis is for `renewal-refresh`

---

## 4. Plan Type: `renewal-refresh` — Renewal-Refresh Plan

### Purpose

The renewal-refresh canvas is a pre-renewal review artifact. It surfaces delivered outcomes to support the renewal narrative and identifies OCV gaps (committed-but-not-delivered outcomes) as renewal-risk items requiring remediation before the renewal conversation.

### Document Header

```
# Renewal-Refresh Plan: [account_name]
```

### Component Presence

| Component | Present | Section Name | Required |
|-----------|---------|-------------|----------|
| Goal Alignment | Yes (as Renewal Objectives) | Renewal Objectives | Required |
| Milestones | Implicit (in At-Risk Outcomes) | — | Embedded |
| Responsibilities | Yes | Responsibilities | Required |
| Success Metrics | Implicit (in OCV Gap Analysis) | — | Embedded |
| Timelines | Implicit (in Next Steps) | — | Embedded |
| Risks and Assumptions | Implicit (in At-Risk Outcomes) | — | Embedded |
| Communication Strategy | Yes | Communication Strategy | Required |
| — | Yes | Renewal Context | Required |
| — | Yes | OCV Gap Analysis | Required (with placeholder if no OCV) |
| — | Yes | Delivered Outcomes | Required (with placeholder if no OCV) |
| — | Yes | At-Risk Outcomes | Required |
| — | Yes | Next Steps | Required |
| Notes | Conditional | Notes | Present if `notes` provided |

### OCV Gap Detection Logic

A gap is an outcome that has been committed but not yet delivered. Gaps represent renewal risk.

**Gap definition:**
An outcome from `ocv_snapshot` is classified as a **gap** when:
- `status` is `committed` OR `status` is `in_progress`
- AND the outcome does NOT have a `delivered` resolution (i.e., `status != delivered`)

**Not a gap:**
- `status == delivered` → delivered; render in Delivered Outcomes section
- `status == not_started` → not committed; note separately if materially significant

**Gap risk surface rule:**
All gaps must appear in both the **OCV Gap Analysis** section (as the detection report) and the **At-Risk Outcomes** section (as the remediation-planning section).

### Section Templates

**## Renewal Context**
Account-level renewal context.

```markdown
## Renewal Context

| Field | Value |
|-------|-------|
| Account | [account_name] |
| CSM | [csm_name] |
| Account Stage | [account_stage] |
| Renewal Date | [Date if known, or TBD] |
| Plan Date | [created_at date] |
```

**## OCV Gap Analysis**
Gap detection output. Only rendered when `ocv_snapshot` is provided.

```markdown
## OCV Gap Analysis

The following outcomes are committed or in progress with no delivered resolution.
These represent renewal-risk gaps requiring attention before the renewal conversation.

| Outcome | Status | Owner | Gap Risk |
|---------|--------|-------|----------|
| [outcome_name] | committed / in_progress | [owner] | ⚠ Gap |
```

If no `ocv_snapshot` provided:
```markdown
## OCV Gap Analysis

No OCV snapshot provided. Supply ocv_snapshot data to enable gap analysis.
```

If no gaps detected (all outcomes delivered):
```markdown
## OCV Gap Analysis

No gaps detected. All committed outcomes have been delivered. ✓
```

**## Delivered Outcomes**

```markdown
## Delivered Outcomes

| Outcome | Status | Owner |
|---------|--------|-------|
| [outcome_name] | delivered | [owner] |
```

If no `ocv_snapshot` provided:
```markdown
## Delivered Outcomes

No OCV snapshot provided. Supply ocv_snapshot data to populate delivered outcomes.
```

If no delivered outcomes:
```markdown
## Delivered Outcomes

No delivered outcomes recorded in the provided OCV snapshot.
```

**## At-Risk Outcomes**
Each gap from OCV Gap Analysis gets a remediation planning entry.

```markdown
## At-Risk Outcomes

| Outcome | Gap Status | Remediation Plan | Owner | Target Close Date |
|---------|-----------|-----------------|-------|------------------|
| [outcome_name] | committed / in_progress | [CSM action to close gap] | [Owner] | [Date] |
```

If no gaps detected:
```markdown
## At-Risk Outcomes

No at-risk outcomes. OCV gap analysis shows full delivery. ✓
```

**## Renewal Objectives**
CSM-provided `key_objectives` for the renewal motion.

```markdown
## Renewal Objectives

- [Objective 1 from key_objectives]
- [Objective 2 from key_objectives]
```

If no `key_objectives` provided:
```markdown
## Renewal Objectives

[Define renewal objectives. Common objectives: close outstanding OCV gaps,
align on next-year success criteria, confirm executive sponsor renewal intent.]
```

**## Responsibilities**

```markdown
## Responsibilities

| Role | Name | Responsibility |
|------|------|---------------|
| CSM | [csm_name] | Renewal coordination, gap closure |
| Champion | [Name or TBD] | Internal renewal advocacy |
| Executive Sponsor | [Name or TBD] | Renewal decision authority |
| Account Management | [Name or TBD] | Commercial terms |
```

**## Communication Strategy**

```markdown
## Communication Strategy

| Cadence | Format | Participants | Purpose |
|---------|--------|-------------|---------|
| Pre-renewal QBR | Executive briefing | [CSM], [Exec Sponsor] | Value review, renewal alignment |
| Gap closure check-in | Working session | [CSM], [Champion] | At-risk outcome remediation |
| Renewal close | Executive meeting | [CSM], [AE], [Exec Sponsor] | Contract finalization |
```

**## Next Steps**
Pre-renewal action items.

```markdown
## Next Steps

| Action | Owner | Due Date |
|--------|-------|----------|
| Schedule pre-renewal QBR | [csm_name] | [Date] |
| Develop gap closure plan for [outcome] | [csm_name] | [Date] |
| Confirm executive sponsor renewal intent | [csm_name] | [Date] |
```

---

## 5. Component Presence Summary

| CCSM-104 Component | `initial` | `expansion` | `renewal-refresh` |
|-------------------|-----------|-------------|-------------------|
| Goal Alignment | Goals (Required) | Expansion Rationale (Required) | Renewal Objectives (Required) |
| Milestones | Onboarding Milestones (Required) | Embedded in Proposed Outcomes | Embedded in At-Risk Outcomes |
| Responsibilities | Responsibilities (Required) | Responsibilities (Required) | Responsibilities (Required) |
| Success Metrics | Success Metrics (Required) | Success Metrics (Required) | Embedded in OCV Gap Analysis |
| Timelines | Timelines (Required) | Embedded in Next Steps | Embedded in Next Steps |
| Risks and Assumptions | Risks and Assumptions (Required) | Risks and Assumptions (Required) | Embedded in At-Risk Outcomes |
| Communication Strategy | Communication Strategy (Required) | Communication Strategy (Required) | Communication Strategy (Required) |

All 7 CCSM-104 components are present in all plan types. In `expansion` and `renewal-refresh`, some components are embedded within plan-type-specific sections rather than appearing as named standalone sections.
