---
name: csm-call-prep
description: >
  Pre-call preparation brief — agenda, attendee context, recommended talking
  points, and questions calibrated to your CS motion and account state.
  Use 24-48 hours before any customer call: kickoff, QBR, health check,
  renewal conversation, or ad-hoc check-in. Pairs with account-research for
  full context.

---

## Company Context

**AutogenAI** is an AI-powered proposal and bid writing platform for enterprise teams. Primary value metric: win rate improvement and proposal throughput. Top churn drivers: low platform adoption, low bid volume through tool, champion departure, competitive displacement.

**CS role:** Enterprise CSM — hybrid/segmented model (high-touch enterprise, tech-touch SMB/mid-market). Typical book: 25-50 enterprise accounts. GRR target: 90%. NRR target: 110%.

**Integrations available:** HubSpot CRM (verified), Microsoft 365/Outlook/Calendar/Teams (verified), Glyphic AI call recording (verified). CS Platform (Gainsight/Totango/ChurnZero/Vitally) not connected — health signals come from CRM and call recordings. Google Drive configured but unverified.

**Health model (no CS Platform):** Manual signals — product/platform usage 40%, executive sponsor engagement 20%, support ticket volume 20%, NPS/sentiment from calls 20%. Red = 2+ concurrent churn signals. Yellow = 1 churn signal or declining engagement.

**Source attribution tags:** `[CRM — HubSpot]` `[Call recording — Glyphic AI]` `[M365]` `[Computed]` `[user provided]` `[model knowledge]` `[conversation context]`

**Escalation defaults:** Red health → manager (24h); at-risk renewal → Head of CS (48h); executive escalation → manager + AE (same day); product blocker → CS Ops + Product (48h); qualified expansion → AE/AM (48h).

**Decision posture:** Flag ambiguous signals `[review]` — over-flagging is a two-way door, under-flagging is not. Data freshness required on every output; flag data older than 7 days.

---

## Skill Instructions

# /call-prep

[PROPOSED]

## Use When
- 24-48 hours before any customer call: kickoff, QBR, health check, renewal, escalation, check-in
- Account has active risk signals and you need a structured brief before engaging
- Executive sponsor is attending and preparation needs to be higher-fidelity than usual
- Renewal is within 90 days and you need value alignment framing before the commercial conversation

## Do NOT Use For
- Post-call logging or follow-up drafting — use post-call workflow instead
- Renewal commercial prep (pricing, negotiation) — use /csm:renewal-readiness
- Account deep-dive research without a call scheduled — use /csm:account-research
- Kickoff calls where no account context exists in session yet — run `/csm:account-research` first to populate session context, then call-prep
- Routine check-ins with nothing substantive to discuss — recommend async instead

## Typical Activation
"/csm:call-prep Acme Corp qbr"
"/csm:call-prep Acme Corp renewal"
"/csm:call-prep Acme Corp kickoff"
"Prep me for my call with [customer] tomorrow"
"Build a brief for my health check with [account]"

---

Produce a focused pre-call brief: what to cover, who's attending, what to listen
for, and what to leave the call having accomplished.

---

## Reasoning Protocol

Before generating output, apply these primers:

1. **CLASSIFY**: What type of call prep request is this?
   - **Discovery / Kickoff**: First or early-stage call — sparse historical data, goal is success criteria alignment and relationship establishment
   - **QBR / Executive Business Review**: Periodic value review with executive audience — requires metrics against success criteria, forward-looking priorities
   - **Health Check / At-Risk**: Triggered by a health signal — usage drop, NPS decline, support spike, or champion departure; shorter format, diagnostic focus
   - **Renewal Conversation**: Within 90 days of renewal — commercial context present but CSM role is value alignment and objection surfacing, not close
   - **Escalation / Recovery**: Active or recently resolved escalation — trust is damaged; goal is confidence restoration and remediation confirmation
   - **Routine Check-in**: Regular cadence call with no major trigger — risk of perfunctory meeting; may recommend async instead

