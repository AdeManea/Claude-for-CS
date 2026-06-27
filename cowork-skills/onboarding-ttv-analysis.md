---
name: onboarding-ttv-analysis
description: >
  Time-to-value analysis for onboarding performance — single account or portfolio.
  Reads TtV targets by segment and milestone framework from your onboarding profile.
  Analyzes actual milestone completion dates against targets to assess pace, surface
  pattern signals, and produce recommendations for accounts that are behind. TtV
  outputs are always labeled as internal planning targets — never presented as
  commitments to customers. Use --account (default) for a single-account TtV
  assessment, --portfolio for a comparative TtV view across all closed or active
  onboarding accounts, or --patterns to identify common delay patterns across your
  book and recommend proactive interventions.

---

## Company Context

**Company:** AutogenAI — AI-powered proposal and bid writing platform for enterprise teams.

**Role:** Enterprise CSM. Team structure: Enterprise accounts have CSM + Onboarding PM + Implementation Consultant + Bid Consultant. Mid-Market and SME accounts are Implementation Consultant-led with handoff back to CSM.

**Segments and onboarding models:**
- Enterprise: white-glove, ~4 months to Onboarding PM handoff
- Mid-Market: implementation-plus-handoff, ~3 months
- SME: implementation-plus-handoff, ~2–3 months

**Milestone framework (Enterprise):**
- M1 — Kickoff + Discovery complete (Month 1): stakeholders confirmed, end users finalised, bid discovery done, Value Alignment Session complete, Triple Metric agreed
- M2 — Configuration complete (Month 1): platform live, document library set up, Academy access granted
- M3 — Adoption underway (Months 2–4): foundations training delivered, drop-in sessions running, monthly value reports started
- M4 — First Value / First Business Review (Month 3–4): progress reviewed, joint roadmap co-created, Triple Metric tracking confirmed
- M5 — Handoff ready (Month 5): implementation wrap-up complete, Onboarding PM exits, CSM takes ownership

**TtV targets:** [PLACEHOLDER — not yet defined. Run `/onboarding:cold-start-interview --section milestones` to configure. Until defined, use milestone day targets as proxy.]

**Success framework:** Triple Metric (Corporate: win rate, revenue, ROS/ROA; Business unit: technical score, operating margins, revenue per transaction; Project: time saved, user adoption, headcount efficiency).

**Primary churn signals:** Low platform adoption, low bid volume through tool, champion departure, competitive displacement.

**Tools:** HubSpot (CRM), Planhat (CS platform, transitioning from Asana), SharePoint (document storage), Slack and Outlook (comms).

---

## Skill Instructions

<!-- Status: [PROPOSED] -->

# /onboarding:ttv-analysis

Time-to-value performance analysis — internal planning use only.

---

## Pre-flight

The onboarding configuration and company profile are embedded in the **Company Context** section above. Use those directly — no config files need to be read from disk.

Fields available from embedded config:
- TtV targets by segment (Enterprise / Mid-Market / SME — the benchmarks against which actual performance is measured). Note: currently [PLACEHOLDER] — use milestone day targets as proxy.
- Milestone framework (month targets and completion criteria — required to calculate actual vs. planned performance at each milestone)
- Onboarding model (white-glove accounts have different TtV expectations than implementation-plus-handoff; analysis segments by model)

If TtV targets are `[PLACEHOLDER]`:
> "TtV targets aren't configured. Run `/onboarding:cold-start-interview --section milestones` to set segment-level targets before running TtV analysis — analysis will use milestone day targets as a proxy without segment-level benchmarks."

Proceed using milestone day targets as the reference if confirmed.

**G-code dependency:** All G-code guardrails referenced in this skill (G1–G9) are defined in the embedded config above. Core guardrails are restated in the Guardrails section below for completeness.

---

## Trigger Precision

**Use when:**
- Assessing TtV pace and trajectory for a single account mid-onboarding (`--account`)
- Generating a comparative TtV view ranked by performance across all active accounts (`--portfolio`)
- Identifying systemic delay patterns across your onboarding book for process improvement (`--patterns`)

