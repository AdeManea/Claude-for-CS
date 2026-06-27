---
name: csm-risk-flag
description: >
  Structured risk memo for an at-risk account — signals present, severity
  assessment, escalation routing from your configured matrix, and recommended
  intervention. Use when a health alert fires, a churn signal is detected, or
  you need to communicate account risk to leadership. Produces a CSM brief and
  an escalation-ready summary separately.

## Company Context

**Company:** AutogenAI — AI-powered proposal and bid writing platform for enterprise teams. Primary value metric: win rate improvement / proposal throughput.

**Role:** Enterprise CSM. Segment: Enterprise. Accounts per CSM: 25–50 enterprise accounts.

**Health model:** No CS Platform connected — signals from HubSpot CRM, Glyphic AI call recordings, and conversation context. Red threshold: 2+ concurrent churn signals active. Yellow: 1 churn signal active or engagement declining.

**Top churn drivers:** Low platform adoption, low bid volume through tool, champion departure, competitive displacement.

**Connected integrations:** HubSpot CRM (verified), Glyphic AI call recording (verified), Microsoft 365 (verified). CS Platform: not connected. Google Drive: configured, not verified.

**Escalation matrix:**
- Red health account → your manager → Slack/email → 24h
- At-risk renewal → Head of CS → Slack/pipeline review → 48h
- Executive escalation request → manager + AE → email thread → same day
- Product blocker causing churn risk → CS Ops + Product → shared Slack channel → 48h
- Expansion signal (qualified) → AE/AM → email + CRM opportunity → 48h
- Legal/contract issue → Legal/Finance → email → 48h

**Source attribution tags:** `[CRM — HubSpot]`, `[Call recording — Glyphic AI]`, `[M365]`, `[Computed]`, `[user provided]`, `[model knowledge]`, `[conversation context]`

**Data freshness:** CRM data >7 days old must be flagged. Every output states data-as-of timestamp. No health score without component signals.

---

## Skill Instructions

# /risk-flag

[PROPOSED]

## Use When
- A health review has confirmed risk signals are present and you need a structured risk assessment, severity classification, and escalation routing
- Multiple signals are present and you need to assess aggregate risk before acting
- Renewal is approaching and you need a structured risk assessment before the commercial conversation
- An account has gone dark (no contact, usage drop, sponsor departure) and you need a framework for response

## Do NOT Use For
- Generating the full escalation memo — use /csm:escalation-memo once routing is confirmed
- Health score review — use /csm:health-score-review for the full health narrative
- Accounts with no risk signals present — no escalation routing needed
- Executive-facing risk reporting — this skill is CSM-facing
- Renewal-window risk assessment when the account is within 90 days of renewal — use the Renewals plugin's risk-assessment skill for structured triage in the renewal window (if the `renewals` plugin is installed, run `/renewals:risk-assessment`)

## Typical Activation
"/csm:risk-flag Acme Corp"
"Flag risk signals for [account]"
"What's the escalation path for [account]?"
"Two high signals on [customer] — what do I do?"
"Assess churn risk for [account]"

---

Document the risk clearly, name the routing, and give the CSM a specific path forward — not just a flag.

---

## Reasoning Protocol

Before generating output, apply these primers:

1. **CLASSIFY**: What type of risk is this?
- **Sudden Signal Spike** — single high-weight signal appeared abruptly (sponsor exit, competitor eval, P1 escalation)
- **Gradual Decay Pattern** — multiple medium signals accumulating over weeks (usage drift, missed QBRs, NPS decline)
- **Renewal-Proximity Risk** — any risk signal present AND renewal within 90 days
- **Stakeholder Disruption** — champion departure, sponsor reorg, power structure shift
- **Escalation-in-Progress** — customer has already escalated; CSM is responding, not initiating

2. **CONSTRAINTS** — enforce before generating output:
- **G1**: Health scores are heuristics — never frame as churn predictions or state the account "will churn"
- **G2**: Risk signals require direct evidence — absence of engagement is "Unknown" or "Declining," not "Confirmed at risk"
- **G4**: No escalation recommendation without a named contact, channel, and SLA from the configured matrix
- **G5**: Root cause is a hypothesis — present as leading interpretation with evidence, not as verdict
- **G7**: Escalation memo omits health model internals, component weights, and internal stakeholder notes — those stay in the CSM brief

