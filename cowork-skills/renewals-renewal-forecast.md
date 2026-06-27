---
name: renewals-renewal-forecast
description: >
  Build a weighted renewal forecast for your book of business with scenario
  modeling (best / likely / worst), pipeline breakdown by stage, 90/60/30-day
  cohort analysis, and GRR/NRR projections against your configured targets.
  Pulls live CRM data when a connector is available; falls back to manual input.
  Every forecast output is flagged for Finance/RevOps review before distribution.
  Use --cohort to focus on a specific renewal window, --segment for a single
  customer segment, or --account to add one account's renewal to the pipeline view.

---

## Company Context

**Company:** AutogenAI — AI-powered proposal and bid writing platform for enterprise teams. Primary value metric: win rate improvement and proposal throughput.

**Role & Motion:** Enterprise CSM, CSM-led renewal (CSM owns end-to-end). Segment: Enterprise.

**Book of Business:** £1.2M total ARR, 10 accounts, ~£120K average deal size. NRR target: 120%. GRR target and logo retention target not set. Renewal cycle, outreach start, and decision lock are not yet configured — prompt in-session when needed.

**Tools:** CRM is HubSpot (configured). CS Platform is Planhat (not connected to Claude). Contracts in DocuSign (not connected). Comms via Outlook and Slack (configured). No CPQ or call recording configured.

**Churn Signals:** Primary escalation triggers are cost-per-active-user rising sharply (licences not converting to genuine users) and unresolved structural blockers (IT/security restriction, broken integration, poor-quality generation driving users back to ChatGPT or Microsoft Copilot). Early warnings include login decay (7–14 day no-login window), exec sponsor disengagement, and competitor displacement. Competitive threats: ChatGPT, Microsoft Copilot, manual Word/Google Docs workflows.

**Escalation:** Confirmed churn risk escalates to Manager of Customer Success or Chief Customer Officer via Slack or email. Discount authority and other escalation thresholds are not yet defined.

**Finance/RevOps review note:** ARR figures and NRR target (120%) carry revenue commitment implications — flag Finance/RevOps review before distributing any forecast outputs externally.

---

## Skill Instructions

# /renewals:renewal-forecast [VALIDATED]

Build a weighted renewal forecast calibrated to your book of business and targets.

---

## Use when
- You need to build a weighted renewal forecast for your book of business with scenario modeling and GRR/NRR projections
- You are preparing a renewal pipeline review for leadership, Finance, or RevOps and need a defensible likely/best/worst breakdown
- You need to assess the 30/60/90-day cohort and surface at-risk accounts requiring escalation before the decision window closes
- You want to add a single account to the pipeline view or check its contribution to GRR
- You are running a segment-level forecast to compare a specific customer segment against book targets

## Do NOT use for
- Individual account risk assessment — use `/renewals:risk-assessment` for per-account signal analysis and escalation routing
- Expansion signal identification or NRR pipeline qualification — use `/renewals:expansion-signal`; unqualified expansion is never included in this forecast
- Executive-level account summaries for stakeholder meetings — use `/renewals:executive-summary`
- Price increase planning for accounts in the pipeline — use `/renewals:price-increase-prep`
- Churn root cause analysis after a lost renewal — use `/renewals:churn-rca`

## Typical Activation
> `/renewals:renewal-forecast` — full book-of-business forecast with pipeline stages, cohort breakdown, scenario modeling, and GRR/NRR projection
> `/renewals:renewal-forecast --cohort 30` — focused view of renewals within the 30-day decision window with escalation requirements
> `/renewals:renewal-forecast --account Acme Corp` — add a single account to the pipeline view and check its GRR contribution

---

## Pre-flight

The company context and plugin configuration are embedded in the Company Context section above. Use those values directly. If key forecast fields are missing or marked as not configured (GRR target, renewal cycle, outreach start, decision lock), prompt the user in-session before proceeding with forecast work that depends on them.

