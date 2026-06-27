---
name: renewals-expansion-signal
description: >
  Identify and qualify expansion signals in a renewal account — seat growth,
  usage expansion, product line upsell, and adjacent team opportunities. Maps
  each signal to a qualification tier (early signal / pipeline-ready / qualified)
  and recommends a TARO play for conversion. Use during pre-renewal research,
  QBR prep, or after an adoption milestone to surface upsell and cross-sell leads.
  Expansion leads are never included in GRR calculations and require a qualifying
  economic buyer conversation before entering NRR pipeline.
---

## Company Context

**Company:** AutogenAI — AI-powered proposal and bid writing platform for enterprise teams. Primary value metric: win rate improvement and proposal throughput.

**CSM role:** Enterprise CSM, CSM-led renewal motion (CSM owns end-to-end). Book of business: ~10 enterprise accounts, ~£1.2M ARR, ~£120K average deal size. NRR target: 120%.

**Pricing model:** Not yet configured — prompt in-session for seat count, usage limits, or tier structure when evaluating signal types. Usage-based expansion signals may not apply until pricing model is confirmed.

**Key churn signals to check before expansion:** (1) Cost per active user rising — licences not converting to genuine active users. (2) Unresolved structural blocker (IT/security, integration failure, competitor displacement by ChatGPT or Microsoft Copilot). (3) Login decay — 7–14 day no-login window. (4) Exec sponsor disengaged.

**Integrations available:** HubSpot (CRM), Planhat (CS Platform), Outlook, Slack. DocuSign for contracts. No CPQ or call recording configured.

**AE partner:** Not configured — prompt in-session when routing a qualified signal. Playbook location: Planhat.

**Competitors:** ChatGPT, Microsoft Copilot, manual Word/Google Docs workflows.

---

## Skill Instructions

# /renewals:expansion-signal [VALIDATED]

Surface and qualify expansion signals in a renewal account. Every signal found here is a lead until proven otherwise.

---

## Use when
- You are researching an account for expansion opportunities before a renewal or QBR
- You need to identify and qualify seat growth, usage expansion, product tier upsell, or cross-sell signals in a specific account
- An adoption milestone has occurred and you want to evaluate whether it unlocks an expansion conversation
- You need to map each expansion signal to a qualification tier and a recommended TARO play before engaging the account or routing to the AE
- You want to confirm whether an account is at Pipeline-ready or Qualified tier before involving an AE in a commercial conversation

**Downstream dependency:** After this skill produces qualified expansion signals, use the CSM plugin's expansion-business-case skill to build the formal business case document for AE engagement (if the `csm` plugin is installed, run `/csm:expansion-business-case`).

## Do NOT use for
- Accounts with active churn risk signals — run `/renewals:risk-assessment` first; expansion pursuit on at-risk accounts damages trust and is counterproductive
- GRR or renewal-rate forecasting — expansion ARR is never included in GRR calculations
- Moving an expansion opportunity into NRR pipeline without a qualifying economic buyer conversation — this skill identifies and qualifies signals; formal pipeline entry requires AE involvement
- Post-churn expansion analysis — use `/renewals:churn-rca`
- Quick renewal status checks without an expansion research goal — use `/renewals:executive-summary`

## Typical Activation
> `/renewals:expansion-signal Acme Corp` — full expansion signal audit across all six signal types with qualification tier and TARO play recommendations
> `/renewals:expansion-signal Acme Corp --quick` — targeted pass for highest-probability signal before a renewal call
> `/renewals:expansion-signal --catalog` — list all detectable signal types for your configured pricing model

---

## Reasoning Protocol

Before generating output, apply these primers:

