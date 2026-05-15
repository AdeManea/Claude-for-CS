# Advocacy Builder — Managed Agent Cookbook

**Lifecycle Stage:** Stage 6 — Advocacy
**Pipeline type:** Conditional branch (advocate-qualifier → reference-matcher OR story-builder)
**Subagents:** advocate-qualifier, reference-matcher, story-builder

---

## What This Cookbook Does

The Advocacy Builder orchestrates the SuccessCOACHING Stage 6 Advocacy workflow. It protects the
advocate pool through a two-tier burnout gate, then routes to one of two subagents based on the
type of advocacy ask.

**Inputs:**
- `account_name` — required
- `contact_name` — optional; if omitted, qualifier identifies the best-fit contact from the record
- `request_type` — required: `reference_call` | `case_study` | `testimonial` | `event_speaker`
- `prospect_profile` — required for `reference_call`; recommended for `event_speaker`; describes
  the prospect's industry, company size, use case, and pain points
- `urgency` — optional: `standard` (default) | `high`

---

## Pipeline Architecture

```
Orchestrator
  │
  ▼
advocate-qualifier
  │
  ├── HARD LIMIT HIT → Pipeline stops. No override.
  │
  ├── SOFT LIMIT HIT → Gate pause. CSM types PROCEED [rationale] or STOP.
  │
  └── QUALIFIED / CONDITIONAL (soft limits cleared) ──────────────┐
                                                                   │
                    ┌──────────────────────────────────────────────┘
                    │
                    ├── request_type: reference_call → reference-matcher
                    ├── request_type: event_speaker  → reference-matcher
                    │
                    ├── request_type: case_study     → story-builder
                    └── request_type: testimonial    → story-builder
                                                                   │
                    └──────────────────────────────────────────────┘
                                                                   │
                                                          Orchestrator packages output
                                                          + creates cs-platform task
```

---

## Burnout Protection

The advocate pool is a finite, relationship-dependent resource. Overburdening advocates
damages retention, suppresses future advocacy, and signals that SuccessCOACHING customers
are instruments rather than partners.

### Hard Limits (Non-Overridable)

These stop the pipeline regardless of urgency, deal size, or CSM rationale.

| Condition | Stop Reason |
|---|---|
| Account health At Risk or Critical | Advocacy ask during fragile health risks the relationship and the renewal |
| NPS < 7 (documented) | Negative advocate sentiment — any ask would be tone-deaf |
| ≥ 3 asks in last 180 days | Frequency ceiling — advocate is overloaded |
| Last call completion < 21 days ago | Rest period — minimum recovery window not elapsed |

No mechanism exists to override a hard limit. The orchestrator does not prompt for override
rationale and does not accept "PROCEED" for hard limit conditions.

### Soft Limits (CSM Gate)

These pause the pipeline. The CSM must type `PROCEED [rationale]` or `STOP`.

| Condition | Flag |
|---|---|
| No documented opt-in | CSM must confirm verbal opt-in before proceeding |
| Last ask 21–44 days ago | Prefer not recently asked; CSM confirms relationship can support another ask |
| Would be 2nd+ ask in 90 days | Frequency concern; CSM confirms timing is right |
| Consecutive declines ≥ 2 | Relationship check needed; CSM must personally check in before another automated ask |
| Account tenure < 6 months | Relationship may not be deep enough for advocacy commitment |
| Account health Developing (60–79) | Permitted but flagged; CSM confirms account is on an upward trajectory |

When a PROCEED rationale is provided, the orchestrator logs it in the cs-platform task
description so the advocacy request is traceable.

---

## Subagent Responsibilities

### advocate-qualifier
- Queries cs-platform for health score, NPS records, advocacy opt-in, and prior ask history
- Queries CRM for account contacts, tenure, and relationship depth indicators
- Applies hard limit check (stops pipeline if any hard limit is met)
- Applies soft limit check (gates if any soft limit is met)
- Computes qualification score across 4 dimensions (Health, Relationship Depth, Advocacy History, Satisfaction)
- Returns: qualification status, score, burnout record, recommended contact (if contact_name not provided)

### reference-matcher
- Receives: qualified advocate record, prospect_profile, request_type
- Queries cs-platform and CRM for advocate's documented wins, industry, use case, geographic context
- Scores prospect-advocate fit across 5 dimensions (Industry, Company Size, Use Case, Pain Point Resonance, Geography)
- Produces: match quality rating, recommended contact, 3–5 talking points, ask script draft for CSM
- Flags partial or poor matches rather than silently proceeding

### story-builder
- Receives: qualified advocate record, request_type (case_study | testimonial)
- Queries cs-platform for QBR records, CSM notes, documented outcomes
- Queries CRM for deal history, stated success criteria, executive sponsor contacts
- Produces output structured by story_type:
  - `case_study`: 4-section narrative (Before State, Why They Chose, The Journey, Results)
  - `testimonial`: 1–3 draft quote structures with mandatory CSM validation step
- Labels all evidence: [Measured] / [Documented] / [Estimated]
- Surfaces evidence gaps that marketing must fill before publication

---

## Connector Requirements

| Connector | Used By | Purpose |
|---|---|---|
| cs-platform | Orchestrator (write), all subagents (read) | Health scores, NPS, advocacy history, QBR records, CSM notes, task creation |
| crm | All subagents (read) | Account contacts, tenure, deal history, stated success criteria |

**No product-analytics connector.** Advocacy qualification is based on relationship quality
and documented history — not product telemetry.

---

## Environment Variables

```
CS_PLATFORM_MCP_URL   — URL for the cs-platform MCP server
CRM_MCP_URL           — URL for the CRM MCP server
```

---

## cs-platform Task Creation

After a successful pipeline run, the orchestrator creates a task in the account's cs-platform
success plan:

- **Task type:** Advocacy Ask
- **Task title:** `Advocacy Ask — [request_type] — [account_name]`
- **Assigned to:** `csm_name` (if provided); otherwise unassigned with a flag
- **Due date:** 5 business days from today
- **Description:** Qualification score, request_type, matched advocate contact (or story
  structure summary), soft limit rationale (if PROCEED was invoked), and next step for the CSM

If task creation fails, the orchestrator reports the failure explicitly and provides the
full task content for manual entry. It never silently skips task creation.

---

## What This Cookbook Does Not Do

- **Does not send outreach.** The ask script is for the CSM to use — the orchestrator does
  not contact the advocate directly.
- **Does not approve quotes.** Testimonial draft structures require CSM validation with the
  champion and legal review before use. The orchestrator flags this requirement explicitly.
- **Does not replace the relationship.** The orchestrator surfaces context and flags risks —
  the CSM owns the actual ask and the advocate relationship.
- **Does not override hard limits.** No urgency flag, no deal size, no escalation path
  overrides a hard burnout limit. The pipeline stops.