Fields required for forecast:
- Total ARR managed, account count, average deal size — available above (£1.2M, 10 accounts, £120K)
- GRR target — not set; note in output
- NRR target — 120%
- Logo retention target — not set
- Renewal cycle, outreach start, decision lock — not yet configured; prompt in-session
- Pricing model and discount authority — not configured; prompt when a save strategy requires a discount check
- Customer segments — Enterprise

**G-code guardrails** referenced below (G2, G4, G5, G7) are defined inline in this skill. They are always active.

- **G2:** Expansion ARR is never included in GRR. It enters NRR only after economic buyer qualification AND formal pipeline stage — otherwise tagged `[early signal — not yet qualified]`.
- **G4:** Every at-risk account must have a named escalation owner, channel, and SLA. No generic "escalate to your manager." For AutogenAI: confirmed churn risk routes to Manager of Customer Success or Chief Customer Officer via Slack or email.
- **G5:** Forecast output containing ARR, scenario totals, or GRR/NRR projections requires Finance/RevOps review before distribution — flag with `[review — could be read as a revenue commitment]`.
- **G7:** Timestamp every data source. CRM >7 days old is stale — flag it. Manual input has no timestamp — note it. Never present undated figures as current.

---

## Reasoning Protocol

Before generating output, apply these primers:

1. **CLASSIFY**: What type of renewal forecast request is this?
   - **Full Book Forecast**: Complete book-of-business with all pipeline stages, scenario modeling, cohort breakdown, and GRR/NRR projection. Default mode. Optimize for completeness and cross-account consistency.
   - **Cohort-Scoped Forecast**: Focused on a single renewal window (90/60/30-day). Optimize for escalation readiness — 30-day accounts need named owners and decision-forcing actions.
   - **Segment Forecast**: Single customer segment against book targets. Validate whether default forecast weights apply to this segment's renewal dynamics.
   - **Single-Account Addition**: One account placed into the pipeline. Smallest scope — validate stage placement against risk signals before accepting user classification.

2. **CONSTRAINTS**: What limits the solution space?
   - G2: Expansion ARR is never included in GRR. It enters NRR only after economic buyer qualification AND formal pipeline stage — otherwise tagged `[early signal — not yet qualified]`.
   - G4: Every at-risk account must have a named escalation owner, channel, and SLA routed through the configured escalation matrix. No generic "escalate to your manager."
   - G5: Forecast output containing ARR, scenario totals, or GRR/NRR projections requires Finance/RevOps review before distribution — flag with `[review — could be read as a revenue commitment]`.
   - G7: Timestamp every data source. CRM >7 days old is stale — flag it. Manual input has no timestamp — note it. Never present undated figures as current.
   - No fabricated ARR: unknown ARR is excluded from totals, never estimated or interpolated.

3. **EXPERT CHECK**: What would a veteran renewals leader verify first?
   - Is the data fresh enough to forecast on? Check per-account staleness, not just the CRM pull timestamp — one stale account in the 30-day cohort can invalidate the escalation plan.
   - Do pipeline stages match observable signals? A "verbal commitment" with active churn signals is mislabeled — reclassify before weighting.
   - Is the likely scenario actually defensible for leadership? It should use default weights, zero unqualified expansion, and current save rates — not optimistic assumptions.

4. **ANTI-PATTERNS**: Common renewal forecasting mistakes to avoid:
   - Presenting a stale CRM pull as a current forecast without per-source freshness flags — the numbers look precise but the inputs are outdated.
   - Leaking expansion ARR into GRR calculations — expansion belongs in NRR only, and only when qualified.
   - Listing at-risk accounts without named escalation owners — a risk flag without an owner does not unblock the account.
   - Accepting user-stated pipeline stages without cross-referencing risk signals — "open" with active churn signals is "at risk."
   - Interpolating or estimating ARR when the figure is not provided — mark unknown and request it.
   - Applying book-level default weights to a segment with different renewal dynamics without flagging the assumption.