1. **CLASSIFY**: What type of expansion signal request is this?
   - **Seat/Usage Capacity**: Quantitative signal — active users near licensed seats or usage approaching contracted volume. Data-driven, often auto-surfaced by CS Platform.
   - **Feature/Tier Upsell**: Qualitative signal — customer requesting higher-tier features, expressing feature gaps in tickets or calls, or fully adopting current tier.
   - **Cross-Sell / Multi-Product**: Adjacent product opportunity — customer solving a problem manually or with a competitor tool that your product line addresses.
   - **Geographic/Organizational Expansion**: Structural signal — new offices, M&A activity, separate business units, or teams evaluating independently.
   - **At-Risk Account with Expansion Signals**: Mixed signal — expansion indicators coexist with churn risk. Requires triage: renewal risk addressed before expansion pursuit.

2. **CONSTRAINTS**: What limits the solution space?
   - G2: Expansion ARR is not counted until an economic buyer has been qualified — every signal is tagged `[early signal — not yet qualified]` until formal pipeline entry. Non-negotiable.
   - G4: AE routing is mandatory for any signal reaching Qualified tier — the CSM owns signal identification and qualification handoff; the AE owns commercial conversion.
   - G5: ARR potential estimates, contract terms, and qualification tiers are internal-only. Confidentiality check required before any output leaves the CSM's view.
   - G7: Flag stale data with source date — CRM >7 days, CS Platform >3 days. Never silently omit a data gap.
   - Pricing model from config constrains which signal types apply (usage-based expansion is irrelevant for flat-fee accounts).

3. **EXPERT CHECK**: What would a veteran renewals manager verify first?
   - Is there active churn risk on this account? If yes, expansion signals are deferred until the account is stabilized — run `/renewals:risk-assessment` first.
   - Is the signal coming from a champion or an economic buyer? Champion enthusiasm alone never upgrades qualification tier — apply the Champion vs. Buyer Test.
   - Does the 90-day trend support the signal, or is this a point-in-time anomaly? A single data point is anecdotal; three consecutive months of directional signal is a pattern.

4. **ANTI-PATTERNS**: Common mistakes to avoid:
   - Treating a seasonal usage spike as sustained growth — require 90-day trend confirmation before qualifying as pipeline-ready.
   - Conflating feature frustration with purchase intent — a customer saying "why can't you do X?" is a support issue first, expansion signal second.
   - Pursuing expansion on a Red account — expansion conversations on accounts with active churn risk feel tone-deaf and damage trust.
   - Presenting ARR potential estimates as committed pipeline — all pre-proposal estimates are `[Low Confidence]` and must be labeled as such.
   - Routing to AE at early signal stage — premature AE involvement wastes AE time and pressures the customer relationship. Route at pipeline-ready, not before.
   - Assuming M&A or org expansion equals product expansion — the new entity may have existing vendor relationships or independent budget authority.

**After execution**, verify:
- Does each signal have a qualification tier with explicit justification for the tier assignment?
- Are all ARR potential figures tagged `[Low Confidence]` unless a formal proposal exists?
- Is the output mode (--deep / --quick / --catalog) matched to the actual need?
- Confidence: [High] if 2+ live sources corroborate signals / [Medium] if single-source or partially stale / [Low] if user-provided context only — state which.

## Mode

`--deep` (default): Full signal audit across all expansion domains with qualification tier assignment, TARO play mapping, and recommended next actions. Pulls live data when connectors are available.

`--quick`: Targeted pass — asks 4–5 focused questions to surface the highest-probability expansion signal in the account and recommend one immediate action. Use when you have limited prep time before a renewal call.

`--catalog`: List all expansion signal types the skill can detect for the configured pricing model, with descriptions and qualifying questions for each. Use to educate the team or build a pre-call checklist.

---

## Account identification and data pull

Ask: "Which account are you researching for expansion signals? Provide the account name, and tell me ARR, renewal date, and current product tier/license count if you have them."

If a CRM connector is available, pull:
- Current ARR and product tier
- Contract seat count or usage limit
- Contract start date and renewal date
- Expansion history (prior upsell or cross-sell events and dates)
- Open expansion opportunities in pipeline

