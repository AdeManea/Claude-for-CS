---
name: renewals-downgrade-analysis
description: "Analyzes customer contract downgrade requests (contraction only) and produces a value chain failure map, counter-proposal inputs, and recommended response strategy for CSM/AM negotiation. Scoped to contraction scenarios — full cancellation redirected to renewals:churn-rca."
---

## Company Context

**AutogenAI** builds an AI-powered proposal and bid writing platform for enterprise teams. Primary value metric: win rate improvement and proposal throughput. Segment: Enterprise.

**CSM role:** Enterprise CSM, CSM-led renewals (CSM owns renewal end-to-end). Book: ~10 accounts, £1.2M ARR, £120K avg deal size. NRR target: 120%.

**Key churn signals relevant to downgrade analysis:**
- Cost per active user rising — licences not converting to genuine active users is the primary retention diagnostic
- Login decay (7–14 day no-login window is the intervention point); users generating first drafts then finishing in Word, never returning
- Unresolved structural blockers: IT/security restrictions, broken integrations, poor-quality generation sending users back to ChatGPT or Microsoft Copilot
- Exec sponsor disengaged: no QBR attendance, sponsor change, no outreach response

**Competitive threats:** ChatGPT, Microsoft Copilot, manual Word/Google Docs workflows. Competitors fill the gap after a bad experience or structural blocker — displacement is one causal chain from blocker to churn.

**Escalation for confirmed churn risk:** Manager of Customer Success or Chief Customer Officer via Slack or email.

**Tools available:** HubSpot (CRM), Planhat (CS platform/playbooks), DocuSign (contracts), Outlook, Slack. Negotiation posture: consultative. No formal CS methodology framework.

---

## Skill Instructions

# /renewals:downgrade-analysis [VALIDATED]

## Overview

**Use when:**
- A customer has requested a contract reduction, tier downgrade, seat reduction, module removal, or ARR contraction
- A CSM or AM needs structured analysis of the downgrade driver before entering negotiation
- Preparing a counter-proposal or escalation brief for a renewal at risk of contracting

**Do NOT use for:**
- Full contract cancellations, terminations, or "cancel everything" requests → use `renewals:churn-rca`
- Expansion or upsell analysis
- Renewal risk scoring without a specific downgrade request in hand
- Post-mortem analysis after a downgrade has already closed

**Typical activation:**
> "Run downgrade analysis for Acme Corp — they want to cut from 500 to 200 seats, saying the team shrank."
> "Analyze this downgrade request: customer says they're switching to a cheaper tool."
> `/renewals:downgrade-analysis` — then provide operation inputs when prompted

**Churn boundary:**
If the request contains signals of full contract cancellation ("cancel everything," "terminate contract," "no longer proceeding," "shut down the account"), this skill redirects to `renewals:churn-rca`. Partial reduction of any kind — seats, modules, tier, spend — is in scope.

---

## Use When
- A customer has requested a contract reduction, tier downgrade, seat reduction, module removal, or ARR contraction
- A CSM or AM needs structured analysis of the downgrade driver before entering negotiation
- Preparing a counter-proposal or escalation brief for a renewal at risk of contracting

## Do NOT Use For
- Full contract cancellations, terminations, or "cancel everything" requests → use `renewals:churn-rca`
- Expansion or upsell analysis
- Renewal risk scoring without a specific downgrade request in hand
- Post-mortem analysis after a downgrade has already closed

## Typical Activation
> "Run downgrade analysis for Acme Corp — they want to cut from 500 to 200 seats, saying the team shrank."
> "Analyze this downgrade request: customer says they're switching to a cheaper tool."
> `/renewals:downgrade-analysis` — then provide operation inputs when prompted

---

## Pre-flight

- Confirm account context (company name, CSM/AM, current contract, renewal date)
- Identify the specific analysis goal (new analyze or update to existing DGA)
- Note any prior analysis or existing documentation to reference

**G-code dependency:** All G-code guardrails referenced in this skill (G1–G9) apply as follows:
- G4: Escalation triggers must be specific and actionable — not generic "escalate to your manager."
- G5: Counter-proposal inputs (walk-away figures, concession authority, competitive analysis) are CSM/AM internal use only — never include in customer-facing output.

---

## Operations

This skill supports two operations: `analyze` and `update`.

---

### Operation: `analyze`

Creates a new downgrade analysis record and writes it to disk.

**Input fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| operation | string | Yes | Must be `"analyze"` |
| account_name | string | Yes | Account name — used for file path and display |
| csm_name | string | Yes | CSM or AM initiating the analysis |
| downgrade_request | string | Yes | Free-text description of what the customer wants to reduce |
| current_contract | string | No | Current tier, product name, or ARR |
| driver_category | string | No | One of: `budget_pressure`, `reduced_scope`, `feature_underutilization`, `competitive_pressure`, `dissatisfaction`. Inferred if omitted. |
| ocv_snapshot | string | No | OCV/outcome data snapshot — advisory signal for value delivery correlation |
| notes | string | No | Additional CSM/AM context |

**Auto-generated fields (immutable after creation):**
- `dga_id`: `DGA-[ACCT]-[YYYYMMDD]-[SEQ]` — ACCT is first 8 chars of safe_account uppercased, SEQ is 3-digit sequence starting 001
- `created_at`: ISO 8601 datetime
- `created_by`: session user

**Scope redirect — full churn detection:**
If `downgrade_request` contains any of the following signals, return the redirect message and do not create a file:
- "cancel everything" / "cancel the contract" / "cancel our subscription"
- "terminate contract" / "terminate the agreement"
- "no longer proceeding" / "shutting down" / "going out of business"
- "end our relationship" / "close the account"

Redirect message (exact):
```
Scope redirect: This request describes full contract cancellation, not a downgrade.
Please use renewals:churn-rca for churn analysis.
```