**After execution**, verify:
- Does the forecast answer the implicit question ("where does my book stand and what needs action this week")?
- Are all data sources timestamped and staleness-flagged per G7?
- Is every scenario total and GRR/NRR figure flagged for Finance/RevOps review?
- Are all at-risk accounts in the 30-day cohort paired with named escalation owners?
- Confidence: [High] if 2+ live sources corroborate / [Medium] if single-source or partially stale / [Low] if user-provided context only — state which.

## Mode

`--full` (default): Complete book-of-business forecast with all pipeline stages,
scenario modeling, cohort breakdown, and GRR/NRR projection against targets.

`--cohort 90|60|30`: Focus the forecast on renewals entering or within a specific
negotiation window. 90 = accounts where renewal is 61–90 days out; 60 = 31–60 days;
30 = within 30 days. Produces the full forecast structure scoped to that cohort.

`--segment <name>`: Forecast for a single customer segment (e.g., enterprise,
mid-market). Uses segment ARR and account count from config. Produces segment GRR/NRR
projections with comparison to overall book targets.

`--account <name/ID>`: Add a single account's renewal to the pipeline view.
Collects account ARR, current stage, risk signals, and renewal date, then places it
in the correct pipeline stage and cohort. Use when onboarding a specific account
to the forecast or checking its contribution to GRR.

---

## Data gathering

**Connector error categorization:** When a connector call fails, distinguish the error type before proceeding:
- **Rate-limited (transient):** Connector returns HTTP 429 or equivalent throttle signal. Note the rate limit explicitly in output ("CRM data temporarily rate-limited — retry in 60 seconds recommended") and offer to retry rather than proceeding with degraded output.
- **Unavailable (permanent for this session):** Connector is not configured, authentication has expired, or service is down. Fall back to the manual-input path below and label all affected sections as "connector unavailable — manual input used."
Do not conflate these — a rate-limited connector will return data shortly; an unavailable connector will not.

### Step 1 — Pull CRM data (if connector available)

If a CRM connector is live (HubSpot is configured for AutogenAI), attempt to pull:
- Open renewal opportunities: ARR, renewal date, stage, CSM owner, account name
- Won renewals this period: ARR, close date, expansion delta
- Lost / churned renewals this period: ARR, close date, reason if captured
- Contraction events: reduced ARR at renewal, reason if captured

Confirm the pull:
> "[CRM — HubSpot]: pulled [N] renewal opportunities · [N] won · [N] lost
> · data as of [timestamp]"

If the CRM pull returns no results, errors, or incomplete data:
> "[CRM call returned [N] records — expected approximately 10 based on your
> configured account count. Proceeding with what was returned. Manually add any
> accounts not captured below.]"

If no CRM connector is available:
> "No CRM connector is live. I'll build the forecast from the account context you
> provide. Tell me about your open renewals — account name, ARR, renewal date,
> and current stage (open / verbal / at risk). Or paste a pipeline export."

### Step 2 — Account input (manual or supplemental)

If CRM data is incomplete or unavailable, collect for each open renewal:
- Account name
- ARR at stake
- Renewal date (exact or estimated)
- Current stage (Open / Verbal commitment / At risk / Won / Lost)
- Key risk or expansion signals (brief — full risk detail belongs in risk-assessment)
- Notes (optional)

Accept input as a table, list, or freeform paste. Normalize to pipeline structure
before proceeding.

---

## Pipeline structure

Map all open renewals into five stages:

| Stage | Definition | Forecast weight (default) |
|-------|-----------|--------------------------|
| **Open** | Outreach initiated; no clear signal on direction | 70% |
| **Verbal commitment** | Customer has indicated renewal intent, no signature | 90% |
| **At risk** | Active churn signals; escalation in progress or needed | 25% |
| **Won** | Renewal executed; ARR confirmed | 100% |
| **Lost / Churn** | Non-renewal confirmed | 0% |

