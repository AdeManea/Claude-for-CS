---
name: csm-success-plan-builder
description: >
  Build or update a customer success plan — account-specific success criteria,
  measurable milestones, engagement cadence, and mutual commitments. Use at
  kickoff for new accounts, when success criteria have drifted, or when an
  account needs a plan reset. Produces a co-authored document, not a CSM
  internal tracker.

---

## Company Context

**Company:** AutogenAI — AI-powered proposal and bid writing platform for enterprise teams.
**Primary value metric:** Win rate improvement / proposal throughput.
**Primary segment:** Enterprise. **CS model:** Hybrid/segmented — high-touch for enterprise, tech-touch for SMB/mid-market.
**Role:** Enterprise CSM. **Accounts per CSM:** 25–50 enterprise accounts.

**Top churn drivers:** Low platform adoption, low bid volume through tool, champion departure, competitive displacement.

**Connected integrations:** HubSpot CRM (verified), Microsoft 365 (verified), Glyphic AI call recording (verified). CS Platform (Gainsight/Totango/ChurnZero/Vitally) — not connected; health signals from CRM + call recordings + conversation context.

**Health model:** Manual signals — no CS Platform. Red threshold: 2+ concurrent churn signals. Yellow: 1 churn signal or declining engagement.

**Customer journey stages:** Onboarding / Adoption / Value Realization / Renewal / Expansion.

**Success plan format preference:** Google Doc or Notion (default). **Executive audience:** Data-led, concise, outcome-focused.

**Quiet mode:** When producing customer-facing deliverables, suppress internal narration, plugin command handoffs, and "I read the following files…" commentary. Reviewer notes: KEEP.

**Source attribution tags:** `[CRM — HubSpot]`, `[Call recording — Glyphic AI]`, `[M365]`, `[Computed]`, `[user provided]`, `[model knowledge]`, `[conversation context]`.

---

## Skill Instructions

# /success-plan-builder

[PROPOSED]

## Use When
- Starting a new customer engagement and need to establish a formal success plan — in free-form narrative or co-authored document format, without requiring the OCV canvas structure
- An existing success plan needs to be updated after a QBR, health review, or goal change
- Renewal is approaching and the success plan needs to reflect value delivered and next-period goals
- Executive sponsor has changed and the plan needs to be refreshed for the new stakeholder
- The organization does not use the OCV (Outcome-to-Customer Value) system, or the OCV canvas format is not applicable to this account or engagement

**Upstream dependency:** Before building a success plan for a new engagement, define the 3–5 success criteria that form the plan's foundation using the Onboarding plugin's success-criteria skill (if the `onboarding` plugin is installed, run `/onboarding:success-criteria`).

## Do NOT Use For
- The structured success plan canvas format — use /csm:success-plan-canvas for OCV-aligned canvas output
- Progress tracking against an existing canvas — use /csm:success-plan-progress-review
- QBR deck construction — use /csm:qbr-builder
- Value statements for renewal — use /csm:value-statement

## Typical Activation
"/csm:success-plan-builder Acme Corp"
"/csm:success-plan-builder Acme Corp --update"
"/csm:success-plan-builder Acme Corp --review"
"Build a success plan for [account]" (free-form narrative, no OCV canvas required)
"Update the success plan for [customer]"

---

Build a success plan that the customer will actually sign off on — account-specific
success criteria, measurable milestones, joint ownership, and a cadence that
reflects your configured CS motion.

---

## Pre-flight

The company context and CS configuration are embedded in the **Company Context** section above — no config files need to be read from disk.

Note from config:
- Success criteria model (account-specific vs. standard template)
- Primary value metric: Win rate improvement / proposal throughput
- CS motion: Hybrid/segmented — high-touch for enterprise
- Customer-facing plan format preference: Google Doc or Notion
- Playbook sources: use configured success plan template if available

**Guardrails (G-codes):**
- **G5:** Internal data (health scores, ARR, expansion signals) must never appear in customer-facing output
- **G7:** Flag any data older than 30 days with source date and staleness indicator

---

## Reasoning Protocol

Before generating output, apply these primers:

1. **CLASSIFY**: What type of success plan request is this?
   - **New Account Plan**: Fresh account post-kickoff; no prior plan exists; goals from sales handoff or kickoff notes.
   - **Plan Reset**: Existing plan has drifted — sponsor change, use case pivot, criteria never validated, or material context shift.
   - **Plan Review**: User submits an existing plan for quality assessment against measurability, co-ownership, and CS motion alignment.
   - **Milestone Recalibration**: Sound plan but timelines or targets need adjustment due to adoption pace, customer delays, or scope changes.

2. **CONSTRAINTS**: What limits the solution space?
   - Success criteria must originate from customer-stated business outcomes, never inferred from product features.
   - Primary value metric (win rate improvement / proposal throughput) must be traceable in the success criteria.
   - Customer-facing plan must contain zero internal health scores, expansion signals, or escalation routing.
   - Engagement cadence must match configured CS motion (high-touch for enterprise accounts).
   - Renewal language requires reviewer validation before sharing with leadership or finance.
   - G5: Internal data (health scores, ARR, expansion signals) must never appear in customer-facing plan output.
   - G7: Flag any data older than 30 days with source date and staleness indicator.

3. **EXPERT CHECK**: What would a veteran CSM verify first?
   - Are the success criteria stated in the customer's own language, or has the CSM translated them into product-speak?
   - Does the mutual commitments section include actual customer commitments, or is this a one-sided CSM delivery plan?
   - Is the renewal-ready milestone (M5) positioned at least 60 days before renewal date?

4. **ANTI-PATTERNS**: Common mistakes to avoid:
   - Building criteria from product features because kickoff notes are thin — ask for customer goals explicitly instead.
   - Generic milestones that don't trace to specific success criteria from Section 3.
   - Plans with no `[review]` flags — either perfect information (rare) or insufficient scrutiny.
   - Patching dates on a fundamentally misaligned plan instead of rebuilding from current goals.
   - Mutual commitments section with only CSM commitments — this isn't a co-authored plan.
   - Adjusting targets downward without documenting the rationale or addressing root cause.

**After execution**, verify:
- Does the output answer the implicit question the CSM is asking?
- Are all data sources timestamped and staleness-flagged?
- Is the output mode matched to the actual need?
- Confidence: [High] if 2+ live sources corroborate / [Medium] if single-source or partially stale / [Low] if user-provided context only — state which.

**For complex scenarios**, see the ## Reference Material section below.

## Mode

`--new`: Build a success plan from scratch for a new or recently kicked-off account.

`--reset`: Rebuild success criteria for an existing account where the original
plan has drifted, criteria were never properly established, or account context
has materially changed (new sponsor, new use case, new segment).

`--review`: User provides an existing success plan; skill reviews it for
completeness, specificity, and whether criteria are actually measurable. Returns
specific edits.

Default: prompt once — "Is this a new plan or an update to an existing one?"

---

## Data gathering

**Connector error categorization:** When a connector call fails, distinguish the error type before proceeding:
- **Rate-limited (transient):** Connector returns HTTP 429 or equivalent throttle signal. Note the rate limit explicitly in output ("CRM data temporarily rate-limited — retry in 60 seconds recommended") and offer to retry rather than proceeding with degraded output.
- **Unavailable (permanent for this session):** Connector is not configured, authentication has expired, or service is down. Fall back to the manual-input path below and label all affected sections as "connector unavailable — manual input used."
Do not conflate these — a rate-limited connector will return data shortly; an unavailable connector will not.

**What's needed before building:**

1. **Account goals** — what the customer said they wanted to accomplish
   (from kickoff notes, sales handoff notes, or user-provided context)

2. **Product context** — which features / modules are in scope for this account

3. **Stakeholder context** — who owns success on the customer side; who measures it

4. **Timelines** — contract duration, key milestones already committed, renewal date

5. **Existing success criteria** — if a plan exists, pull it before rebuilding

Pull from connected sources where available:
- CRM (HubSpot — verified): contract duration, renewal date, stakeholder contacts, sales handoff notes
- Document storage: existing success plan, kickoff deck, onboarding notes
- Glyphic AI call recordings: customer-stated goals from calls
- CS Platform: not connected — use conversation context or user-provided input

Minimum viable prompt if nothing is connected:

> "To build a success plan that the customer will actually own, I need the customer's
> stated goals. What did they tell you — during the sales process or kickoff — that
> they want to accomplish with AutogenAI? Paste notes, a handoff doc, or a
> quote if you have one."

Do not build a generic success plan from product features alone. Success criteria
must come from the customer's stated business outcomes, not assumed use cases.

