# Adoption Motion Agent

Diagnoses feature adoption gaps and prescribes specific CSM interventions for Stage 2 accounts. Maps product surface coverage, classifies root-cause gap types, and outputs a TARO-based motion with a week-by-week execution plan.

---

## What the Agent Does

When a CSM is looking at a Stage 2 account with stalled or shallow adoption, the adoption-motion agent runs a three-stage diagnostic pipeline:

1. **Product Surface Analyzer** — Maps actual feature usage against the licensed product surface. Produces a coverage map (Core / Advanced / Integrations), depth signals for active features, and seat utilization metrics.

2. **Adoption Gap Identifier** — Diagnoses root cause using a six-type gap taxonomy: Skill, Awareness, Workflow Fit, Organizational, Technical, Engagement. Returns primary and contributing gaps with confidence and severity ratings.

3. **Motion Planner** — Prescribes a specific TARO play (Teach, Activate, Re-engage, Orchestrate) matched to the diagnosed gap type. Produces a week-by-week execution plan, success criteria, and escalation triggers.

A completed run delivers an Adoption Motion Report with four sections: Surface Coverage Summary, Gap Diagnosis, Prescribed Motion, and CSM Next Actions.

---

## Architecture

```
adoption-motion/
├── agent.yaml                              # Orchestrator manifest
├── README.md                               # This file
├── steering-examples.json                  # Invocation examples
└── subagents/
    ├── adoption-motion-agent.md            # Orchestrator system prompt
    ├── product-surface-analyzer.yaml       # Subagent manifest
    ├── product-surface-analyzer.md         # Subagent instruction body
    ├── adoption-gap-identifier.yaml        # Subagent manifest
    ├── adoption-gap-identifier.md          # Subagent instruction body
    ├── motion-planner.yaml                 # Subagent manifest
    └── motion-planner.md                   # Subagent instruction body
```

The orchestrator runs subagents sequentially: product-surface-analyzer → adoption-gap-identifier → motion-planner. Each subagent receives the outputs of the previous stage as context.

---

## Prerequisites

### Required Connectors

| Connector | Environment Variable | Purpose |
|---|---|---|
| cs-platform | `CS_PLATFORM_MCP_URL` | Account health scores, success plan, CSM notes |
| product-analytics | `PRODUCT_ANALYTICS_MCP_URL` | Feature usage telemetry, seat activation, DAU/WAU |
| crm | `CRM_MCP_URL` | Deal tier, contract term, stakeholder records |

### Optional Connectors

| Connector | Environment Variable | Purpose |
|---|---|---|
| support | `SUPPORT_MCP_URL` | Open tickets, repeated issues — corroborates technical gaps |
| lms | `LMS_MCP_URL` | Training completion — corroborates skill gaps |

The agent degrades gracefully when optional connectors are unavailable. See **Data Gap Behavior** below.

### Plugin Dependency

Requires the `csm` plugin to be co-located at `../../csm` relative to this cookbook directory. The csm plugin provides shared skill context for the orchestrator.

---

## Configuration

### Required Fields

| Field | Type | Description |
|---|---|---|
| `account_name` | string | Account name as it appears in the CRM and CS platform |
| `deal_tier` | string | SMB / Mid-Market / Enterprise — affects motion sizing and escalation thresholds |

### Optional Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `analysis_period_days` | integer | 30 | Rolling window for usage data (14, 30, or 90) |
| `csm_name` | string | — | Attributed CSM; included in the motion brief header |
| `specific_concern` | string | — | Freeform concern to steer the diagnosis (e.g., "team is not using the reporting module") |

### Coverage Thresholds

These thresholds determine the coverage score and At Risk / Critical classifications.

| Surface Tier | Healthy | Developing | At Risk | Critical |
|---|---|---|---|---|
| Core features | ≥ 80% | 60–79% | 40–59% | < 40% |
| Advanced features | ≥ 40% | 25–39% | 10–24% | < 10% |
| Seat utilization | ≥ 70% | 50–69% | 30–49% | < 30% |
| DAU/WAU ratio | ≥ 40% | 25–39% | 10–24% | < 10% |