If a CS Platform connector is available, pull:
- Active user count vs. licensed seat count
- Product feature adoption breadth (which modules are in use)
- Team or department breakdown of active users
- Usage trend (growing / flat / declining) over last 90 days
- Any usage-based overage or approaching limit signals

Confirm data pull before proceeding:
> "[CRM + CS Platform]: [account name] · $[ARR] ARR · [N] licensed seats / [N] active users · [module adoption] · data as of [timestamp]"

---

## Signal catalog — six expansion types

Evaluate each signal type based on data pulled or user input.

### 1. Seat expansion
- Active users approaching or exceeding licensed seat count
- New team members onboarded since last renewal not yet on license
- Departments using product via shared credentials (workaround for unlicensed seats)
- Recent hiring activity in teams using the product

Qualifying question to surface: "Are there users at this account accessing the product outside of licensed seats, or departments that would benefit from access but aren't currently licensed?"

### 2. Usage-based expansion (usage-model accounts only)
- Usage approaching or exceeding contracted volume limit
- Recent consumption spike vs. prior period
- Seasonal usage pattern suggesting coming overage
- Usage growing faster than contracted tier covers

Qualifying question: "Their usage is at [N]% of contracted volume — have you discussed an upgrade conversation or are they managing to stay under the cap?"

### 3. Product tier or feature upsell
- Core tier features fully adopted; advanced tier features not unlocked
- Customer requesting features that exist in a higher tier
- Feature gap expressed in support tickets or CSM notes
- Recent product release unlocking new tier features customer has expressed interest in

Qualifying question: "Which features are they asking about that they don't currently have access to?"

### 4. Multi-product or cross-sell
- Adjacent product line that addresses a problem mentioned in calls or QBRs
- Integration between current product and a new product line customer could benefit from
- Customer currently solving a problem with a competitor product or manual process that your product line addresses

Qualifying question: "Is there a workflow they're handling manually or with a point solution that [product] could replace?"

### 5. Geographic or team expansion
- New office, region, or subsidiary not yet on the product
- M&A activity — acquisition target not yet onboarded
- Separate team or business unit at the same company evaluating independently

Qualifying question: "Are there other teams or offices at this account that aren't currently on the product?"

### 6. Professional services or implementation
- Complex use case mentioned that requires configuration or integration support
- Customer building internal workarounds that a services engagement could replace
- Expansion of use case scope that warrants a new implementation project

Qualifying question: "Are they building something internally that your PS team would normally handle?"

---

## Qualification tier assignment

For each signal identified, assign a qualification tier:

| Tier | Definition | NRR treatment | Next action |
|------|-----------|--------------|-------------|
| **Early signal** | Data or conversation suggests expansion need; no economic buyer conversation yet | `[early signal — not yet qualified]` — not in NRR | Validate with champion; set qualifying conversation goal |
| **Pipeline-ready** | Champion has confirmed interest; economic buyer awareness but no commitment | `[early signal — not yet qualified]` — not in NRR | Book qualifying conversation with economic buyer |
| **Qualified** | Economic buyer has confirmed interest or approved expansion budget; expansion is in formal pipeline | Include in NRR forecast; create CPQ quote or opportunity | Route to AE partner; build expansion proposal |

> **Signal qualification rule (non-negotiable):** Expansion signals are tagged `[early signal — not yet qualified]` unless an economic buyer conversation has occurred AND the expansion has moved to formal pipeline (CPQ quote, opportunity stage, or written commitment). Do not include unqualified signals in NRR figures.

---

## TARO play recommendation

For each qualified or pipeline-ready signal, recommend a TARO play:

**Trigger:** The specific adoption, usage, or need signal that makes this expansion timely and relevant to surface now.

**Action:** The recommended play — reference configured playbook if available. Common expansion plays: Adoption milestone review → tier upgrade conversation; Usage approaching limit → proactive expansion proposal; Adjacent team discovery → multi-product introduction.