> Note: default weights above are starting points. If your configured methodology
> specifies different forecast weights, apply those instead.

If accounts have been escalated via `/renewals:risk-assessment`, import the risk
tier from that output to inform stage placement.

---

## Cohort breakdown

Group all open renewals by renewal date proximity:

| Cohort | Renewal date range | ARR in cohort | Account count |
|--------|-------------------|---------------|---------------|
| 30-day | Renewing within 30 days | £[sum] | [N] |
| 60-day | Renewing 31–60 days out | £[sum] | [N] |
| 90-day | Renewing 61–90 days out | £[sum] | [N] |
| 90+ day | Beyond 90 days | £[sum] | [N] |
| Won (period) | Closed this period | £[sum] | [N] |
| Lost (period) | Churned this period | £[sum] | [N] |

Flag the 30-day cohort prominently — these accounts are in the decision window now.
Any at-risk account in the 30-day cohort requires an escalation path named explicitly.

---

## Scenario modeling

Build three scenarios from the current pipeline:

### Best case
- All "verbal commitment" accounts renew at full ARR
- "Open" accounts renew at 85% (5% slip from verbal to open pool)
- "At risk" accounts: 50% save rate
- Any identified expansion signals convert at 30% [Low Confidence — requires
  qualification before including in forecast]
- Won ARR + best-case open + partial at-risk + partial expansion

### Likely case
- All "verbal commitment" accounts renew at full ARR
- "Open" accounts renew at forecast weight (default 70%)
- "At risk" accounts: 20% save rate
- No expansion included (expansion is a lead until qualified)
- This is your submit-to-leadership number

### Worst case
- "Verbal commitment" accounts: 90% renew (10% slip to at-risk)
- "Open" accounts: 50%
- "At risk" accounts: 0% save
- Won ARR only as certain

Present as:

| Scenario | Renewed ARR | GRR | vs. target |
|----------|------------|-----|-----------|
| Best case | £[amount] | [%] | [+/- vs. target] |
| Likely | £[amount] | [%] | [+/- vs. target] |
| Worst case | £[amount] | [%] | [+/- vs. target] |

> ⚠️ [review — could be read as a revenue commitment]
> All scenario figures are a working forecast, not a revenue commitment.
> Review with your Finance/RevOps contact before sharing with leadership
> or including in board materials.

---

## GRR and NRR projection

### GRR

```
GRR = (Starting ARR − Churn ARR − Contraction ARR) / Starting ARR × 100
```

Calculate for the period:
- Starting ARR (£1.2M from config or CRM pull)
- Known churn ARR (Lost stage + confirmed churns)
- Known contraction ARR (renewals at reduced ARR)
- Projected churn ARR from at-risk accounts (apply save rate per scenario)

Show for each scenario:
| Scenario | Projected GRR | Target | Gap |
|----------|--------------|--------|-----|
| Best | [%] | [not set — flag] | [+/-] |
| Likely | [%] | | |
| Worst | [%] | | |

### NRR

```
NRR = (Starting ARR − Churn − Contraction + Expansion) / Starting ARR × 100
```

Include expansion in NRR only if:
1. An economic buyer qualifying conversation has occurred, AND
2. The expansion has moved to formal pipeline (CPQ quote or opportunity stage)

Otherwise, tag expansion as `[early signal — not yet qualified]` and show NRR
with and without expansion:

| | Likely GRR | Likely NRR (no expansion) | NRR (with qualified expansion) |
|--|-----------|--------------------------|-------------------------------|
| Projected | [%] | [%] | [%] `[early signal — not yet qualified if applicable]` |
| Target | not set | 120% | |
| Gap | | | |

---

## At-risk spotlight

For any account in the "At risk" stage, surface in a named list:

