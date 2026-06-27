---
name: renewals-executive-summary
description: >
  Generate an executive-ready renewal summary for a strategic account — written
  for CRO, CEO, or board consumption. Surfaces commercial status, relationship
  health, risk tier, save strategy, and recommended executive action in a format
  calibrated for leaders who need the bottom line without raw signal data.
  Suppresses internal positioning, walk-away figures, and operational detail from
  the output. All ARR figures flagged for Finance/RevOps review before distribution.
  Use for strategic accounts requiring executive sponsorship, escalated renewals,
  or board/investor reporting packages.
---

## Company Context

**Company:** AutogenAI — AI-powered proposal and bid writing platform for enterprise teams. Primary value metric: win rate improvement and proposal throughput.

**CSM role:** Enterprise CSM. CSM-led renewal motion (CSM owns end-to-end). Segment: Enterprise. Total book: £1.2M ARR across 10 accounts (~£120K average deal size). NRR target: 120%.

**Churn signals to surface in risk framing:**
- Cost per active user rising (licences not converting to genuine active users)
- Unresolved structural blockers (IT/security, broken integrations, poor-quality generation pushing users to ChatGPT or Microsoft Copilot)
- Login decay — 7–14 day no-login window is the intervention point
- Exec sponsor disengaged or departed
- Competitor displacement: ChatGPT, Microsoft Copilot, manual Word/Google Docs workflows

**Escalation routing:** Confirmed churn risk escalates to Manager of Customer Success or Chief Customer Officer via Slack or email. Other escalation paths (discount authority, price increase pushback, executive escalations, contract terms) are not yet configured — prompt in-session if needed.

**Executive summary format:** Email or one-pager briefing. Negotiation posture: consultative. CRM: HubSpot. CS platform: Planhat (playbook location). Comms: Outlook and Slack.

**Finance/RevOps review:** Required before distributing any ARR figures or forecast outputs externally. GRR target not set; NRR target 120%.

---

## Skill Instructions

# /renewals:executive-summary [VALIDATED]

Strategic account renewal summary calibrated for executive audiences.

---

## Use when
- A strategic account renewal requires executive sponsorship and you need a brief formatted for CRO, CEO, or board consumption
- You are preparing materials for a board or investor reporting package that includes renewal pipeline exposure
- An escalated renewal is being handed to executive leadership and they need commercial status, relationship health, and a specific recommended action without raw signal data
- You have completed `/renewals:risk-assessment` for an account and need to translate that risk tier into executive-ready language

## Do NOT use for
- CSM-level internal prep — use `/renewals:negotiation-prep` for call preparation and concession authority
- Customer-facing renewal proposals — use `/renewals:negotiation-prep --export` for proposals the customer will see
- Risk scoring without a specific account in hand — use `/renewals:risk-assessment` first
- Accounts that do not meet your configured strategic account threshold (deal size or segment)

## Typical Activation
> `/renewals:executive-summary Acme Corp` — full internal executive summary for a named strategic account
> `/renewals:executive-summary Acme Corp --brief` — condensed brief: commercial status, risk tier, and recommended executive action only
> `/renewals:executive-summary Acme Corp --board` — board-format version with anonymization if account identity is not authorized for external sharing

---

## Pre-flight

The company context and plugin configuration are embedded in the Company Context section above. Use those values directly.

If any required fields for this summary are missing (GRR/NRR targets, customer segments, escalation matrix, executive contacts), stop and prompt the user to provide them in-session rather than proceeding with incomplete context.

Fields used from config:
- Company name and brand (for header): AutogenAI
- Customer segments and deal size tiers: Enterprise, ~£120K average
- NRR target: 120% (GRR target not set)
- Escalation matrix: confirmed churn risk → Manager of Customer Success or Chief Customer Officer via Slack or email; other paths not configured — prompt in-session
- Negotiation posture: consultative

**G-code dependency:** All G-code guardrails referenced in this skill (G1–G9) apply as described below. G4 governs revenue commitment language. G5 governs confidentiality gradient. G7 governs executive ask specificity.

---

## Reasoning Protocol