Thresholds can be customized in `subagents/product-surface-analyzer.md` under the **Coverage Score** section.

---

## Scheduling

The adoption-motion agent is designed for on-demand invocation by a CSM or as a scheduled monthly scan for Stage 2 accounts.

### Suggested cron schedules

```
# Monthly scan — first Monday of each month at 8:00 AM
0 8 1-7 * 1

# Weekly scan for At Risk accounts — every Monday at 7:30 AM
30 7 * * 1

# On-demand via CLI — no cron; invoke directly with account context
```

When running as a scheduled scan, pass a portfolio segment (e.g., all Stage 2 accounts with health score below 70) rather than a single account name. The orchestrator handles batching.

---

## Output Reference

A completed adoption-motion run produces a four-section Adoption Motion Report.

| Section | Content |
|---|---|
| **Surface Coverage Summary** | Coverage map (Core / Advanced / Integrations), depth signals, seat utilization, overall coverage score (0–100) |
| **Gap Diagnosis** | Primary gap type with classification, evidence, confidence (H/M/L), severity (Critical/High/Medium/Low); contributing gaps table |
| **Prescribed Motion** | TARO play selection with rationale; week-by-week execution plan; success criteria checklist; escalation triggers |
| **CSM Next Actions** | Prioritized action list with owners and due dates; re-assessment date |

---

## Data Gap Behavior

| Missing Data | Agent Behavior |
|---|---|
| No product-analytics connector | Coverage map section is skipped; diagnosis proceeds from CSM notes, support tickets, and CRM data only; report flags: "Coverage map unavailable — product analytics not connected" |
| No crm connector | Deal tier defaults to Mid-Market with a flag; stakeholder section of motion brief is omitted |
| No cs-platform connector | Agent cannot retrieve health score or success plan; run is aborted with a clear error message and connector setup instructions |
| Partial usage data (< 14 days) | Analysis period is flagged as insufficient; report notes confidence is reduced; 30-day window recommended |
| Account not found in cs-platform | Agent returns a not-found error with the account name used in the query; CSM should verify the account name matches the cs-platform record |

---

## Customization

**Adjusting gap taxonomy:** The six gap types (Skill, Awareness, Workflow Fit, Organizational, Technical, Engagement) are defined in `subagents/adoption-gap-identifier.md`. Additional types can be added by extending the taxonomy table and the motion library mapping in `subagents/motion-planner.md`.

**Adjusting motion library:** The motion library in `subagents/motion-planner.md` maps gap types to motion types (training, feature intro, workflow workshop, exec re-engagement, technical remediation, CSM execution recovery). Duration and owner defaults can be modified for your CS team structure.

**Adjusting coverage thresholds:** Thresholds in `subagents/product-surface-analyzer.md` under the **Coverage Score** section reflect SuccessCOACHING standards. Adjust to match your product's usage benchmarks.

**Organizational gap escalation:** The motion-planner is hardcoded to escalate Organizational gaps at High or Critical severity to AE + CS Manager. This is a behavioral guardrail, not a threshold — it cannot be disabled via configuration. To change the escalation chain, edit the **Organizational Gap Protocol** section in `subagents/motion-planner.md`.

---

## Subagent Reference

| Subagent | Role | Required Connectors | Output |
|---|---|---|---|
| `product-surface-analyzer` | Maps feature coverage and seat utilization against the licensed product surface | cs-platform, product-analytics | Coverage map, depth signals, seat utilization table, coverage score (0–100) |
| `adoption-gap-identifier` | Diagnoses root cause of adoption gaps using a six-type taxonomy | cs-platform, crm | Primary gap classification, contributing gaps table, diagnostic profile statement |
| `motion-planner` | Prescribes TARO play and week-by-week execution plan matched to diagnosed gap | cs-platform, crm | Motion brief with TARO play, execution plan, success criteria, escalation triggers |

---

*SuccessCOACHING · Customer Success Infrastructure · Stage 2 Adoption Motion v1.0*