**Driver category inference (when `driver_category` not provided):**

| Signal vocabulary in request | Inferred category |
|------------------------------|-------------------|
| cost, price, budget, expensive, afford, overpaying, ROI, justify spend | `budget_pressure` |
| fewer users, less volume, smaller team, headcount, reduced seats, team shrank | `reduced_scope` |
| not using, don't need all features, too complex, not adopted, only using part | `feature_underutilization` |
| competitor, alternative, switching, vendor, cheaper tool, evaluating others | `competitive_pressure` |
| problems, unhappy, support, broken, frustrated, disappointed, not working | `dissatisfaction` |

Mixed-signal handling: if two or more categories are signaled, identify the primary (strongest/most explicit signal) and note secondary signals in the analysis.

**Analysis output structure:**

```
## Downgrade Analysis: [Account Name]
### Analysis ID: [DGA-ID]
### Driver Category: [category] ([inferred|provided])

## Value Chain Failure Map
### Failure Classification: [Missing link | Broken link | Non-value-chain driver]
[Narrative: what failed, where in the value chain, evidence from request and OCV]

## Counter-Proposal Inputs (CSM/AM Internal Use)
### Retention Levers Available
[Structured inputs: concessions, alternatives, commitment asks]
### Negotiation Anchors
[Key facts and positions CSM/AM can reference]

## Recommended Response Strategy
### Primary Action
[Highest-priority response action for this driver category]
### Supporting Actions
[Ordered list of supporting moves]
### Escalation Trigger
[Condition under which this should escalate beyond CSM/AM]
```

**Output file path:** `context/downgrade-analysis-[safe_account]-[YYYY-MM-DD].md`

---

### Operation: `update`

Appends new information to an existing downgrade analysis. Never overwrites prior content.

**Input fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| operation | string | Yes | Must be `"update"` |
| dga_id | string | Yes | Target analysis ID (e.g., `DGA-ACMECORP-20260517-001`) |
| additional_context | string | Yes | New information to append |
| revised_driver_category | string | No | If new info changes driver classification — one of the 5 valid categories |
| ocv_snapshot | string | No | Updated OCV data — appended alongside prior snapshot |
| notes | string | No | Additional notes |

**Immutable fields — update rejected if provided as modifications:**
`dga_id`, `created_at`, `created_by`, `account_name`

Error message format:
```
Immutable field error: [field_name] cannot be modified after analysis creation.
```

**Update block format (appended to file):**
```
## Update Log

### Update [N] — [YYYY-MM-DDTHH:MM:SSZ]
**Added by:** [csm_name or session user]
**Additional context:** [additional_context content]
**Driver reclassification:** [if applicable — from X to Y, rationale]
**OCV update:** [if provided]
**Notes:** [notes if provided]
```

---

## Output Format

### File naming convention
`context/downgrade-analysis-[safe_account]-[YYYY-MM-DD].md`

### safe_account derivation (file paths only — never used for display)
1. Lowercase the full `account_name`
2. Replace all non-alphanumeric characters with `-`
3. Collapse consecutive hyphens to a single hyphen
4. Trim to 30 characters maximum

Examples:
- `Acme Corp` → `acme-corp`
- `O'Brien & Associates, LLC` → `o-brien-associates-llc` (trimmed if over 30 chars)

### Output file YAML frontmatter
```yaml
---
dga_id: DGA-[ACCT]-[YYYYMMDD]-[SEQ]
account_name: [original account_name verbatim]
safe_account: [derived safe_account]
csm_name: [csm_name]
driver_category: [category]
driver_source: inferred | provided
created_at: [ISO 8601 datetime]
created_by: [session user]
updated_at: [ISO 8601 datetime — set on each update]
current_contract: [value or omitted if not provided]
---
```

---

## Reasoning Protocol

Before generating output, apply these primers:

1. **CLASSIFY**: What type of downgrade analysis request is this?
   - **New Analysis** (`analyze`): New downgrade request — driver classification, value chain failure map, counter-proposal inputs, response strategy.
   - **Update** (`update`): Append new context to an existing DGA record — preserve prior analysis verbatim, append as numbered update block.

2. **CONSTRAINTS**: What limits the solution space?
   - G4: Escalation triggers must be specific and actionable — not generic "escalate to your manager."
   - G5: Counter-proposal inputs (walk-away figures, concession authority, competitive analysis) are CSM/AM internal use only — never include in customer-facing output.
   - Never infer driver category from a single ambiguous signal — require clear signal vocabulary match or flag as mixed-signal.

3. **EXPERT CHECK**: What would a veteran renewals specialist verify first?
   - Is the value chain failure classification (Missing link / Broken link / Non-value-chain) correctly matched to the driver category and OCV data?
   - Does the recommended response strategy address the actual driver — not the surface objection?

4. **ANTI-PATTERNS**: Common mistakes to avoid:
   - Accepting the stated downgrade reason as the root driver without checking for secondary signals in the vocabulary.
   - Classifying a budget cut as a value chain failure when it is a non-value-chain driver (external mandate).
   - Generating escalation triggers that are time-based but not tied to a specific outcome or escalation owner.
   - Proceeding to counter-proposal without first checking for full-churn signals — always run the redirect check first.
   - Modifying immutable fields (dga_id, created_at, created_by, account_name) in update operations — these are locked at creation.

**After execution**, verify:
- Did the full-churn signal check run before any analysis?
- Is the value chain failure classification consistent with the driver category and OCV evidence?
- Are escalation triggers specific (condition, owner, timeframe)?
- Confidence: [High] when account data is complete and driver vocabulary is unambiguous / [Medium] when partial data or mixed signals / [Low] if inputs are manual or inferred

---

### For `analyze` operations

**Step 1 — Full-churn signal check**
Scan `downgrade_request` for cancellation vocabulary. If found, return scope redirect message and stop. Do not create a file.

