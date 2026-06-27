---
name: csm-health-score-review
description: >
  Structured health review for one account or a portfolio cohort — health signal
  breakdown, trend analysis, risk classification, and recommended actions. Use
  for weekly account reviews, portfolio triage, or when a health alert fires.
  Applies your configured health model components and thresholds.
argument-hint: "[account name | --portfolio] [--triage | --deep]"
version: "1.0.0"
---

## Company Context

**Company:** AutogenAI — AI-powered proposal and bid writing platform for enterprise teams. Primary value metric: win rate improvement and proposal throughput.

**Role:** Enterprise CSM. CS model: high-touch for enterprise, tech-touch for SMB/mid-market. Accounts per CSM: 25–50 enterprise accounts.

**Primary segment:** Enterprise. Top churn drivers: low platform adoption, low bid volume through tool, champion departure, competitive displacement.

**Health model (no CS Platform connected — manual signals):**
- Product/platform usage signals: 40%
- Executive sponsor engagement: 20%
- Support ticket volume/open escalations: 20%
- NPS/sentiment from calls: 20%
- Red threshold: 2+ concurrent churn signals active
- Yellow threshold: 1 churn signal active or engagement declining

**Connected integrations:** HubSpot CRM (verified), Glyphic AI call recording (verified), Microsoft 365 (verified). CS Platform (Gainsight/Totango/ChurnZero/Vitally): not connected — signals come from CRM and call recordings.

**Escalation matrix:**
- Red health account → manager → Slack/email → 24h
- At-risk renewal → Head of CS → Slack/pipeline review → 48h
- Executive escalation request → manager + AE → email thread → same day
- Product blocker causing churn risk → CS Ops + Product → shared Slack channel → 48h
- Expansion signal (qualified) → AE/AM → email + CRM opportunity → 48h

**Guardrail posture:** Health scores are heuristics, not verdicts. No silent data gaps. Every output states data-as-of timestamp. No escalation recommendation without a named path and owner.

---

## Skill Instructions

# /health-score-review

[PROPOSED]

## Use When
- Reviewing the health state of a specific account before a call or intervention
- Running portfolio triage to identify which accounts need attention this week
- A health signal has triggered (usage drop, NPS decline, support spike, champion departure)
- Preparing for QBR or renewal and need a structured health narrative
- Escalation routing requires a confirmed health classification before proceeding

## Do NOT Use For
- Real-time health score calculation — this skill interprets signals, it does not compute scores
- Replacing call-prep — use /csm:call-prep after health review for the full pre-call brief
- Generating the escalation memo — use /csm:escalation-memo once risk is confirmed
- Portfolio reporting for leadership — this skill is CSM-facing, not exec-facing
- Producing the structured risk memo or determining escalation routing once health review confirms an at-risk classification — use /csm:risk-flag

## Typical Activation
"/csm:health-score-review Acme Corp"
"/csm:health-score-review --triage"
"What's the health status of [account]?"
"Run a portfolio triage"
"Flag any at-risk accounts in my book"

---

Review account health using the configured health model — signal by signal, not just a color verdict.

---

## Reasoning Protocol

Before generating output, apply these primers:

1. **CLASSIFY** — Determine input type before proceeding:
- **A: Single Account — Data-Rich** — CS Platform connected, component scores and trends available
- **B: Single Account — Data-Sparse** — partial signals, user-provided data, missing components
- **C: Portfolio Triage** — multiple accounts, ranked prioritization needed
- **D: Alert-Triggered** — reactive review fired by health alert, usage drop, or escalation event
- **E: Pre-Renewal** — renewal within 90 days, proactive review with stakeholder visibility

2. **CONSTRAINTS** — Apply before generating any output:
- **G1:** Health scores are heuristics — never frame output as churn prediction or likelihood. Show components and evidence; classification is the CSM's judgment.
- **G2:** No churn signal marked "Present" without direct evidence. Absence of data = "Unknown," never "No."
- **G4:** No escalation recommendation without a named escalation path (person, channel, SLA). Flag missing path before proceeding.
- **G5:** Portfolio triage contains ARR and health classifications — run destination check before any output distributed beyond the CSM.
- **G7:** Flag any health data older than 30 days with source date and staleness indicator. Stale NPS (>6 months) treated as Unknown.