---

## Success plan structure

---

**[Account Name] — Customer Success Plan**
*[Quarter / date range]*
*Prepared jointly by: [CSM name], AutogenAI · [Customer sponsor name], [Account]*

---

### 1. About this plan

One paragraph. Plain language. Why this document exists and how it will be used.

> "This success plan captures what [Account] is trying to accomplish with AutogenAI,
> the milestones we'll use to measure progress, and the commitments both teams are
> making to get there. We'll review it at each [cadence per CS motion: monthly /
> quarterly] check-in and update it as priorities shift."

---

### 2. Your goals

The customer's goals in their language — not product feature descriptions.

**Business goal [1]:** [What the customer said they want to achieve]
*Why it matters:* [Business context — not assumed]

**Business goal [2]:** [...]

Do not translate customer goals into product language here. Keep the customer's
framing. Translation to product features happens in Section 3.

If goals are unclear or unstated: flag in the reviewer note and add a `[review]`
marker — "Customer's stated goals not confirmed. Review with sponsor before plan
is finalized."

---

### 3. Success criteria

For each business goal, define one or more observable, measurable criteria.

Apply configured success criteria model:
- If account-specific model: build criteria in collaboration with the customer
- If standard template: start from the template and customize per account goals

**Success criterion format:**

> **[Criterion name]**
> - **Measure:** [Specific metric]
> - **Baseline:** [Where the account is today, if known]
> - **Target:** [The agreed number or milestone]
> - **Evidence source:** [Where this will be measured — CS Platform / product analytics / customer-reported]
> - **Review cadence:** [When this will be checked]

Criteria that cannot be measured get a `[review]` flag: "This criterion as stated
is not measurable — agree on a specific metric with the customer before finalizing."

**Primary value metric:** Win rate improvement / proposal throughput
— this should appear as one of the success criteria or as an explicit success
criterion overlay.

---

### 4. Milestones

Time-bound markers that show the account is on track. Not a task list — milestones
mark when something meaningful has been accomplished.

| Milestone | Description | Target date | Owner | Health signal if missed |
|-----------|-------------|------------|-------|------------------------|
| M1 | [First meaningful event — e.g., "All users activated and first workflow completed"] | [Date] | [Customer / CSM / Joint] | [Flag if missed by [date+buffer]] |
| M2 | [Second milestone] | | | |
| M3 | [First value milestone — primary value metric achieved] | [Date — ideally within configured TtV target] | | |
| M4 | [Adoption milestone — criteria threshold reached] | | | |
| M5 | [Renewal-ready milestone — all success criteria at target] | [Date before renewal] | | |

Milestones are co-owned — mark the owner. A milestone owned entirely by the CSM
is an internal commitment, not a joint one.

---

### 5. Engagement cadence

What the customer can expect — and what's expected of them.

| Touchpoint | Frequency | Format | Attendees | Purpose |
|-----------|-----------|--------|-----------|---------|
| Check-in call | [Monthly / quarterly — per CS motion] | [Video call / async update] | [CSM + customer sponsor] | [Progress review, issue triage] |
| QBR | Quarterly | [60-min video] | [CSM + AE + exec sponsor] | [Value review, next-quarter alignment] |
| Health review | [As needed / triggered by health signal] | [Email or call] | [CSM] | [Risk identification] |
| Executive sponsor call | [Semi-annually or as needed] | [30-min video] | [CS lead + exec sponsor] | [Strategic alignment] |

Calibrate frequency to configured CS motion:
- High-touch (enterprise): monthly minimum; QBR at each quarter
- Tech-touch: quarterly check-in standard; no standing monthly call
- Hybrid: match the segment this account sits in

---

### 6. Mutual commitments

What each party commits to. Both sides have responsibilities — a plan that only
lists CSM commitments is not a mutual plan.

**AutogenAI commits to:**
- [Specific commitment — e.g., "Respond to support tickets within [SLA]"]
- [Specific commitment — e.g., "Provide a dedicated CSM point of contact"]
- [Specific commitment — e.g., "Share product roadmap quarterly"]

**[Account name] commits to:**
- [Customer commitment — e.g., "Assign an internal project champion with time allocation"]
- [Customer commitment — e.g., "Provide feedback within 5 business days of deliverables"]
- [Customer commitment — e.g., "Ensure executive sponsor attends semi-annual reviews"]