Before generating output, apply these primers:

1. **CLASSIFY**: What type of executive summary request is this?
   - **Escalation Brief**: Account is High/Critical risk, executive intervention needed within days. Optimize for specificity of the ask and urgency of the timeline.
   - **Sponsorship Request**: Renewal requires sustained executive co-sponsorship — QBR participation, relationship rebuild, or strategic partnership signal over weeks.
   - **Portfolio / Board Roll-Up**: Account included in board materials, investor briefings, or strategic portfolio reviews. Audience is non-operational; strip relationship detail.
   - **Proactive Visibility**: Account meets strategic threshold but is on track — executive awareness without action. Clearly signal "no action needed" and name next checkpoint.

2. **CONSTRAINTS**: What limits the solution space?
   - G4: Revenue commitment language — every ARR figure flagged `[review — not yet a revenue commitment]`; Finance/RevOps review required before distribution to leadership or board.
   - G5: Confidentiality gradient — `--brief` and `--full` are internal-only; `--board` strips relationship detail; confirm audience authorization before generating.
   - G7: Executive asks must name a specific person, specific action, and specific date — "executive involvement needed" is not an ask.
   - Escalation routing must use the configured escalation matrix with named owner — no generic "escalate to your manager."
   - Value claims require account-specific evidence — generic value language undermines executive credibility.

3. **EXPERT CHECK**: What would a veteran CRO or Head of CS verify first?
   - Does the bottom line stand alone in two sentences — would an executive who reads nothing else know the situation and what's being asked of them?
   - Is the risk tier imported from `/renewals:risk-assessment`, not re-derived independently? If no prior assessment exists, flag that explicitly.
   - Does the "what executive involvement changes" section articulate what the executive achieves that the CSM cannot — or is it just requesting presence?

4. **ANTI-PATTERNS**: Common mistakes to avoid:
   - Vague executive ask — "We need CRO support" without naming who calls whom, about what, by when.
   - Risk drivers stated as domain labels ("engagement is low") instead of specific data points ("no executive sponsor contact in 120 days").
   - Including customer-specific relationship detail or save strategy language in `--board` output.
   - Shipping `--full` mode with generic value language instead of downgrading to `--brief` with the gap flagged.
   - Including unconfirmed competitive intelligence — hedged competitive context is worse than omission.
   - Manufacturing urgency for on-track accounts — prompting unnecessary executive intervention on a healthy renewal.

**After execution**, verify:
- Does the summary pass the two-sentence test — can the bottom line stand alone?
- Are all ARR figures flagged with `[review — not yet a revenue commitment]`?
- Is the output mode (brief/full/board) matched to the actual audience, not just the requested mode?
- Confidence: [High] if CRM + risk-assessment data corroborate / [Medium] if single-source or partially stale / [Low] if user-provided context only — state which.

## What makes an account eligible for executive summary

An executive summary is warranted when one or more of the following applies:
- ARR at or above the strategic account threshold configured in your profile
- Account is at High or Critical risk tier (from `/renewals:risk-assessment`)
- Executive escalation has been requested by either side
- The renewal requires CRO or CEO involvement to close
- The account is being included in board or investor materials
- A competitor displacement threat is confirmed

If none of these apply:
> "This account doesn't appear to meet the strategic account threshold for
> an executive summary. Consider `/renewals:negotiation-prep` or
> `/renewals:risk-assessment` instead — or confirm that executive visibility
> is needed here before proceeding."

---

## Mode

`--brief` (default): One-page executive summary — commercial status, risk
tier, key relationship signals, recommended executive action, and one clear
ask. Appropriate for CRO weekly review or escalation routing brief.

`--full`: Extended summary — all `--brief` content plus competitive context
(if applicable), relationship timeline, value delivered summary, and proposed
executive engagement plan. Appropriate for accounts requiring active executive
co-sponsorship or QBR preparation with the customer's executive.

`--board`: Board or investor format — narrows to: ARR at stake, risk level,
current trajectory, and one recommended action. No customer-specific relationship
detail. Uses only language appropriate for board audiences. Appropriate for
board materials, investor briefings, and strategic account portfolio summaries.

