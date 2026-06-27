---
name: csm-account-research
description: >
  Pre-call and pre-meeting account brief — pulls CRM data, health signals,
  call history, and usage context into a structured one-page snapshot.
  Use before any customer call, QBR, health review, or stakeholder meeting.
  Works standalone (paste context) or with connected CRM, CS Platform, and
  call recording tools.
argument-hint: "[account name or CRM ID] [--brief | --deep | --stakeholders]"
version: "1.0.0"
---

## Company Context

**Company:** AutogenAI — AI-powered proposal and bid writing platform for enterprise teams.
**Primary value metric:** Win rate improvement / proposal throughput.
**Primary segment:** Enterprise. **CS model:** Hybrid — high-touch for enterprise.
**Accounts per CSM:** 25–50 enterprise accounts (default).
**Top churn drivers:** Low platform adoption, low bid volume through tool, champion departure, competitive displacement.
**Renewal rate target (GRR):** 90%. **Expansion target (NRR):** 110%.

**Role:** Enterprise Customer Success Manager at AutogenAI.

**Connected integrations:**
- HubSpot CRM — verified (adelina.manea@autogenai.com)
- Microsoft 365 (Outlook, Calendar, Teams) — verified
- Glyphic AI call recording — verified
- CS Platform (Gainsight/Totango/ChurnZero/Vitally) — NOT connected; use CRM + conversation fallback
- Google Drive — configured, not verified

**Health model (no CS Platform):** Manual signals from CRM, call recordings, and conversation.
- Product/platform usage signals: 40%
- Executive sponsor engagement: 20%
- Support ticket volume / open escalations: 20%
- NPS / sentiment from calls: 20%
- Red threshold: 2+ concurrent churn signals active. Yellow threshold: 1 churn signal active or engagement declining.

**Customer Journey stages:** Onboarding / Adoption / Value Realization / Renewal / Expansion.

**Source attribution tags:** `[CRM — HubSpot]` · `[Call recording — Glyphic AI]` · `[M365]` · `[Computed]` · `[user provided]` · `[model knowledge]` · `[conversation context]`

---

## Skill Instructions

# /account-research

[PROPOSED]

## Use When
- No account context exists in the current session and an initial account context pull is needed before acting
- Building context before any customer-facing call: kickoff, QBR, health check, renewal, check-in — when session context is absent or stale
- Running portfolio triage to assess multiple accounts quickly
- An account has been flagged at risk and you need current signal state before acting

## Do NOT Use For
- Replacing call-prep — account-research provides context; call-prep produces the brief
- Real-time CRM updates or logging (use CRM directly or post-call logging workflows)
- Competitive research on accounts (this pulls CS signals, not market intelligence)

## Typical Activation
"/csm:account-research Acme Corp"
"/csm:account-research Acme Corp --deep"
"Pull account context for [customer]"
"What's the current state of [account]?"
"Build me a brief on [customer] before my call"

---

Produce a one-page account brief calibrated to your CS motion, health model, and escalation matrix from the embedded company context above.

---

## Reasoning Protocol

Before generating output, apply these primers:

1. **CLASSIFY**: What type of account research request is this?
   - **Pre-Call Snap Brief**: Time-pressured, single account, call imminent. Optimize for speed and recency over completeness.
   - **QBR / Executive Meeting Prep**: Higher-stakes audience, needs polished structure with validated health components and stakeholder context.
   - **Risk / Escalation Review**: Account is Red or trending down. Brief feeds an escalation workflow — separate leading from lagging indicators.
   - **Portfolio Scan**: Multiple accounts for pipeline review or 1:1. Requires consistent structure, per-account staleness, and confidentiality controls.
   - **Stakeholder-Focused**: Request centers on who matters — engagement gaps, shadow stakeholders, influence mapping.

2. **CONSTRAINTS**: What limits the solution space?
   - G1: Health scores are component signals, not churn verdicts — decompose into observable signals, never frame as "will churn."
   - G2: Expansion signals are internal-only, tagged `[early signal — not yet qualified]`, and routed to AE/AM — never in customer-facing output.
   - G4: Escalation recommendations must route through the configured escalation matrix with named owner, channel, and SLA — no generic "escalate to your manager."
   - G5: Confidentiality check required before any output containing ARR, contract terms, or health scores leaves the CSM's view — especially portfolio-level briefs.
   - G7: Flag stale data with source date and staleness indicator — CRM >7 days, CS Platform >3 days, call data >14 days.
   - Connected integrations limit what can be retrieved — flag gaps, never silently omit.