3. **EXPERT CHECK** — What a veteran CSM would verify first:
- Which single component is driving the overall score? (Score-anchoring trap: composite hides the signal)
- Is there a usage drop >20% in 30 days? (H1 — usage drops precede churn by 60-90 days)
- Has the executive sponsor changed? (H4 — sponsor departure overrides component scores)
- Are multiple moderate signals converging across independent components? (H7 — convergence > single strong signal)
- For renewals <90 days: apply renewal proximity amplifier (H6 — Yellow becomes Red-tier urgency)
- For portfolio: is ranking compound (health x ARR x renewal proximity), not health-only?

4. **ANTI-PATTERNS** — Mistakes to avoid:
- Reporting composite score without component decomposition
- Classifying Red/Yellow/Green without naming which components are missing or stale
- Flat portfolio ranking by health score alone (ignores ARR and renewal proximity)
- Investigating only the triggering alert signal without full component scan
- Treating days-since-contact as standalone metric (H3 — weight by current health tier)
- Producing generic interventions ("monitor closely") instead of specific who/what/when actions

**After execution**, verify:
- Does the output answer the actual request (single review, triage, deep dive)? Is mode selection appropriate?
- Check against classification-specific failure modes in the Reference Material section below. For Type B: are all gaps named? For Type C: is ranking compound? For Type E: was optimism bias checked?
- How many components have live data? Flag partial-data classifications. Any component >30 days old flagged in reviewer note?
- Confidence: [High] if CS Platform live with all configured components / [Medium] if partial integrations or some stale data / [Low] if user-provided context only — state which.

## Mode

**Single account review:** Default. Produce a full health breakdown for one account.

`--triage`: Portfolio triage — rapid scan of all accounts in the book; surface Yellow and Red accounts only; no deep analysis per account. Returns a prioritized list with top risk factor per account.

`--deep`: Full account analysis — health signal breakdown, trend direction over 30/60/90 days, root cause hypothesis, and recommended intervention.

`--portfolio`: Alias for `--triage`.

---

## Data gathering

**Connector error categorization:** When a connector call fails, distinguish the error type before proceeding:
- **Rate-limited (transient):** Connector returns HTTP 429 or equivalent throttle signal. Note the rate limit explicitly in output ("CRM data temporarily rate-limited — retry in 60 seconds recommended") and offer to retry rather than proceeding with degraded output.
- **Unavailable (permanent for this session):** Connector is not configured, authentication has expired, or service is down. Fall back to the manual-input path below and label all affected sections as "connector unavailable — manual input used."
Do not conflate these — a rate-limited connector will return data shortly; an unavailable connector will not.

### Single account

Pull from connected integrations per configured profile:
- CS Platform: current health score, component breakdown, lifecycle stage, CTAs
- Product data / CRM: usage metrics (DAU, feature adoption, last login)
- Support: open tickets, P1/P2 history, ticket velocity
- Call recording: engagement frequency, last call date, attendees
- NPS: most recent score and date

Fallback if nothing connected:
> "I need health signal data for this account. Share what you have — a CS Platform export, recent usage numbers, last call date, or NPS score. Even partial data produces a more useful review than a blank sheet."

### Portfolio triage

Pull account list from CRM or CS Platform. For each account: current health score (or last known), days since last contact, renewal date.

If CRM is not connected:
> "For portfolio triage, paste your account list with current health status, ARR, and renewal dates. I'll prioritize from there."

---

## Single account health review

---

**Health Review — [Account Name]**
*[Date] · [CS motion] · [Segment]*

---

### Health summary

| Component | Weight | Signal | Direction | Score contribution |
|-----------|--------|--------|-----------|-------------------|
| [Component 1: e.g., Product usage] | [configured %] | [current value] | [↑↓→ vs 30-day] | [Red/Yellow/Green for this component] |
| [Component 2: e.g., Engagement] | [configured %] | [last contact: N days] | [↑↓→] | |
| [Component 3: e.g., Support] | [configured %] | [N open tickets / P1: Y/N] | [↑↓→] | |
| [Component 4: e.g., NPS] | [configured %] | [score / date] | [↑↓→] | |
| [Additional configured component] | | | | |

**Overall health:** [Red / Yellow / Green]
*Threshold applied: Red = [configured], Yellow = [configured]*

If no CS Platform configured and components are not defined: use the manual signal prompts from the company profile (what signals make you worried). Classify as [At Risk / Watch / Healthy] based on signals present.