---

## Account identification and data pull

Ask: "Which account is this executive summary for? Provide the account name,
and tell me the ARR, renewal date, current risk tier (if known), and any
signals you want me to include."

If a CRM connector is available (HubSpot is configured), pull:
- ARR and product tier
- Renewal date and days remaining
- Opportunity stage
- Escalation history (prior escalations, who was involved, outcomes)
- Executive sponsor and champion records
- Open support tickets or unresolved escalations
- Prior renewal history (tenure, prior renewals, expansion history)

If a risk assessment has already been run:
> "Import the risk tier from `/renewals:risk-assessment` output if available —
> this summary should reflect the same tier, not re-derive it independently."

Confirm the pull:
> "[CRM]: [account name] · £[ARR] · renewal [date] · [N] days out ·
> risk tier: [Critical/High/Medium/Low from risk-assessment or 'not yet assessed']
> · data as of [timestamp]"

---

## Executive summary content — `--brief`

The brief format is a single-page document structured for a 90-second read.
Every section should be answerable in 1–3 sentences. Do not pad.

---

### Section 1 — Bottom line

State the commercial situation and recommended executive action in two sentences.
This is the first thing the executive reads; it must stand alone if they read
nothing else.

> "[Account name] (£[ARR]) renews [date] and is currently [on track / at risk
> / in active negotiation / escalated]. The recommended executive action is
> [specific: e.g., 'a call from [CRO name] to [Economic buyer name] this week'
> / 'executive sponsor engagement before [date]' / 'no action needed at executive
> level at this time']."

---

### Section 2 — Commercial status

| Field | Value |
|-------|-------|
| Current ARR | £[amount] |
| Proposed renewal ARR | £[amount] [flat / +X% increase / -X% contraction risk] |
| Renewal date | [date] — [N] days out |
| Renewal stage | [Open / Verbal commitment / At risk / In negotiation] |
| Risk tier | [Critical / High / Medium / Low] |
| GRR contribution | £[amount] `[review — not yet a revenue commitment]` |

---

### Section 3 — Relationship health

Surface the signals an executive needs — not the full domain breakdown from
risk-assessment. Keep to what's relevant for the executive action being
recommended.

> **Champion:** [Name, title] — [engaged / disengaged / recently changed]
> **Economic buyer:** [Name, title] — [aware of renewal / not yet engaged / leading negotiation]
> **Executive sponsor:** [Name, title] — [active / departed / not yet identified]
>
> **Key signal:** [One sentence — the single most important relationship fact
> the executive should know. Examples: "The economic buyer is new and hasn't
> built a relationship with our team." / "The champion left 60 days ago and has
> not been replaced." / "The executive sponsor has been vocal about value."]

---

### Section 4 — Risk summary

If risk tier is Low or Medium:
> "No executive action required. CSM is managing the renewal through the
> standard motion. [1 sentence on what would change this assessment.]"

If risk tier is High or Critical:
> "**Risk drivers:** [2–3 specific signals — not domain summaries, specific
> data points. Examples: 'Login activity dropped 40% in 90 days' / 'Competitor
> evaluation confirmed by champion on [date]' / 'No executive sponsor contact
> in 120 days']"
>
> "**Current save strategy:** [What the CSM is doing — specific, not generic]"
>
> "**What executive involvement changes:** [Specific: what an executive call or
> email from [name] accomplishes that the CSM cannot. Don't ask for executive
> involvement without a specific reason.]"

---

### Section 5 — Recommended executive action

State the action clearly. One of:

**Outreach ask:** "[CRO / CEO / Head of CS] — a [call / email] to [Economic
buyer name / Executive contact] by [date]. Purpose: [specific: relationship
check-in / strategic partnership signal / negotiation support]. Draft available
on request."

**Sponsorship ask:** "[Executive name] to join the [renewal call / QBR /
executive briefing] scheduled for [date]. Objective: [re-establish relationship /
signal strategic commitment / close the negotiation]."

**No action needed:** "No executive action required at this time. The renewal
is [on track / in negotiation within authority]. Next checkpoint: [date]."