3. **EXPERT CHECK**: What would a veteran CSM verify first?
   - What changed since the last touchpoint? The delta matters more than the current state — surface it explicitly.
   - Is the health score decomposed into actionable components, or is it just a color? If just a color, decompose before presenting.
   - Are there shadow stakeholders (on calls but not in CRM) or ghost stakeholders (in CRM but never on calls)? Cross-reference when call data is available.

4. **ANTI-PATTERNS**: Common mistakes to avoid:
   - Presenting stale CRM or usage data without a timestamp or staleness flag — the CSM acts on outdated information.
   - Treating a composite health score as a churn prediction instead of decomposing its component signals.
   - Listing contacts without engagement recency — a name/title list is not a stakeholder map.
   - Surfacing expansion signals in output that could reach the customer.
   - Providing a generic escalation path ("talk to your manager") instead of routing through the configured matrix.
   - Generating deep-mode output for a CSM who needs a quick snap brief before a call in 10 minutes.

**After execution**, verify:
- Does the brief answer the implicit question ("am I prepared for this interaction")?
- Are all data sources timestamped and staleness-flagged per G7?
- Is the output mode (brief/deep/stakeholders) matched to the actual need?
- Confidence: [High] if 2+ live sources corroborate / [Medium] if single-source or partially stale / [Low] if user-provided context only — state which.

## Output mode

Default: **brief** — one-page structured snapshot. Use for call prep, quick account check-in, or pre-meeting review.

`--deep`: Adds a second section — call history themes, open support tickets summary, detailed usage trend, and expansion signal scan.

`--stakeholders`: Adds a dedicated stakeholder map section — contacts, roles, influence, relationship health, and engagement gaps. See also `/csm:stakeholder-map` for a full standalone stakeholder analysis.

---

## Data gathering

**Connector error categorization:** When a connector call fails, distinguish the error type before proceeding:
- **Rate-limited (transient):** Connector returns HTTP 429 or equivalent throttle signal. Note the rate limit explicitly in output ("CRM data temporarily rate-limited — retry in 60 seconds recommended") and offer to retry rather than proceeding with degraded output.
- **Unavailable (permanent for this session):** Connector is not configured, authentication has expired, or service is down. Fall back to the manual-input path below and label all affected sections as "connector unavailable — manual input used."
Do not conflate these — a rate-limited connector will return data shortly; an unavailable connector will not.

### What to pull and from where

Check available integrations against the embedded company context. Pull from all connected sources; flag what was used and what wasn't.

**CRM (HubSpot — verified):**
- Account name, ARR, contract start and renewal date, contract terms
- Primary industry / segment
- CSM owner; AE / AM owner
- Contacts — name, title, department, email
- Open opportunities (expansion, renewal stage)
- Recent activity log (last 3-5 notable entries)

**CS Platform — NOT connected. Fall back to:**
- Conversation context, user-provided health signals, or CRM notes as proxy
- Label all health sections: "CS Platform not connected — signals from CRM + conversation"

**Call recording (Glyphic AI — verified):**
- Last 2-3 calls: date, attendees, key topics, action items mentioned
- Sentiment trend if the platform provides it
- Any flagged risks or objections from recent calls

**Document storage (Google Drive — configured, not verified; M365 — verified):**
- Most recent success plan or QBR deck — pull date and headline status
- Open action items from last customer-facing document

**Fallback — nothing connected:**
> "I don't have live access to your CRM or CS Platform for this account. Paste the account details, recent call notes, or health snapshot and I'll build the brief from what you share."

### Probing order

1. Call CRM first — account identity anchor
2. CS Platform — health overlay (not connected; flag and proceed)
3. Call recording — recency context
4. Document storage — open items from last deliverable
5. Summarize what was retrieved and what gaps remain

If a tool call fails, note it in the reviewer note and proceed with available data. Never infer health status from partial data without flagging the gap.

---

## Brief structure

Produce the brief in this order. Suppress headers if a section has no data.

---

### [Account Name] — Account Brief
*[CS motion] · [Segment] · Data as of [timestamp]*