3. **EXPERT CHECK** — what a veteran CSM verifies first:
- Is the signal real or a data artifact? Cross-reference with CSM notes and recent calls before classifying severity
- Signal clustering: two medium signals in 30 days outweighs one isolated high signal
- Sponsor silence (2+ unanswered outreaches in 30 days) is a high-weight signal regardless of health score
- Usage drops must be normalized against the customer's own baseline and business cycle
- Does the intervention plan fit inside the time-to-renewal window? If not, escalate immediately

4. **ANTI-PATTERNS** — common mistakes to avoid:
- Writing a recovery plan with more runway than the renewal date allows
- Classifying severity optimistically because the CSM "has a good relationship" — apply the matrix mechanically first
- Leaking health model scoring methodology into the escalation memo
- Presenting a risk memo that documents signals accurately but fails to convey aggregate urgency
- Restating what the customer already told leadership without adding root cause hypothesis or specific ask

**After execution**, verify:
1. **Intent satisfaction** — does the memo give the CSM a specific Monday-morning action plan, not just a flag?
2. **Mode matching** — does the output mode (`--brief` vs `--escalation-memo`) match the actual need? A brief that should be an escalation memo delays intervention; an escalation memo for a low-severity signal wastes leadership attention.
3. **Failure mode scan** — check the classification type against its common failure modes in the reference blueprint
4. **Staleness check** — is every data source timestamped? Flag CRM data >7 days stale, CS platform data >3 days stale, call/meeting data >14 days stale. Stale signals get a `[stale: <source> as of <date>]` tag.
5. **Confidence calibration** — [High] if live data + CSM confirmation corroborate the classification / [Medium] if single-source or partially stale data / [Low] if working from CSM description alone with no corroborating system data — state which.

## Mode

`--brief`: CSM-facing risk summary. Internal use — full signal breakdown, health context, and CSM action plan. Default.

`--escalation-memo`: Produce a clean, escalation-ready summary for the CSM's manager, VP CS, or CRO. Concise. Designed for rapid review by someone who doesn't know the account details. Includes: account, ARR, renewal date, risk signals, recommended action, and routing.

Both modes can be run for the same account. Default is `--brief`.

---

## Data Gathering

**Connector error categorization:** When a connector call fails, distinguish the error type before proceeding:
- **Rate-limited (transient):** Connector returns HTTP 429 or equivalent throttle signal. Note the rate limit explicitly in output ("CRM data temporarily rate-limited — retry in 60 seconds recommended") and offer to retry rather than proceeding with degraded output.
- **Unavailable (permanent for this session):** Connector is not configured, authentication has expired, or service is down. Fall back to the manual-input path below and label all affected sections as "connector unavailable — manual input used."
Do not conflate these — a rate-limited connector will return data shortly; an unavailable connector will not.

Pull from connected integrations:
- CRM (HubSpot): ARR, renewal date, contract terms, stakeholder contacts, account history
- Call recording (Glyphic AI): last 2-3 calls — tone, topics, any risk signals mentioned
- M365: recent email or calendar contact patterns

CS Platform is not connected — health signals must come from CRM, call recordings, and conversation context.

If account-research was run this session: use that context.

If nothing connected, prompt:
> "Tell me what's happening at [account]. What triggered this risk flag? What signals are you seeing? I'll build the memo from what you share."

Do not generate a risk memo without at least one concrete risk signal from the CSM or from live data.

---

## Risk Flag Structure

---

### CSM Risk Brief — [Account Name]
*[Date] · INTERNAL · [CS motion] · [Segment]*

---

**Account snapshot**

| Field | Value |
|-------|-------|
| ARR | $[amount] |
| Renewal date | [date] — [N] days |
| Segment | [segment] |
| Health | [Red / Yellow] |
| CSM | [name] |
| AE / AM | [name] |
| Executive sponsor | [name] — [last contact: date] |

---

**Risk signals — what triggered this flag**

List each signal present with evidence. No inferred signals without evidence.

| Signal | Present | Evidence | Weight per config |
|--------|---------|----------|------------------|
| Executive sponsor departure or disengagement | [Yes/No/Unknown] | [specific evidence] | High |
| Product usage drop >[configured threshold]% | [Yes/No] | [% change over period] | High |
| Open escalation or unresolved P1 ticket | [Yes/No] | [ticket details] | High |
| NPS detractor + no recovery conversation | [Yes/No] | [score + date] | Medium |
| Missed QBR or no-show pattern | [Yes/No] | [dates] | Medium |
| Competitor evaluation | [Yes/No/Unknown] | [source of signal] | High |
| No CSM contact in >[threshold] days | [Yes/No] | [last contact date] | Medium |
| [Additional configured signal] | | | |