---

### Signal narrative

3-5 sentences interpreting the component breakdown. Do not just restate the table.

> "The account's product usage component is the primary concern — weekly active users dropped 22% over 30 days, which puts this below the configured Yellow threshold. Engagement is holding: the last call was [N] days ago, within the [configured threshold] for [high-touch/tech-touch]. Support load increased (3 open tickets vs. 1 last month), but none are P1/P2. NPS is from [N] months ago — stale enough to flag for update."

Language always shows components and evidence — not a verdict like "this account is likely to churn."

---

### Churn signals present

From configured churn signal definitions:

| Signal | Present | Evidence | Weight |
|--------|---------|----------|--------|
| Executive sponsor departure | [Yes / No / Unknown] | [context] | High |
| Product usage drop >20% | [Yes / No] | [% change] | High |
| Open escalation or P1 ticket | [Yes / No] | [ticket details] | High |
| NPS detractor + no recovery | [Yes / No] | [score + date] | Medium |
| Missed QBR or no-show pattern | [Yes / No] | [last attended: date] | Medium |
| Competitor evaluation underway | [Yes / No / Unknown] | [source] | High |
| No contact in >[threshold] days | [Yes / No] | [last contact: date] | Medium |

Flag each present signal as `[review]` with the specific evidence.

---

### Risk classification

Based on configured thresholds and signals present:

**Classification:** [Red — immediate intervention / Yellow — monitor and engage / Green — healthy / Unknown — data insufficient]

**Escalation trigger:** [Does this account meet the configured escalation threshold?]
If yes: "Route per matrix — [escalation route from config] within [SLA]."
If no: "Below escalation threshold. CSM-managed watch."

---

### Trend analysis

If historical data is available (CS Platform or prior account-research sessions):

| Metric | 90 days ago | 60 days ago | 30 days ago | Today | Trend |
|--------|------------|------------|------------|-------|-------|
| [Health score or primary metric] | | | | | [↑↓→ and rate] |
| [Usage metric] | | | | | |

If trend data is unavailable: "Trend analysis not available — no historical signal data retrieved this session. Point-in-time assessment only."

**Trend interpretation:** Is the account stable, improving, or declining? Name the direction and the rate of change. A slow decline is different from a sudden drop.

---

### Recommended interventions

Calibrate to the risk classification and configured CS motion.

**Red account:**
1. [Specific action with timeline] — e.g., "Escalate to [route from matrix] within [SLA]"
2. [Specific outreach action] — e.g., "Request executive sponsor check-in within 48h"
3. [Recovery action] — e.g., "Run risk-flag to prepare structured memo: `/csm:risk-flag [account]`"

**Yellow account:**
1. [Specific monitoring action] — e.g., "Proactive check-in call within 7 days focused on [specific signal]"
2. [Prevention action] — e.g., "Engage champion on usage drop — ask what changed"
3. [Escalation prep] — e.g., "If [specific trigger] occurs, escalate per matrix"

**Green account:**
1. [Growth action] — e.g., "Schedule QBR if >90 days since last one"
2. [Expansion awareness] — internal note only, tagged `[early signal — not yet qualified]`

Do not produce generic interventions like "monitor closely" or "improve engagement." Every recommendation is specific — who, what, when.

---

## Portfolio triage (`--triage`)

Return a ranked list — highest risk first.

---

**Portfolio Health Triage**
*[Date] · [N accounts reviewed]*

---

**Red accounts — immediate action:**

| Account | ARR | Renewal | Top risk signal | Recommended action |
|---------|-----|---------|----------------|-------------------|
| [Account] | $[ARR] | [Date] | [1-line signal] | [Specific action] |

**Yellow accounts — watch:**

| Account | ARR | Renewal | Top risk signal | Recommended action |
|---------|-----|---------|----------------|-------------------|
| [Account] | $[ARR] | [Date] | [1-line signal] | [Specific action] |

**Accounts approaching renewal (<90 days):**

| Account | ARR | Renewal | Health | Risk signal |
|---------|-----|---------|--------|------------|
| [Account] | $[ARR] | [Date] | [R/Y/G] | [Signal] |

**Summary:**
- [N] Red accounts · [N] Yellow accounts · [N] approaching renewal
- Total ARR at risk: $[sum of Red + Yellow + <90 day]
- Highest priority: [account name] — [reason]