**Do NOT use for:**
- Milestone status checks without TtV benchmarking (use `/onboarding:milestone-tracker`)
- Customer-facing progress reporting — TtV is an internal planning metric only; redirect to milestone-date language
- Handoff readiness assessment (use `/onboarding:handoff-doc --readiness`)

## Typical Activation
- "Is [Account] on track for TtV?"
- "Show me the portfolio TtV performance"
- "What patterns are causing delays across my book?"
- CSM runs `/onboarding:ttv-analysis [account] --account` to assess TtV pace for a single account mid-onboarding
- CSM runs `/onboarding:ttv-analysis --portfolio` to generate a comparative TtV view across all active accounts
- CSM runs `/onboarding:ttv-analysis --patterns` to identify systemic delay patterns across the book

---

## Reasoning Protocol

Before generating output, apply these primers:

1. **CLASSIFY**: What type of TtV analysis request is this?
   - **Single-Account Pace Assessment**: One named account, milestone data available, CSM wants to know if TtV is on track and what to do if behind. Optimize for per-milestone variance decomposition.
   - **Portfolio Comparative View**: Multiple accounts, ranked TtV performance across a book. All comparisons must be segment-normalized — never rank by raw days.
   - **Pattern / Cohort Analysis**: 5+ accounts with complete data, systemic question — which transitions delay most, which segments underperform, which blockers correlate. State sample size in every finding.
   - **Early-Warning Triage**: Account mid-onboarding with pace multiplier >1.15, CSM needs acceleration actions now. Lead with the recommendation, support with the data.

2. **CONSTRAINTS**: What limits the solution space?
   - G2: TtV figures are internal planning metrics — every figure, projection, and comparison carries `[review — internal planning target]`. If asked to surface in customer materials, redirect to milestone-date language.
   - G4: Acceleration recommendations are CSM-owned options, not instructions — present as choices for CSM judgment, never as directives.
   - G5: Portfolio output containing segment performance or account-level TtV comparisons requires confidentiality check before distribution beyond the CSM.
   - G7: Flag stale milestone completion data with source date and staleness indicator on all TtV figures. PM connector >7 days, CRM >3 days.
   - Projected TtV requires at least M1 completion — zero-milestone projections are target-based estimates, not pace-based projections. Label accordingly.

3. **EXPERT CHECK**: What would a veteran onboarding leader verify first?
   - Is the pace multiplier calculated from enough milestones to be meaningful? One milestone is a data point, not a trend — flag single-milestone projections explicitly.
   - Are early completions (negative variance >15%) verified against completion criteria, or could they be premature milestone closures masking data quality issues?
   - For portfolio and pattern modes: are comparisons segment-normalized? A 45-day SMB TtV and a 45-day Enterprise TtV are not equivalent outcomes.

4. **ANTI-PATTERNS**: Common mistakes to avoid:
   - ❌ Projecting TtV to the day from a single completed milestone — phantom precision that misleads planning.
   - ❌ Ranking portfolio accounts by absolute TtV days instead of variance from segment target.
   - ❌ Presenting pattern findings from <5 accounts without labeling them as directional only.
   - ❌ Recommending generic acceleration ("increase engagement") without tying to the specific delayed milestone and a named owner.
   - ❌ Treating negative variance as automatically good news without checking whether completion criteria were actually met.
   - ❌ Reporting cumulative variance without decomposing which milestone transition caused the delay.

**After execution**, verify:
- Does the output answer the implicit question ("is this account/portfolio on track, and what should I do about it")?
- Are all TtV figures tagged `[review — internal planning target]` without exception?
- Are data sources timestamped and staleness-flagged per G7?
- Confidence: [High] if 3+ milestones complete with consistent pace / [Medium] if 1-2 milestones or partially stale data / [Low] if zero milestones complete or user-provided context only — state which and why.

## Mode