**Step 2 — Derive safe_account**
Apply the 4-step derivation: lowercase → replace non-alphanumeric with `-` → collapse hyphens → trim to 30 chars. Confirm derivation before using in file path.

**Step 3 — Driver category resolution**
If `driver_category` is provided: validate against the 5-category enum. If invalid, return error and stop.
If `driver_category` is not provided: scan `downgrade_request` for signal vocabulary per inference table. Identify primary category. Note secondary signals. Set `driver_source` to `inferred`.

**Step 4 — Value chain failure classification**
Apply the three-way classification:
- **Missing link**: Outcome committed but not delivered. OCV shows gap between expected and actual outcome delivery. Root cause: CSM/delivery failure.
- **Broken link**: Outcome delivered but customer has not recognized or adopted it. Low usage despite feature availability. Root cause: adoption/change management failure.
- **Non-value-chain driver**: Budget cuts, reorg, market conditions, headcount reduction, competitive pricing. Value delivery is not the root cause.

Classification heuristics:
- `budget_pressure` without dissatisfaction signals → Non-value-chain driver (unless OCV shows delivery gap)
- `reduced_scope` (team shrank, fewer users) → Non-value-chain driver
- `feature_underutilization` → Broken link (delivered but not adopted) or Missing link (never onboarded)
- `competitive_pressure` → Non-value-chain driver unless price objection masks dissatisfaction
- `dissatisfaction` → Missing link (failed delivery) — corroborate with OCV if provided

When `ocv_snapshot` is provided: OCV gap between committed and actual outcomes strengthens Missing link; strong outcome delivery + low adoption strengthens Broken link.

**Step 5 — Counter-proposal inputs**
Generate retention levers and negotiation anchors per driver category. Mark all counter-proposal inputs as CSM/AM internal use only.

**Step 6 — Recommended response strategy**
Generate primary action, supporting actions (ordered by priority), and escalation trigger. Escalation trigger must be specific and actionable, not generic.

**Step 7 — DGA-ID derivation**
Construct candidate ID: `DGA-[ACCT]-[YYYYMMDD]-001` where ACCT = first 8 chars of safe_account uppercased, with hyphens removed. Check `context/` for existing `downgrade-analysis-[safe_account]-[YYYY-MM-DD].md` files. If one exists, scan frontmatter for the highest existing SEQ with matching ACCT+date prefix and increment by 1. If no match, SEQ = 001.

**Step 8 — Write output file**
Write complete file to `context/downgrade-analysis-[safe_account]-[YYYY-MM-DD].md` with YAML frontmatter followed by full analysis content. Confirm write succeeded. Return file path and DGA-ID.

---

### For `update` operations

**Step 1 — Immutable field check**
If input attempts to modify `dga_id`, `created_at`, `created_by`, or `account_name`, return immutable field error and stop. (`dga_id` as lookup key is valid; providing it as a modification is not.)

**Step 2 — Locate target file**
Parse `dga_id` to extract date component. Search `context/downgrade-analysis-*` for file whose frontmatter contains the exact `dga_id`. If not found, return: `Analysis not found: no file with dga_id [dga_id] in context/downgrade-analysis-*.`

**Step 3 — Validate revised_driver_category (if provided)**
Validate against 5-category enum. Return error if invalid.

**Step 4 — Count existing updates**
Scan file for `### Update [N]` pattern. Set next N = count + 1.

**Step 5 — Append update block**
Append update block. Omit lines for absent optional fields. Set `updated_at` in YAML frontmatter to current ISO 8601 datetime.

**Step 6 — Confirm and return**
Return: updated file path, update number, timestamp.

---

## Security & Permissions

```
network:        none — no external API calls, no web fetch
read_scope:     context/downgrade-analysis-* only
write_scope:    context/downgrade-analysis-* only
ocv_data:       read from ocv_snapshot input parameter only — never from filesystem
subprocess:     none
dynamic_code:   none — no eval, no exec, no runtime code execution
```

This skill operates exclusively within the `context/` directory on files matching the `downgrade-analysis-*` prefix. It does not read from, write to, or reference any other path.

---

## Trust & Verification

**safe_account sanitization:**
`safe_account` is derived deterministically from `account_name` using the 4-step normalization. It is used exclusively in file paths. The original `account_name` is preserved verbatim in frontmatter and all display contexts. `safe_account` is never shown to users as an account identifier.

**Free-text field handling:**
`downgrade_request`, `notes`, and `additional_context` are stored verbatim as display data. They are not executed, evaluated, or used to derive file paths. Prompt injection via free-text fields cannot affect file paths, IDs, or system behavior.

**driver_category validation:**
When provided, `driver_category` is validated against the 5-item enum before use. Values outside this set return an error and halt execution. Inferred categories are constrained to the same enum.

**Immutable field enforcement:**
After a downgrade analysis is created, `dga_id`, `created_at`, `created_by`, and `account_name` cannot be modified. Update operations attempting to change these fields are rejected before any file write occurs.

**Update operations are append-only:**
No update operation modifies existing content. All updates are written as new numbered blocks. Prior analysis sections are preserved verbatim.

**ocv_snapshot integrity:**
OCV data is accepted only through the `ocv_snapshot` input parameter. This skill never reads OCV data from the filesystem, from external sources, or from prior session context not provided in the current input.

---

## Examples

### Example 1 — `analyze` with driver inference

**Input:**
```
operation: analyze
account_name: Meridian Health Group
csm_name: Jordan Reyes
downgrade_request: "Customer says they're cutting software spend across the board — CFO mandate. They want to drop from the Enterprise tier to the Professional tier."
current_contract: Enterprise tier, $84,000 ARR
ocv_snapshot: "QBR outcomes delivered: 3/5. Two outcome areas (reporting automation, manager dashboards) not yet adopted. Usage at 34% of licensed seats."
```