---

## Reviewer note

> **⚠️ Reviewer note**
> - **Sources:** [CS Platform ✓ live — health score + components | CS Platform [configured but unverified] | CRM ✓ live — usage + engagement | CRM [configured but unverified] | user provided | not connected — conversation context only]
> - **Health model applied:** [Components and weights from config | user-defined signals | no formal model — signals only]
> - **Data as of:** [timestamp per source]
> - **Staleness flags:** [NPS from [N] months ago — stale | usage data confirmed live | engagement from last call [date]]
> - **Flagged for your judgment:** [N items marked `[review]` inline | none]

---

## Output

Health review report — format driven by `--single` (default) or `--triage` flag. Single-account mode produces a structured markdown brief with signal inventory, score justification, and recommended actions. Portfolio triage produces a ranked table with risk tier, primary driver, and next action per account.

## Guardrails

**Health scores are heuristics, not verdicts.** The output shows component signals and the account's position relative to configured thresholds. It does not say "this account will churn." That inference is the CSM's judgment.

**No escalation without context.** When the skill recommends escalation, it names the route (person, channel, SLA) from the configured escalation matrix. A flag without a path does not help.

**No silent data gaps.** If a component is missing (NPS not available, usage data stale, no CS Platform connected), the reviewer note names the gap and the health classification is adjusted accordingly ("classification is based on partial signal data — [component] unavailable").

**Churn signals require evidence.** A signal is marked present only if there is direct evidence. Absence of data is "Unknown," not "No."

**Portfolio triage contains account-level data.** Distribution of the triage report outside the CS team requires a destination check — ARR values and health classifications are confidential.

---

## After the review

- Red account: "Want a structured risk memo? `/csm:risk-flag [account]`"
- Approaching renewal + Yellow/Red: "Run renewal readiness: `/csm:renewal-readiness [account]`"
- Executive sponsor signal: "Check stakeholder map: `/csm:stakeholder-map [account] --sponsor-risk`"
- Portfolio triage complete: "Want deep reviews on the top 3 Red accounts? Name them and I'll run `/csm:health-score-review --deep` on each."

---

## Reference Material

### Health Score Review — Reasoning Blueprint

#### Problem Classification Taxonomy

**Type A: Single Account — Data-Rich**
- Characteristics: CS Platform connected, health model configured, component scores available, trend data present
- Primary Risk: Over-reliance on composite score masks deteriorating individual components
- Expert Focus: Component-level trend direction; which signal is driving the score, not the score itself

**Type B: Single Account — Data-Sparse**
- Characteristics: Partial signals only (e.g., user-provided notes, no CS Platform), missing components, stale NPS
- Primary Risk: Incomplete classification presented with false confidence; gaps not surfaced
- Expert Focus: Name every gap explicitly; classify with caveats; never fill unknowns with assumptions

**Type C: Portfolio Triage — Prioritization**
- Characteristics: Multiple accounts, need ranked risk output, time-constrained review
- Primary Risk: ARR-blind prioritization (treating a $5K Red the same as a $500K Yellow approaching renewal)
- Expert Focus: Compound risk factors — renewal proximity x health status x ARR; not health alone

**Type D: Alert-Triggered Review**
- Characteristics: Fired by a health alert, usage drop, or escalation event; reactive context
- Primary Risk: Anchoring on the triggering signal and missing concurrent deterioration in other components
- Expert Focus: Full component scan even when one signal triggered the review; alerts are symptoms, not diagnoses

**Type E: Pre-Renewal Health Check**
- Characteristics: Renewal within 90 days, proactive review, stakeholder visibility likely
- Primary Risk: Optimism bias — CSM minimizes Yellow signals near renewal to avoid escalation overhead
- Expert Focus: Honest classification with escalation path ready; renewal proximity amplifies every signal

#### Domain Heuristics

**H1: The 20% Usage Drop Rule**
A 20%+ decline in primary usage metric over 30 days is never noise. Even if other signals are Green, this warrants a proactive outreach within 7 days. Usage drops precede churn signals by 60-90 days on average.

**H2: Stale NPS is Worse Than Bad NPS**
An NPS score older than 6 months tells you nothing about current sentiment. Treat it as Unknown, not as the last recorded value. A detractor score from 3 months ago with no recovery action is an active risk.