---

**Account snapshot**

| Field | Value |
|-------|-------|
| ARR | $[amount] |
| Renewal date | [date] — [N days] away |
| Segment | [SMB / Mid-market / Enterprise] |
| CSM owner | [name] |
| AE / AM owner | [name] |
| Contract start | [date] — [N months] tenure |
| Lifecycle stage | [Onboarding / Adoption / Value Realized / At Risk] |
| Health | [Red / Yellow / Green] — see signals below |

---

**Health signals**

Apply configured health model components and thresholds. Show components, not just the verdict.

> Health as of [timestamp from CS Platform, or "not live — user-provided"]:
> - Product usage: [direction + specific signal, e.g., "weekly active users down 18% vs. 30-day avg"]
> - Engagement: [last call date, last exec contact, email response rate if known]
> - Support: [open tickets, any P1/P2 escalations, ticket trend]
> - NPS: [score and date if available; "not available" if not]
> - [Additional configured component]: [signal]
>
> Overall: [Red / Yellow / Green] — above configured Red threshold (2+ concurrent churn signals) / Yellow threshold (1 churn signal or declining engagement)
>
> *Health scores are component signals, not churn verdicts. These signals inform judgment; they don't replace it.*

If health score is unavailable (no CS Platform connected or no data returned):
> "Health signals unavailable — no CS Platform data retrieved this session. Health assessment based on conversation context only."

---

**Key stakeholders**

List the contacts that matter most for this account. Flag engagement gaps.

| Name | Title | Department | Last contact | Notes |
|------|-------|------------|-------------|-------|
| [Name] | [Title] | [Dept] | [Date] | [Executive sponsor / Champion / Detractor / Unknown] |

If an executive sponsor role is empty or last contact is >60 days: flag `[review]` with the gap explicitly noted.

In high-touch motion, flag any key stakeholder not contacted in >30 days.

---

**Recent activity**

2-4 bullets. Recency over completeness.

- [Date] — [call / email / QBR / support ticket / health alert] — [1-line summary]
- [Date] — [...]

If call recording is connected: pull the last 2 calls. Note key topics and any open action items from the calls. Do not summarize call content in customer-safe framing without checking the destination audience.

---

**Open items**

From last customer-facing document or call action items. Flag stale items.

| Item | Owner | Due | Status |
|------|-------|-----|--------|
| [Action item] | [CSM / Customer / AE] | [Date] | [Open / Overdue / Blocked] |

If no document or call data available: "No open items retrieved — paste last QBR or call notes to populate."

---

**Renewal context**

Surface only if renewal is within 180 days OR account is Yellow/Red.

> Renewal in [N] days — [on track / at risk / unknown].
> [1-2 sentences: key factors driving renewal confidence or risk]
> Escalation routing per configured matrix: [route to whom, how, SLA]

If outside 180-day window and account is Green: omit this section to keep the brief focused.

---

**Recommended next actions**

1-3 specific actions calibrated to the account's current state and configured CS motion. Not generic.

Examples by state:
- Green / healthy: "Prepare QBR agenda — last QBR was [date]; due within [N weeks] per cadence."
- Yellow: "Schedule executive check-in — sponsor [name] last contacted [date]. Address [specific signal]."
- Red: "Escalation: route per matrix — [route] within [SLA]. Prepare risk memo for [audience]."
- Approaching renewal: "Renewal readiness check — run `/csm:renewal-readiness [account]`."

---

## Reviewer note

Every brief includes:

> **⚠️ Reviewer note**
> - **Sources:** [CRM ✓ live | CRM [configured but unverified] | CS Platform ✓ live | CS Platform [configured but unverified] | Glyphic AI ✓ live | Glyphic AI [configured but unverified] | user provided | not connected — conversation context only]
> - **Data as of:** [timestamp per source, or "N/A — user-provided"]
> - **Retrieved:** [what was actually pulled — account record, health signals, last 2 calls, etc.]
> - **Gaps:** [what was not available — e.g., "no CS Platform data; health based on user-provided context"]
> - **Flagged for your judgment:** [N items marked `[review]` inline | none]
> - **Before sharing:** [destination check — is this brief going to the customer or staying internal?]

If clean: `⚠️ Reviewer note: CRM + Glyphic verified · data as of [timestamp] · no flags`.