**Resource:** What to bring to the expansion conversation. Consider:
- Usage data showing value delivered to current team
- ROI evidence or case study relevant to the expanding use case
- Product roadmap items relevant to expanded tier or cross-sell product
- Pricing and packaging options within your configured discount authority

**Outcome:** Observable state marking the play successful (expansion opportunity created in CRM / CPQ quote generated / economic buyer meeting scheduled / expansion contract signed).

> TARO play recommendations are leads, not mandates. Validate the trigger against what you know from direct account experience before executing.

---

## AE handoff guidance

For any signal at Pipeline-ready or Qualified tier, note the AE handoff:

> "Expansion signals at [pipeline-ready / qualified] tier should be routed to [AE partner — not yet configured; ask the CSM in-session] for the commercial conversation. Prepare:
> - Account context brief (current ARR, expansion signal, qualifying conversation summary)
> - Proposed expansion scope (seats / tier / product)
> - Draft pricing or CPQ input within your configured parameters
> - Recommended timing relative to renewal date"

If no AE partner is confirmed in-session:
> "No AE partner is configured. Please provide your AE contact name so the handoff brief can be addressed correctly."

---

## Output format

---

**Expansion Signal Report — [Account Name]**
*ARR: £[amount] · Renewal: [date] · [N] days out*
*Analyzed: [date] · Sources: [list]*

**Expansion signals found: [N]**

| Signal type | Tier | ARR potential | Qualifying question answered? |
|-------------|------|--------------|-------------------------------|
| [Type] | [Early / Pipeline-ready / Qualified] | £[estimate] `[early signal — not yet qualified]` | [Y / N] |

> Note: ARR potential figures are estimates `[Low Confidence]` until a formal expansion proposal is built and reviewed with the economic buyer.

**Signal detail**

*[For each signal: 2–3 sentences on what was observed, why it indicates expansion, and what the immediate next step is.]*

**TARO play recommendations**

*[For pipeline-ready and qualified signals: Trigger / Action / Resource / Outcome]*

**AE handoff items**
*[What to prepare for AE routing, if applicable]*

**Next actions**
1. [Specific action for highest-probability signal]
2. [Qualifying conversation goal]
3. [AE handoff item, if applicable]

---

> Reviewer note
> - **Sources:** [CRM verified | CS Platform verified | call notes | manual input]
> - **Data as of:** [timestamp per source | N/A]
> - **Read:** [account record | usage data | expansion history | [N] call notes]
> - **Flagged for your judgment:** [N items — expansion qualification tiers pending buyer conversation | none]
> - **Before including in NRR forecast:** Validate qualification tier for each signal — `[early signal — not yet qualified]` signals must NOT appear in NRR figures

---

> [review before sending]

---

## Security & Permissions

```
network:        read-only connector access — CRM and CS Platform reads; no external
                API writes, no web fetch
read_scope:     HubSpot (CRM) and Planhat (CS Platform) account records scoped to
                the named account only (read-only)
write_scope:    none — all signal reports and recommendations output to conversation;
                no file writes
subprocess:     none
dynamic_code:   none — no eval, no exec, no runtime code execution
```

This skill reads account data from HubSpot and Planhat connectors to identify and qualify expansion signals. All output is delivered to the conversation only. No data is written to disk. Connector reads are scoped to the specific account name provided and are read-only.

## Trust & Verification

- **Signal qualification integrity:** Every expansion signal is tagged `[early signal — not yet qualified]` until an economic buyer conversation has occurred and the expansion has entered formal pipeline. This tag is enforced at the Reasoning Protocol and output format levels and cannot be removed by configuration or user instruction.
- **ARR estimate labeling:** All pre-proposal ARR potential estimates carry `[Low Confidence]` tags. The skill will not present expansion ARR estimates as committed pipeline or include them in GRR calculations.
- **Account data handling:** CRM and CS Platform data is used for signal identification and qualification only. Data is not persisted, cached, or written to any file.
- **Churn-first guard:** The CLASSIFY step and Guardrails both require that active churn risk signals are surfaced before expansion signals. Accounts flagged as at-risk are redirected to `/renewals:risk-assessment` before expansion pursuit continues.
- **AE routing gate:** Signals that reach Qualified tier require AE partner routing. The skill does not initiate commercial conversations directly.
- **Free-text field handling:** Account name, call notes, and context provided by the user are used for display and analysis only. They are not executed or used to derive file paths or system behavior.