**Skill behavior:**
1. Churn signal check: No cancellation vocabulary — proceed.
2. safe_account: `meridian-health-group` → trimmed to `meridian-health-group` (21 chars, under limit).
3. Driver inference: "cutting software spend," "CFO mandate" → primary `budget_pressure`. OCV shows low adoption (34% seats, 2 undelivered outcomes) → secondary `feature_underutilization` noted.
4. Value chain failure: Non-value-chain driver (CFO mandate) with Broken link as contributing factor (low adoption gives the CFO justification).
5. DGA-ID: No existing file — `DGA-MERIDIANHEAL-20260517-001`.

**Output file:** `context/downgrade-analysis-meridian-health-group-2026-05-17.md`

**Recommended response strategy excerpt:**
```
### Primary Action
Secure CFO-level engagement to present ROI data before the tier change is finalized.
This is a CFO mandate — not negotiable at CSM/AM level.

### Escalation Trigger
If CSM/AM cannot access CFO or VP-level sponsor within 5 business days,
escalate to Customer Success leadership for exec-to-exec outreach.
```

---

### Example 2 — `update` operation

**Input:**
```
operation: update
dga_id: DGA-MERIDIANHEAL-20260517-001
additional_context: "Spoke with champion Sarah Kim — confirmed CFO mandate is a 90-day cost reduction cycle, not permanent. Budget expected to normalize Q3. She's open to a temporary downgrade with re-expansion commitment clause."
revised_driver_category: budget_pressure
notes: "Consider 90-day bridge pricing offer rather than full tier downgrade."
```

**Appended block:**
```
## Update Log

### Update 1 — 2026-05-17T14:32:00Z
**Added by:** Jordan Reyes
**Additional context:** Spoke with champion Sarah Kim — confirmed CFO mandate is a
90-day cost reduction cycle, not permanent. Budget expected to normalize Q3.
She's open to a temporary downgrade with re-expansion commitment clause.
**Driver reclassification:** budget_pressure (confirmed — no change to prior classification)
**Notes:** Consider 90-day bridge pricing offer rather than full tier downgrade.
```

---

### Example 3 — scope redirect

**Input:**
```
operation: analyze
account_name: Pinnacle Dynamics
csm_name: Alex Torres
downgrade_request: "Customer wants to terminate the contract entirely — they're shutting down their CS department."
```

**Skill response:**
```
Scope redirect: This request describes full contract cancellation, not a downgrade.
Please use renewals:churn-rca for churn analysis.
```

No file is written.

---

## Reference Material

### Reasoning Blueprint

This skill applies structured reasoning to customer contract downgrade analysis — mapping value chain failure, generating counter-proposal inputs, and recommending response strategy for CSM/AM negotiation.

**D1 — Cognitive Stance**
CLASSIFY the request type and select the appropriate downgrade analysis mode (analyze or update).
CONSTRAINTS: Distinguish correlation from causation; flag data quality issues explicitly; never assign driver category without signal evidence.
EXPERT: Approach this as a customer success analyst with contract negotiation and churn prevention expertise.

---

### Downgrade Driver Taxonomy

**Used by:** `renewals:downgrade-analysis`
**Purpose:** Authoritative definitions, signal vocabulary, diagnostic questions, and mixed-signal handling for the 5 downgrade driver categories.

#### Driver Category Definitions

##### 1. `budget_pressure`

**Definition:** The customer is contracting primarily because of cost constraints, budget reductions, or a need to demonstrate spend reduction. The value of the product may be acknowledged, but the spend cannot be justified in the current financial environment.

**Key characteristic:** The driver is financial — not product-driven. The customer would likely retain the full contract if budget were not a constraint.

**Signal vocabulary:**
- "cutting costs," "reducing spend," "budget freeze," "CFO mandate"
- "too expensive," "overpaying," "can't justify the price," "price increase"
- "ROI question," "need to show savings," "renegotiate pricing"
- "financial pressure," "cost reduction initiative," "belt tightening"
- "afford," "budget cuts," "budget cycle," "fiscal constraints"

**Diagnostic questions to confirm or refine:**
1. Is this a time-bounded constraint (e.g., 90-day freeze, Q2 cut) or an ongoing budget change?
2. Is the budget driver a mandate from finance/CFO, or is the champion choosing to reduce spend?
3. Has the customer expressed satisfaction with the product itself, separate from cost?
4. Is there a specific budget number they need to hit, or is it "as low as possible"?
5. Have pricing or payment terms been discussed as an alternative to a tier change?

---

##### 2. `reduced_scope`

**Definition:** The customer's legitimate need for the product has contracted. Fewer users, reduced volume, smaller team size, or organizational restructuring means they no longer need the same scale of product.

**Key characteristic:** The driver is organizational — not dissatisfaction or budget. The customer is trying to right-size to actual usage, not avoid paying for value they receive.

**Signal vocabulary:**
- "team shrank," "headcount reduction," "layoffs," "restructuring"
- "fewer users," "less volume," "reduced seats needed," "smaller team"
- "department eliminated," "project ended," "use case went away"
- "we only need X users now," "half the team is gone"
- "org change," "merger," "acquisition," "reorganization"

**Diagnostic questions to confirm or refine:**
1. Is the headcount reduction permanent or temporary (e.g., hiring freeze)?
2. Are there other teams or departments that could absorb the unused licenses?
3. Is this a company-wide contraction or limited to the team using this product?
4. Would a seat-level adjustment (partial reduction) satisfy their need, or are they looking for tier/module changes?
5. Is there a growth scenario on the horizon (new hire plan, expansion plan) that makes a temporary accommodation better than a full downgrade?

---

##### 3. `feature_underutilization`

**Definition:** The customer believes they are paying for capabilities they are not using and sees no path to using them. The product may be functioning correctly, but adoption is low and the full value proposition has not been realized.