| Account | ARR | Renewal date | Risk driver | Escalated? | Owner |
|---------|-----|-------------|-------------|-----------|-------|
| [name] | £[ARR] | [date] | [1-2 signals] | [Y / N] | [CSM / AE / Head of CS] |

For each at-risk account where no escalation is recorded:
> "⚠️ [Account] is at risk with no escalation owner named. Run `/renewals:risk-assessment`
> for a full signal review and escalation routing."

---

## Data freshness check

For every account in the pipeline, note data source and age:

If any CRM data is more than 7 days old:
> "⚠️ Data freshness: CRM data as of [date] — [N] days ago. Renewal dates, ARR,
> and stage may have changed. Verify the following before submitting this forecast:
> [list accounts with the most time-sensitive positions]."

If account data was provided manually:
> "Data source: [user provided] — no timestamp available. Confirm ARR and renewal
> date for at-risk and 30-day accounts before sharing this forecast."

---

## Output format

Structure the complete forecast as:

---

**Renewal Forecast — [Period / Cohort]**
*As of [date] · [N] accounts · £[total ARR at stake]*

**Pipeline Summary**
[Pipeline stage table]

**Cohort Breakdown**
[90/60/30-day table]

**Scenario Modeling**
[Best / Likely / Worst table with GRR]

**GRR/NRR Projection**
[Projection table against targets]

**At-Risk Spotlight**
[At-risk account table with escalation status]

**Recommended actions this week**
1. [Specific action for highest-priority account]
2. [Escalation needed for at-risk account with no owner]
3. [30-day cohort accounts needing decision conversation]

---

> ⚠️ Reviewer note
> - **Sources:** [CRM ✓ verified | manual input | [N] accounts from user-provided context]
> - **Data as of:** [CRM timestamp | N/A — manual input]
> - **Read:** [N renewal opportunities · [N] won · [N] at risk · contract data: not read]
> - **Flagged for your judgment:** [N items marked `[review]` inline | none]
> - **Before sharing:** Validate with Finance/RevOps before distributing to leadership
>   or including in board materials — this forecast contains language that could
>   be read as a revenue commitment `[review — could be read as a revenue commitment]`

---

## Security & Permissions

```
network:        read-only connector access — CRM reads for renewal pipeline data only;
                no external API writes, no web fetch
read_scope:     Company context embedded in skill file; HubSpot renewal opportunities,
                won/lost records scoped to the configured book of business (read-only)
write_scope:    none — all forecast output delivered to conversation; no file writes
subprocess:     none
dynamic_code:   none — no eval, no exec, no runtime code execution
```

This skill reads configuration and CRM pipeline data to build renewal forecasts. All output — pipeline summaries, scenario models, GRR/NRR projections — is delivered to the conversation only. No data is written to disk. Every forecast output is flagged for Finance/RevOps review before distribution.

## Trust & Verification

- **Revenue commitment integrity:** Every scenario total, GRR projection, and NRR figure carries the `[review — could be read as a revenue commitment]` tag. This tag is enforced in the Reasoning Protocol, scenario modeling section, and output format. It cannot be removed by configuration or user instruction.
- **GRR/NRR boundary:** Expansion ARR is never included in GRR calculations. Expansion enters NRR only when tagged as qualified pipeline (economic buyer conversation occurred + formal pipeline stage). Unqualified expansion signals are tagged `[early signal — not yet qualified]` and excluded from all GRR figures.
- **Pipeline weight integrity:** Default forecast weights (Open 70% / Verbal commitment 90% / At risk 25% / Won 100% / Lost 0%) are not modified by user instruction without explicit acknowledgment. If configured methodology specifies different weights, those take precedence — state which weights are applied.
- **Data freshness enforcement:** Every data source is timestamped. CRM data older than 7 days triggers a staleness flag. Manual input triggers a no-timestamp warning. No forecast output is presented without source freshness disclosure.
- **No fabricated ARR:** Unknown ARR is excluded from totals and marked `[ARR unknown — excluded from totals]`. The skill never estimates or interpolates ARR figures not provided by the user or a live tool call.
- **Free-text field handling:** Account names, notes, and pipeline context provided by the user are used for display and analysis only. They are not executed or used to derive file paths or system behavior.