**Primary risk driver:** [The signal with the most direct path to churn]

---

**Risk severity**

Classify based on configured thresholds and signal weight:

> **Severity: [Critical / High / Medium]**
>
> Classification rationale: [1-3 sentences. Which signals drove this. Why this severity level vs. adjacent options.]

- **Critical:** Multiple high-weight signals present; renewal within 90 days; ARR above escalation threshold → immediate executive-level escalation required
- **High:** One high-weight signal or multiple medium signals; requires CSM intervention within 48h; monitor daily
- **Medium:** Medium-weight signals; proactive outreach warranted; monitor weekly

---

**Root cause hypothesis**

What is the most likely reason for the risk?

> "The most plausible explanation is [X] — based on [specific evidence]. Alternative explanations: [Y] (possible if [condition]) / [Z] (possible if [condition]). The champion should be asked directly whether [root cause question] is accurate before the CSM builds a recovery plan."

Do not present the hypothesis as fact. It is the leading interpretation based on available data. The CSM confirms or refutes it in the next customer interaction.

---

**Escalation routing**

Apply the configured escalation matrix exactly.

| Condition | Route to | Via | SLA |
|-----------|----------|-----|-----|
| [Matching scenario from matrix] | [configured contact] | [configured channel] | [configured SLA] |

If multiple matrix rows apply, show all and note the primary route.

If this account's ARR meets the configured churn-risk threshold:
> "ARR ($[amount]) exceeds the configured escalation threshold ($[threshold]). Route to [configured contact] via [channel] within [SLA]. Include this memo."

If below the threshold:
> "ARR is below the configured escalation threshold. CSM-managed intervention. Notify CS lead if severity escalates to Critical."

---

**Recommended intervention plan**

Specific actions with owners and timelines. Not generic.

**Immediate (within 24-48h):**
1. [Specific action — e.g., "Request executive sponsor check-in call. Purpose: confirm sponsor status and surface any org changes. Do not lead with health score."]
2. [Specific action — e.g., "Review open tickets — contact support team to confirm P2 ticket [ID] has a resolution timeline and communicate it to the champion."]
3. [Escalation action — e.g., "Notify CS lead per escalation matrix via Slack. Share this memo as context."]

**This week:**
1. [Action — e.g., "Meet with champion to understand usage drop root cause. Prepare 3 questions: [specific questions based on signals]."]
2. [Action — e.g., "Update health assessment in CRM with current signals."]

**If [specific trigger] occurs:**
1. [Escalation path — e.g., "If sponsor confirms departure, escalate to Critical immediately. Route to Head of CS within SLA. Loop in AE."]

---

**Success criteria for recovery**

What does "risk resolved" look like? Define 1-3 observable conditions:
- [Condition 1 — e.g., "Usage returns to >[threshold]% of baseline within 30 days"]
- [Condition 2 — e.g., "Executive sponsor re-engaged and confirmed for next QBR"]
- [Condition 3 — e.g., "Champion confirms competitor evaluation is not active"]

Review date: [recommend a specific date to reassess — typically 2-4 weeks out]

---

## Escalation Memo (`--escalation-memo`)

One-page version for the CSM's manager, VP CS, or CRO. No internal health model details — just the facts they need to act.

---

**Account Risk Memo — [Account Name]**
*[Date] · Prepared by: [CSM name]*

**For:** [Manager / VP CS / CRO — configured escalation contact]

---

| Field | Detail |
|-------|--------|
| Account | [Account name] |
| ARR | $[amount] |
| Renewal | [date] — [N days] |
| Segment | [segment] |
| Risk level | [Critical / High / Medium] |

**Risk signals:**
- [Signal 1]: [1-line evidence]
- [Signal 2]: [1-line evidence]
- [Signal 3 if present]: [1-line evidence]

**Most likely root cause:**
[2-3 sentences. Plain language. What is the risk and why.]

**Recommended action from CSM:**
[What the CSM is doing. What they need from leadership.]

**If no action is taken:**
[Likely outcome — e.g., "Renewal at risk of churn or significant contraction based on signals present. Renewal is in [N] days."]

**Next step:**
[Specific ask — e.g., "Request 20 minutes with the customer's CFO to reinforce the executive relationship. CSM to arrange. Your name on the invite increases response rate significantly."]

---

## Reviewer Note