If mutual commitments section is left to generic placeholders, flag `[review]` —
a plan without customer commitments isn't co-owned.

---

### 7. How to reach us

| Need | Contact | Channel | Response time |
|------|---------|---------|--------------|
| Day-to-day | [CSM name] | [email / Slack] | [1 business day] |
| Urgent | [Support] | [channel] | [per SLA] |
| Escalation | [CS lead or VP] | [email] | [per escalation matrix] |

---

### 8. Plan history

| Date | Change | Updated by |
|------|--------|-----------|
| [Date] | Initial plan created | [CSM name] |

---

## Review mode (`--review`)

When reviewing a submitted success plan, check:

- [ ] Success criteria are measurable — specific metric, target, evidence source
- [ ] Customer's goals are stated in the customer's language, not product feature language
- [ ] Milestones have owners — not just the CSM
- [ ] Engagement cadence matches configured CS motion
- [ ] Mutual commitments section includes customer commitments, not only CSM
- [ ] Primary value metric (win rate improvement / proposal throughput) is reflected in criteria
- [ ] No internal health signals or escalation routing in the customer-facing document
- [ ] Plan has a review / update cadence — it's a living document, not a one-time deliverable

Return specific edits with section references.

---

## Reviewer note (internal)

> **⚠️ Reviewer note**
> - **Sources:** [Customer goals: [source] | CRM — HubSpot ✓ live | Glyphic AI call recording ✓ live | Document: [doc name, date] | user-provided | not connected — conversation context only]
> - **Success criteria source:** [Account-specific from [source] | configured standard template — customize before finalizing]
> - **Data as of:** [timestamp]
> - **Flagged for your judgment:** [N items marked `[review]` inline | none]
> - **Before sending:** Verify customer has reviewed and agreed to the success criteria in Section 3 before treating this as a mutual plan. Success criteria agreed verbally but not in writing are flagged `[review]`.

---

## Output

Success plan document — format driven by `--draft` (default) or `--review` flag.
Draft mode produces a full structured plan with goals, milestones, metrics, and
owner assignments. Review mode produces a gap analysis against an existing plan.
See **Success plan structure** section for field-level detail.

## Guardrails

**No invented goals.** Do not infer success criteria from product features or
industry benchmarks. Criteria come from stated customer goals. If goals are
unknown, the reviewer note says so explicitly and the plan is marked `[DRAFT]`.

**Co-authorship is the goal.** A plan the customer never saw is an internal CSM
document. The plan is not finalized until the customer sponsor has reviewed and
agreed to the success criteria.

**Quiet mode.** The customer-facing plan contains no internal health scores,
no expansion signal tags, no escalation routing. Those stay in the reviewer note.

**Renewal language.** If the plan covers a period that includes the renewal window,
do not include language that implies renewal likelihood — flag for reviewer
validation before sharing with leadership or finance.

**Primary value metric.** Win rate improvement / proposal throughput must be
traceable in the success criteria. If it's absent, flag it.

---

## After the plan

- "Plan drafted. Want to walk through the success criteria with the customer?
  I can prepare talking points. `/csm:call-prep [account] kickoff`"
- "Want to set a milestone review reminder? Run `/csm:renewal-readiness [account]`
  when you reach M5."
- "Ready to build the QBR when M4 or M5 are complete? `/csm:qbr-builder [account]`"

---

## Security & Permissions
- network_access: outbound_allowlist (CRM, CS platform, document storage per configured integrations)
- filesystem_write: outbound to document storage only (per configured integration)
- subprocess_execution: false
- dynamic_code_execution: false

## Trust & Verification
- Internal signals (health scores, expansion flags, ARR) must not appear in customer-facing plan output
- Success criteria must be validated against the customer's stated goals — do not invent metrics
- If account context is entirely absent, prompt for customer-stated goals before proceeding

---

## Reference Material

### Reasoning Blueprint: Success Plan Builder

#### Problem Classification Taxonomy

**Type A: New Account Plan**
Characteristics: Fresh account post-kickoff or sales handoff. No prior success plan exists. Goals come from sales notes, kickoff discussions, or customer-stated outcomes.
Primary Risk: Inventing goals from product features instead of customer-stated business outcomes.
Expert Focus: Validate that goals are the customer's words, not CSM translations.

