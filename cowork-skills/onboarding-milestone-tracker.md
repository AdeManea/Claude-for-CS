---
name: onboarding-milestone-tracker
description: >
  Track milestone progress across one account or your full onboarding book of business.
  Reads milestone framework, at-risk signals, and escalation thresholds from your
  onboarding profile. Pulls current milestone status from your PM connector (Asana,
  Linear, Jira, or Monday) if configured, or from CSM input. Use --status (default)
  for a current-state view of one account's milestones, --portfolio for a milestone
  health summary across all active onboarding accounts, or --flag to surface all
  accounts with at-risk or overdue milestones and recommended actions.

## Company Context

**Company:** AutogenAI — AI-powered proposal and bid writing platform for enterprise teams.

**Role:** Enterprise CSM. Team structure: Enterprise accounts are served by CSM + Onboarding Project Manager + Implementation Consultant + Bid Consultant. Mid-Market and SME accounts are Implementation Consultant-led with handoff back to CSM.

**Segments and onboarding models:**
- Enterprise: White-glove, ~4 months to Onboarding PM handoff
- Mid-Market: Implementation-plus-handoff, ~3 months
- SME: Implementation-plus-handoff, ~2–3 months

**Milestone framework (Enterprise):**
| Milestone | Timing | Definition |
|-----------|--------|------------|
| M1 — Kickoff + Discovery complete | Month 1 | Stakeholders confirmed, end users finalised, bid discovery done, Value Alignment Session complete, Triple Metric agreed |
| M2 — Configuration complete | Month 1 | Platform live, document library set up, Academy access granted |
| M3 — Adoption underway | Months 2–4 | Foundations training delivered, drop-in sessions running, monthly value reports started |
| M4 — First Value (First Business Review) | Month 3–4 | Progress reviewed, joint roadmap co-created, Triple Metric tracking confirmed |
| M5 — Handoff ready | Month 5 | Implementation wrap-up complete, Onboarding PM exits, CSM takes ownership |

*Note: Enterprise milestones are month-based. Generic day-based defaults (M1: Day 5 / M2: Day 14 / M3: Day 21 / M4: Day 30 / M5: Day 60) apply as fallback for non-Enterprise accounts.*

**Success criteria model:** Triple Metric — Corporate level (win rate, revenue, ROS/ROA), Business unit level (technical score, operating margins, revenue per transaction), Project level (time saved, user adoption, headcount efficiency). Success criteria defined jointly in the Value Alignment Session and reviewed quarterly.

**Key tools:** HubSpot (CRM), Planhat (CS platform, transitioning from Asana), SharePoint (document storage), Slack and Outlook (comms). No PM connector currently connected to Claude.

**Escalation matrix (partial — SLAs and named contacts are placeholders):**
- M1 missed: Manager of CS + Head of Professional Services via Slack
- Technical blocker >5 days: IC escalates to Head of PS or Build team
- Executive sponsor unresponsive: CCO via Slack + email
- Customer wants to cancel: CSM → Manager of CS + Legal team

**Top churn signals:** Low platform adoption, low bid volume through tool, champion departure, competitive displacement.

---

## Skill Instructions

# /onboarding:milestone-tracker

Milestone health tracking — single account or portfolio view.

---

## Trigger Precision

**Use when:**
- Checking the current milestone status and pace for one or more onboarding accounts (`--status`)
- Generating a portfolio heat map of milestone health across all active accounts (`--portfolio`)
- Triaging at-risk accounts and producing prioritized intervention actions (`--flag`)

**Do NOT use for:**
- Diagnosing a specific active blocker (use `/onboarding:blocker-review --diagnose`)
- Time-to-value analysis with segment-normalized benchmarking (use `/onboarding:ttv-analysis`)
- Generating the onboarding plan or updating milestone targets (use `/onboarding:onboarding-plan`)

## Typical Activation
- "Where are we on milestones for [Account]?"
- "Show me the portfolio heat map for all active accounts"
- "Which accounts are at risk this week?"
- CSM runs `/onboarding:milestone-tracker [account] --status` to check a single account's milestone progress
- CSM runs `/onboarding:milestone-tracker --portfolio` to generate a portfolio-wide milestone heat map
- CSM runs `/onboarding:milestone-tracker --flag` to surface at-risk accounts for intervention