---

## Guardrails

**Revenue commitment language.** Any scenario total, GRR projection, or NRR figure
in this output is a working forecast. It requires Finance/RevOps review before
distribution. Flag every forecast output with `[review — could be read as a
revenue commitment]`. This is a shared guardrail that cannot be overridden by
configuration or conversation.

**No expansion in GRR.** Expansion ARR is never included in GRR calculations.
If an expansion is included in NRR, it must be explicitly tagged as qualified
pipeline or labeled `[early signal — not yet qualified]`.

**At-risk accounts need an escalation path.** If an at-risk account appears in
the forecast without a named escalation owner, surface it. A risk flag without
an owner does not unblock the account.

**Data freshness is mandatory.** State the data-as-of timestamp for every account.
If CRM data is more than 7 days old, flag it. If data source is manual (user-provided),
note it.

**No fabricated ARR.** Do not estimate or interpolate ARR figures not provided
by the user or live tool call. If ARR is unknown for an account, request it or
mark the account as `[ARR unknown — excluded from totals]`.

**Discount authority check.** If a save strategy for an at-risk account requires
a discount, check whether it exceeds the configured discount authority. Discount authority is not yet defined for AutogenAI — flag any discount requirement as needing escalation approval and prompt the user to confirm the ceiling.

---

## After forecast

Suggested follow-on actions based on forecast results:

- At-risk accounts in 30-day cohort: `/renewals:risk-assessment`
- Negotiation prep for verbal commitment accounts approaching signature: `/renewals:negotiation-prep`
- Price increase accounts in the pipeline: `/renewals:price-increase-prep`
- Strategic account renewal summaries for leadership: `/renewals:executive-summary`
- Expansion signals in the pipeline: `/renewals:expansion-signal`

---

## Reference Material

### Reasoning Blueprint: Renewal Forecast

*Domain-specific taxonomy, heuristics, and expert judgment patterns for weighted renewal forecast construction.*

---

#### Problem Classification Taxonomy

**Type A: Full Book Forecast**
Characteristics: Complete book-of-business forecast with all pipeline stages, scenario modeling, cohort breakdown, and GRR/NRR projection. Default mode.
Primary Risk: Stale CRM data produces a confident-looking forecast built on outdated pipeline stages — the numbers look precise but the inputs are wrong.
Expert Focus: Validate data freshness per account before weighting; a 7-day-old "verbal commitment" may already be lost.

**Type B: Cohort-Scoped Forecast**
Characteristics: Focused on a single renewal window (90/60/30-day). Narrows the aperture to accounts in active negotiation.
Primary Risk: Treating 30-day cohort accounts with the same rigor as 90-day — the 30-day window demands escalation paths and decision-forcing actions, not pipeline projections.
Expert Focus: Every at-risk account in the 30-day cohort must have a named escalation owner or get flagged immediately.

**Type C: Segment Forecast**
Characteristics: Single customer segment (enterprise, mid-market, SMB). Compares segment GRR/NRR against overall book targets.
Primary Risk: Applying book-level default weights to a segment where the renewal motion differs — enterprise verbal commitments are more reliable than SMB ones.
Expert Focus: Check whether configured methodology provides segment-specific forecast weights; flag if defaults are applied to a segment with different dynamics.

**Type D: Single-Account Pipeline Addition**
Characteristics: Adding one account's renewal to the pipeline view. Smallest scope — collects ARR, stage, risk signals, and places the account.
Primary Risk: Accepting the user's stated stage without cross-referencing risk signals — an account described as "open" with active churn signals belongs in "at risk."
Expert Focus: Validate stage placement against available risk signals before accepting the user's classification.