**Escalation confirmation:** "This account is already escalated to [owner]. The
executive ask is to [authorize the save offer / directly engage the customer's
[title] / accelerate the decision timeline]."

---

### Section 6 — One clear ask

End with a single, time-bounded ask:

> **Ask:** [Specific action] by [date].

This is not a summary of the summary. It's the one thing the executive needs
to decide or do.

---

## Additional sections — `--full` mode

In `--full` mode, add after Section 6:

### Value delivered summary

2–4 sentences on what the customer has realized from the product. Specific,
data-backed, account-specific. No generic value language.

> "[Account name] has [specific adoption milestone / outcome / ROI evidence].
> [Usage data: X active users, Y% adoption of [feature], Z workflows enabled].
> [Business outcome if available: time saved, revenue impacted, efficiency gain]."

If data is unavailable:
> "[Value delivered data is not available from connected systems. Fill this
> section before sharing — generic value language will reduce the summary's
> credibility with an executive audience.]"

### Competitive context (if applicable)

Only include if a competitor evaluation is confirmed.

> "[Competitor name] is in an active evaluation. The evaluation was [triggered
> by / confirmed by] [source — champion mention / procurement RFP / call
> recording]. Our current positioning advantage: [specific]. The risk: [specific].
> Executive engagement helps because: [specific]."

Do not fabricate competitive intelligence. If not confirmed, omit.

### Proposed executive engagement plan

If executive sponsorship is recommended, provide a structured engagement plan:

| Step | Action | Owner | By when |
|------|--------|-------|---------|
| 1 | [Specific first action] | [Executive name] | [date] |
| 2 | [Follow-on action] | [CSM + Executive] | [date] |
| 3 | [Close action] | [CSM] | [date] |

---

## Board format — `--board` mode

Board format uses only aggregate or anonymized data appropriate for external
audiences. It does not include customer-specific relationship detail.

---

**Strategic Account Alert — AutogenAI**
*Prepared for: Board / Investor Review*
*Period: [date]*

| Field | Detail |
|-------|--------|
| Account (anonymized if required) | [Name or "Strategic Account [A/B/C]"] |
| ARR at stake | £[amount] `[review — not yet a revenue commitment]` |
| Renewal date | [date] |
| Risk level | [Critical / High / Medium] |
| Current trajectory | [On track / At risk / In negotiation] |
| Recommended action | [One sentence] |
| GRR impact if lost | -[%] from [segment] GRR target |

> Board materials note: All ARR figures require Finance/RevOps review before
> inclusion in board packages. Do not include an account-level ARR figure in
> board materials without confirming it represents the correct renewal scenario
> and hasn't been superseded by a signed amendment.

---

## Output format — Brief (`--brief`)

---

**Renewal Executive Summary — [Account Name]** *(internal — do not share)*
*ARR: £[amount] · Renewal: [date] · [N] days out · Risk: [tier]*
*Prepared: [date] · For: [CRO / CEO / Head of CS]*

**Bottom line:** [2 sentences]

**Commercial status:** [Table]

**Relationship health:** [Champion / Economic buyer / Executive sponsor / Key signal]

**Risk summary:** [Risk drivers + save strategy + what executive involvement changes]

**Recommended executive action:** [Specific action]

**Ask:** [Single time-bounded ask]

---

> Reviewer note
> - **Sources:** [CRM (HubSpot) verified | risk-assessment imported | manual input]
> - **Data as of:** [timestamp]
> - **Risk tier source:** [risk-assessment run [date] / estimated from signals / not yet assessed]
> - **ARR figures:** `[review — not yet a revenue commitment]` — Finance/RevOps
>   review required before distributing to leadership or including in board materials
> - **Flagged for your judgment:** [Value delivered section requires account data /
>   competitive context unconfirmed / executive contacts need verification | none]
> - **Before distributing:** Confirm the recipient is authorized for this account's
>   ARR data. Confirm ARR figure reflects the current renewal scenario.

---

## Security & Permissions

