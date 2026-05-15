# expansion-builder

**Managed Agent Cookbook — Stage 4: Growth**

Identifies expansion whitespace for healthy accounts, builds a customer-centric business case grounded in documented evidence, and produces an AE-ready handoff package. Part of the SuccessCOACHING claude-for-customer-success plugin ecosystem.

---

## Overview

The expansion-builder runs a sequential four-stage pipeline:

1. **Whitespace Analyzer** — Maps seat utilization, feature coverage, CRM signals, and documented customer requests to identify the highest-confidence expansion opportunity.
2. **Adoption Health Gate** — Evaluates the account's coverage score. Accounts scoring At Risk or Critical pause the pipeline and require explicit CSM override before continuing.
3. **Business Case Builder** — Constructs a customer-centric value narrative using documented outcomes and value projections, labeled by evidence quality.
4. **Expansion Handoff Coordinator** — Packages the AE briefing message, stakeholder map, timing recommendation, and CSM next actions; creates a task in the cs-platform success plan.

---

## When to Use

**Run when:**
- The account is in Stage 4 Growth with active usage and a stable champion relationship
- The CSM has observed signals suggesting expansion potential (seat ceiling, feature requests, new team interest)
- Renewal is 90–180 days out and expansion should be socialized before commercial negotiation begins
- A QBR or business review has surfaced a documented success that creates a natural expansion conversation

**Do NOT run when:**
- The account's coverage score is At Risk (40–59) or Critical (<40) and the CSM has no documented reason to override — run adoption-motion first
- The account is in active escalation or health recovery
- Renewal is fewer than 30 days out — the window for non-transactional expansion conversations has closed
- No AE is assigned — the handoff coordinator requires an AE recipient

---

## Adoption Health Prerequisite

The expansion-builder enforces a coverage score gate before proceeding to business case construction:

| Coverage Score | Status | Pipeline Behavior |
|---|---|---|
| 80–100 | Healthy | Proceeds automatically |
| 60–79 | Developing | Proceeds automatically |
| 40–59 | At Risk | **Pauses — CSM override required** |
| 0–39 | Critical | **Pauses — CSM override required** |

Coverage score formula: Core feature coverage % × 0.50 + Seat utilization % × 0.30 + Advanced feature coverage % × 0.20

If product-analytics is unavailable, coverage score cannot be calculated. The pipeline prompts the CSM to manually confirm account health before continuing.

**Override protocol:** A CSM override is documented in the expansion report with the override rationale. Overrides are not blocked — they are recorded.

---

## Pipeline Architecture

```
Input Parameters
      │
      ▼
┌─────────────────────┐
│  whitespace-analyzer │  ← cs-platform, product-analytics, crm
└──────────┬──────────┘
           │
           ▼
┌──────────────────────┐
│  Adoption Health Gate │  ← coverage score evaluation
│  (orchestrator logic) │
└──────────┬───────────┘
           │
    ┌──────┴──────┐
    │             │
  PASS          PAUSE
    │             │
    │         CSM override?
    │           YES │
    │             │
    ▼             ▼
┌──────────────────────┐
│  business-case-builder│  ← cs-platform, crm
└──────────┬───────────┘
           │
           ▼
┌────────────────────────────┐
│  expansion-handoff-         │  ← cs-platform (read + write), crm
│  coordinator               │
└──────────┬─────────────────┘
           │
           ▼
      Final Report
```

---

## Required Connectors

| Connector | Purpose | Required |
|---|---|---|
| `cs-platform` | Account record, success plan, QBR history, stakeholder records, task creation | Yes |
| `product-analytics` | Feature usage telemetry, seat utilization, DAU/WAU metrics | Yes (gate falls back to CSM input if unavailable) |
| `crm` | Renewal date, AE assignment, deal history, expansion signals, contacts | Yes |

---

## Configuration

Set the following environment variables before deployment:

| Variable | Description |
|---|---|
| `CS_PLATFORM_MCP_URL` | MCP endpoint URL for the cs-platform connector |
| `PRODUCT_ANALYTICS_MCP_URL` | MCP endpoint URL for the product-analytics connector |
| `CRM_MCP_URL` | MCP endpoint URL for the crm connector |

---

## Input Parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `account_name` | string | Yes | Account name as it appears in cs-platform |
| `deal_tier` | string | Yes | SMB / Mid-Market / Enterprise |
| `csm_name` | string | No | CSM name for action item attribution |
| `ae_name` | string | No | AE name; used in handoff briefing; pulled from CRM if not provided |
| `expansion_type_hint` | string | No | CSM-suggested expansion type: `seats`, `tier`, `module`, `cross-sell`, `new-team` |
| `target_expansion_sku` | string | No | Specific product SKU or module the CSM suspects is relevant |

---

## Output Format

The pipeline produces a four-section report:

**Section 1 — Expansion Whitespace Summary**
- Adoption Health Check result (coverage score, status, gate decision)
- Whitespace inventory across all five expansion types with signal strength ratings
- Recommended expansion type with rationale
- Conflict flag if expansion_type_hint contradicts evidence

**Section 2 — Business Case**
- Value Delivered to Date (labeled by evidence type: Measured / Documented / Estimated)
- Expansion Value Projection (type-specific framing)
- Business Case Narrative (3–5 sentence exec summary)
- Confidence Assessment (High / Moderate / Low)

**Section 3 — AE Handoff Package**
- CSM→AE Briefing Message (max 200 words, internal use only)
- Stakeholder Map
- Timing Recommendation with rationale
- Caution flags (if applicable)

**Section 4 — CSM Next Actions**
- Prioritized action list (max 5, completable within 5 business days)
- cs-platform task creation confirmation
- Re-assessment date (default: 30 days from run date)

---

## Expansion Types

| Type | Description | Primary Signals |
|---|---|---|
| Seats | Additional licenses for existing team | Seat utilization ≥80%, access requests, DAU/WAU growth |
| Feature/Module | Add-on capability not currently licensed | Documented feature requests for unlicensed capability |
| Tier Upgrade | Move to a higher product tier | Documented requests for tier-gated features |
| Adjacent Team | New department or business unit | Champion references peer team, CRM contacts in adjacent org |
| Cross-Sell | Complementary product line | AE-documented cross-sell opportunity, success plan gap |

---

## AE Handoff Protocol

The expansion-handoff-coordinator produces materials for internal CSM→AE communication only. No customer-facing commercial language is generated by this pipeline.

- AE briefing message is structured for internal Slack/email delivery (max 200 words)
- Commercial negotiation, pricing, and contracting are AE-owned — the pipeline does not enter that territory
- The CSM's role after handoff: facilitate introductions, provide customer-facing context, support AE-led conversations

---

## Known Limitations

- **Cross-sell whitespace** relies on AE-documented CRM records, not product catalog knowledge. Cross-sell signals not logged in CRM will not be surfaced.
- **Coverage score** is a lagging indicator — very recent usage changes (past 7 days) may not be reflected in the analysis window.
- **Stakeholder map** is sourced from cs-platform and CRM records only — unlogged contacts will not appear.
- **Value projections** labeled [Estimated] are directional indicators, not financial commitments. AEs should validate projections before using in commercial conversations.