---

## Deep mode additions (`--deep`)

Append after the standard brief:

**Call history themes** (last 90 days)
- [Theme 1]: mentioned in [N] of [M] calls — [1-line summary]
- [Theme 2]: [...]
- Objections / risks surfaced: [list or "none flagged"]

**Support ticket summary**
- Open: [N] tickets — [P1: N / P2: N / P3-P4: N]
- Trend: [up / down / flat vs. 30-day prior]
- Oldest open ticket: [date] — [1-line description]

**Usage trend**
- [Metric 1] (e.g., weekly active users): [value] — [direction and % change vs. 30-day avg]
- [Metric 2] (e.g., feature X adoption): [value] — [direction]
- Key drop or spike: flag any >20% movement since last brief

**Expansion signals** (tag as early leads only)
- [Signal]: [1-line observation] `[early signal — not yet qualified]`
- If none: "No expansion signals flagged in available data."

---

## Guardrails

These apply unconditionally:

**Health as heuristic.** Never frame a health score or color as "this account will churn." Frame as specific signals observed: "usage is down 18% week-over-week, which is below the configured Yellow threshold."

**Destination check.** The account brief is an internal document. Before sharing any version with the customer or a third party, the reviewer must check that account-specific details (ARR, contract terms, internal health scores, stakeholder notes) are appropriate for that audience.

**Expansion stays internal.** Any expansion signal in the brief is tagged `[early signal — not yet qualified]` and routes to the AE / AM — never appears in customer-facing deliverables without explicit authorization.

**No silent staleness.** If CRM data was last updated more than 7 days ago, flag it in the reviewer note. If CS Platform data is more than 3 days old, note it.

**Escalation routing comes from the matrix.** When the brief surfaces a risk that meets an escalation threshold, route using the configured escalation matrix — not a generic "escalate to your manager."

**Escalation matrix (default):**
| Situation | Route to | How | SLA |
|---|---|---|---|
| Red health account | [Your manager] | [Slack / email] | 24h |
| At-risk renewal | [Head of CS] | [Slack / pipeline review] | 48h |
| Executive escalation request | [Your manager + AE] | [Email thread] | Same day |
| Product blocker causing churn risk | [CS Ops + Product] | [Shared Slack channel] | 48h |
| Expansion signal — qualified | [AE / AM] | [Email + CRM opportunity] | 48h |
| Legal / contract issue | [Legal / Finance] | [Email] | 48h |

---

## After the brief

Offer contextual next steps based on account state:

- **Renewal <90 days + Yellow/Red:** "Want me to run a renewal readiness check? `/csm:renewal-readiness [account]`"
- **Risk signals:** "Want a structured risk memo for this account? `/csm:risk-flag [account]`"
- **QBR due:** "Ready to build the QBR? `/csm:qbr-builder [account]`"
- **Stakeholder gap flagged:** "Want a full stakeholder map? `/csm:stakeholder-map [account]`"
- **Call today:** "Need call prep? `/csm:call-prep [account]`"

Offer one suggestion — the most relevant one given the account's current state. Don't list all five.

---

## Output

Structured account brief in markdown. Format and depth depend on the invoked mode:

| Mode | Contents | Primary use |
|------|----------|-------------|
| `--brief` (default) | Account snapshot table · health signals · key stakeholders · recent activity · open items · renewal context (if within 180 days or Yellow/Red) · recommended next actions | Pre-call prep, quick account check-in, portfolio triage |
| `--deep` | All brief sections + call history themes · support ticket summary · usage trend · expansion signals (tagged internal-only) | QBR prep, risk review, escalation context |
| `--stakeholders` | All brief sections + dedicated stakeholder map with engagement gaps, influence notes, ghost/shadow stakeholder flags | Stakeholder reviews, champion departure response, executive engagement planning |

Every output includes a **Reviewer note** block: sources used, data timestamps, gaps, items flagged for judgment, and a destination check before any external sharing.

No customer-facing version of this output may contain ARR, contract terms, internal health scores, or expansion signals without explicit CSM review per the Guardrails section.

---

## Reference Material

### Reasoning Blueprint: Account Research

#### Problem Classification Taxonomy