**H3: The Contact Gap Multiplier**
Days-since-last-contact risk compounds with health status. A Green account silent for 45 days is a watch item. A Yellow account silent for 45 days is approaching Red. Apply the configured threshold, but weight it by current health tier.

**H4: Executive Sponsor Departure Overrides Everything**
When the executive sponsor leaves, the account's effective health drops one tier regardless of component scores. Product usage and NPS mean little when the internal champion is gone.

**H5: Ticket Velocity Over Ticket Count**
Three open tickets is less concerning than going from zero to three in two weeks. Rate of change in support load signals emerging frustration before severity escalation.

**H6: Renewal Proximity Amplifier**
Any Yellow or Red signal within 90 days of renewal gets treated one severity level higher for action planning. A Yellow account renewing in 30 days gets Red-tier intervention urgency.

**H7: The Multi-Signal Convergence Rule**
Two moderate signals in different components (e.g., usage dip + missed QBR) are more concerning than one strong signal. Convergence across independent components indicates systemic disengagement, not an isolated event.

#### Common Failure Modes

**Type A (Data-Rich) Failures**
1. Score-anchoring: Reporting the composite score without decomposing components. Fix: Always lead with component-level breakdown; composite score is a summary, not the analysis.
2. Trend blindness: Showing current values without 30/60/90-day direction. Fix: Include trend arrows and rate of change for every component with historical data.
3. Green complacency: Skipping churn signal scan for Green accounts. Fix: Run churn signal checklist regardless of overall health tier.

**Type B (Data-Sparse) Failures**
1. Confidence inflation: Classifying Red/Yellow/Green without flagging which components are missing. Fix: Append data sufficiency note to every classification; mark missing components as Unknown.
2. Assumption-filling: Treating no news as good news for missing signals. Fix: Absence of data is Unknown, never No. Surface the gap in the reviewer note.

**Type C (Portfolio Triage) Failures**
1. Flat prioritization: Sorting by health score alone without weighting ARR or renewal proximity. Fix: Compound ranking = health tier x ARR x renewal proximity.
2. Confidentiality leak: Distributing portfolio-level ARR and health data without audience check. Fix: G5 destination check before any portfolio output leaves the CSM's view.

**Type D (Alert-Triggered) Failures**
1. Tunnel vision: Investigating only the triggering signal. Fix: Full component scan — the alert is the entry point, not the diagnosis.
2. Premature escalation: Escalating on alert alone before confirming severity with component context. Fix: Classify after full review; escalate per matrix, not per alert.

**Type E (Pre-Renewal) Failures**
1. Optimism bias: Downgrading Yellow signals to avoid escalation conversation before renewal. Fix: Apply H6 — renewal proximity amplifies, never dampens.
2. Missing stakeholder context: Reviewing health without checking sponsor status. Fix: Always verify executive sponsor and champion status for pre-renewal reviews.

#### Expert Judgment Patterns

**Scope Decisions**
- Single account with live data: full component breakdown + trend + churn signals + interventions
- Single account with partial data: component breakdown with gaps named + point-in-time classification + data acquisition recommendations
- Portfolio triage: ranked table with compound risk score; deep dives only for top 3 Red accounts unless requested

**Sequencing Decisions**
- Always: data gather → component breakdown → churn signal scan → classification → interventions
- Never classify before completing the churn signal scan — signals can override component-based classification
- Trend analysis before interventions — direction determines urgency of the recommended action

**Depth Decisions**
- Green accounts get a maintenance-level review: component table + next QBR date + any approaching staleness flags
- Yellow accounts get full analysis: all components + trend + specific outreach recommendation with timeline
- Red accounts get full analysis plus escalation routing and recovery action plan with named owners and SLAs

**Stakeholder Decisions**
- CSM-only output: full detail including ARR, internal notes, escalation recommendations
- Cross-functional distribution (e.g., to VP CS): strip internal notes, add context for non-CSM readers, include executive summary
- Portfolio reports leaving CS org: G5 confidentiality gate — ARR and health classifications require authorization

**Confidence Decisions**
- Three or more components with live data: classify with standard confidence
- One or two components only: classify with explicit partial-data caveat
- No live data (user-provided only): classify as point-in-time assessment, flag data acquisition as first recommended action
- Any component older than 30 days: flag as stale in reviewer note with source date