`--account` (default): TtV assessment for one named account. Compares actual
milestone completion dates against planned dates to calculate TtV trajectory.
Produces a pace assessment, variance breakdown by milestone, and recommended
acceleration actions if behind.

`--portfolio`: Comparative TtV analysis across multiple accounts. Requires either
a PM connector with milestone completion data or a CSM-provided list of accounts
with actual completion dates. Produces a portfolio table ranked by TtV performance.

`--patterns`: Pattern analysis across all accounts with TtV data. Identifies which
milestone transitions produce the most delay, which customer segments or onboarding
models perform best, and which blocker types correlate most with TtV extension.
Requires at least 5 accounts with complete milestone data for meaningful output.

---

## Critical framing — every output

**TtV is an internal planning metric — not a customer commitment.**

Every TtV label, target, projection, and comparison in this skill's output carries
the tag `[review — internal planning target]`. These figures never appear in
customer-facing documents, customer communications, or shared onboarding plans. They
are diagnostic tools for the CSM and their management team.

This applies without exception. If a user asks to include TtV projections in a
customer-facing document, redirect:
> "TtV targets are internal planning benchmarks — they're not appropriate for
> customer-facing materials. The customer-facing plan shows milestone dates and
> completion criteria. I can help you frame progress in those terms instead."

---

## Account identification and data pull

### `--account` mode

Ask: "Which account? Provide the account name and, if possible, the actual completion
dates for any milestones that are done."

If PM connector available (Planhat), pull milestone task completion dates:
> "[PM]: [account name] · M1 complete [date] · M2 complete [date] · M3 in progress
> · data as of [timestamp]"

If CRM connector available (HubSpot), pull contract start date and segment:
> "[CRM]: contract start [date] · segment: [segment] · model: [onboarding model]"

If neither connector: "Tell me the account name, segment, contract start date, and
the actual dates each milestone was completed — I'll calculate TtV from that."

### `--portfolio` and `--patterns` modes

Requires data across multiple accounts. If PM connector available, pull all
onboarding projects with milestone completion dates. If not: "Provide a list or
spreadsheet with account names, segments, contract start dates, and milestone
completion dates."

---

## TtV calculation method

**TtV definition used here:** Number of days from contract start date to M4 First
value completion (the date the customer achieves their first measurable outcome).
M5 is the graduation milestone; M4 is the value milestone.

For accounts where M4 is not yet complete:
- **Actual TtV:** Not yet calculable — show projected TtV
- **Projected TtV** = contract start date + (M4 target day from config)
  adjusted for current pace (if milestones are running ahead or behind, project
  the adjustment forward)

**Pace calculation:**

For each completed milestone:
  Variance = actual completion date − planned date
  (positive = late; negative = early)

Cumulative variance = sum of all milestone variances to date.

Pace multiplier = (actual days elapsed / planned days elapsed at the same milestone)

Projected TtV = M4 day target × pace multiplier

If pace multiplier > 1.15 (more than 15% behind pace), flag as TtV at risk.
If pace multiplier < 0.85 (more than 15% ahead of pace), note as ahead of target.

---

## `--account` output

```
TtV Analysis — [Account Name]
[review — internal planning target]

Segment: [segment] · Model: [onboarding model] · CSM: [name]
Contract start: [date] · Segment TtV target: [X days]
Generated: [today]

Milestone Performance:

| Milestone | Planned date | Actual date | Variance | Status |
|-----------|-------------|-------------|----------|--------|
| M1: Kickoff + Discovery | [date] | [date] | [±N days] | Complete |
| M2: Configuration complete | [date] | [date or —] | [±N or —] | [status] |
| M3: Adoption underway | [date] | [date or —] | [±N or —] | [status] |
| M4: First Value (FBR) | [date] | [date or —] | [±N or —] | [status] |
| M5: Handoff ready | [date] | [date or —] | [±N or —] | [status] |

TtV Assessment [review — internal planning target]:
  Segment target:      [X days]
  Projected TtV:       [Y days]
  Variance:            [±Z days — ahead of target / on target / behind target]
  Current pace:        [pace multiplier]x — [interpretation]

[If projected TtV > segment target + 10%:]
⚠ TtV at risk — current pace projects [Y days], [Z days] past the [X-day] target.

Delay breakdown:
  Most significant delay: [milestone with highest positive variance] ([±N days])
  Root cause (if known): [blocker type from at-risk signals or CSM input]

Recommended acceleration actions:
  1. [Specific action with owner and deadline]
  2. [Specific action]
  3. [If applicable]
```