## Reasoning Protocol

Before generating output, apply these primers:

1. **CLASSIFY**: What type of milestone tracking request is this?
   - **Single-Account Status**: One named account, CSM wants current milestone state before a touchpoint or review. Optimize for recency and actionable next-step.
   - **Portfolio Health Scan**: Cross-account milestone view for book-of-business management or 1:1 prep. Requires segment normalization and consistent structure.
   - **At-Risk Triage**: Surface accounts needing intervention — overdue or signal-triggered. Score by signal severity first, then date proximity.
   - **Escalation-Ready Brief**: Specific account has breached escalation thresholds — output feeds an escalation workflow. Pair flag with root cause and escalation matrix routing.

2. **CONSTRAINTS**: What limits the solution space?
   - G7: All milestone data must carry a source timestamp and staleness indicator — PM connector data >24h old is flagged, manual input is labeled as such.
   - G5: TtV projections and internal planning targets appear only in internal sections and reviewer notes — never in customer-facing output.
   - G4: Escalation recommendations route through the configured escalation matrix with named owner, channel, and SLA — no generic "escalate to your manager."
   - G2: Portfolio output is internal-only by default — apply confidentiality check before any distribution beyond the CSM.
   - G1: Overdue is a flag, not a verdict — do not present an overdue milestone as a failure without CSM context on agreed deferrals or dependencies.
   - Contract start date is the anchor for all date calculations — if missing, all milestone dates for that account are `[unverified]`. Never silently estimate.

3. **EXPERT CHECK**: What would a veteran onboarding manager verify first?
   - Are behavioral at-risk signals (no login, missing attendees, credentials not received) assessed independently of date math? A milestone with 15 days remaining but a confirmed signal is more urgent than one 2 days overdue with no signal.
   - Is milestone pace normalized to segment-specific duration targets from config? Enterprise M3 at Day 30 is on track; SMB M3 at Day 30 may be overdue.
   - When PM connector data conflicts with CSM-reported status, is the discrepancy surfaced rather than silently resolved? The conflict itself is a signal.

4. **ANTI-PATTERNS**: Common mistakes to avoid:
   - Presenting stale PM data without a timestamp or staleness flag — the CSM acts on outdated milestone completion status.
   - Sorting at-risk triage by days-overdue alone, missing accounts with behavioral signals that haven't breached date thresholds yet.
   - Recommending generic actions ("follow up with the customer") instead of pulling specific escalation steps from the configured matrix.
   - Comparing SMB and enterprise accounts on the same absolute day thresholds without normalizing to segment duration targets.
   - Running date calculations when contract start date is missing or estimated — every downstream milestone target becomes unverified.
   - Escalating from portfolio view alone without running single-account status first for full context.

**After execution**, verify:
- Does the output match the requested mode (--status / --portfolio / --flag) and the actual need?
- Are all data sources timestamped and staleness-flagged per G7?
- Are at-risk signals assessed independently of date math, not just as a date countdown?
- Confidence: [High] if PM connector + CRM data corroborate / [Medium] if single-source or partially stale / [Low] if manual input only — state which.

## Mode

`--status` (default): Current milestone status for one named account. Shows milestone
table with dates, completion status, days-to-next-milestone, at-risk signals, and
recommended actions. Use before a customer touchpoint or QBR prep.

`--portfolio`: Milestone health summary across all active onboarding accounts. Requires
either a PM connector or a list of account names with contract start dates from the
CSM. Output is a portfolio heat map — not a per-account deep-dive.

`--flag`: At-risk and overdue milestone report. Surfaces only accounts with problems:
overdue milestones, approaching at-risk thresholds, blocked owners, or missing milestone
evidence. Ordered by severity (overdue first, then approaching deadline, then at-risk signal
count). Includes recommended action per flagged account.

---

## Account identification and data pull

### `--status` mode

Ask: "Which account?" If a CRM connector is available, pull contract start date and
CSM/AE assignment. If a PM connector is configured, pull current task status for
this account's milestone tasks.