```
network:        none — no external API calls, no web fetch
read_scope:     company context embedded above
write_scope:    none — all summary output to conversation; no file writes
subprocess:     none
dynamic_code:   none — no eval, no exec, no runtime code execution
```

This skill operates exclusively in conversation. The `--brief`, `--full`, and `--board` flags control output format and filtering — they do not trigger file writes or external data transmission.

---

## Trust & Verification

**Account data handling:** All account-level data (ARR figures, risk signals, relationship health indicators) is accepted as CSM input or imported from configured tools. All ARR figures are flagged `[review — not yet a revenue commitment]` in every output variant. This flag cannot be suppressed.

**Board format authorization:** The `--board` flag does not bypass authorization requirements. Board output is gated on explicit CSM confirmation that account-level data is authorized for external distribution. If unconfirmed, the skill applies anonymized account labels.

**Competitive intelligence sourcing:** Competitive context is included only when confirmed via call recording, CRM notes, or direct customer communication. General market inference is not included. Sources are cited inline.

**Internal content suppression:** The `--brief` and `--full` output variants are marked internal. They contain risk tier, walk-away context, and relationship health data not intended for customer audiences. The `--export` path is handled by `/renewals:negotiation-prep`, not this skill.

**Free-text field handling:** Account name and all CSM-supplied narrative fields are stored and displayed as provided. They are not executed, evaluated, or used to derive paths or IDs.

---

## Guardrails

**Executive asks must be specific.** An executive summary that asks for
"executive involvement" without naming the specific person, action, and date
is not useful — it will sit unread. Every summary must name who, what, and when.

**Value claims require evidence.** The value delivered section must contain
specific, account-sourced data — not generic value statements. If data is
unavailable, flag the section for manual completion before sharing. Do not
fabricate value evidence.

**Competitive intelligence must be confirmed.** Include competitive context
only when a competitor evaluation has been confirmed via call recording, CRM
notes, or direct customer communication. Do not infer competitive risk from
general market dynamics.

**Revenue commitment language.** All ARR figures in this output require
Finance/RevOps review before distribution to leadership or board audiences.
Flag every ARR figure with `[review — not yet a revenue commitment]`.

**Internal content stays internal.** The `--brief` and `--full` outputs are
internal. They contain risk tier, walk-away context (if imported), and
relationship health data not intended for the customer. Do not share the
summary with the customer — generate a `/renewals:negotiation-prep --export`
for customer-facing renewal proposals.

**Board format requires explicit authorization.** Do not generate `--board`
output without confirming that account-level data is authorized for board
distribution. Use anonymized account labels if account identity hasn't been
authorized for external sharing.

**Executive asks go up, not sideways.** An executive summary is a request
for executive sponsorship — it routes to Manager of Customer Success or Chief Customer Officer depending on the situation. It does not route to peers or AE partners (those requests go through standard channels). Confirm the escalation path before sending.

---

## Reference Material

### Reasoning Blueprint: Executive Renewal Summary

Load this blueprint for Tier 3 reasoning. It provides the domain-specific taxonomy, heuristics, and expert judgment patterns for expert-level executive communication about strategic account renewals.

---

#### Problem Classification Taxonomy

**Type A: Escalation Brief**
Characteristics: Account is High/Critical risk, executive intervention needed to save the renewal. CRO or CEO must act within days.
Primary Risk: Vague executive ask — requesting "involvement" without naming the person, action, and deadline.
Expert Focus: Whether the save strategy articulates what executive engagement changes that the CSM cannot achieve alone.

**Type B: Sponsorship Request**
Characteristics: Renewal requires sustained executive co-sponsorship — QBR participation, relationship rebuild, or strategic partnership signal over weeks.
Primary Risk: Confusing sponsorship (ongoing engagement) with escalation (urgent intervention) — wrong framing, wrong cadence.
Expert Focus: Whether the engagement plan has sequenced steps with owners and dates, not a single ask.

**Type C: Portfolio / Board Roll-Up**
Characteristics: Account included in board materials, investor briefings, or strategic portfolio reviews. Audience is non-operational.
Primary Risk: Including customer-specific relationship detail or internal positioning language inappropriate for board audiences.
Expert Focus: Whether ARR figures have Finance/RevOps review flags and whether account identity is authorized for external sharing.

