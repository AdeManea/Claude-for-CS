# Churn Intelligence — Stage 7 Managed Agent Cookbook

Managed agent cookbook for the **SuccessCOACHING Stage 7 Churn/Non-Renewal** workflow.
Invoked after a customer has given formal churn notice or confirmed non-renewal. Coordinates
four subagents to extract maximum learning from the loss, structure the exit interview,
facilitate the internal postmortem, and assess whether a win-back path exists.

---

## Workflow Position

```
S6 Advocacy → S7 Churn/Non-Renewal ← this cookbook
                      ↓
               Win-Back Gate
                 /         \
          Not Eligible    Eligible
                              ↓
                       S0 Pre-Onboard
                    (win-back handoff)
```

This workflow does **not** run during active retention efforts. Those belong to Stage 5
Retention. This workflow begins only after the save window is closed — the decision to
churn has been made.

---

## When to Invoke

**Invoke when:**
- A customer has given written notice of non-renewal
- A contract has ended without renewal (reactive invocation)
- A CSM has formally marked an account as churned in cs-platform

**Do NOT invoke when:**
- Active save efforts are still underway
- A customer has expressed churn risk but not yet confirmed non-renewal
- A retention offer or escalation is pending customer response

---

## Architecture

### Orchestrator

`churn-intelligence-agent` — coordinates the full Stage 7 pipeline. Pulls account context
from three connectors, dispatches subagents in the required sequence, assesses win-back
eligibility (this gate is not delegated), and writes the Churn Intelligence Report to
cs-platform before returning to the CSM.

### Subagents

| Subagent | Role | Dispatch Timing |
|---|---|---|
| `exit-interviewer` | Structures the exit interview guide; if a completed interview is provided, captures and organizes responses | Step 2 — parallel with postmortem-facilitator |
| `postmortem-facilitator` | Facilitates the internal blameless postmortem; maps failures to process and lifecycle stage | Step 2 — parallel with exit-interviewer |
| `learning-extractor` | Synthesizes both Step 2 outputs into categorized, tagged playbook learnings | Step 3 — sequential after Step 2 |
| `winback-profiler` | Profiles the account for re-entry potential; produces a Stage 0-compatible handoff record | Step 5 — conditional on win-back eligibility |

### Connector Access

All three connectors are required for this workflow. Usage decline data from product-analytics
is a core churn signal — it drives the Churn Signals Timeline and feature adoption regression
analysis.

| Connector | Role |
|---|---|
| `cs-platform` | Account health history, CSM notes, QBR records, escalation records, report write target |
| `crm` | Original deal context, account stage history, contacts with tenure, contract dates |
| `product-analytics` | 90-day usage trend, usage cliff indicators, last active session, feature adoption regression |

---

## Pipeline Sequence

```
Input: account_name, notice_date, contract_end_date
       [optional: contact_name, churn_reason, winback_eligible]

Step 1 — Context Pull (parallel, all 3 connectors)
  ├── cs-platform: health history, CSM notes, QBR records, escalation records
  ├── crm: deal notes, account stages, contacts, contract dates
  └── product-analytics: usage trend, cliff dates, last session, adoption regression

Step 2 — Parallel Dispatch
  ├── exit-interviewer (receives full Step 1 context)
  └── postmortem-facilitator (receives full Step 1 context)

Step 3 — Learning Extraction (sequential)
  └── learning-extractor (receives Step 2 outputs + Step 1 context)

Step 4 — Win-Back Eligibility Gate (orchestrator assesses)
  Not eligible: explicit false override | competitor lock-in | conduct breakdown |
                active legal dispute | customer requested no contact
  Eligible with timeline:
    budget/pricing → 9–12 months
    product gap → when gap is closed
    timing/switching costs → 6–12 months
    champion departure → when new leader identified
    unknown reason → low-confidence profile

Step 5 — Win-Back Profile (conditional)
  └── winback-profiler (if eligible — receives all prior outputs + context)

Step 6 — Compile and Write Report
  └── Write Churn Intelligence Report to cs-platform
  └── Write Stage 0 Handoff Record (if win-back eligible)

Step 7 — Return to CSM
  └── Structured summary with artifact paths and required next steps
```

---

## Report Structure

The Churn Intelligence Report written to cs-platform contains eight sections:

1. **Account Summary** — name, industry, size, tenure, notice date, contract end, primary contact
2. **Churn Signals Timeline** — chronological list of every risk signal sourced to a specific record
3. **Identified Churn Drivers** — primary driver + contributing factors; lifecycle stage mapping
4. **Exit Interview** — guide status and captured responses if interview was completed
5. **Internal Postmortem Summary** — key findings, process failures, known-vs-acted signal gap
6. **Playbook Learnings** — categorized learnings tagged with category, priority, and lifecycle stage
7. **Win-Back Assessment** — eligible/not eligible with rationale; Stage 0 handoff record status
8. **Required Next Steps** — CSM actions required before contract_end_date

---

## Win-Back Output

When win-back eligible, the `winback-profiler` produces two outputs:

1. **Win-back section** embedded in the main Churn Intelligence Report
2. **Stage 0 Handoff Record** — written separately to cs-platform; flagged for CS manager
   review before activation. Format is compatible with Stage 0 Pre-Onboard for re-entry.

The CS manager reviews and decides whether to activate the win-back motion. The orchestrator
does not initiate outreach or schedule calls — it produces the record and waits for
human decision.

---

## Behavioral Principles

**Learning workflow, not recovery workflow.** Save strategies, retention offers, and discount
proposals are out of scope. The save window is closed.

**Blameless framing throughout.** Postmortem findings map to process failures and lifecycle
stages — not individual CSM judgment or performance.

**Sourced signals only.** Every entry in the Churn Signals Timeline traces to a specific
record in cs-platform, crm, or product-analytics. Undocumented signals are flagged as
gaps, not included as findings.

**Write before return.** The Churn Intelligence Report is written to cs-platform before
the final response is delivered. If the write fails, the failure is surfaced explicitly
and the report is provided inline — it is never suppressed.

**No customer contact.** The exit-interviewer produces a guide and capture framework for
the CSM to use. The orchestrator does not generate customer-facing communications.

---

## Required Environment Variables

```bash
CS_PLATFORM_MCP_URL      # MCP server URL for cs-platform
CRM_MCP_URL              # MCP server URL for CRM
PRODUCT_ANALYTICS_MCP_URL  # MCP server URL for product analytics
```

---

## File Structure

```
churn-intelligence/
├── agent.yaml                              # Orchestrator manifest
├── README.md                               # This file
├── steering-examples.json                  # 5 example invocations
└── subagents/
    ├── churn-intelligence-agent.md         # Orchestrator system prompt
    ├── exit-interviewer.yaml               # Exit interviewer manifest
    ├── exit-interviewer.md                 # Exit interviewer system prompt
    ├── postmortem-facilitator.yaml         # Postmortem facilitator manifest
    ├── postmortem-facilitator.md           # Postmortem facilitator system prompt
    ├── learning-extractor.yaml             # Learning extractor manifest
    ├── learning-extractor.md               # Learning extractor system prompt
    ├── winback-profiler.yaml               # Win-back profiler manifest
    └── winback-profiler.md                 # Win-back profiler system prompt
```

---

## Related Cookbooks

| Cookbook | Stage | Relationship |
|---|---|---|
| `expansion-builder` | Stage 4 Growth | Accounts that churn without ever reaching expansion |
| `advocacy` | Stage 6 Advocacy | Advocates who subsequently churn — high-signal accounts |
| *(future) retention-playbook* | Stage 5 Retention | Pre-churn intervention; must complete before this workflow starts |
