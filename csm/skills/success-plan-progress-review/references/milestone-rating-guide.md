# Milestone Rating Guide

**File:** `reference/milestone-rating-guide.md`  
**Skill:** `csm:success-plan-progress-review`  
**Version:** 1.0.0  
**Purpose:** Rating criteria for milestone statuses; escalation thresholds; CSM action generation logic; Success Criteria evaluation logic; Measures of Success assessment guidance; Key Benefits Already Realized guidance.

---

## 1. Milestone Status Definitions

The CSM assigns one of three statuses to each milestone in `milestone_updates`. This guide defines what each status means, the detection signals that typically trigger it, and how the skill interprets it.

### On Track

**Definition:** The milestone is progressing as planned. The customer is meeting the expected pace, and there are no active blockers or meaningful deviation from the plan timeline.

**Detection signals (CSM judgment):**
- Completion percentage or activity metrics are at or above target for this point in the plan timeline
- No reported blockers from customer stakeholders
- Customer engagement is active and responsive
- No material change in customer circumstances (staffing, budget, priorities) that would affect delivery

**Skill interpretation:**
- Logged in the Progress Scorecard with status `On Track`
- No action item generated in the CSM Action List for this milestone
- Contributes to `milestone_summary.on_track` count
- Customer-Facing Summary tone remains positive if all or most milestones are On Track

---

### At Risk

**Definition:** The milestone is in jeopardy. There is a credible threat to on-time completion or quality delivery, but the milestone has not yet been formally missed. Intervention is possible and indicated.

**Detection signals (CSM judgment):**
- Completion rate is materially below target for this point in the plan timeline
- A specific blocker has been identified (resourcing, access, stakeholder availability, competing priority)
- Customer engagement has declined or gone silent for 2+ weeks
- Key sponsor or champion contact has changed
- Dependency on another milestone or third party that is itself at risk
- Soft signals: customer expressing doubt in QBR prep, de-escalating urgency, or delaying scheduled calls

**Skill interpretation:**
- Logged in the Progress Scorecard with status `At Risk`
- Generates one or more action items in the CSM Action List (see Section 3)
- Contributes to `milestone_summary.at_risk` count
- Customer-Facing Summary tone shifts to cautionary if 1–2 milestones are At Risk

---

### Missed

**Definition:** The milestone has not been completed by its planned date, or the deliverable has been formally deferred without a committed recovery date. The plan baseline is broken for this milestone.

**Detection signals (CSM judgment):**
- Planned completion date has passed without delivery
- Customer has formally or informally cancelled the milestone activity
- CSM has confirmed with internal stakeholders that completion is no longer achievable within the current plan period
- Milestone was dependent on a predecessor that was also missed

**Skill interpretation:**
- Logged in the Progress Scorecard with status `Missed`
- Generates escalated action items in the CSM Action List (see Section 3)
- Contributes to `milestone_summary.missed` count
- Customer-Facing Summary tone shifts to escalation if any milestone is Missed

---

## 2. Escalation Thresholds

Escalation thresholds govern how the skill adjusts the tone and urgency of the CSM Action List and (when enabled) the Customer-Facing Summary.

### Threshold 1 — Standard monitoring
**Condition:** All milestones are `On Track`  
**Action List response:** "No immediate actions required — continue standard monitoring cadence."  
**Summary tone:** Positive

### Threshold 2 — Active management required
**Condition:** 1 milestone is `At Risk`; no `Missed` milestones  
**Action List response:** Investigate + intervention plan for the At Risk milestone  
**Summary tone:** Cautionary (acknowledge progress, name the risk, signal next steps)

### Threshold 3 — Elevated risk; multi-milestone attention
**Condition:** 2 or more milestones are `At Risk`; no `Missed` milestones  
**Action List response:** Investigate + intervention for each At Risk milestone; add a coordination note to avoid compounding risk  
**Summary tone:** Cautionary with urgency language; recommend alignment call

### Threshold 4 — Recovery planning required
**Condition:** 1 or more milestones are `Missed`; any number `At Risk`  
**Action List response:** Recovery plan for each Missed milestone; intervention for each At Risk milestone; escalation to internal account team  
**Summary tone:** Escalation — honest, calm, outcome-focused; avoid minimizing language

### Threshold 5 — Executive escalation
**Condition:** 3 or more milestones are `Missed`, OR any Missed milestone is on the critical path of the customer's primary stated objective  
**Action List response:** All of Threshold 4 plus: recommend executive sponsor outreach; flag for internal QBR or account review  
**Summary tone:** Escalation — signal need for leadership alignment

---

## 3. CSM Action Generation Logic

The CSM Action List is generated from `milestone_updates`. The following rules govern what action items are produced per milestone status.

### On Track → No action item

No entry is added to the CSM Action List for `On Track` milestones. If all milestones are `On Track`, the CSM Action List contains a single line: "No immediate actions required — continue standard monitoring cadence."

### At Risk → Investigation + Intervention actions

For each `At Risk` milestone, generate the following action items:

| Action Type | Action Template |
|-------------|-----------------|
| Investigate | Review notes and history for [milestone_name] — identify root cause of risk (resourcing, blocker, engagement, dependencies). |
| Diagnose | Schedule a focused conversation with the customer to confirm the blocker and assess their commitment to the milestone. |
| Intervention plan | Draft an intervention plan: revised timeline, escalation path if blocker is customer-side, or support engagement if blocker is product/delivery-side. |
| Internal flag | Flag [milestone_name] in the next internal account review. |

**Notes field content** from `milestone_updates` should be referenced in the action item where it provides specifics (e.g., "IT resourcing delayed — action: engage customer IT lead and escalate to sponsor if not resolved by [date]").