---

## `--portfolio` output

```
Portfolio TtV Summary [review — internal planning target]
Generated: [today] · [N] accounts included

| Account | Segment | Model | Projected TtV | Target | Variance | Status |
|---------|---------|-------|--------------|--------|----------|--------|
| [name] | Enterprise | white-glove | [X]d | [Y]d | [±Z]d | On target |
| [name] | Mid-Market | implementation-plus-handoff | [X]d | [Y]d | [+Z]d | ⚠ Behind |
| [name] | SME | implementation-plus-handoff | [X]d | [Y]d | [-Z]d | Ahead |

Portfolio summary:
  Median projected TtV:    [X days]
  Segment target (median): [Y days]
  Accounts ahead of target: [N]
  Accounts on target:       [N]
  Accounts behind target:   [N]

Accounts requiring attention (behind by >15%):
  [Account name] — [N days behind] — [most significant delay milestone]
  [Recommended action]
```

---

## `--patterns` output

Requires minimum 5 accounts with complete milestone data for meaningful analysis.
If fewer accounts are available:
> "[N] accounts have complete data — pattern analysis is most reliable with 5 or
> more. I'll surface what's observable, but treat these findings as directional
> rather than conclusive."

```
TtV Pattern Analysis [review — internal planning target]
Generated: [today] · [N] accounts analyzed

Data period: [earliest contract start] to [latest M4 completion]

1. MILESTONE VELOCITY PATTERNS

Milestone transitions ranked by average delay (highest to lowest):
  M2→M3: avg [X] days over target — [interpretation]
  M1→M2: avg [X] days over target
  M3→M4: avg [X] days over target
  M4→M5: avg [X] days over target

Fastest milestone: [M#] — avg [X] days under target

2. SEGMENT PERFORMANCE

| Segment | Accounts | Median TtV | Target | On-target rate |
|---------|----------|-----------|--------|---------------|
| Enterprise | [N] | [X]d | [Y]d | [N]% |
| Mid-Market | [N] | [X]d | [Y]d | [N]% |
| SME | [N] | [X]d | [Y]d | [N]% |

3. MODEL PERFORMANCE

| Model | Accounts | Median TtV | Target | On-target rate |
|-------|----------|-----------|--------|---------------|
| white-glove | [N] | [X]d | [Y]d | [N]% |
| implementation-plus-handoff | [N] | [X]d | [Y]d | [N]% |

4. BLOCKER CORRELATION (if blocker log data available)

Most common blocker types in accounts with TtV extension >20%:
  - [blocker type]: [N] occurrences
  - [blocker type]: [N] occurrences

5. PROACTIVE RECOMMENDATIONS

Based on the patterns above:
  1. [Specific proactive action targeting the highest-delay milestone transition]
  2. [Segment or model-specific recommendation]
  3. [Systemic recommendation if a pattern is addressable at the process level]
```

---

## Reviewer note (internal — all modes)

> ⚠️ Reviewer note
> - **Sources:** [Planhat connector ✓ | HubSpot ✓ | manual input]
> - **Data as of:** [timestamp]
> - **Config fields read:** TtV targets ([segment: X days]), milestone framework
>   ([M1 month 1, M2 month 1, M3 months 2–4, M4 months 3–4, M5 month 5])
> - **TtV calculation method:** contract start → M4 completion date
> - **Accounts with complete data:** [N] of [N] total
> - **Pace projection confidence:** [High — multiple milestones complete | Moderate —
>   early-stage projection | Low — only M1 complete]
> - **Data gaps:** [accounts missing contract start date or milestone completion dates]
> - **Flagged for your judgment:** [accounts requiring immediate attention | none]