2. **CONSTRAINTS**: What limits the solution space?
   - Which data sources are available? (CRM, CS platform, call recordings, documents) — fewer than 3 of 4 = low-confidence brief, flag explicitly
   - Is the attendee list confirmed or assumed? Unconfirmed attendees cap the brief's value
   - What was the outcome of the last call? Open action items carry forward — never drop them silently
   - Is there an active escalation or competitive signal that changes the entire call framing?
   - G4: Do not recommend escalation without a named escalation path configured in the escalation matrix
   - G5: Internal data (health scores, ARR, expansion signals) must never appear in customer-facing output
   - G7: Flag any data older than 30 days with source date and staleness indicator

3. **EXPERT CHECK**: What would a veteran CSM verify first?
   - Are the success criteria being referenced still the criteria the customer cares about, or have priorities shifted since they were set?
   - Is the right stakeholder on this call? Missing executive sponsor on a QBR or missing economic buyer on a renewal changes the call's ceiling
   - Has anything been promised to this customer (in escalation, prior calls, or success plans) that hasn't been delivered yet?

4. **ANTI-PATTERNS**: Common mistakes to avoid:
   - :x: Building the brief around product usage metrics instead of business outcomes the customer's executive sponsor cares about
   - :x: Leading a health check or escalation call with platform data instead of opening with curiosity and listening
   - :x: Including expansion signals, health scores, or internal stakeholder assessments in any material that could reach the customer
   - :x: Preparing a full brief for a routine check-in when there's nothing substantive to discuss — recommend async instead
   - :x: Producing renewal prep that crosses into pricing, discount, or negotiation territory — that's AE/AM scope

**After execution**, verify:
- Does every talking point reference account-specific context, not generic advice?
- Is the internal/external boundary respected — no health scores, expansion tags, or relationship assessments in customer-visible content?
- Does the brief identify the single most important thing to accomplish on this call?
- Confidence: [High/Medium/Low] because [data source coverage, attendee confirmation status, signal freshness]

## Call type detection

If the user provides a call type argument, use it. If not, infer from context:

- **Kickoff** — first call with a new account; onboarding just started
- **QBR** (Quarterly Business Review) — periodic value review; typically Q-aligned
- **Health check** — triggered by health signal; may be Yellow/Red account
- **Renewal** — within 90 days of renewal date; commercial context present
- **Check-in** — regular cadence call; no major trigger
- **Custom** — user describes the call purpose; adapt the brief structure to it

If type cannot be determined: ask one question — "What's the purpose of this call?"
Do not build a generic brief.

---

## Data gathering

**Connector error categorization:** When a connector call fails, distinguish the error type before proceeding:
- **Rate-limited (transient):** Connector returns HTTP 429 or equivalent throttle signal. Note the rate limit explicitly in output ("CRM data temporarily rate-limited — retry in 60 seconds recommended") and offer to retry rather than proceeding with degraded output.
- **Unavailable (permanent for this session):** Connector is not configured, authentication has expired, or service is down. Fall back to the manual-input path below and label all affected sections as "connector unavailable — manual input used."
Do not conflate these — a rate-limited connector will return data shortly; an unavailable connector will not.

Before building the brief, pull what's available.

**First, check if an account-research brief exists in this session.** If the user already ran `/csm:account-research` for this account, use that context instead of re-pulling from connectors.

**If no prior account-research:**

Pull from connected integrations:
- CRM (HubSpot): account snapshot, renewal date, ARR, key contacts and their titles
- CS Platform: not connected — skip; note in reviewer note
- Call recording (Glyphic AI): last 1-2 calls — date, attendees, key topics, open action items
- Document storage (Google Drive — unverified): most recent success plan or QBR (date + status)

Minimum viable brief (no connectors):
> "I don't have live account data. Tell me: Who's on the call? What's the account
> health? Any recent context I should know? I'll build the brief from what you share."

---

## Brief structure

Produce in this order. Omit sections with no data rather than filling with placeholders.

---

### Call Prep — [Account Name]
*[Call type] · [Date / time if known] · [Duration if known]*

---

**Purpose and desired outcome**

One sentence: why this call exists.
One sentence: what "a good call" looks like at the end.