**Type D: Proactive Visibility**
Characteristics: Account meets strategic threshold but is on track — executive awareness requested, not action.
Primary Risk: Manufacturing urgency where none exists, prompting unnecessary executive intervention on a healthy renewal.
Expert Focus: Whether the summary clearly signals "no action needed" and names the next checkpoint date.

---

#### Domain Heuristics

1. **The Two-Sentence Test**: If the bottom line can't be stated in two sentences, the summary isn't ready. Executives who read past the first paragraph are the exception, not the rule.

2. **The Named Ask Rule**: Every executive ask must name a specific person, a specific action, and a specific date. "Executive involvement needed" is not an ask — it's a wish.

3. **The Delta Rule**: Executives care about what changed, not what is. Lead with movement (risk tier changed, champion departed, competitor confirmed) rather than static state.

4. **The One-Ask Ceiling**: A summary with three asks gets zero done. Distill to the single most important action. If genuinely two asks, send two summaries.

5. **The Revenue Flag Discipline**: Every ARR figure gets `[review — not yet a revenue commitment]` without exception. One unreviewed figure in a board deck destroys credibility.

6. **The Confidentiality Gradient**: Brief and Full are internal-only. Board mode strips relationship detail. Never let mode selection happen by default — confirm audience before generating.

7. **The Evidence-or-Flag Rule**: Value delivered claims without account-specific data are worse than a flagged gap. "We delivered value" with no evidence undermines the entire summary.

---

#### Common Failure Modes by Summary Type

**Escalation Brief Failures**
- Vague executive ask: "We need CRO support" without naming who calls whom, about what, by when.
  Fix: Apply Named Ask Rule — fill in [Executive name] → [Customer contact] → [purpose] → [date].
- Risk drivers as domain labels: "Engagement is low" instead of "No executive sponsor contact in 120 days."
  Fix: Replace every domain label with the specific data point that drives it.

**Sponsorship Request Failures**
- Single-step plan for multi-step engagement: Asking for one call when the situation requires sequenced involvement.
  Fix: Build the engagement plan table — Step / Action / Owner / By When — minimum 2-3 steps.
- Missing value evidence: Requesting sponsorship without demonstrating what the customer has realized.
  Fix: Populate value delivered section or flag it explicitly for manual completion before sending.

**Board Roll-Up Failures**
- Relationship detail leaking into board format: Customer-specific champion names, internal risk language, or save strategy detail in board materials.
  Fix: Strip to ARR, risk level, trajectory, one recommended action. Anonymize if identity not authorized.
- Unreviewed ARR figures: Revenue numbers presented without Finance/RevOps validation flag.
  Fix: Apply revenue flag discipline to every figure; add board materials note.

**Proactive Visibility Failures**
- Manufactured urgency: Framing a Green/on-track account as needing executive action.
  Fix: Use the "No action needed" template explicitly; name the next checkpoint date.

---

#### Expert Judgment Patterns

**Audience Calibration Decisions**
- Match mode to the real audience, not the requested mode — a CRO weekly review needs `--brief`, not `--full`, even if asked for "everything."
- Board audiences parse tables and one-liners; narrative paragraphs signal the author doesn't understand the audience.
- When in doubt about authorization for board-level data, default to anonymized account labels.

**Risk Framing Decisions**
- Separate the risk tier (imported from risk-assessment) from the executive narrative — the summary should reflect the tier, not re-derive it.
- For High/Critical accounts, the "what executive involvement changes" section is the highest-value paragraph — spend the most effort here.
- If save strategy is "continue current motion," the account may not warrant an executive summary — recommend `/renewals:negotiation-prep` instead.

**Scope Decisions**
- A `--full` summary that can't populate the value delivered section should be downgraded to `--brief` with the gap flagged, not shipped with generic value language.
- Competitive context is include-or-omit, never hedged — unconfirmed competitive intelligence in an executive summary is worse than no mention.

Use the Write tool to create this file. Return "done" when complete.