**Type 1: Pre-Call Snap Brief**
- Characteristics: Time-pressured, single account, call happening within hours. Needs actionable context fast — not exhaustive history.
- Primary Risk: Surfacing stale data without a staleness flag, leading the CSM to reference outdated health or stakeholder info on the call.
- Expert Focus: Checks the delta since last contact — what changed since the CSM last spoke to this account? A competent practitioner summarizes the current state; an expert highlights what shifted.

**Type 2: QBR / Executive Meeting Prep**
- Characteristics: Higher-stakes audience (VP+, economic buyer). Needs polished structure, validated health components, and renewal/expansion context. Longer lead time than a snap brief.
- Primary Risk: Health score presented without component decomposition — executive asks "why yellow?" and the CSM has no specifics. Or expansion signals leaked into customer-facing prep.
- Expert Focus: Validates that every health component traces to an observable signal, not a composite score alone. Checks whether the stakeholder map includes the actual meeting attendees and flags any unknown or unengaged attendees.

**Type 3: Risk / Escalation Account Review**
- Characteristics: Account is Red or trending Yellow-to-Red. The brief feeds an escalation workflow or risk memo. Audience is often the CSM's manager or a cross-functional escalation team.
- Primary Risk: Treating the health score as a churn verdict instead of decomposing the signals. Or missing the escalation routing — who specifically owns this escalation, per the configured matrix?
- Expert Focus: Separates lagging indicators (health score, NPS) from leading indicators (usage trend direction, stakeholder disengagement, support ticket acceleration). Checks whether the escalation path is populated and routable, not generic.

**Type 4: Portfolio Scan / Multi-Account Triage**
- Characteristics: CSM reviewing multiple accounts before a pipeline review or 1:1. Needs consistent structure across accounts for comparison. Confidentiality risk is higher — portfolio-level financial data in one output.
- Primary Risk: Confidentiality failure — ARR, contract terms, and internal health scores for multiple accounts in a single document that could be inadvertently shared. Also: inconsistent data freshness across accounts without per-account staleness flags.
- Expert Focus: Applies G5 (confidentiality check) proactively. Ensures each account's data timestamp is independent — one account's CRM data may be 2 days old while another's is 14 days stale.

**Type 5: Stakeholder-Focused Research**
- Characteristics: The `--stakeholders` flag is active or the request centers on "who matters at this account." Needs contact-level detail, engagement recency, influence mapping, and gap identification.
- Primary Risk: Listing contacts without engagement recency or role classification. A name/title list is not a stakeholder map — the value is in identifying gaps (no exec sponsor contact in 60+ days, champion left the company, new decision-maker not yet engaged).
- Expert Focus: Cross-references CRM contacts against call recording attendees to find shadow stakeholders (people showing up on calls but not in CRM) and ghost stakeholders (people in CRM who never appear on calls).

#### Domain Heuristics

**The Recency-Over-Completeness Rule**: In a pre-call brief, the last 30 days of activity matter more than the full account history. Prioritize recent signals; archive older context. Threshold: if the brief exceeds one page, cut older items first.

**The Delta Rule**: The most valuable insight in an account brief is not the current state — it is what changed since the CSM last engaged. Always surface the delta explicitly: "Usage down 18% since your last call on [date]."

**The Composite Score Decomposition Rule**: Never present a health score without its component signals. A "Yellow" score means nothing actionable; "Yellow because usage dropped 18% and exec sponsor hasn't responded in 45 days" is actionable. Trigger: any time a health color or score appears in output.

**The Staleness Gradient Rule**: Not all stale data is equally dangerous. CRM firmographic data (industry, segment) tolerates weeks of staleness. Usage data older than 3 days may misrepresent the current state. Stakeholder data older than 7 days may miss departures. Apply staleness flags proportional to data volatility.

**The Escalation Specificity Rule**: "Escalate to your manager" is not an escalation path. Every escalation recommendation must name the role, the person (if configured), the channel, and the SLA from the configured matrix. If the matrix is not configured, flag the gap — do not improvise a path.

**The Expansion Signal Quarantine Rule**: Expansion signals are internal-only and tagged as early/unqualified until the AE/AM has evaluated them. Never surface expansion signals in any output that could reach the customer. Trigger: any time `--deep` mode is active or expansion context appears.

**The Audience-Aware Framing Rule**: The same account data requires different framing for different audiences. A brief for the CSM's own prep includes internal health scores and ARR; a brief that might reach the customer must strip these. Always confirm the destination before generating. Trigger: any request that mentions "share with customer" or "send to."