Examples by type:
- Kickoff: "Establish shared success criteria, introduce the onboarding plan, and confirm sponsor engagement — account leaves knowing what happens next."
- QBR: "Demonstrate value delivered against agreed success criteria and align on next-period priorities — customer leaves feeling forward momentum."
- Health check: "Understand the root cause of [specific signal], agree on a recovery action, and set a follow-up date — call leaves no ambiguity about next steps."
- Renewal: "Surface value realized, pre-empt known objections, advance toward renewal commitment — call ends with a next step, not an open loop."

---

**Who's on the call**

| Name | Title | Role in account | Notes |
|------|-------|----------------|-------|
| [Name] | [Title] | [Executive sponsor / Champion / End user / Finance contact / Detractor] | [Last contact date / relationship note] |

Flag:
- Any attendee who is new or unrecognized — "This may be your first call with [name]; intro context before the call if possible."
- Missing executive sponsor: "Executive sponsor [name] is listed but not confirmed on this call — flag if absent at start."
- High-value attendees who haven't been on a call in >60 days.

Internal attendees (AE, SE, CSM manager) — list separately if provided.

---

**Suggested agenda**

Calibrate depth and format to call type and CS motion. AutogenAI CS model is hybrid/segmented — match the segment this account sits in (high-touch for enterprise, shorter format for mid-market/SMB).

*[Call type: Kickoff — example structure]*

| Time | Agenda item | Owner |
|------|-------------|-------|
| 0:00–0:05 | Introductions and call purpose | CSM |
| 0:05–0:20 | Customer goals and success criteria (draft to validate) | Customer |
| 0:20–0:35 | Onboarding plan walkthrough — milestones, owners, timeline | CSM |
| 0:35–0:45 | Q&A and open items | Both |
| 0:45–0:55 | Next steps — confirmed actions, dates, owners | CSM |
| 0:55–1:00 | Buffer | — |

*[Call type: QBR — example structure]*

| Time | Agenda item | Owner |
|------|-------------|-------|
| 0:00–0:05 | Welcome and framing | CSM |
| 0:05–0:20 | Value delivered — metrics vs. success criteria | CSM |
| 0:20–0:35 | Customer highlights and wins | Customer |
| 0:35–0:45 | Challenges and roadblocks | Both |
| 0:45–0:55 | Next quarter priorities and roadmap alignment | Both |
| 0:55–1:00 | Next steps | CSM |

*[Call type: Health check — example structure]*

| Time | Agenda item | Owner |
|------|-------------|-------|
| 0:00–0:05 | Quick check-in | CSM |
| 0:05–0:15 | Review of specific signal: [signal from health model] | CSM opens |
| 0:15–0:30 | Customer's perspective — what's changed | Customer |
| 0:30–0:40 | Joint recovery plan — specific actions, owners, dates | Both |
| 0:40–0:45 | Confirm follow-up | CSM |

For custom call type: adapt the structure to the described purpose.

---

**Recommended talking points**

3-5 specific talking points calibrated to the account's current state and call type. Not generic. Reference account-specific context where available.

Format:
- **[Topic]:** [One sentence context] → [Suggested framing or question]