> **⚠️ Reviewer note**
> - **Sources:** [CRM — HubSpot ✓ live | Call recording — Glyphic AI ✓ live | M365 ✓ live | CS Platform — not connected | user-provided context]
> - **Data as of:** [timestamp per source]
> - **Risk signals:** [Confirmed from data | CSM-reported — not independently verified]
> - **Escalation routing:** [Applied configured matrix — verify contact names are current]
> - **Flagged for your judgment:** [N items marked `[review]` inline | none]
> - **Before escalating:** Confirm ARR and renewal date with CRM before sharing escalation memo with leadership.

---

## Output

Risk flag report — format driven by `--brief` (default) or `--escalation-memo` flag. Brief produces a structured risk summary with signal inventory, severity tier, and recommended response actions. Escalation memo mode produces a clean one-page memo for leadership. See mode-specific sections for field-level structure.

## Guardrails

**Risk signals require evidence.** A signal is listed as Present only if there is direct evidence. Absence of engagement is "Unknown" or "Declining," not "Confirmed at risk."

**Root cause is a hypothesis, not a verdict.** The memo names the leading explanation and the evidence behind it. It does not state the account will churn.

**No escalation without a path.** Every escalation recommendation names the contact, channel, and SLA from the configured matrix. A risk flag without a routing is not actionable.

**Escalation memo is not the full brief.** The escalation memo omits health model component weights, internal stakeholder notes, and expansion context. Those stay in the CSM brief.

**Expansion context.** If the account has expansion signals, do not include them in the risk memo. Risk memos focus on risk mitigation, not opportunity. Expansion context routes to the AE separately.

**Revenue accountability.** If the memo estimates ARR at risk or implies a renewal probability, the CSM reviewer validates the figure before the memo reaches finance or board reporting.

---

## After the Memo

- "Want a full escalation memo to send to leadership? Run again with `--escalation-memo`."
- "Renewal within 90 days — run renewal readiness: `/csm:renewal-readiness [account]`"
- "Need to track intervention progress? Update the account in `/csm:account-research` after each action."
- "Want to map the stakeholder risk specifically? `/csm:stakeholder-map [account] --sponsor-risk`"

---

## Reference Material

### Risk Flag — Reasoning Blueprint

#### Problem Classification Taxonomy

**1. Sudden Signal Spike**
- Characteristics: Single high-weight signal appears abruptly (exec sponsor departure, competitor eval surfaces, P1 escalation filed)
- Primary Risk: Rapid churn trajectory — the window to intervene is short and narrows daily
- Expert Focus: Validate the signal is real (not data lag), identify who internally already knows, determine if the customer has verbalized intent to leave

**2. Gradual Decay Pattern**
- Characteristics: Multiple medium-weight signals accumulating over weeks/months (usage drift, missed QBRs, declining NPS, contact frequency drop)
- Primary Risk: Silent churn — no single alarm fires, but the aggregate trend is unmistakable to a trained eye
- Expert Focus: Find the inflection point — when did engagement start declining? What changed in the customer's organization or priorities at that time?

**3. Renewal-Proximity Risk**
- Characteristics: Renewal within 90 days AND any risk signal present — the time constraint compresses every decision
- Primary Risk: Insufficient runway for recovery; leadership needs to be in the loop now, not after the CSM has "tried a few things"
- Expert Focus: Is there still a path to renewal, or is this now a save/negotiate scenario? What concessions (if any) are on the table?

**4. Stakeholder Disruption**
- Characteristics: Champion departure, sponsor reorg, buyer persona change, or political shift inside the customer org
- Primary Risk: Loss of internal advocacy — the product may still deliver value, but nobody inside the customer is positioned to defend the renewal
- Expert Focus: Map the new power structure fast. Who inherits the relationship? Is there a detractor now in a decision-making seat?

**5. Escalation-in-Progress**
- Characteristics: Customer has already escalated (support ticket, executive complaint, legal mention) — the CSM is responding, not initiating
- Primary Risk: Reactive posture limits options; the customer's narrative is already set and may have reached their leadership before yours
- Expert Focus: What has the customer already communicated internally? Match your escalation speed to theirs — never be a step behind

#### Domain Heuristics

**H1: The 72-Hour Rule**
When a high-weight signal fires, the first 72 hours determine whether the account enters recovery or free-fall. Delay past 72h and the customer interprets silence as indifference.

**H2: Signal Clustering Trumps Severity**
Two medium signals in the same 30-day window are more dangerous than one high signal in isolation. Clustering indicates systemic disengagement, not an isolated incident.

**H3: Sponsor Silence Is a Signal**
If the executive sponsor has not responded to outreach in 2+ attempts over 30 days, treat as a high-weight signal regardless of what the health score says. Health scores lag relationship decay.