**Key characteristic:** The driver is adoption/complexity — not budget or org change. The customer's contract scope exceeds their actual consumption.

**Signal vocabulary:**
- "not using all the features," "only using a fraction of it"
- "too complex," "we don't need everything," "paying for things we don't use"
- "our team only uses X," "the rest of the features don't apply to us"
- "overpowered for our needs," "simpler tool would work"
- "adoption has been low," "not getting full value"

**Diagnostic questions to confirm or refine:**
1. Which specific features or modules are they not using? Were those ever in scope?
2. Was there a plan to expand usage to those features? If so, what blocked adoption?
3. Is the underutilization a training/onboarding gap, or is the use case genuinely absent?
4. Has the CSM done a feature audit with the champion in the last 90 days?
5. Are there alternative use cases within their organization that could absorb the unused capacity?

---

##### 4. `competitive_pressure`

**Definition:** The customer is considering or actively evaluating a competing product, often citing lower cost or specific feature gaps. The downgrade may be a negotiating tactic, or a transition step before full churn.

**Key characteristic:** This category has the highest churn escalation risk. A "downgrade" motivated by competitive pressure may become full churn if the competitor wins.

**Signal vocabulary:**
- "competitor," "alternative," "evaluating other vendors," "comparing tools"
- "switching," "looking at other options," "demo with another provider"
- "cheaper tool," "does the same thing for less," "market alternatives"
- "your competitor," "we've been approached by," "looking at X platform"
- "vendor consolidation," "rationalizing our stack"

**Diagnostic questions to confirm or refine:**
1. Is the competitive evaluation already underway, or is it a threat/negotiating position?
2. Which competitor is being considered? What is the primary attraction (price, features, brand)?
3. Is this a champion-level decision or has it reached economic buyer/procurement?
4. What would it take to keep the account at current scope?
5. Has there been a trigger event (contract renewal, new leadership, budget review) that opened the door to competitive evaluation?

**Escalation note:** If the customer names a specific competitor and is already in an evaluation, this warrants immediate escalation to leadership and/or an exec sponsor engagement.

---

##### 5. `dissatisfaction`

**Definition:** The customer is contracting because they are unhappy with the product, the service, or outcomes delivered. The downgrade reflects loss of confidence and may precede full churn if not addressed.

**Key characteristic:** This is the highest-urgency driver category from a retention risk perspective. Unresolved dissatisfaction typically escalates to churn.

**Signal vocabulary:**
- "problems," "issues," "bugs," "broken," "not working properly"
- "unhappy," "frustrated," "disappointed," "let down"
- "support is slow," "no one responds," "escalation not resolved"
- "promised X and didn't deliver," "expectations not met"
- "not what we were sold," "implementation was rough," "poor experience"
- "lost confidence," "trust issue," "management is questioning the tool"

**Diagnostic questions to confirm or refine:**
1. What specific incident(s) drove the dissatisfaction — product, support, CSM, or implementation?
2. Has a formal complaint or escalation already been filed?
3. Is the dissatisfaction coming from the end users, the champion, or the economic buyer?
4. Has anything been done to address the issue? If so, was the customer satisfied with the response?
5. Is the customer open to a remediation path, or have they already decided?

---

#### Mixed-Signal Handling

Real downgrade requests often contain signals from more than one driver category.

**Primary driver:** The driver category with the strongest, most explicit, or most actionable signal in the `downgrade_request`.

**Secondary driver:** Any additional category with meaningful signal presence. Report secondary drivers as contributing factors, not co-equal causes.

**Common mixed-signal combinations:**

| Combination | Interpretation |
|-------------|---------------|
| budget_pressure + feature_underutilization | Budget is the forcing function; low adoption gives the champion justification to agree. Address adoption gap even if budget relief is the primary lever. |
| feature_underutilization + dissatisfaction | Low adoption likely caused by a product/support failure, not genuine lack of use case. Treat as dissatisfaction with adoption-failure evidence. |
| competitive_pressure + dissatisfaction | High churn risk. Competitor is filling a gap created by dissatisfaction. Escalate immediately. |
| reduced_scope + budget_pressure | Org change created natural right-sizing opportunity that budget constraints are accelerating. Both drivers are independent and real. |
| competitive_pressure + budget_pressure | Competitive evaluation opened because budget pressure created a "good reason to look." Address budget first; competitive threat may resolve. |

Reporting format for mixed signals: "Primary driver: [category]. Secondary signal: [category] — [brief rationale for why it is secondary]."

---

### Value Chain Failure Map

**Purpose:** Defines the three failure classifications, detection patterns, remediation pathways, and escalation guidance for value chain analysis in downgrade scenarios.

The value chain failure map answers a single diagnostic question: **Is this downgrade happening because we failed to deliver value, or because of factors outside the value chain?**

The answer determines the response strategy. A CSM cannot negotiate their way out of a delivery failure — they must remediate it. Conversely, applying a remediation approach to a budget cut wastes time and may damage the relationship.

#### The Three Failure Classifications

##### Missing Link

**Definition:** An outcome was committed during the sales or onboarding process, but has not been delivered. The gap between promised and actual outcomes is the root cause of the downgrade.

**Accountability:** CSM/delivery failure.

**Indicators:**
- OCV shows outcomes at "not started" or "below target" for one or more committed areas
- Customer references what they were "promised" or "sold on" vs. what actually happened
- Implementation milestones were missed or are significantly delayed
- QBR outcomes reviewed show consistent delivery gap over 2+ quarters
- Customer expresses that they are "not getting what they paid for"
- Usage data is low not because of disinterest but because setup/enablement was not completed

**OCV detection pattern:** If `ocv_snapshot` shows committed outcome count > delivered outcome count, with delivery gaps of 30% or more, and the `downgrade_request` references dissatisfaction with outcomes or delivery, classify as Missing link.