PM connector pull (if available):
> "[PM: Asana/Linear/Jira/Monday]: [account name] project · [N] tasks found ·
> M[#] in progress · data as of [timestamp]"

If no PM connector:
> "No PM connector configured. Tell me: which milestone is currently active, what's
> complete, and the contract start date — I'll calculate dates and assess risk from
> that."

### `--portfolio` mode

If PM connector available:
> "[PM]: pulling all projects tagged as onboarding accounts..."

If not: "Provide a list of active onboarding accounts with their contract start dates
and current milestone status — I'll build the portfolio view from that input."

### `--flag` mode

Requires either PM connector (preferred) or CSM-provided account list with current
milestone status and contract start dates.

---

## Milestone status calculation

For each milestone, calculate:

1. **Target date** = contract start date + milestone day target from config
2. **Days remaining** = target date − today
3. **Status** — assign one of:
   - `On track` — target date is ≥ 5 days away and no at-risk signals present
   - `Due soon` — target date is 1–4 days away; escalate preparation
   - `At risk` — at-risk signals present (see below) OR days remaining < at-risk
     threshold from config
   - `Overdue` — target date is in the past; milestone not marked complete
   - `Complete` — milestone confirmed complete with evidence

**At-risk signal assessment:**

For each milestone, check the at-risk signals from config. Generic defaults (override
with config values):

| Milestone | At-risk signals |
|-----------|----------------|
| M1 | Required attendees not confirmed 48h before kickoff |
| M2 | Integration credentials not received by Day 7 |
| M3 | No product login activity in the first 14 days |
| M4 | No documented use case completion by Day 25 |
| M5 | Success criteria not confirmed by Day 45 |

Flag an account as At risk if any signal is present, regardless of days remaining.

**Days-overdue escalation thresholds** (from config; defaults below):

| Days overdue | Action |
|-------------|--------|
| 1–3 days | CSM self-resolve — reach out to customer champion |
| 4–7 days | Involve AE or escalation contact from config |
| 8+ days | Executive escalation (if white-glove) or formal risk flag |

---

## `--status` output

```
Milestone Status — [Account Name]
Contract start: [date] · CSM: [name] · Model: [onboarding model]
Generated: [today]

| Milestone | Target | Status | Days remaining | Signal |
|-----------|--------|--------|---------------|--------|
| M1: Kickoff | [date] | Complete ✓ | — | — |
| M2: Tech setup | [date] | [status] | [N] | [signal or —] |
| M3: First use | [date] | [status] | [N] | [signal or —] |
| M4: First value | [date] | [status] | [N] | [signal or —] |
| M5: Handoff ready | [date] | [status] | [N] | [signal or —] |

Current milestone: M[#] — [label]
Next action: [specific recommended action based on current status]

[If any milestone is At risk or Overdue:]
⚠ Risk detail: [signal description and recommended escalation action]
```

**Internal section (not for sharing):**

```
TtV projection [review — internal planning target]:
  Current pace: [N days from start to estimated M5]
  Segment target: [X days]
  Gap/surplus: [±Y days — on track | behind | ahead]

Escalation threshold: [current status vs. config thresholds]
```

---

## `--portfolio` output

```
Onboarding Portfolio — Milestone Health
Generated: [today] · [N] active accounts

| Account | Model | M current | Status | Days remaining | Flag |
|---------|-------|-----------|--------|---------------|------|
| [name] | [model] | M[#] | On track | [N] | — |
| [name] | [model] | M[#] | At risk | [N] | ⚠ [signal] |
| [name] | [model] | M[#] | Overdue | -[N] | 🔴 [days overdue] |

Summary:
  On track:  [N] accounts
  Due soon:  [N] accounts
  At risk:   [N] accounts
  Overdue:   [N] accounts

[If at-risk or overdue accounts exist:]
Recommended first action: [highest-severity item]
```

---

## `--flag` output

Ordered by severity — overdue first, then at-risk, then due soon.

```
At-Risk and Overdue Milestones — [today]

🔴 OVERDUE ([N] accounts)

[Account name] · M[#]: [milestone label] · [N] days overdue
  Signal: [specific signal]
  Recommended action: [escalation step from config escalation matrix]
  Owner: [CSM name]

⚠ AT RISK ([N] accounts)

[Account name] · M[#]: [milestone label] · [N] days remaining
  Signal: [specific at-risk signal]
  Recommended action: [proactive step before milestone becomes overdue]

🟡 DUE SOON ([N] accounts)

[Account name] · M[#]: [milestone label] · [N] days remaining
  Preparation needed: [next action]

---
Accounts reviewed: [N] · No issues: [N] · Flagged: [N]
```

---

## Reviewer note (internal — `--status` and `--portfolio` only)

> ⚠️ Reviewer note
> - **Sources:** [PM connector ✓ | CRM ✓ | manual input]
> - **Data as of:** [timestamp]
> - **Config fields read:** milestone framework (M1 [X]d, M2 [X]d, M3 [X]d,
>   M4 [X]d, M5 [X]d), escalation thresholds ([values])
> - **At-risk signals evaluated:** [list of signals checked per milestone]
> - **TtV projection:** [status vs. segment target — internal only]
> - **Flagged for your judgment:** [accounts requiring immediate action | none]
> - **Data gaps:** [accounts where contract start date was not available from CRM
>   — dates are estimates | none]

---

## Output

Milestone tracking output — format driven by flag (`--status`, `--portfolio`,
`--flag`). Status mode: per-account milestone table with RAG status and next action.
Portfolio mode: cross-account summary table. Flag mode: at-risk milestone alert.
See mode-specific sections for field-level structure.

---

## Security & Permissions

This skill operates read-only against configuration files and connected MCP data sources.
No filesystem writes, no subprocess execution, no dynamic code execution.
All data access is through explicitly connected MCP connectors; no outbound network calls are made directly.

## Trust & Verification

All milestone data is timestamped and staleness-flagged per G7 (PM connector >3 days, CRM >7 days).
At-risk flags are calculated from milestone math and configured at-risk signals — not from CSM narrative.
Portfolio outputs containing account-level health data require confidentiality check before sharing beyond the CSM.
CSM judgment is required for intervention actions — skill presents options, never directives.

## Guardrails

**At-risk signals override date-only assessment.** A milestone with 10 days remaining
but a confirmed at-risk signal is flagged as At risk — not On track. Days remaining is
a countdown; at-risk signals are behavioral evidence. Both matter.

**TtV projection is internal.** The internal section with TtV labels appears only in
internal outputs and reviewer notes. Customer-facing milestone views show milestone
dates and completion status — not TtV framing.

**`--portfolio` is a health map — not account management.** The portfolio view surfaces
which accounts need attention. It does not replace per-account context. Before escalating
an at-risk flag, run `--status` on that account to understand the full picture.

**Overdue ≠ failed.** An overdue milestone may be legitimately deferred (customer
request, technical dependency, agreed scope change). The tracker flags it; the CSM
judges it. Do not present an overdue flag as a failure without CSM context.

**PM connector data is authoritative over manual input.** If a PM connector returns
task status that conflicts with CSM input, surface the discrepancy:
> "PM connector shows M2 as incomplete, but you've indicated it's done. Confirm
> the PM task has been updated, or I'll flag this as a data inconsistency."

**`--flag` is for intervention — not reporting.** The flag output is a working
document for the CSM's internal action planning. It is not formatted for sharing
with customers, leadership, or QBR decks. Extract and format specific items for
those audiences separately.

**Missing contract start date breaks date calculation.** If the contract start date
is unavailable for any account, all milestone dates for that account are `[unverified]`.
Do not estimate contract start dates — request them from the CSM or CRM before
proceeding.

---

## Reference Material

### Reasoning Blueprint: Milestone Tracking

Load this blueprint when Tier 3 reasoning is activated for onboarding milestone
tracking. It provides the domain-specific taxonomy, heuristics, and expert judgment
patterns that shape expert-level milestone health assessment and portfolio risk triage.

---

#### Problem Classification Taxonomy

**Type A: Single-Account Status Check**
Characteristics: One named account, CSM wants current milestone state before a touchpoint or internal review.
Primary Risk: Presenting stale PM data as current — CSM acts on outdated milestone completion status.
Expert Focus: Delta since last check matters more than absolute state. Surface what changed, not just what is.

**Type B: Portfolio Health Scan**
Characteristics: Cross-account view of milestone progress for book-of-business management or 1:1 prep.
Primary Risk: Treating all accounts as comparable when segment, model, and duration targets differ.
Expert Focus: Normalize by segment target before RAG-coding — a Day-20 M2 delay in enterprise is different from SMB.

**Type C: At-Risk Triage**
Characteristics: CSM needs to identify which accounts require intervention now — overdue or signal-triggered.
Primary Risk: Sorting by days-overdue alone and missing accounts with behavioral at-risk signals that haven't yet breached date thresholds.
Expert Focus: Behavioral signals (no login, missing attendees, credentials not received) outweigh calendar math for early intervention.

**Type D: Escalation-Ready Brief**
Characteristics: A specific account has breached escalation thresholds — output feeds an escalation workflow or leadership review.
Primary Risk: Presenting the overdue flag without root-cause context, making the escalation look like a status update instead of an action request.
Expert Focus: Pair the flag with owner, recommended action from escalation matrix, and any mitigating context (agreed deferral, dependency block).

---

#### Domain Heuristics

1. **The Signal-Over-Calendar Rule**: A milestone with 15 days remaining but a confirmed at-risk signal is more urgent than one 2 days overdue with no signal. Behavioral evidence beats date math.

2. **The Contract-Start Anchor Rule**: Every date calculation flows from contract start date. If that date is missing or estimated, every downstream milestone target is unverified — flag immediately, never silently default.

3. **The Segment Normalization Rule**: Compare milestone pace to segment-specific duration targets, not absolute day counts. Enterprise M3 at Day 30 is on track; SMB M3 at Day 30 may be overdue.

4. **The PM-Trumps-Manual Rule**: When PM connector data conflicts with CSM-reported status, surface the discrepancy rather than choosing one. The conflict itself is a signal worth investigating.

5. **The Overdue-Is-Not-Failed Rule**: An overdue milestone may be legitimately deferred. Before escalating, check for agreed scope changes, customer-requested delays, or technical dependencies. Overdue is a flag, not a verdict.

6. **The Portfolio-Is-Not-Deep-Dive Rule**: Portfolio mode surfaces which accounts need attention. It does not replace per-account investigation. Never escalate from portfolio view alone — run status mode first.

7. **The Escalation-Matrix-Not-Gut Rule**: Escalation routing comes from the configured matrix with named owner, channel, and SLA. Generic "escalate to manager" is never acceptable.

---

#### Common Failure Modes by Request Type

**Single-Account Status Failures**
- Stale data presented as current: PM connector data is days old but displayed without timestamp. Fix: Always include data-as-of timestamp; flag if PM data >24h old.
- Missing contract start date silently defaulted: Date calculations proceed with an estimated start date. Fix: Hard-stop on missing contract start — request from CSM or CRM before calculating.

**Portfolio Health Failures**
- Cross-segment comparison without normalization: SMB and enterprise accounts RAG-coded on same absolute day thresholds. Fix: Apply segment-specific duration targets from config before assigning status.
- Confidentiality leak in portfolio output: ARR or internal health details included in output shared beyond CSM. Fix: Portfolio output is internal-only by default; apply confidentiality check before any distribution.

**At-Risk Triage Failures**
- Calendar-only triage: Accounts sorted by days-overdue, missing accounts with behavioral signals and days remaining. Fix: Score by signal severity first, then by date proximity. Behavioral signals rank above calendar math.
- Recommended actions are generic: "Follow up with the customer" instead of specific escalation steps. Fix: Pull recommended action from escalation matrix keyed to days-overdue bracket and milestone type.

**Escalation Brief Failures**
- Flag without context: Escalation shows "M2 overdue by 5 days" without root cause or mitigating factors. Fix: Include known blockers, agreed deferrals, and owner assignment alongside the flag.

---

#### Expert Judgment Patterns

**Severity Prioritization Decisions**
- Behavioral at-risk signals with days remaining > 0 rank above calendar-only overdue with no signal — the former is deteriorating, the latter may be administrative.
- Multiple milestones overdue on one account outranks single-milestone overdue across multiple accounts — concentration of risk matters.

**Data Confidence Decisions**
- PM connector data is authoritative over manual input; surface conflicts, don't resolve them silently.
- When contract start date comes from CRM vs. CSM memory, prefer CRM — but flag if CRM field was last updated >30 days ago.
- If no PM connector and no CRM, all milestone dates are [unverified] — state this once at the top, not per row.

**Scope Decisions**
- Portfolio mode with >15 accounts: show summary counts and flag only at-risk/overdue accounts individually — don't enumerate all on-track accounts.
- Escalation brief for leadership: strip internal-only TtV projections unless the audience is the CS ops team.