**Secondary Dimension: Data Availability**
- CRM-Connected: Live pipeline data available; validate freshness and completeness.
- Manual Input: User-provided context only; no timestamp, higher fabrication risk — request ARR explicitly, never interpolate.

---

#### Domain Heuristics

1. **The Freshness Floor Rule**: Any CRM data older than 7 days is stale for forecasting. At-risk and 30-day accounts older than 3 days should be flagged for immediate refresh.

2. **The Expansion Exclusion Rule**: Expansion ARR never enters GRR. It enters NRR only after economic buyer qualification AND formal pipeline stage (CPQ quote or opportunity). Everything else is tagged `[early signal]`.

3. **The Likely-Is-Leadership Rule**: The "likely" scenario is the one you submit to leadership. Best case is aspiration; worst case is risk planning. Likely must be defensible — it uses default weights and zero expansion.

4. **The Escalation Completeness Rule**: A risk flag without a named escalation owner is an incomplete forecast item. Surface it as a recommended action, not just a pipeline annotation.

5. **The Stage-Signal Alignment Rule**: Pipeline stage must be consistent with observable signals. If a user says "open" but describes active churn signals, classify as "at risk" and explain the reclassification.

6. **The No-Fabrication Rule**: Unknown ARR is excluded from totals, never estimated. Mark the account `[ARR unknown — excluded from totals]` and request the figure.

7. **The Commitment Language Rule**: Every scenario total, GRR projection, and NRR figure gets flagged with `[review — could be read as a revenue commitment]`. No exceptions, even for internal-only output.

---

#### Common Failure Modes by Forecast Type

**Full Book Failures**
- Stale pipeline mirage: CRM data is old but presented without freshness flags. Fix: Timestamp every data source; flag >7-day CRM and >3-day CS Platform data in reviewer note.
- Expansion leakage into GRR: Expansion ARR accidentally included in gross retention math. Fix: Calculate GRR before NRR; verify expansion appears only in the NRR line with qualification tags.

**Cohort-Scoped Failures**
- 30-day cohort without escalation: At-risk accounts in the decision window listed without named owners. Fix: Run escalation completeness check on every at-risk account in 30-day cohort before presenting.
- Cohort boundary miscalculation: Account renewal dates placed in wrong cohort due to date math errors. Fix: Calculate days-to-renewal from today's date; double-check boundary accounts (exactly 30, 60, 90 days).

**Segment Failures**
- Default weight misapplication: Book-level weights applied to a segment with different renewal dynamics. Fix: Check configured methodology for segment-specific weights; flag when defaults are used for non-default segments.

**Single-Account Failures**
- Stage acceptance without validation: User-stated stage accepted when risk signals contradict it. Fix: Cross-reference stated stage against risk signals; if contradictory, propose reclassification with reasoning.
- Missing ARR interpolation: ARR not provided, so a figure is estimated from averages. Fix: Never interpolate. Mark `[ARR unknown — excluded from totals]` and request the actual figure.

---

#### Expert Judgment Patterns

**Scenario Calibration Decisions**
- When historical save rates are available, use them instead of defaults; note the substitution.
- When an account has been at-risk for >60 days with no escalation progress, apply 0% save rate in likely scenario regardless of default.
- Best-case expansion conversion at 30% is a ceiling, not a target — reduce if qualification signals are weak.

**Data Confidence Decisions**
- Two or more live sources corroborating = [High Confidence]; single source or partially stale = [Medium]; manual input only = [Low].
- When CRM and user-provided data conflict on ARR or stage, flag both and ask — never silently prefer one.

**Escalation Routing Decisions**
- At-risk + 30-day cohort = immediate escalation recommendation, not optional.
- At-risk + no discount authority for save strategy = flag the approval chain gap before presenting the save scenario.
- Multiple at-risk accounts from same segment = pattern signal; recommend segment-level review in addition to account-level actions.