**Example signals in downgrade_request:**
- "We were supposed to have X set up by now and it hasn't happened"
- "The reporting we were promised never got built"
- "Our CSM said we'd be able to do Y by Q2 and we still can't"
- "We're not getting the value we expected from this"

---

##### Broken Link

**Definition:** The outcome was delivered — the capability is live, the configuration is complete — but the customer has not recognized, adopted, or internalized the value. The breakdown is in change management, not delivery.

**Accountability:** Adoption/change management failure.

**Indicators:**
- OCV shows outcomes marked "delivered" but usage metrics are below threshold (e.g., seat utilization under 40%, feature adoption under 30%)
- Customer references complexity, not getting around to it, or team resistance
- Champion is aware the features are live but end-user adoption has not materialized
- Customer does not associate the delivered capability with outcomes they care about
- Training was completed but behavior change did not follow

**OCV detection pattern:** If `ocv_snapshot` shows high delivery rate (outcomes marked complete) but low usage/adoption metrics alongside the downgrade request, classify as Broken link.

**Example signals in downgrade_request:**
- "We have it set up but honestly the team just isn't using it"
- "Too complicated — people went back to their old way of doing things"
- "We pay for 500 seats but only 150 people ever log in"
- "The features are there but no one adopted them"
- "It's not part of our workflow"

---

##### Non-Value-Chain Driver

**Definition:** The downgrade is driven by factors external to value delivery. Budget constraints, organizational restructuring, headcount reductions, market conditions, competitive pricing pressure, or executive mandates are causing the contraction.

**Accountability:** External factors.

**Indicators:**
- Customer explicitly references CFO mandate, budget freeze, cost reduction initiative, or headcount cuts
- Org change events: merger, acquisition, reorg, department elimination
- Competitive pressure from a lower-cost alternative (not driven by dissatisfaction)
- Seasonal or project-based scope reduction ("the project ended," "we only need it for Q1")
- Customer acknowledges they like the product but the spend cannot be justified

**OCV detection pattern:** If `ocv_snapshot` shows strong outcome delivery and reasonable adoption, but the `downgrade_request` references budget, org change, or competitive price, classify as Non-value-chain driver.

**Example signals in downgrade_request:**
- "It's not about the product — the CFO needs us to cut SaaS spend by 20%"
- "Our team was restructured and we went from 400 to 180 people"
- "We found a cheaper tool that does most of what we need"
- "The project this was for wrapped up last quarter"

---

#### Detection Patterns by Driver Category

| Driver Category | Most likely failure classification | Key detection signal |
|---|---|---|
| `budget_pressure` | Non-value-chain driver | Explicit budget/CFO reference; no delivery complaint |
| `budget_pressure` + OCV gap | Missing link contributing | Budget is forcing function; delivery gap gives internal justification |
| `reduced_scope` | Non-value-chain driver | Headcount or org change; no product complaint |
| `feature_underutilization` (features never adopted) | Broken link | Features live but unused; training completed without behavior change |
| `feature_underutilization` (features never configured) | Missing link | Features never set up; onboarding incomplete |
| `competitive_pressure` | Non-value-chain driver | Competitor named; primary driver is price |
| `competitive_pressure` + `dissatisfaction` | Missing link escalation risk | Competitor is filling a delivery gap — treat as Missing link + high churn risk |
| `dissatisfaction` | Missing link | Explicit delivery failure; outcome promises not met |

---

#### Remediation Pathways by Failure Classification

##### Missing Link — Remediation pathway

**Objective:** Acknowledge the delivery gap, commit to a remediation plan, and use the plan as the basis for retaining the current contract scope.

1. **Acknowledge explicitly** — CSM/AM must validate the customer's experience, not defend it. "You're right that X hasn't been delivered, and that's on us."
2. **Gap quantification** — Enumerate the specific outstanding outcomes (use OCV data if available). Make the remediation concrete and time-bounded.
3. **Remediation plan proposal** — Offer a written 30/60/90-day remediation plan with named milestones.
4. **Contract protection ask** — Request that the customer hold the downgrade decision pending the first 30-day milestone review.
5. **Escalation to delivery resources** — If the delivery gap requires resources beyond the CSM, escalate internally before presenting to the customer.

**Does not resolve without:** A concrete, milestone-driven remediation plan and internal commitment to resourcing it.

---

##### Broken Link — Remediation pathway

**Objective:** Demonstrate that the value is already there, remove adoption barriers, and create a concrete path to recognized ROI.

1. **Usage audit** — Pull actual usage data vs. licensed capacity. Identify the specific adoption gap.
2. **Root cause of non-adoption** — Was it training quality? Workflow integration? Management buy-in? Competing tools?
3. **Adoption sprint proposal** — Offer a focused 30-day adoption engagement: targeted training, workflow integration session, or manager enablement program.
4. **ROI reframe** — Build a value narrative connecting delivered capabilities to business metrics the customer cares about.
5. **Quick win identification** — Find one or two high-visibility use cases the customer can activate within 2 weeks.

**Does not resolve without:** Identifying the specific adoption blocker and a targeted action to remove it.

---

##### Non-Value-Chain Driver — Remediation pathway

**Objective:** Remove or reduce the financial/organizational friction without conceding value.

1. **Validate the driver** — Confirm this is genuinely external. Ask directly: "If budget weren't an issue, would you stay at the current tier?"
2. **Commercial lever exploration** — Explore alternatives: payment terms adjustment, phased renewal, multi-year commitment with year-1 discount, or temporary seat reduction with re-expansion clause.
3. **Scope right-sizing** — If reduced_scope is genuine, work collaboratively to identify the right contract size.
4. **Value reinforcement** — Surface delivered outcomes and ROI data to strengthen the customer's internal case for retention.
5. **Future commitment anchor** — For budget_pressure with a temporary horizon, negotiate a re-expansion commitment.