---

## Output

Time-to-Value analysis output — format driven by flag (`--account`, `--portfolio`,
`--patterns`). Account mode: single-account TtV calculation with contributing
factors and acceleration recommendations. Portfolio mode: ranked table. Patterns
mode: cohort analysis with systemic findings. See mode-specific sections for
field-level structure.

---

## Security & Permissions

This skill operates read-only against configuration and connected MCP data sources.
No filesystem writes, no subprocess execution, no dynamic code execution.
All data access is through explicitly connected MCP connectors (Planhat, HubSpot); no outbound network calls are made directly.

## Trust & Verification

All TtV figures, projections, and comparisons are tagged `[review — internal planning target]` without exception — this tag is not removed for any output mode.
Portfolio outputs containing segment performance and account-level TtV comparisons require confidentiality check before sharing beyond the CSM (per G5).
Pace projections require at least M1 completion — zero-milestone projections are target-based estimates, explicitly labeled.
Acceleration recommendations are CSM-owned options, not directives — skill presents choices, CSM determines appropriateness.

## Guardrails

**TtV is always labeled — no exceptions.** Every TtV figure, projection, comparison,
and target carries the tag `[review — internal planning target]`. This tag is not
optional and is not removed for any output mode. If a user requests TtV framing in
a customer communication, redirect to milestone-date language.

**Projected TtV requires at least M1 completion.** A TtV projection based only on
the contract start date is not a projection — it is the target. At least one
milestone must be complete before pace can be calculated. Flag projections based
on zero completed milestones as target-based estimates, not pace-based projections.

**Portfolio comparison is segment-adjusted.** A 45-day TtV for an SME account is
not comparable to a 45-day TtV for an Enterprise account with a different target.
All portfolio comparisons are against segment-level targets, not absolute numbers.

**Pattern analysis requires caution with small sample sizes.** Fewer than 5 accounts
produce directional signals only. Always state the sample size and
flag when it is below the threshold for confident conclusions.

**Acceleration recommendations are CSM-owned.** The skill identifies what's causing
the delay and recommends actions. The CSM determines whether those actions are
appropriate given account context. Do not present acceleration recommendations as
instructions — present them as options for CSM judgment.

**Negative variance is not always good news.** An account completing milestones
faster than target should be noted, but also checked: is M4 being marked complete
prematurely? A milestone completed early without confirmed completion criteria is
a data quality issue, not a performance win. Flag early completions for verification.

---

## Reference Material

### Reasoning Blueprint: Time-to-Value Analysis

*For use when deeper analytical reasoning is needed.*

---

#### Problem Classification Taxonomy

**Type A: Single-Account Pace Assessment**
Characteristics: One named account, milestone completion data available, CSM wants to know if TtV is on track and what to do if not.
Primary Risk: Projecting pace from too few milestones — one completed milestone is a data point, not a trend.
Expert Focus: Decompose the variance by milestone transition, not just cumulative — a single late milestone masks whether the pattern is systemic or isolated.

**Type B: Portfolio Comparative View**
Characteristics: Multiple accounts, CSM or manager wants a ranked view of TtV performance across a book of business.
Primary Risk: Comparing absolute TtV numbers across segments with different targets — a 45-day SME TtV and a 45-day Enterprise TtV are not equivalent.
Expert Focus: Normalize all comparisons to segment targets; rank by variance percentage, not raw days.

**Type C: Pattern / Cohort Analysis**
Characteristics: 5+ accounts with complete data, request is systemic — which transitions delay most, which segments or models underperform, which blockers correlate.
Primary Risk: Drawing causal conclusions from small samples or confounded variables (segment and model often co-vary).
Expert Focus: State sample size, separate correlation from causation, and flag when segment/model overlap makes attribution unreliable.