---

## Guardrails

**Expansion requires qualification.** Every expansion signal is tagged `[early signal — not yet qualified]` until an economic buyer conversation has occurred and the expansion is in formal pipeline. This tag is not removed by champion confirmation alone. This guardrail cannot be overridden by configuration or conversation.

**ARR potential estimates are speculative.** Expansion ARR estimates before a formal proposal are `[Low Confidence]`. Do not present them as committed pipeline. Flag any estimate that could be read as a revenue forecast.

**Expansion is not included in GRR.** GRR calculations never include expansion ARR. This is mathematically correct and a shared guardrail — never include expansion in a GRR projection regardless of how the expansion is framed.

**AE routing on qualified signals.** For any signal that reaches Qualified tier, the commercial conversation routes to the AE partner. The renewals manager owns the signal identification and qualification handoff; the AE owns the commercial conversion unless configured otherwise.

**Renewal risk first.** If the account also has active churn risk signals, surface them before expansion signals and recommend `/renewals:risk-assessment` before pursuing expansion. An at-risk account's priority is renewal, not growth.

---

## Reference Material

### Reasoning Blueprint: Expansion Signal Identification

*reasoning-blueprint v1.0 — for use when deeper signal classification is needed*

---

#### Problem Classification Taxonomy

**Type A: Seat/Usage Capacity Signal**
Characteristics: Quantitative signal — active users approaching licensed seats, or usage approaching contracted volume limits. Data-driven, often surfaced automatically by CS Platform.
Primary Risk: Mistaking temporary spikes for sustained growth trends; recommending expansion on a seasonal anomaly.
Expert Focus: Compare 90-day trend vs. point-in-time; check whether the overage is organic adoption or a one-off project.

**Type B: Feature/Tier Upsell Signal**
Characteristics: Qualitative signal — customer requesting features in a higher tier, expressing feature gaps in support tickets or calls, or fully adopting current tier features.
Primary Risk: Conflating feature curiosity with purchase intent; pushing tier upgrade when the customer is frustrated, not ready to buy.
Expert Focus: Distinguish between "I wish we had X" (interest) and "We need X to achieve Y by Z date" (budget-backed need).

**Type C: Cross-Sell / Multi-Product Signal**
Characteristics: Adjacent product opportunity — customer solving a problem manually or with a competitor tool that your product line addresses. Often surfaces in QBR conversations or workflow discovery.
Primary Risk: Forcing a cross-sell narrative onto a customer who is satisfied with their current solution; damaging trust by appearing sales-driven.
Expert Focus: Validate that the pain point is real and active, not historical; confirm the adjacent product genuinely fits the workflow.

**Type D: Geographic/Organizational Expansion Signal**
Characteristics: Structural signal — new offices, M&A activity, separate business units, or teams evaluating independently. Often discovered through LinkedIn, news, or executive conversation.
Primary Risk: Assuming corporate expansion equals product expansion; the new entity may have existing vendor relationships or different requirements.
Expert Focus: Verify whether the expansion entity has independent budget authority or rolls up to the existing contract holder.

**Type E: At-Risk Account with Expansion Signals**
Characteristics: Mixed signal — expansion indicators coexist with churn risk (declining engagement, open escalations, stakeholder turnover). Requires triage before pursuit.
Primary Risk: Pursuing expansion on an account that hasn't resolved its renewal risk; the expansion conversation feels tone-deaf.
Expert Focus: Renewal risk must be addressed first. An expansion signal on a Red account is noise until the account is stabilized.

---

#### Domain Heuristics