**Does not resolve without:** A commercial alternative that gives the customer a path to staying that meets their constraints.

---

#### Escalation Guidance

**CSM-addressable (no escalation required):**
- Broken link where adoption gap is addressable through CSM-led enablement
- Non-value-chain budget pressure at CSM-sponsor level (champion has budget autonomy)
- Reduced scope where right-sizing conversation is straightforward
- Missing link where the delivery gap is CSM-owned and resourceable within existing capacity

**Escalate to Customer Success leadership (Manager of CS or Chief Customer Officer via Slack or email):**
- Missing link where delivery failure requires PS resources, product involvement, or additional headcount
- Competitive pressure with a named competitor and active evaluation underway
- Dissatisfaction where a formal complaint has been filed or executive sponsor is involved
- Any scenario where CSM/AM cannot reach economic buyer within 5 business days

**Escalate to Product/Engineering:**
- Missing link where the delivery gap is caused by a product limitation, bug, or missing feature
- Feature_underutilization where adoption barrier is product complexity or UX (not training/enablement)
- Scenarios where customer has documented product failures that contributed to the downgrade request

**Escalate to Executive sponsor (exec-to-exec):**
- CFO-mandate budget_pressure where only exec-level conversation can protect the contract
- Competitive_pressure + dissatisfaction combination (high churn risk)
- Any downgrade that, if it proceeds, brings ARR below a retention threshold defined by CS leadership
- Customer has escalated to VP or C-level on their side

---

### Counter-Proposal Framework

**Purpose:** Internal CSM/AM use only — never customer-facing. Defines retention levers, negotiation anchors, concession guidance, and escalation trigger conditions per driver category.

**Critical Usage Note:** This framework is strictly for CSM/AM internal preparation. The retention levers and negotiation positions defined here are inputs to the CSM/AM's strategy — they are not scripts, customer-facing proposals, or commitments. Do not share this framework or its outputs directly with customers.

#### Retention Levers by Driver Category

##### `budget_pressure`

| Lever | Description | When to use |
|-------|-------------|-------------|
| Payment term adjustment | Extend payment schedule (quarterly or monthly instead of annual upfront) to reduce immediate cash burden | Customer has cash flow constraint but budget will normalize |
| Multi-year commitment discount | Offer year-1 price reduction in exchange for 2–3 year commitment | Customer is wavering but not at risk of leaving entirely |
| Phased renewal | Renew at current scope for 6 months at reduced rate, then step back to full rate | Budget constraint is temporary (90-day freeze, fiscal year boundary) |
| Tier restructure (right-size) | Move to a lower tier with a documented re-expansion clause | Tier gap is genuine; downgrade is preferable to churn |
| Re-expansion commitment clause | Accept a downgrade but contractually or verbally anchor a re-expansion at a named milestone | Customer agrees the constraint is temporary |
| Bundle consolidation | If customer uses multiple products, consolidate into a single contract with a package discount | Customer is cutting individual line items |
| Success milestone credit | Offer a credit against future renewal tied to achievement of named outcomes | Customer doubts ROI; credit de-risks their commitment |

**What to hold:**
- Do not agree to a downgrade without a re-expansion clause or timeline commitment
- Do not offer a discount without a multi-period commitment or a concrete return condition
- Do not accept verbal re-expansion promises — document them in the renewal agreement or a follow-up email

---

##### `reduced_scope`

| Lever | Description | When to use |
|-------|-------------|-------------|
| Seat reduction with floor | Accept reduced seat count but set a contract floor above the minimum to preserve ARR baseline | Headcount reduction is genuine |
| Re-expansion trigger clause | Reduce seats with a named trigger for expansion (new hires above X, team growth, new department onboarding) | Org change is likely temporary or bounded |
| Department expansion conversation | Identify other teams or departments that could absorb unused licenses | Current users shrank, but product use case exists in adjacent teams |
| Module right-sizing | Remove unused modules rather than reducing tier — preserve strategic footprint | Customer only uses subset of features |
| Usage-based pricing exploration | If the product supports it, explore whether a usage-based model better fits reduced scope | Fixed seat pricing is the friction |

**What to hold:**
- Do not reduce below a seat count that makes the account unprofitable for CS investment
- Do not remove modules on the roadmap for future expansion without documenting the re-introduction path

---

##### `feature_underutilization`

| Lever | Description | When to use |
|-------|-------------|-------------|
| Adoption sprint commitment | Offer a focused 30-day enablement engagement to drive adoption of specific unused features | Customer is open to trying; adoption gap is recoverable |
| Usage audit + ROI report | Pull actual usage data and build a value narrative showing what has been achieved | Customer doesn't see ROI because it hasn't been quantified or surfaced |
| Champion re-engagement | Schedule a working session with the champion and key end users to remove specific adoption blockers | Adoption gap is relationship/communication-driven |
| Training redesign | Replace generic training with role-specific, workflow-integrated training | Prior training didn't stick because it wasn't contextualized |
| Feature sunset negotiation | If specific features are genuinely not needed, explore a module removal that right-sizes the contract | Pure right-sizing; unused features have no realistic adoption path |
| Success milestone gate | Propose a 60-day adoption milestone review before any contract change is finalized | Customer is willing to defer the decision |

**What to hold:**
- Do not accept the downgrade before attempting an adoption sprint — feature_underutilization is often recoverable
- Do not promise adoption outcomes (usage %) as contract conditions unless the CSM has direct influence over those metrics

---

##### `competitive_pressure`