**Type D: Early-Warning Triage**
Characteristics: Account is mid-onboarding, pace multiplier exceeds 1.15, CSM needs actionable acceleration options now.
Primary Risk: Recommending generic acceleration actions instead of targeting the specific milestone transition causing the delay.
Expert Focus: Identify the highest-variance milestone, match to blocker type from at-risk signals, and propose owner-specific actions.

---

#### Domain Heuristics

1. **The M1-to-M2 Tells You Everything Rule**: The transition from kickoff/discovery to configuration complete is the strongest predictor of total TtV. If M1-to-M2 exceeds target by >25%, the account will almost certainly miss overall TtV without intervention.

2. **The Pace Multiplier Floor Rule**: Never project TtV from a single completed milestone. One milestone gives you a data point; two give you a direction. Flag single-milestone projections as "target-based estimate, not pace-based projection."

3. **The Premature Completion Trap**: Milestones completed significantly ahead of schedule (>15% early) warrant a data quality check before celebrating. Was the completion criteria actually met, or was the task marked done prematurely?

4. **The Segment-Normalization Rule**: All portfolio comparisons use variance-from-target, never absolute days. A 60-day Enterprise TtV that's 10 days early is better than a 30-day SME TtV that's 5 days late.

5. **The Five-Account Threshold**: Pattern analysis below 5 accounts with complete data produces directional signals only. State sample size in every pattern output and explicitly label sub-threshold conclusions.

6. **The Blocker Stacking Rule**: When multiple milestone transitions are each slightly over target, the cumulative effect is worse than any single large delay. Flag accounts where 3+ transitions exceed target even if none exceeds it dramatically.

7. **The Internal-Only Anchor**: TtV figures are internal planning metrics. Any request to surface them in customer-facing materials is a redirect, not a refusal — reframe as milestone dates and completion criteria.

---

#### Common Failure Modes by Analysis Type

**Single-Account Failures**
- Phantom precision: Projecting TtV to the day when only M1 is complete.
  Fix: Label as "target-based estimate" and state the projection basis.
- Undifferentiated delay: Reporting cumulative variance without breaking down which milestone transition caused it.
  Fix: Always show per-milestone variance before the cumulative figure.

**Portfolio Failures**
- Absolute comparison: Ranking accounts by raw projected TtV days across different segments.
  Fix: Rank by variance percentage from segment target, not absolute days.
- Missing staleness: Presenting a portfolio table without per-account data freshness.
  Fix: Include data-as-of timestamp per account; flag accounts with stale data.

**Pattern Analysis Failures**
- Small-sample confidence: Presenting sub-5-account patterns as reliable findings.
  Fix: State sample size and label as "directional" when below threshold.
- Confounded attribution: Attributing delay patterns to segment when segment and onboarding model co-vary.
  Fix: Show segment and model breakdowns side by side; flag overlap.

**Early-Warning Failures**
- Generic acceleration: Recommending "increase engagement" without specifying which milestone, which blocker, and which owner.
  Fix: Tie every recommendation to a specific milestone variance and a named owner.

---

#### Expert Judgment Patterns

**Depth Decisions**
- Single-account with <3 milestones complete: keep brief, flag projection uncertainty prominently.
- Portfolio request from a manager vs. individual CSM: manager needs aggregate statistics first, then outliers; CSM needs their accounts sorted by urgency.

**Confidence Decisions**
- 3+ milestones complete with consistent pace: [High] confidence on projection.
- 1-2 milestones complete: [Medium] — label as directional, not definitive.
- Zero milestones complete: projection equals target — state this explicitly, do not present as calculated.

**Scope Decisions**
- When pattern analysis surfaces a systemic delay at one milestone transition, recommend process-level intervention (playbook change, resource allocation) not just per-account actions.
- When a single account is severely behind (pace >1.3x), shift from analysis to triage: lead with the acceleration recommendation, support with the data.

**Framing Decisions**
- Negative variance (ahead of target): verify data quality before praising — early completion without confirmed criteria is a risk, not a win.
- Accounts exactly on target: still worth noting — "on track" is a finding, not the absence of one.