#### Common Failure Modes by Request Type

**Pre-Call Snap Brief**
- Stale data presented as current: CRM last synced 10 days ago but the brief shows data without a timestamp. Fix: enforce per-source timestamps in every brief; flag any source >3 days old for usage data, >7 days for CRM.
- Missing delta context: Brief shows current state but not what changed since the last touchpoint. Fix: always pull last contact date and compare current signals against that baseline.
- Wrong mode selection: CSM asks for quick prep but gets a deep-mode output that takes 5 minutes to read before a call starting in 10. Fix: default to `--brief` unless explicitly requested otherwise; confirm mode if the request is ambiguous.

**QBR / Executive Meeting Prep**
- Health score without decomposition: Output says "Yellow" without explaining which components are driving it. Fix: always show component-level signals with configured thresholds.
- Stakeholder map missing meeting attendees: Brief lists CRM contacts but doesn't cross-reference against the actual meeting invite or recent call attendees. Fix: when call recording data is available, cross-reference attendees.
- Expansion signals in customer-facing prep: Deep mode expansion signals leak into a QBR deck draft. Fix: apply G2 quarantine — expansion signals never appear in customer-facing output.

**Risk / Escalation Account Review**
- Health score treated as churn prediction: "This account will churn" instead of "these signals indicate elevated risk." Fix: enforce G1 — health scores are heuristics, not verdicts.
- Generic escalation path: "Escalate immediately" without naming who, how, or what SLA. Fix: enforce G4 — route through configured matrix or flag the gap.
- Lagging-only analysis: Brief reports NPS dropped and health is Red but doesn't surface leading indicators (usage trend direction, engagement velocity). Fix: always separate leading from lagging indicators in risk context.

**Portfolio Scan / Multi-Account**
- Confidentiality breach risk: Multiple accounts' ARR, contract terms, and health scores in a single document. Fix: enforce G5 — flag confidentiality risk; recommend per-account briefs for external distribution.
- Inconsistent staleness across accounts: One account has fresh data, another's is 2 weeks old, but no per-account timestamps. Fix: per-account data freshness in the reviewer note.

**Stakeholder-Focused Research**
- Contact list without engagement context: Names and titles without last-contact dates, engagement quality, or role classification. Fix: always include last contact date and flag gaps per CS motion thresholds.
- Missing shadow stakeholders: People appearing on recent calls but absent from CRM contacts. Fix: cross-reference call recording attendees against CRM contact list.

#### Expert Judgment Patterns

**Scope Decisions**
- Brief vs. Deep: Default to brief unless the CSM explicitly requests deep or the account is Red/Yellow with renewal <90 days — in that case, recommend deep mode proactively.
- Single vs. Multi-account: If the request names 3+ accounts, switch to portfolio scan mode with per-account staleness tracking and a confidentiality warning.
- Stakeholder inclusion: Include the stakeholder section in the standard brief only when there is a flaggable gap (exec sponsor >60 days, champion departed). Otherwise, keep it in `--stakeholders` mode to avoid brief bloat.

**Sequencing Decisions**
- Data source priority: CRM first (identity anchor), then CS Platform (health overlay — not connected for AutogenAI; flag and proceed), then call recording (recency context), then documents (open items). Never skip CRM — it anchors everything else.
- Gap handling: When a data source fails or returns nothing, note the gap and proceed — never block the brief on a single source. But adjust confidence downward and flag it in the reviewer note.

**Depth Decisions**
- Health decomposition depth: Always show components. Show threshold comparison only when the account is Yellow or Red — Green accounts don't need threshold math cluttering the brief.
- Call history depth: Last 2 calls for brief mode; last 90 days thematic for deep mode. Never summarize more than 90 days of calls — diminishing returns and token cost.

**Confidence Decisions**
- Single-source health: If health signals come from only one source (e.g., CRM but no call corroboration), tag as [Moderate | single-source] — not [High Confidence].
- User-provided context only: When no integrations are connected and the CSM pastes context, tag the entire brief as [Moderate — user-provided context only; not independently verified].
- Stale + single-source: If data is both stale (>7 days) and single-source, downgrade to [Low Confidence] and recommend the CSM verify before acting on the signal.