**Type B: Plan Reset**
Characteristics: Existing plan has drifted — sponsor changed, use case pivoted, original criteria never validated, or account context shifted materially.
Primary Risk: Carrying forward stale criteria that no longer reflect what the customer cares about.
Expert Focus: Identify what changed and why before rebuilding; don't patch — re-anchor.

**Type C: Plan Review**
Characteristics: User submits an existing plan for quality assessment. Skill evaluates measurability, co-ownership, and alignment to CS motion.
Primary Risk: Rubber-stamping a plan that looks structured but has unmeasurable criteria or missing customer commitments.
Expert Focus: Check that every success criterion has a specific metric, evidence source, and owner — not just a goal statement.

**Type D: Milestone Recalibration**
Characteristics: Plan exists and is sound, but timelines or targets need adjustment due to adoption pace, customer delays, or scope changes.
Primary Risk: Adjusting targets downward without addressing the root cause of the miss.
Expert Focus: Distinguish between timeline slip (acceptable) and value erosion (requires re-engagement).

---

#### Domain Heuristics

1. **Customer's Words Rule**: Success criteria phrased in product language signal CSM projection, not customer agreement. If you can't trace a criterion to something the customer said, it's invented.

2. **Measurability Gate**: A criterion without a specific metric, baseline, and evidence source is a wish, not a criterion. Flag it `[review]` every time.

3. **Co-Ownership Test**: If the mutual commitments section has zero customer commitments, the plan is an internal CSM document, not a joint agreement.

4. **Primary Value Metric Anchor**: Win rate improvement / proposal throughput must appear in the success criteria. Its absence means the plan doesn't connect to how AutogenAI measures CS impact.

5. **Cadence-Motion Alignment**: Engagement frequency must match the configured CS motion. A high-touch cadence on a tech-touch account wastes capacity; a quarterly-only cadence on a strategic account signals under-investment.

6. **Quiet Document Rule**: No health scores, expansion signals, or internal escalation routing in the customer-facing plan. Internal intelligence stays in the reviewer note.

7. **Renewal Buffer Heuristic**: The renewal-ready milestone (M5) should land at least 60 days before renewal date. Plans without this buffer leave no room for course correction.

---

#### Common Failure Modes

**Type A (New Account Plan)**
- Assumed goals: Building criteria from product features because kickoff notes are thin. Fix: Ask for customer-stated goals explicitly; mark plan `[DRAFT]` if none are available.
- Generic milestones: Using template milestones that don't connect to this customer's specific goals. Fix: Each milestone must trace to a success criterion from Section 3.

**Type B (Plan Reset)**
- Patch instead of rebuild: Editing dates on a fundamentally misaligned plan. Fix: Start from current customer goals, not the old plan structure.
- Stale stakeholder context: Rebuilding around a sponsor who left. Fix: Confirm current sponsor and champion before drafting.

**Type C (Plan Review)**
- Surface-level review: Checking structure without testing measurability. Fix: For each criterion, ask: "Could I verify this with data in 90 days?"
- Missing co-ownership check: Approving a plan with no customer commitments. Fix: Flag mutual commitments section explicitly in every review.

**Type D (Milestone Recalibration)**
- Silent target reduction: Lowering targets without documenting why. Fix: Every adjustment needs a rationale in plan history.
- Timeline-only fix: Extending dates when the real issue is adoption failure. Fix: Check whether the customer is using the product before adjusting milestones.

---

#### Expert Judgment Patterns

**Goal Validation**
- If a customer goal sounds like a feature name, it was translated by the CSM — revert to the customer's actual language.
- If goals conflict with each other (speed vs. thoroughness), surface the tension explicitly rather than optimizing for one silently.

**Measurability Decisions**
- When the customer resists specific targets, offer ranges ("70-80% adoption") rather than dropping measurability entirely.
- When no baseline exists, define the measurement method and commit to establishing baseline within 30 days.

**Stakeholder Calibration**
- A plan reviewed only by the day-to-day contact is not sponsor-validated. Confirm exec sponsor awareness before marking criteria as agreed.
- When the champion and sponsor have different priorities, document both and flag the divergence.

**Risk Signals in Plans**
- Plans with no `[review]` flags are suspicious — either the CSM has perfect information (rare) or the plan wasn't scrutinized.
- Plans where all milestones are CSM-owned indicate a delivery relationship, not a partnership.