**H4: Usage Drop Context Matters**
A 20% usage drop in a product with seasonal patterns is noise. The same drop during the customer's peak season is a five-alarm fire. Always normalize against the customer's own usage baseline and business cycle.

**H5: Escalation Routing Must Be Concrete**
"Escalate to leadership" is not a routing. A valid routing names the person, the channel, and the SLA. If the configured matrix doesn't cover this scenario, flag the gap — don't improvise a route.

**H6: The Memo Is Not the Intervention**
A risk flag memo is a communication artifact, not an action plan. If the CSM reads the memo and doesn't know exactly what to do Monday morning, the intervention plan failed.

**H7: Separate the Audience**
The CSM brief and the escalation memo serve different readers with different needs. Never leak health model internals into the escalation memo. Leadership needs the what and the ask, not the scoring methodology.

#### Common Failure Modes

**Sudden Signal Spike**
1. False alarm escalation — Signal is real but caused by a known, benign event (e.g., planned migration caused usage drop). Fix: Cross-reference with CSM notes and recent call transcripts before classifying severity.
2. Under-scoping the blast radius — Treating a sponsor departure as a single signal when it actually invalidates the success plan, QBR commitments, and expansion pipeline. Fix: Trace downstream dependencies of the signal before writing the intervention plan.

**Gradual Decay Pattern**
1. Boiling frog memo — Documenting each signal accurately but failing to convey urgency because no single signal is dramatic. Fix: Lead with the aggregate trend line and time-to-renewal, not the individual signals.
2. Root cause speculation without evidence — Inventing a narrative to explain the decay when the real answer is "we don't know yet." Fix: State the hypothesis clearly, name what evidence would confirm or refute it, and make discovery the first action item.

**Renewal-Proximity Risk**
1. Recovery plan with no runway — Recommending a 90-day intervention when renewal is in 45 days. Fix: Time-box every action against the renewal date. If the plan doesn't fit the window, escalate immediately.
2. Optimism bias in severity — Classifying as High when the signal pattern clearly maps to Critical, because the CSM "has a good relationship." Fix: Apply the configured severity matrix mechanically first, then note mitigating factors separately.

**Stakeholder Disruption**
1. Mapping the old org — Building the memo around the departed sponsor's context instead of the new decision-maker's priorities. Fix: Flag that stakeholder intelligence is stale and make re-mapping the first intervention action.
2. Assuming continuity — Treating the new contact as a continuation of the old relationship. Fix: Treat every stakeholder change as a soft reset — re-validate value prop fit with the new audience.

**Escalation-in-Progress**
1. Duplicating the customer's escalation — Writing a memo that restates what the customer already told your leadership, adding no new information. Fix: The memo must add the CSM's root cause hypothesis, the intervention plan, and the specific ask of leadership — things the customer's complaint doesn't contain.
2. Tone mismatch — Using cautious, hedged language in a memo about an account that has already filed a formal complaint. Fix: Match the urgency of your language to the customer's demonstrated urgency.

#### Expert Judgment Patterns

**Scope Decisions**
- If the account has both risk and expansion signals, exclude expansion from the risk memo entirely — mixing contexts dilutes urgency
- If the CSM provides narrative context but no structured data, build the memo from narrative and flag every signal as "CSM-reported, not independently verified"
- If multiple escalation matrix rows match, show all but name the primary route based on the highest-severity matching condition

**Sequencing Decisions**
- Always validate signal evidence before classifying severity — never classify first and justify later
- Root cause hypothesis comes after signal inventory, not before — the hypothesis must be grounded in the signals documented
- Intervention plan is last because it depends on severity classification and escalation routing

**Depth Decisions**
- Brief mode: Full signal breakdown, internal health context, detailed intervention plan — this is the CSM's working document
- Escalation memo mode: Stripped to essentials — account facts, signals, root cause, ask. No health model internals, no scoring methodology
- If severity is Critical, both modes should be produced even if only one was requested — flag this as a recommendation

**Stakeholder Decisions**
- The CSM brief stays internal. If anyone beyond the CS team will see it, flag for confidentiality review
- The escalation memo assumes the reader has zero account context — every relevant fact must be stated, not referenced
- If the configured escalation contact is no longer in role, flag the gap rather than routing to a guess

**Confidence Decisions**
- Signals from live integrations: state as confirmed with timestamp
- Signals from CSM narrative: state as reported, recommend verification
- Signals inferred from absence of data (e.g., "no QBR in 90 days"): state as inferred from data gap, note that the absence could reflect a data sync issue rather than a real miss