| Lever | Description | When to use |
|-------|-------------|-------------|
| Competitive displacement analysis | Build an internal brief on the named competitor: feature gaps, total cost of ownership (including switching costs), implementation timeline | Customer has not done a full TCO analysis |
| Switching cost framing | Surface the real cost of migration: data migration, retraining, integration rebuild, productivity loss | Customer is focused on license price only |
| Strategic roadmap briefing | Request access to product leadership to brief the customer on upcoming features | Customer has a feature gap that is on the product roadmap |
| Price match (limited) | If the competitive threat is purely price-based and the customer has strong retention indicators, explore a targeted price adjustment | Customer is price-sensitive and satisfied with the product |
| Executive sponsor engagement | Bring in CS/Sales leadership for an executive relationship conversation | Economic buyer is driving the competitive evaluation |
| Proof of value data package | Build a comprehensive value summary: ROI achieved, outcomes delivered, time saved | Customer or their CFO has not seen a formal ROI case |

**What to hold:**
- Do not offer a price match without VP-level approval and a multi-year commitment in return
- Do not dismiss the competitive threat without getting specific about the competitor and the evaluation stage
- If the customer is already in a formal RFP or evaluation process, escalate immediately

---

##### `dissatisfaction`

| Lever | Description | When to use |
|-------|-------------|-------------|
| Formal acknowledgment and apology | CSM/AM explicitly acknowledges the failure, names what went wrong, and apologizes without defensiveness | Always — no other lever is effective until this step is complete |
| Executive escalation to resolution | Bring in CS leadership to own the remediation commitment | Customer has lost confidence in the CSM or the standard support channel |
| SLA commitment with penalties | Offer a formal SLA for the remediation period with defined remedies if milestones are missed | Customer needs contractual protection to re-engage |
| Dedicated support assignment | Assign a named senior resource (TAM, senior CSM, support engineer) to the account for a defined recovery period | Customer's trust in the standard support model is broken |
| Credit or fee waiver | Offer a credit or partial fee waiver for the period of degraded service — tied to the remediation plan | Customer has measurable financial impact from the delivery failure |
| Root cause briefing | Provide the customer with a written root cause analysis of what went wrong | Customer needs assurance this won't happen again |
| Re-contracting at reduced scope | If remediation is not sufficient to retain full scope, negotiate a reduced contract at a price that reflects the reduced delivery — with a defined re-expansion path | Customer cannot return to full scope until trust is rebuilt |

**What to hold:**
- Do not jump to commercial concessions before completing formal acknowledgment
- Do not assign blame (internally or externally) in any customer-facing communication
- Do not promise a remediation timeline the delivery team cannot commit to

---

#### Negotiation Anchors

##### Universal anchors (applicable to all driver categories)

1. **Total cost of ownership:** The customer's all-in cost for switching includes migration, retraining, integration rebuild, and productivity loss — not just license price differential.
2. **Relationship investment:** Reference the time already invested in onboarding, configuration, and customization. Switching resets this investment.
3. **Outcome delivery record:** Use OCV data to quantify what has been delivered. Frame the downgrade in terms of outcomes at risk, not features.
4. **Future roadmap relevance:** If the product roadmap addresses a stated gap, brief the customer on the timeline.
5. **Account team continuity:** The CSM/AM relationship has organizational memory. A new vendor starts from zero.

##### Category-specific anchors

| Driver Category | Key anchor |
|---|---|
| budget_pressure | Payment flexibility doesn't require losing the capability — frame as a cashflow solution, not a product decision |
| reduced_scope | Right-sizing to current need is reasonable, but future re-expansion will cost more than maintaining the current floor |
| feature_underutilization | The capability exists and is configured — the cost to achieve value is lower now than at any other point in the relationship |
| competitive_pressure | Competitor's published price excludes implementation, migration, and integration costs — build the true TCO comparison |
| dissatisfaction | A downgrade doesn't resolve the service failure — remediation with current vendor is faster than rebuilding with a new one |

---

#### Escalation Trigger Conditions

| Condition | Escalation target | Rationale |
|-----------|-------------------|-----------|
| CSM/AM cannot access economic buyer within 5 business days | CS Manager / VP Customer Success | Decision is being made above the CSM's relationship level |
| Customer is in an active RFP or competitive evaluation with a named vendor | CS leadership + Sales leadership | Formal eval requires formal response at leadership level |
| Dissatisfaction involves a product failure, legal complaint, or data issue | CS leadership + Product/Engineering | Technical or legal exposure requires immediate escalation |
| Downgrade would reduce ARR below the account's strategic threshold | CS leadership | Business case for retention investment must be approved at leadership level |
| Customer explicitly requests executive conversation | CS leadership / Executive sponsor | Honor the request — declining signals the problem isn't being taken seriously |
| Remediation plan requires resources not within CSM's control (PS hours, engineering time, product commitment) | CS leadership + delivery owner | CSM cannot commit to resources they don't control |
| Missing link failure was caused by a product deficiency, not CSM execution | Product leadership | Product-caused delivery failures require a product-level response |
| Champion is no longer the decision-maker (new economic buyer, reorg) | CS leadership + Sales | New buyer requires exec-level introductions; CSM-level relationship is insufficient |

---

#### Concession Guidance

Concessions should be structured, conditional, and documented. Unstructured concessions signal willingness to accept any terms and weaken future negotiating positions.

**Concession structuring principles:**

1. **Every concession requires a condition.** "We can adjust payment terms [concession] if you commit to a 2-year renewal [condition]." Never offer a concession without attaching a return ask.
2. **Concede scope before price.** Right-sizing the contract preserves the per-unit value. Discounting reduces the per-unit value and sets a new price anchor for future renewals.
3. **One concession at a time.** Do not offer multiple concessions simultaneously.
4. **Document all concessions.** Every verbal concession should be followed by a written confirmation email.
5. **Escalate before exceeding standard authority.** Any concession outside defined authority requires manager approval before being offered.

**What never to concede without leadership approval:**
- Permanent price reductions with no term commitment
- SLA guarantees with financial penalties
- Feature development commitments (product roadmap items)
- Credits that exceed one month of ARR
- Re-contracting at below-cost pricing