Examples:
- **Success criteria progress:** "The team hit [metric] last quarter — X% above baseline. Lead with this before moving to gaps."
- **Usage signal:** "Weekly active users dropped 18% last month — ask what changed on their end before attributing it to product."
- **Renewal positioning:** "Renewal is in 67 days. This call isn't the commercial conversation — the goal is alignment, not close. Defer pricing until the sponsor is engaged."
- **Expansion lead (internal, don't raise directly):** "Champion mentioned interest in [feature] on the last call — listen for confirmation and route to AE if validated."

Flag any talking point that references a sensitive topic (executive departure, open escalation, competitive evaluation) with `[review]` and a note on whether to raise it proactively or wait for the customer to open it.

---

**Key questions to ask**

3-5 open questions to ask during the call. Not a script — prompts to open the conversation in the direction that matters.

By call type:
- Kickoff: "What does success look like for you personally, not just the team?" / "Who will feel the impact most when this is working?"
- QBR: "What hasn't worked as well as you'd hoped?" / "What decision is this review helping you make?"
- Health check: "What's changed on your end in the last [timeframe]?" / "What would need to be true for this to be a non-issue in 30 days?"
- Renewal: "What would make you confident recommending a renewal to your leadership?" / "What's your main concern going into renewal?"

---

**What to listen for**

2-3 specific signals to track during the call. If heard, note them for post-call action.

- **Risk signals:** executive sponsor departure signal, budget freeze language, competitor mention, "we're evaluating our options"
- **Expansion signals:** "we're thinking about [use case]", mention of new team or initiative that could use the product — tag `[early signal — not yet qualified]`
- **Relationship signals:** tone shift vs. previous calls, new decision-maker introduced without advance notice, team structure change mentioned

---

**Post-call actions (pre-loaded)**

Prepare these now so the call ends with immediate follow-through.

| Action | Owner | Due | Notes |
|--------|-------|-----|-------|
| Send follow-up email with recap and agreed actions | CSM | Within 24h | Use `/csm:account-research --brief` to verify action item list |
| Log call in CRM with next activity date | CSM | Same day | — |
| [Context-specific action] | [Owner] | [Date] | — |

If health check or risk call: pre-load the escalation routing per configured matrix. "If [escalation trigger] is confirmed on this call, route to [person] via [channel] within [SLA]."

---

## Reviewer note

> **⚠️ Reviewer note**
> - **Sources:** [CRM — HubSpot ✓ live | Glyphic AI ✓ live | M365 ✓ live | CS Platform — not connected | Google Drive — unverified | prior account-research session | user provided | conversation context only]
> - **Data as of:** [timestamp per source]
> - **Flagged for your judgment:** [N items marked `[review]` inline | none]
> - **Before the call:** [1-2 specific things to confirm — e.g., "verify attendee list is current", "confirm exec sponsor is joining"]

If clean: `⚠️ Reviewer note: data verified · no flags · confirm attendee list before call`.

---

## Output

Call preparation brief — single structured markdown document. Sections vary by detected call type (EBR, renewal, health check, expansion, escalation, kickoff). All briefs include: account context snapshot, attendee profiles, agenda, key questions, and suggested next step. See **Brief structure** section for field-level detail.

## Guardrails

**Expansion stays internal.** Any expansion signal in the call prep is for internal reference — not a topic to raise with the customer unless the AE or AM has qualified it.

**Health signals inform, not script.** If the account is Yellow/Red, the brief names the signals and suggests questions — it doesn't tell the CSM to "tell the customer they're at risk." That framing is the CSM's judgment call.

**Renewal framing is not a sales script.** In a renewal call, the brief supports the conversation — it doesn't produce a pricing or negotiation script. For commercial prep, use `/csm:renewal-readiness`.

**No destination check bypass.** If any part of this brief is shared externally (e.g., a pre-read for the customer), the reviewer must remove internal health scores, stakeholder notes, and any expansion signal tags before sending.

---

## After the brief

> "Brief ready. Anything you want to add or adjust before the call?"

If the call is less than 2 hours away: "Need a shorter version? I can trim to agenda + talking points only."

Post-call: "Want to log action items and send the follow-up? Paste the call notes and I'll draft the email and CRM update."

---

## Reference Material

### Reasoning Blueprint: Call Preparation

Load this blueprint when Tier 3 reasoning is activated for call prep work.

#### Problem Classification Taxonomy

**Type 1 — Discovery / Kickoff Prep**
- **Characteristics**: First or early-stage call; limited historical data; goal is relationship establishment and success criteria alignment. Account data is sparse — CRM may have only deal-stage context.
- **Primary Risk**: Generic agenda that fails to surface the customer's actual success criteria, leaving the onboarding plan misaligned from day one.
- **Expert Focus**: Whether the attendee list includes the economic buyer or only the operational champion — missing executive alignment at kickoff creates downstream sponsor risk that compounds through the lifecycle.

**Type 2 — QBR / Executive Business Review Prep**
- **Characteristics**: Periodic value review with executive audience; requires metrics against agreed success criteria; forward-looking priorities. Data-rich — multiple quarters of usage, health, and engagement history available.
- **Primary Risk**: Building the QBR around product usage metrics instead of business outcomes the executive cares about — the deck answers "what did they do" instead of "what value did they get."
- **Expert Focus**: Whether the success criteria being reported against are still the criteria the customer cares about — priorities shift between quarters and stale criteria make the review feel irrelevant.

**Type 3 — Health Check / At-Risk Prep**
- **Characteristics**: Triggered by a health signal (usage drop, NPS decline, support spike, champion departure). Emotionally charged — the CSM needs to listen more than present. Shorter format; focused on diagnosis and recovery commitment.
- **Primary Risk**: Leading with the health score or platform data instead of opening with curiosity — the customer feels monitored rather than supported, and the real root cause stays hidden.
- **Expert Focus**: Distinguishing between a symptom signal (usage dropped) and a root cause (reorg changed the team, budget was cut, champion left) — the prep must arm the CSM with diagnostic questions, not conclusions.

**Type 4 — Renewal Conversation Prep**
- **Characteristics**: Within 90 days of renewal; commercial context present but the CSM call is not the commercial close — it is alignment and objection surfacing. Requires awareness of contract terms, pricing history, and competitive landscape without scripting a sales pitch.
- **Primary Risk**: Blurring the line between value alignment (CSM's job) and commercial negotiation (AE/AM's job) — the CSM either avoids renewal entirely or overreaches into pricing discussion without authority.
- **Expert Focus**: Whether known objections have been pre-empted in prior calls — a renewal prep that surfaces objections for the first time on the renewal call means earlier calls missed the signal.

**Type 5 — Escalation / Recovery Prep**
- **Characteristics**: Active escalation or recently resolved incident; trust is damaged; the call goal is restoring confidence and confirming remediation. Requires knowledge of the escalation timeline, what was promised, and what was delivered.
- **Primary Risk**: Treating the call as a status update on the fix rather than a trust repair conversation — the customer needs to feel heard before they need a timeline.
- **Expert Focus**: Whether the person who made the original commitment is on the call — if not, the CSM must explicitly bridge accountability ("Alex committed to X on [date]; here's where that stands") rather than letting it feel like institutional amnesia.

**Type 6 — Routine Check-in Prep**
- **Characteristics**: Regular cadence call; no major trigger; risk of the call feeling perfunctory. The brief needs to surface something worth discussing or recommend skipping/shortening.
- **Primary Risk**: Producing a brief for a call that shouldn't happen — if there's nothing meaningful to discuss, the best prep is recommending an async update instead.
- **Expert Focus**: Whether the cadence still matches the account's lifecycle stage — weekly calls that made sense during onboarding become annoying in steady-state; the prep should flag cadence misalignment.

#### Domain Heuristics

**The Attendee Alignment Rule**: The value of a call prep is capped by the accuracy of the attendee list. If the attendee list is unconfirmed, flag it as the first action item — a perfectly prepared agenda for the wrong audience is wasted effort.

**The Last-Call Continuity Rule**: Every call prep must reference the outcome of the previous call. If the last call had open action items, the new brief must account for them — customers notice when commitments disappear between meetings.

**The 80/20 Agenda Rule**: No more than 20% of call time should be CSM-presented content; the remaining 80% should be structured for customer voice. Briefs that script the full call produce monologues, not conversations.

**The Signal Freshness Rule**: Health signals and usage data older than 7 days should be flagged with their age. A usage drop that resolved 3 days ago changes the entire call framing — stale data produces stale conversations.

**The Stakeholder Surprise Rule**: If a new attendee appears who wasn't on the last 2 calls, treat it as a signal, not just a logistics note. New attendees mean something changed — org structure, decision authority, or evaluation scope. Arm the CSM to ask why.

**The One-Thing Rule**: Every call prep should identify the single most important thing to accomplish on this call. If the CSM remembers nothing else from the brief, this one thing should stick. Bury it and the brief failed.

**The Internal-External Boundary Rule**: Expansion signals, health scores, and stakeholder relationship assessments are internal context — they inform the CSM's approach but never appear in customer-facing materials. Every brief must maintain this boundary explicitly.

#### Common Failure Modes by Request Type

**Discovery / Kickoff**
- **Generic success criteria prompt**: Asking "what does success look like?" without researching what the customer said during the sales process. **Fix**: Pull deal notes and echo back what was promised, then ask the customer to validate or revise.
- **Missing onboarding timeline in agenda**: Kickoff without a concrete onboarding plan preview feels abstract. **Fix**: Always include a "here's what happens next" section even if the plan is draft.

**QBR / EBR**
- **Metric dump without narrative**: Presenting 15 usage metrics without connecting them to business outcomes. **Fix**: Select 3-4 metrics that map directly to the customer's stated success criteria; narrate the story, not the dashboard.
- **Backward-only review**: Spending the entire QBR on what happened with no forward-looking section. **Fix**: Allocate at least 30% of the agenda to next-period priorities and roadmap alignment.

**Health Check / At-Risk**
- **Leading with the score**: Opening the call by telling the customer their health score dropped. **Fix**: Open with a genuine check-in question; let the customer surface their experience before introducing platform signals.
- **Recovery plan without customer input**: Pre-building the recovery plan before the call. **Fix**: Bring diagnostic questions to the call; co-create the recovery plan with the customer during the conversation.

**Renewal**
- **Pricing discussion in the brief**: Including pricing, discount, or contract term details in the CSM's prep. **Fix**: Limit the renewal brief to value alignment and objection surfacing; route commercial details to the AE/AM.
- **Ignoring competitive signals**: Preparing the renewal brief without checking for competitive evaluation indicators. **Fix**: Include a "competitive landscape" check — recent vendor mentions in calls, support tickets, or champion conversations.

**Escalation / Recovery**
- **Treating it as a status call**: Preparing a timeline update instead of a trust repair conversation. **Fix**: Structure the brief around acknowledgment first, then accountability, then remediation status.
- **Missing the commitment trail**: Not referencing what was specifically promised during the escalation. **Fix**: Pull the escalation thread and list each commitment with its current status.

**Routine Check-in**
- **Preparing for a call that shouldn't happen**: Building a full brief when there's nothing meaningful to discuss. **Fix**: If no signals, no open items, and no upcoming milestones exist — recommend async update instead and give the customer 30 minutes back.
- **Same agenda every time**: Recycling the standard check-in structure without adapting to lifecycle stage. **Fix**: Rotate focus areas — one call on adoption, next on value realization, next on strategic alignment.

#### Expert Judgment Patterns

**Scope Decisions**
- **Depth vs. breadth trade-off**: A health check brief should go deep on one signal rather than shallow on five. A QBR brief should go broad on value delivered, then deep on the one area that needs attention. Match scope to call type.
- **When to recommend "don't have this call"**: If the check-in cadence has produced three consecutive calls with no substantive discussion, recommend cadence adjustment rather than producing another brief.

**Sequencing Decisions**
- **Agenda ordering by emotional arc**: Health check and escalation calls should open with listening, not presenting. QBR calls should open with wins before gaps. Renewal calls should open with value before commercial context. The emotional sequence matters more than the logical sequence.
- **When to front-load the hard topic**: If there's an escalation or known objection, don't bury it at minute 40 — address it early while energy and attention are high. The "save the hard stuff for last" instinct is usually wrong.

**Depth Decisions**
- **Attendee profile depth**: For a routine check-in with a known champion, a one-line attendee note suffices. For a QBR with a new VP attending, a full profile with LinkedIn context, reporting structure, and likely priorities is warranted.
- **Talking point specificity**: "Discuss adoption" is useless. "Weekly active users in the analytics module dropped 18% after the March release — ask whether the new UI is causing friction" is actionable. Specificity scales with call stakes.

**Stakeholder Decisions**
- **Who should be on this call but isn't**: The prep should flag missing stakeholders — especially executive sponsors absent from QBRs and economic buyers absent from renewal conversations. A missing stakeholder is a finding, not just a gap.
- **Internal audience for the brief**: If the CSM's manager or an AE is joining, the brief should note internal alignment points — what message the team wants to land, who leads which section.

**Confidence Decisions**
- **Data completeness threshold**: If fewer than 3 of the 4 standard data sources (CRM, CS platform, call recording, documents) returned data, flag the brief as low-confidence and name what's missing. Don't produce a confident brief from incomplete inputs.
- **Inference vs. observation**: When a talking point is based on inferred context (e.g., "usage drop likely due to holiday season") vs. observed data (e.g., "customer mentioned team reorg on last call"), label the distinction explicitly so the CSM knows what to verify.