1. **The 80% Seat Rule**: When active users exceed 80% of licensed seats for 30+ days, the account is a seat expansion candidate — but only if the growth trend is sustained, not a one-time spike.

2. **The Champion vs. Buyer Test**: A champion saying "we'd love more seats" is an early signal. An economic buyer asking "what would 50 more seats cost?" is pipeline-ready. Never conflate the two.

3. **The Frustration-to-Expansion Trap**: Feature requests born from frustration ("why can't your product do X?") are support issues first, expansion signals second. Address the frustration before positioning the upsell.

4. **The 90-Day Trend Rule**: Any single data point (usage spike, seat overage, feature request) is anecdotal. Three consecutive months of directional signal is a pattern worth qualifying.

5. **The Renewal Proximity Gate**: Expansion signals within 90 days of renewal should be packaged with the renewal conversation, not pursued independently — unless the expansion is urgent and the renewal is healthy.

6. **The Silent Department Rule**: If only one department is using the product in a multi-department account, the silent departments are either unaware or uninterested. Discovery is required before assuming cross-sell opportunity.

7. **The AE Handoff Timing Rule**: Route to AE at pipeline-ready, not at early signal. Premature AE involvement on an unqualified signal wastes AE time and can pressure the customer relationship.

---

#### Common Failure Modes by Signal Type

**Seat/Usage Capacity Failures**
- Seasonal spike misread: Treating a Q4 usage surge as sustained growth. Fix: Require 90-day trend confirmation before qualifying as pipeline-ready.
- Shared credential blindness: Missing that 10 users share 3 logins — actual demand is higher than metrics show. Fix: Ask the qualifying question about shared credentials explicitly.

**Feature/Tier Upsell Failures**
- Curiosity-as-intent: Treating a "that looks cool" comment as a buying signal. Fix: Apply the Champion vs. Buyer Test — who said it, and did they reference budget or timeline?
- Support ticket misclassification: Counting frustrated feature requests as upsell signals without resolving the underlying issue. Fix: Check support ticket sentiment; address frustration before positioning expansion.

**Cross-Sell Failures**
- Solution-in-search-of-problem: Pitching an adjacent product the customer hasn't expressed a need for. Fix: Require an observed pain point (call notes, QBR discussion, support ticket) before flagging cross-sell.
- Competitor displacement assumption: Assuming the customer wants to replace their existing tool when they may be satisfied with it. Fix: Ask whether the current solution is a pain point or working fine before positioning replacement.

**Geographic/Organizational Expansion Failures**
- M&A assumption: Assuming an acquired company will adopt the parent's tech stack. Fix: Verify with the champion whether the acquisition includes technology consolidation plans.

**At-Risk Account Failures**
- Expansion on a burning platform: Pursuing growth signals while ignoring active churn risk. Fix: Run risk assessment first; expansion conversations on Red accounts require explicit renewal stabilization.

---

#### Expert Judgment Patterns

**Qualification Decisions**
- Early signal stays early until an economic buyer has been engaged — champion enthusiasm alone never upgrades the tier.
- When multiple signals exist in one account, qualify the highest-ARR-potential signal first; don't spread effort across all simultaneously.
- If the qualifying question has been asked and the answer is vague or deflecting, the signal is weaker than it appears — do not upgrade tier.

**Sequencing Decisions**
- Always check renewal risk before expansion signals — a Red account's expansion signals are deferred until stabilized.
- Pull CRM expansion history before qualifying new signals — if a prior expansion attempt stalled, understand why before re-engaging.
- Surface signals to the CSM before routing to AE — the CSM validates context; the AE validates commercial viability.

**Confidence Decisions**
- ARR potential estimates on early signals are always [Low Confidence] — state this explicitly; never let an estimate read as committed pipeline.
- Signals corroborated by 2+ data sources (CRM + CS Platform + call notes) can be [Moderate]; single-source signals stay [Low Confidence].
- Qualification tier assignment is a judgment call — when uncertain, default to the lower tier and note what evidence would upgrade it.