### Missed → Recovery + Escalation actions

For each `Missed` milestone, generate the following action items:

| Action Type | Action Template |
|-------------|-----------------|
| Root cause | Document root cause for [milestone_name] — was this a customer-side failure, CSM-side delivery gap, or external dependency? |
| Recovery plan | Define a recovery path: revised target date, adjusted scope (if needed), and owner confirmation from customer. |
| Customer communication | Communicate the miss and recovery plan to the customer sponsor — use escalation tone from customer-summary-templates.md if `include_customer_summary: true`. |
| Internal escalation | Escalate [milestone_name] missed status to CSM manager / account team lead. Add to account risk log. |
| Sponsor outreach | (Threshold 5 only) Engage executive sponsor to reaffirm commitment to plan objectives and revised delivery path. |

---

## 4. Success Criteria Evaluation Logic

When `success_criteria_status` is provided, the skill generates a Success Criteria Evaluation section. This section is distinct from the Progress Scorecard — it evaluates whether defined success thresholds have been met, not just whether milestones are on track.

### Interpretation: `met: true`

The criterion has been met. Signal:
- Display "Yes" in the Met column of the Success Criteria table
- Count toward the "criteria met" tally (if a tally summary is included)
- If notes are present, display them as context (e.g., date met, evidence cited)

### Interpretation: `met: false`

The criterion has not been met. Signal:
- Display "No" in the Met column
- If associated milestone is `At Risk` or `Missed`, the criterion failure reinforces the action items for that milestone
- If a criterion is `met: false` but no associated milestone is `At Risk` or `Missed`, add a CSM Action item: "Review [criterion] — success criterion not yet met despite active milestone. Identify gap and corrective action."
- Notes field should explain what is missing or what the current state is

### Partial Success Interpretation

There is no `partial` value for `met` — the field is boolean. When a CSM believes a criterion is "almost met," the guidance is:
- Set `met: false` to accurately reflect the current state
- Use the `notes` field to describe the gap (e.g., "Currently 72% of 80% target — within 2 weeks of meeting criterion")
- The CSM Action List should reflect the specific gap rather than treating this the same as a fully unmet criterion

### Success Criteria and Milestone Alignment

Success criteria often map to specific milestones. Where the connection is evident from naming, the skill should note the alignment in the CSM Action List. If a criterion is met but the corresponding milestone is `At Risk`, flag the discrepancy — the criterion may have been met in a prior period but could regress.

---

## 5. Measures of Success Assessment

Measures of Success (MoS) are quantitative outcome targets defined in the upstream success plan canvas (typically in the Value Blueprint section as specific metrics: "Reduce manual reporting time by 40%", "Achieve 80% DAU adoption", etc.). When these targets appear in the canvas and the CSM provides status via `ocv_updates` or `success_criteria_status`, the skill assesses progress against defined targets.

### Assessment approach

1. **Identify the target:** Pull the quantitative target from the upstream canvas for each relevant OCV outcome or success criterion.
2. **Assess current state:** Use the `notes` field in `ocv_updates` or `success_criteria_status` to understand current measurement (e.g., "currently at 52% of 80% target").
3. **Rate trajectory:** Determine if the current state is on a trajectory to meet the target within the plan period:
   - On trajectory → note the current measurement and expected completion date
   - Off trajectory → note the gap and flag in CSM Action List with a measurement/course-correction action
   - Target met → note achievement date and evidence

### Quantitative gap framing for CSM actions

When a MoS is off trajectory, the CSM Action item should be specific:
- "DAU adoption is at 52% against an 80% target. With [n] weeks remaining in the plan period, the required adoption rate increase is [x]% per week. Schedule a focused adoption sprint or revise the target with sponsor alignment."

### No target defined

If the success criterion or OCV outcome does not have a quantitative target defined in the canvas, the assessment is qualitative only. The skill should not manufacture a numeric target. Notes from the CSM are the primary signal.

---

## 6. Key Benefits Already Realized Guidance

The `key_benefits_realized` input captures concrete, customer-acknowledged value that has already been delivered since the success plan was created. This is distinct from milestone completion — it focuses on outcomes the customer has experienced, not activities completed.

### What to include

- Benefits the customer has explicitly acknowledged (verbally in calls, in writing, in prior reviews)
- Quantitative outcomes where data is available (e.g., "40% reduction in manual report generation time — confirmed by ops manager")
- Qualitative improvements the customer values and has articulated (e.g., "Sales team now has real-time pipeline visibility — VP Sales cited this as a top win")
- Early-stage value signals even if not fully measured (e.g., "First integration deployed — admins no longer manually exporting data")

### What not to include

- Milestones completed (these are already in the Progress Scorecard)
- Features enabled that have not yet been used or acknowledged
- Benefits the CSM believes exist but the customer has not confirmed
- Internal CSM activities (these belong in the CSM Action List or Notes)

### How the skill surfaces key benefits

- In the Progress Scorecard as a `### Key Benefits Already Realized` subsection — internal CSM record
- In the Customer-Facing Summary (when `include_customer_summary: true`) — translated into customer-appropriate language using templates from `reference/customer-summary-templates.md`
- In the QBR Pre-Work Note (when `include_qbr_note: true`) — as the "Key Wins" section

### Framing guidance

Benefits should be framed in terms of customer outcomes, not product features:
- Not: "Customer has the reporting dashboard enabled"
- Yes: "Ops team has eliminated weekly manual data export — estimated 3 hours/week saved"

Benefits should be attributed to the customer's voice where possible:
- "VP Sales confirmed real-time pipeline visibility as a top productivity win in the March check-in"
