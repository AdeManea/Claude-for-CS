# Claude for Customer Success — Skill Inventory

**Status:** [VALIDATED]
**Date:** 2026-05-22
**Source:** Direct read of all 81 SKILL.md files
**Total skills:** 81 (csm: 17 · onboarding: 9 · cs-ops: 9 · renewals: 12 · rev-ops: 34)

---

## CSM Plugin (17 skills)

### account-research
- **Version:** 1.0.0
- **What it does:** Produces a pre-call or pre-meeting one-page account brief covering CRM data, health signals, call history, and usage context.
- **How it does it:** Pulls from connected CRM, CS Platform, and call-recording tools (or accepts pasted context); supports `--brief`, `--deep`, and `--stakeholders` modes against an account name or CRM ID.
- **What it produces:** A structured one-page account snapshot for CSMs to consume before any customer call, QBR, health review, or stakeholder meeting.

### call-prep
- **Version:** 1.0.0
- **What it does:** Generates a pre-call preparation brief with agenda, attendee context, talking points, and questions calibrated to CS motion and account state.
- **How it does it:** Takes account name and call type (`kickoff | qbr | health | renewal | check-in | custom`), reads CSM motion config, and pairs with account-research for full context 24–48 hours before a call.
- **What it produces:** A call-ready prep brief consumed by the CSM running the customer call.

### cold-start-interview
- **Version:** 1.0.0
- **What it does:** Cold-start setup interview that learns the company's CS motion, account portfolio, health model, escalation matrix, and tool integrations.
- **How it does it:** Guided interview with `--redo`, `--check-integrations`, and `--generate-outcome-catalog` flags; runs on fresh install or when CLAUDE.md contains `[PLACEHOLDER]` markers.
- **What it produces:** A CSM company profile (CLAUDE.md / company-profile.md) that every other CSM-plugin skill reads as config.

### customize
- **Version:** 1.0.0
- **What it does:** Reconfigures the CSM plugin — CS motion, health model, churn signal definitions, escalation matrix, playbook, and tool integrations — without a full re-interview.
- **How it does it:** Supports `--full`, `--section <name>`, and `--reset`; alias for cold-start-interview when run fresh, otherwise partial section update.
- **What it produces:** Updated plugin config files (CLAUDE.md / company-profile.md) consumed by all other CSM skills.

### escalation-memo
- **Version:** 1.0.0
- **What it does:** Creates, updates, or closes a formal escalation record for technical, customer-complaint, executive, or internal-process escalations.
- **How it does it:** Supports `--open | --update | --close` with `--type technical|complaint|executive|internal` for the named account; manages the full escalation lifecycle including stakeholder communications and resolution tracking.
- **What it produces:** An escalation record plus stakeholder communications, distinct from risk-flag's one-shot escalation output.

### expansion-business-case
- **Version:** 1.0.0
- **What it does:** Generates a customer-facing expansion proposal (csm-led mode) or a CSQL Qualification Package for Sales handoff (csql mode).
- **How it does it:** Uses account health, usage signals, and expansion opportunity context; applies 5 constraints, 5 expert checks, and 5 anti-pattern guards before output; lifecycle stage stage-4-expansion.
- **What it produces:** Either a customer-facing expansion proposal (csm-led) or a CSQL Qualification Package consumed downstream by `rev-ops:csql-tracking`.

### expansion-onboarding
- **Version:** 2.0.0
- **What it does:** Five-phase expansion onboarding guided session for CSMs handling either CSQL or csm-led expansions.
- **How it does it:** Classifies expansion type, scaffolds the success plan, logs the OCV entry, sets M1–M5 milestones; requires `account_name`, `csm_name`, `expansion_product`; optional `mode`, `csql_id`, `expansion_committed_outcomes`, `expansion_amount`, `target_start_date`, `milestone_cadence`.
- **What it produces:** A scaffolded success plan, OCV log entry, M1–M5 milestones, and a kickoff agenda — all in a single 5-minute session.

### health-score-review
- **Version:** 1.0.0
- **What it does:** Structured health review for one account or a portfolio cohort — signal breakdown, trend analysis, risk classification, and recommended actions.
- **How it does it:** Takes an account name or `--portfolio`, with `--triage` or `--deep` modes; applies the configured health model components and thresholds.
- **What it produces:** A health review brief used for weekly account reviews, portfolio triage, or response to a fired health alert.

### qbr-builder
- **Version:** 1.0.0
- **What it does:** Builds or reviews a Quarterly Business Review — value delivered vs. success criteria, metrics summary, next-period priorities, and executive narrative.
- **How it does it:** Takes an account name with `--draft | --review | --exec-brief`; separates internal working document from customer-facing deliverable.
- **What it produces:** Two distinct artifacts — an internal working QBR document and a customer-facing QBR deliverable.

### renewal-readiness
- **Version:** 1.0.0
- **What it does:** Assesses renewal readiness across health, relationship, value delivery, stakeholder coverage, and commercial risk, then produces a renewal action plan.
- **How it does it:** Run 90–180 days before renewal on a named account with `--brief | --timeline | --customer-summary`; covers Green accounts too, not just at-risk.
- **What it produces:** An internal readiness brief, a renewal timeline, and optionally a customer-facing renewal prep summary.

### risk-flag
- **Version:** 1.0.0
- **What it does:** Produces a structured risk memo for an at-risk account — signals present, severity, escalation routing, and recommended intervention.
- **How it does it:** Takes an account name with `--brief | --escalation-memo`; applies the configured escalation matrix when a health alert fires or churn signal is detected.
- **What it produces:** A CSM brief and a separate escalation-ready summary for leadership communication.

### stakeholder-map
- **Version:** 1.0.0
- **What it does:** Builds or updates a stakeholder map — contacts, roles, influence, relationship health, engagement gaps, and sponsor risk.
- **How it does it:** Takes an account name with `--map | --gap-analysis | --sponsor-risk`; used before QBRs, after org changes, or when relationship coverage feels thin.
- **What it produces:** An internal analysis plus a clean contact reference document.

### success-plan-builder
- **Version:** 1.0.0
- **What it does:** Builds or updates a customer success plan — account-specific success criteria, milestones, engagement cadence, and mutual commitments.
- **How it does it:** Takes an account name with `--new | --reset | --review`; used at kickoff, when criteria drift, or for a plan reset.
- **What it produces:** A co-authored success plan document shared between CSM and customer (not an internal-only tracker).

### success-plan-canvas
- **Version:** 1.0.0
- **What it does:** Generates or refreshes a structured 7-component Customer Success Plan canvas for `initial`, `expansion`, or `renewal-refresh` plan types.
- **How it does it:** OCV-aware — reads Outcome & Value Catalog snapshot data; requires the OCV system to be in use (otherwise routes to `success-plan-builder`); supports `generate` and `refresh` operations.
- **What it produces:** A dated, standalone exportable Markdown canvas file (`context/success-plan-[safe_account]-[YYYY-MM-DD].md`) consumed downstream by `success-plan-progress-review`.

### success-plan-progress-review
- **Version:** 1.0.0
- **What it does:** Reviews an existing success plan canvas and produces a structured progress review artifact.
- **How it does it:** Reads the upstream canvas file, ingests CSM-provided milestone updates (`On Track / At Risk / Missed`), OCV outcome status, and success-criteria assessments; supports optional customer-facing summary and QBR pre-work note.
- **What it produces:** A dated progress review document with milestone scorecard, OCV outcome ratings, CSM action list, and optional customer-facing summary and QBR pre-work note.

### taro-play-runner
- **Version:** 1.0.0
- **What it does:** Selects and executes a TARO play (Trigger, Action, Resource, Outcome) from the configured playbook for a specific account situation.
- **How it does it:** Takes an account name with `--situation <description>` or `--play <play-name>`; identifies the right play and contextualizes it to the account.
- **What it produces:** Execution-ready outputs (email drafts, agenda, talking points, action plan) — not a generic playbook excerpt.

### value-statement
- **Version:** 1.0.0
- **What it does:** Builds a value statement for an account — what value has been realized, against which success criteria, with what evidence.
- **How it does it:** Takes an account name with `--internal | --customer | --exec-brief | --ae-handoff`; isolates the value story without the full QBR structure.
- **What it produces:** Two artifacts — an internal analysis (with health signals and expansion context) and a customer-facing value narrative (clean, evidence-based, no internal data).

---

## Onboarding Plugin (9 skills)

### blocker-review
- **Version:** 1.0.0
- **What it does:** Diagnoses and resolves onboarding blockers preventing a customer from reaching their next milestone on time.
- **How it does it:** Reads escalation thresholds and contacts from the onboarding profile; supports `--diagnose` (default guided diagnostic), `--escalate` (formatted escalation brief), or `--log` (record a resolved blocker).
- **What it produces:** A classified blocker action plan with named owners and deadlines, plus optional escalation brief or historical log entry.

### cold-start-interview
- **Version:** 1.0.0
- **What it does:** First-time setup interview for the onboarding company profile.
- **How it does it:** Collects role, onboarding model, segment config, TtV targets, milestone definitions, success criteria model, kickoff/handoff format, escalation matrix, integrations, and CS methodology; supports `--full | --quick | --redo | --check-integrations | --redo-company-profile | --section <name>`.
- **What it produces:** A populated onboarding CLAUDE.md that drives every other onboarding skill.

### customize
- **Version:** 1.0.0
- **What it does:** Views and updates the onboarding plugin configuration — milestone targets, TtV benchmarks, success criteria format, escalation contacts, graduation criteria, and methodology references.
- **How it does it:** Supports `--view` (default), `--update <section>`, `--reset <section>`, or `--validate` (checks for missing required fields, placeholders, and consistency).
- **What it produces:** Updated onboarding configuration files, with validation diagnostics on demand.

### handoff-doc
- **Version:** 1.0.0
- **What it does:** Generates the onboarding graduation handoff document for transferring account context to the post-onboarding team (CSM, AE, support, or partner).
- **How it does it:** Reads graduation criteria, escalation contacts, and handoff format from the onboarding profile; pulls account history from CRM and PM connectors; supports `--draft` (default full doc), `--readiness` (pre-check), or `--summary` (abbreviated brief).
- **What it produces:** A full handoff document, a graduation readiness check, or an abbreviated handoff brief — consumed by the receiving CSM / AE / support team.

### kickoff-prep
- **Version:** 1.0.0
- **What it does:** Prepares for an onboarding kickoff — produces a customer-facing agenda and an internal pre-kickoff checklist.
- **How it does it:** Reads kickoff format, required attendees, onboarding model, and milestone targets from the onboarding profile; pulls account context from CRM; supports `--prep` (both), `--agenda` (customer-facing only), or `--checklist` (internal only).
- **What it produces:** A shareable customer-facing agenda plus an internal preparation checklist covering pre-call logistics, attendee confirmation, and materials readiness.

### milestone-tracker
- **Version:** 1.0.0
- **What it does:** Tracks milestone progress across one account or the full onboarding book of business.
- **How it does it:** Reads milestone framework, at-risk signals, and escalation thresholds from the onboarding profile; pulls status from the PM connector (Asana / Linear / Jira / Monday) or CSM input; supports `--status` (single-account), `--portfolio` (book-wide summary), or `--flag` (at-risk and overdue surfacing).
- **What it produces:** A single-account milestone status view, a portfolio milestone health summary, or an at-risk/overdue list with recommended actions.

### onboarding-plan
- **Version:** 1.0.0
- **What it does:** Generates, updates, or summarizes the customer-facing onboarding plan document — the primary shared artifact between CSM and customer.
- **How it does it:** Reads onboarding model, milestone framework, duration targets, and plan format from the onboarding profile; pulls contract start date, segment, and stakeholders from CRM; supports `--draft` (full plan), `--update` (post-milestone or scope change), or `--summary` (abbreviated status).
- **What it produces:** A customer-facing onboarding plan (milestone timeline, ownership, first priorities, communication cadence, success-criteria placeholder), an updated revision, or an async status summary.

### success-criteria
- **Version:** 1.0.0
- **What it does:** Defines, refines, and tracks onboarding success criteria with the customer — typically 3–5 measurable outcomes for the onboarding period.
- **How it does it:** Reads criteria format, review cadence, and methodology from the onboarding profile; supports `--define` (default), `--refine` (after scope change), `--review` (progress assessment), or `--export` (clean customer-facing summary).
- **What it produces:** A structured success criteria document, with internal notes or a clean customer-facing export depending on mode.

### ttv-analysis
- **Version:** 1.0.0
- **What it does:** Time-to-value analysis for onboarding performance — single account or portfolio.
- **How it does it:** Reads TtV targets by segment and milestone framework from the onboarding profile; analyzes actual completion dates vs. targets; supports `--account` (single-account default), `--portfolio` (book-wide comparison), or `--patterns` (delay-pattern detection); TtV outputs always labeled internal planning targets.
- **What it produces:** A single-account TtV assessment, a comparative portfolio view, or a delay-pattern analysis with proactive intervention recommendations — never presented as customer commitments.

---

## CS-Ops Plugin (9 skills)

### capacity-planner
- **Version:** 1.0.0
- **What it does:** Assesses CSM capacity and coverage across the book — current load vs. target ratios, coverage gaps, headcount recommendations, and account redistribution.
- **How it does it:** Supports `--current | --headcount | --redistribution | --departure <csm-name>`; used for quarterly planning, headcount requests, CSM-departure coverage, or Red-tier concentration diagnosis.
- **What it produces:** A capacity assessment with specific redistribution or hiring recommendations.

### cold-start-interview
- **Version:** 1.0.0
- **What it does:** Runs the CS-Ops plugin configuration interview for portfolio analytics, capacity planning, and data quality.
- **How it does it:** Collects CS data model, metrics definitions, tooling stack, health model governance, reporting cadence, and team structure; supports `--full | --section <section-name>`; runs automatically on first use if config missing.
- **What it produces:** The cs-ops practice config file consumed by every other cs-ops skill; distinct from the CSM plugin's cold-start-interview.

### customize
- **Version:** 1.1.0
- **What it does:** Updates the CS-Ops plugin configuration — segment definitions, CSM ratios, health thresholds, playbook settings, escalation matrix, data quality rules, and reporting defaults.
- **How it does it:** Supports `--section <section-name> | --show | --reset <section-name>`; wraps the cold-start config into targeted section updates without a full re-interview.
- **What it produces:** Updated cs-ops configuration files.

### data-quality-check
- **Version:** 1.0.0
- **What it does:** Audits CRM and CS-platform data quality against configured field requirements — completeness, staleness, consistency, and orphaned records.
- **How it does it:** Supports `--full | --completeness | --staleness | --consistency | --field <field-name>`; used before reporting cycles, after CSM transitions, or when analytics outputs appear unreliable.
- **What it produces:** A field-level data quality scorecard plus a prioritized remediation backlog (audits data feeding the health model, not the model itself).

### health-model-review
- **Version:** 1.0.0
- **What it does:** Audits the portfolio health model — distribution, component weights, threshold calibration, signal freshness, and predictive accuracy.
- **How it does it:** Supports `--distribution | --calibration | --component-audit | --full`; used quarterly or when churn patterns diverge from health classifications.
- **What it produces:** An ops-level health model assessment with calibration recommendations (not per-account reviews — use `/csm:health-score-review` for those).

### metric-dashboard
- **Version:** 1.0.0
- **What it does:** Generates a CS metrics dashboard — portfolio health snapshot, retention metrics, CSM performance summary, renewal pipeline view, and leading indicator trends.
- **How it does it:** Supports `--weekly | --monthly | --quarterly | --board | --csm-performance`; calibrated to the configured primary performance indicator and reporting period.
- **What it produces:** A structured CS metrics view for weekly leadership reporting, monthly executive summaries, quarterly board presentations, or any stakeholder audience.

### playbook-auditor
- **Version:** 1.0.0
- **What it does:** Audits the CS playbook for coverage gaps, trigger specificity, outcome measurability, play adoption rates, and dead plays.
- **How it does it:** Supports `--full | --coverage | --adoption | --dead-plays | --play <play-name>`; used quarterly or when churn patterns suggest missing plays.
- **What it produces:** An ops-level playbook assessment with gap and improvement recommendations (distinct from `taro-play-runner`, which executes plays).

### process-doc
- **Version:** 1.1.0
- **What it does:** Creates or updates CS Ops process documentation — SOPs, governance records, handoff guides, decision logs, and data quality standards.
- **How it does it:** Supports `--csm-handoff | --playbook-governance | --data-quality | --escalation | --segment-change | --sop <process-name>`; produces durable process docs, not analysis outputs.
- **What it produces:** Publication-ready Markdown documentation calibrated to the specified process type.

### segment-analyzer
- **Version:** 1.0.0
- **What it does:** Analyzes the CS book by segment — ARR distribution, per-segment health distribution, CSM coverage ratios, motion-to-segment fit, and reclassification candidates.
- **How it does it:** Supports `--full | --segment <name> | --reclassification | --at-risk`; used for quarterly planning, headcount requests, or motion calibration.
- **What it produces:** A segment analysis report plus an optional reclassification queue.

---

## Renewals Plugin (12 skills)

### churn-analysis
- **Version:** 1.0.0
- **What it does:** Root cause analysis of a closed churn or contraction event — signal timeline, root cause categorization, lessons captured, and portfolio pattern flagging.
- **How it does it:** Takes account name with `--deep | --quick | --portfolio-scan`; used within 30 days of a confirmed non-renewal; surfaces whether the same signals are present in other active accounts.
- **What it produces:** A structured RCA report with portfolio pattern flags and pre-emptive action recommendations for similar at-risk accounts.

### churn-rca
- **Version:** 1.0.0
- **What it does:** Structured Root Cause Analysis for individual churn events (full cancellations) and cohort-level churn pattern analysis.
- **How it does it:** Supports three operations — `analyze` (single-account RCA), `cohort` (multi-account pattern with portfolio escalation at ≥25% cohort churn rate), and `export` (formatted retrieval); strictly scoped to full cancellation (contractions redirect to `downgrade-analysis`).
- **What it produces:** A structured RCA document with root cause taxonomy, contributing factors, timeline reconstruction, OCV delivery analysis, win-back assessment, and remediation pathway.

### cold-start-interview
- **Version:** 1.0.0
- **What it does:** Runs the Renewals plugin configuration interview.
- **How it does it:** Collects renewal motion, book of business details, pricing model, discount authority, churn signal definitions, escalation matrix, and integration status; supports `--full | --quick | --redo | --check-integrations | --redo-company-profile | --section <name>`.
- **What it produces:** The renewals practice config file consumed by every other renewals skill.

### contract-review
- **Version:** 1.0.0
- **What it does:** Extracts and flags renewal-relevant contract terms — auto-renewal mechanics, price protection, termination rights, MFN, payment terms, and data portability.
- **How it does it:** Takes account name with `--extract | --flag | --summary`; routes any non-standard or ambiguous clause to Legal before customer response is drafted.
- **What it produces:** An internal contract risk register (not a legal opinion), used before `negotiation-prep`, `price-increase-prep`, or any renewal where terms constrain commercial motion.

### customize
- **Version:** 1.0.0
- **What it does:** Updates the renewals company profile — edits any section of CLAUDE.md / company-profile.md without rerunning the full cold-start interview.
- **How it does it:** Supports `--section <section-name>` with `--show | --edit | --validate`; displays current values before editing, confirms changes before writing.
- **What it produces:** Updated renewals configuration files; cold-start-interview is used instead for first-time setup or a complete rebuild.

### downgrade-analysis
- **Version:** 1.0.0
- **What it does:** Analyzes customer contract downgrade requests (contraction only — full cancellation redirects to `churn-rca`).
- **How it does it:** Two operations — `analyze` (creates a new DGA record with driver category, current contract, OCV snapshot) and `update`; auto-detects full-churn signals ("cancel everything," "terminate") and redirects.
- **What it produces:** A value chain failure map, counter-proposal inputs, and recommended response strategy for CSM/AM negotiation.

### executive-summary
- **Version:** 1.0.0
- **What it does:** Generates an executive-ready renewal summary written for CRO, CEO, or board consumption.
- **How it does it:** Takes account name with `--brief | --full | --board`; suppresses internal positioning, walk-away figures, and operational detail; all ARR figures flagged for Finance/RevOps review before distribution.
- **What it produces:** An executive-format summary covering commercial status, relationship health, risk tier, save strategy, and recommended executive action.

### expansion-signal
- **Version:** 1.0.0
- **What it does:** Identifies and qualifies expansion signals in a renewal account — seat growth, usage expansion, product upsell, adjacent-team opportunities.
- **How it does it:** Takes account name with `--deep | --quick | --catalog`; maps each signal to a qualification tier (early signal / pipeline-ready / qualified) and recommends a TARO play for conversion.
- **What it produces:** A qualified expansion-signal report; signals never enter GRR calculations and require qualifying economic-buyer conversation before entering NRR pipeline.

### negotiation-prep
- **Version:** 1.0.0
- **What it does:** Builds a renewal negotiation brief — pricing anchor, walk-away position, discount authority check, objection handling, and competitive counter-positioning.
- **How it does it:** Takes account name with `--brief | --full | --export`; pulls commercial context from CRM and call recordings; validates discount authority against configured ceiling; suppresses internal positioning from customer-facing exports.
- **What it produces:** An internal negotiation brief and (with `--export`) a clean customer-facing renewal proposal; offers requiring escalation are flagged before reaching the customer.

### price-increase-prep
- **Version:** 1.0.0
- **What it does:** Plans and executes a price increase for a single account or a cohort — rationale, customer communication, approval routing, and objection handling.
- **How it does it:** Takes account name or `--cohort` with `--plan | --draft | --objections`; validates against configured policy and escalation thresholds; never drafts customer comms without a completed value narrative and confirmed approval routing.
- **What it produces:** A price-increase plan, customer-facing draft, and prepared objection responses, all gated on approval routing.

### renewal-forecast
- **Version:** 1.0.0
- **What it does:** Builds a weighted renewal forecast with scenario modeling (best / likely / worst), pipeline by stage, 90/60/30-day cohort analysis, and GRR/NRR projections.
- **How it does it:** Supports `--full | --cohort 90|60|30 | --segment <name> | --account <name>`; pulls live CRM data when connector available, falls back to manual input; every output flagged for Finance/RevOps review.
- **What it produces:** A weighted renewal forecast with scenario ranges and GRR/NRR projections against configured targets.

### risk-assessment
- **Version:** 1.0.0
- **What it does:** Structured churn risk assessment for a single renewal account — aggregates signals across product usage, engagement, support history, sentiment, and relationship health into a risk tier.
- **How it does it:** Takes account name with `--deep | --quick | --triage`; pulls live data from CRM and CS Platform when connectors available; assigns Low / Medium / High / Critical risk tier.
- **What it produces:** A risk-tier assessment with escalation routing recommendation and TARO play suggestion; used at 90/60/30-day windows, on churn-signal detection, or for pre-pipeline-review triage.

---

## Rev-Ops Plugin (34 skills)

### annual-planning-workflow
- **Version:** 1.0.0
- **What it does:** Orchestrates the six-phase annual (or mid-year) planning cycle: forecast scenarios → UoG capacity → quota sensitivity → comp simulation → baseline save → change communication.
- **How it does it:** Each phase gates on human approval before the next; invokes `unit-of-growth-calculator` for capacity modeling; triggers include "annual planning," "planning cycle," "run the plan," "quota comp planning," "mid-year replan."
- **What it produces:** A phase status tracker plus phase-specific artifacts: three-scenario capacity table, quota sensitivity table, comp simulation, baseline save confirmation, change communication package — each phase artifact labeled `[DRAFT]` until its gate is cleared.

### change-communication-packaging
- **Version:** 1.0.0
- **What it does:** Produces a three-part communication package for sensitive planning changes (territory, quota, or comp).
- **How it does it:** Triggered automatically by `annual-planning-workflow` for sensitive changes; requires RevOps lead review before any distribution; triggers include "communicate changes," "territory announcement," "quota rationale," "comp change communication."
- **What it produces:** A data-backed rationale memo for reps, an FAQ with top-5 anticipated objections and data responses, and a rollout sequence with audience, channel, and order.

### closed-won-to-cs-capacity-modeling
- **Version:** 1.0.0
- **What it does:** Converts rolling sales forecast into CS resource demand using UoG formulas; flags CS capacity gaps with a hiring lead-time signal.
- **How it does it:** Runs three-scenario capacity check (P10/P50/P90 from `scenario-modeling`); compares projected CSM demand to current headcount and UoG annual plan baseline; updates when SA1 forecast moves >10%.
- **What it produces:** A CS capacity demand projection with hiring lead-time flag, surfaced to CS leadership when capacity gaps appear.

### cold-start-interview
- **Version:** 1.0.0
- **What it does:** Rev-ops plugin setup interview.
- **How it does it:** Reads existing company-profile.md if present, then collects only rev-ops-specific configuration: planning parameters, headcount, discount thresholds, lead definitions, OCV catalog path, and connector status.
- **What it produces:** The rev-ops practice config file at `~/.claude/plugins/config/claude-for-customer-success/rev-ops/CLAUDE.md`.

### comp-simulation
- **Version:** 1.0.0
- **What it does:** Models comp plan payout scenarios before plan finalization — stress-tests OTE, accelerators, and threshold structures against attainment distributions.
- **How it does it:** Models against attainment levels 50/65/75/85/100%; G3 applies — all outputs require HR + Finance dual review before rep distribution; triggers include "comp simulation," "stress test comp plan," "what does the plan cost at 80/100/120%."
- **What it produces:** A comp simulation showing cost-to-company and unintended payout behaviors at each attainment level, gated on HR + Finance dual review.

### crm-hygiene-audit
- **Version:** 1.0.0
- **What it does:** Produces an overall CRM health score plus a rep-level hygiene scorecard across completeness, accuracy, and recency.
- **How it does it:** Weekly cadence; requires RevOps review before sharing with Sales managers; never writes to CRM — all corrections are proposals.
- **What it produces:** A weekly CRM hygiene report (overall score + per-rep scorecard) with proposed corrections.

### cross-system-reconciliation
- **Version:** 1.0.0
- **What it does:** Traces conflicting numbers from different sources (CRM vs. Finance Sheets vs. CS platform) to their root cause.
- **How it does it:** Applies a fixed data authority hierarchy (HubSpot > Finance Sheets > CS platform > Slack/Linear); never silently resolves conflicts; triggers include "which number is right," "reconcile," "conflicting numbers."
- **What it produces:** A reconciliation memo with source attribution and recommended resolution.

### csql-tracking
- **Version:** 1.0.0
- **What it does:** Manages the lifecycle of Customer Success Qualified Leads (CSQLs) — create, update, close, query operations.
- **How it does it:** Persistent filesystem storage (`context/csql-*.md`), status state machine enforcement, inter-skill contract consumption from `csm:expansion-business-case [mode=csql]`; G9 requires explicit user confirmation on every filesystem write.
- **What it produces:** A persistent, queryable portfolio of CSQL records (active and closed) for RevOps; the downstream consumer in the expansion-business-case → csql-tracking two-skill contract.

### data-decay-tracking
- **Version:** 1.0.0
- **What it does:** Monitors contact and account data freshness and flags records where enrichment is overdue.
- **How it does it:** Decay signals: contact title unchanged >18 months, account employee count stale >12 months, primary contact no email activity >6 months, account domain change detected; prioritizes enterprise accounts.
- **What it produces:** A prioritized data-decay flag list with enrichment recommendations.

### deal-classification
- **Version:** 1.0.0
- **What it does:** Independently scores each open opportunity as Commit / Best Case / Pipeline using CRM activity data — without relying on rep self-reporting.
- **How it does it:** Surfaces delta vs. rep's stated forecast category when classification disagrees by more than one tier; triggers include "deal classification," "classify the pipeline," "commit vs best case."
- **What it produces:** An independent forecast classification per opportunity, with disagreement deltas surfaced for forecast review.

### deal-desk-workflow-management
- **Version:** 1.0.0
- **What it does:** Manages the complete deal desk approval routing workflow: submit → review → route → decide → log.
- **How it does it:** Assembles context brief (discount rationale, competitive context, Tier 1 churn risk signal) before routing to approver; enforces SLA (24h standard, 4h final 2 weeks of quarter); SLA breach escalates to #revops-ops.
- **What it produces:** A routed deal desk record with context brief, approval decision, and SLA tracking.

### deal-health-scoring
- **Version:** 1.0.0
- **What it does:** Scores each open opportunity on five dimensions — activity recency, stakeholder coverage, stage-age ratio, competitive signal, and rep forecast accuracy history.
- **How it does it:** Composite 0–100 score per deal; deals below 50 flagged for `next-best-action-recommendation`.
- **What it produces:** A per-deal composite health score with flagged at-risk deals routed to next-best-action.

### deal-to-outcome-tracing
- **Version:** 1.0.0
- **What it does:** Links every closed/won opportunity to its downstream CS trajectory using the OCV Outcome Catalog.
- **How it does it:** At deal close — checks catalog completeness (OCV entry, trigger match, measurement source); at 30/60/90/180-day checkpoints — assesses L0–L3 rubric level per OCV entry; when OCV catalog absent, falls back to structural risk signals with `[Confidence: Low]`.
- **What it produces:** A traced outcome trajectory per account, surfacing rubric-level progression or structural risk signals.

### discount-threshold-monitoring
- **Version:** 1.0.0
- **What it does:** Flags deals where discount exceeds the approved threshold for that segment and tracks discount frequency by rep and segment.
- **How it does it:** Routes approval to the correct authority based on discount depth using thresholds from company profile; never approves deals autonomously.
- **What it produces:** Discount-flag records with routed approval requests plus rep/segment frequency tracking.

### duplicate-detection
- **Version:** 1.0.0
- **What it does:** Identifies duplicate accounts, contacts, and opportunities in HubSpot.
- **How it does it:** Produces merge candidates with confidence scores (High/Medium/Low); never merges autonomously — all merges require Write-tier human confirmation.
- **What it produces:** A merge-candidate list with confidence scores for human-confirmed cleanup.

### early-churn-downgrade-signal-detection
- **Version:** 1.0.0
- **What it does:** Three-tier churn signal model starting at deal close (not renewal).
- **How it does it:** Tier 1 fires at close (rule-mode thresholds or cohort-mode correlations with 6+ months of data); Tier 2 fires 30–90 days post-onboarding on behavioral signals; Tier 3 fires 90–120 days pre-renewal on late-stage risk; every flag includes escalation path and owner.
- **What it produces:** Tiered churn/downgrade flags per account with escalation path and assigned owner.

### field-completion-monitoring
- **Version:** 1.0.0
- **What it does:** Tracks required field completion rates by stage gate across both Sales new-logo pipeline and CS expansion/renewal pipeline.
- **How it does it:** Flags missing data that will break downstream forecasting before quarter close; escalates deals in Negotiation+ or Commercial Terms+ with missing fields 2 weeks before quarter close; CS gates include OCV entry reference, commercial terms, churn risk resolution.
- **What it produces:** A field-completion compliance report with pre-quarter-close escalation flags.

### forecast-variance-analysis
- **Version:** 1.0.0
- **What it does:** Compares submitted forecast to actual closed/won outcomes and classifies variance by root cause.
- **How it does it:** Classifies variance by rep-level, deal-size band, stage-entry, seasonal, or product/segment; requires minimum 3 deals or 2 consecutive quarters before surfacing a pattern; surfaces systemic patterns, not one-off explanations.
- **What it produces:** A variance analysis identifying systemic root-cause patterns in forecast accuracy.

### growth-model-vs-actuals-tracking
- **Version:** 1.0.0
- **What it does:** Monitors unit economics of growth against the UoG annual plan baseline on three vectors: new logo/Sales-owned (CAC, TtFV, ARR-at-12-months), expansion NRR/CS-owned, retention GRR/CS-owned.
- **How it does it:** Fires a variance memo when any vector diverges >15% (NRR >5pp, GRR >3pp); routes to `mid-year-replan-triggering` when threshold crossed; vectors 2–3 routed to CS leadership as CS accountability signals.
- **What it produces:** A variance memo per vector when thresholds breach, plus replan routing.

### gtm-unified-metrics-pulse
- **Version:** 1.0.0
- **What it does:** Weekly cross-functional metrics report covering revenue system pipeline, handoff quality, CS capacity status, early churn flags, and outcome realization summary.
- **How it does it:** Five sections with audience-targeted routing — account-level early churn flags go to #cs-leadership only; outcome realization aggregate to all, account-level to #cs-leadership; all Slack posts require Write-tier confirmation.
- **What it produces:** A weekly GTM-wide pulse report distributed via Slack to audience-specific channels.

### mid-year-replan-triggering
- **Version:** 1.0.0
- **What it does:** Monitors plan-vs-actual drift against the UoG baseline and fires a replan recommendation when thresholds breach.
- **How it does it:** Triggers a replan when actuals diverge >15% from P50 plan, CS headroom crosses 10%, AE attainment runs >20pp below plan for 2+ months, or a material territory event occurs.
- **What it produces:** A replan recommendation memo with supporting data, routed into `annual-planning-workflow` mid-year mode.

### next-best-action-recommendation
- **Version:** 1.0.0
- **What it does:** Produces specific, rationale-backed interventions for deals flagged at-risk by `deal-health-scoring` or `pipeline-velocity-tracking`.
- **How it does it:** Intervention types — executive sponsor escalation, competitive response, procurement/legal acceleration, CS pre-close engagement, closed/lost reclassification; every recommendation names what to do, who does it, and why.
- **What it produces:** Per-deal intervention recommendations with named owner and rationale.

### non-standard-terms-detection
- **Version:** 1.0.0
- **What it does:** Scans deal notes and opportunity fields for payment terms, contract structures, and custom provisions outside the standard playbook.
- **How it does it:** Detection patterns — payment terms net >30 days, multi-year ramps/price locks, SLA commitments, data residency requirements, indemnification carve-outs, source-code escrow; routes to Legal or Finance when required.
- **What it produces:** A flag list of non-standard terms per deal with required Legal/Finance routing.

### outcome-statement-builder
- **Version:** 1.0.0
- **What it does:** Transforms raw product or service capabilities into structured, verifiable outcome statements using the Seven-Stage Value Chain, then assigns business metrics and a multi-level achievement rubric.
- **How it does it:** Two sequential phases with an explicit approval gate — Phase 1 builds outcome statements (role-specific, trigger-conditioned, measurable), Phase 2 assigns metrics and L0–L3 rubric; G8 applies (all draft outcomes excluded from Sales/CS use until ratification).
- **What it produces:** A local Outcome & Value Registry in machine-readable Markdown plus a presentation-ready HTML review deck for cross-functional ratification with Sales, Marketing, RevOps, and CS.

### outcome-to-value-tracking
- **Version:** 1.0.0
- **What it does:** Maps each customer to their L0–L3 rubric level on their referenced OCV entries at OCV-defined verification milestones.
- **How it does it:** Reads OCV catalog and customer references; provides portfolio view of % at each rubric level per OCV entry; surfaces systemic L0 persistence (>40% of accounts stuck at L0 past 90-day milestone) as a delivery pattern signal.
- **What it produces:** An OCV rubric-level portfolio report with systemic delivery pattern flags.

### pipeline-coverage-analysis
- **Version:** 1.0.0
- **What it does:** Calculates pipeline coverage ratio by segment, rep, and quarter; flags when coverage falls below the win-rate-derived threshold.
- **How it does it:** Required coverage = 1 ÷ win rate (not a universal 3x); produces exposure ranking and week-over-week trend.
- **What it produces:** A pipeline coverage report with exposure ranking and trend, used before forecast calls, board reviews, or quarter-close.

### pipeline-velocity-tracking
- **Version:** 1.0.0
- **What it does:** Tracks average sales cycle time by segment, rep, and deal size and flags deals aging past 1.5x historical average for their current stage.
- **How it does it:** Surfaces velocity trend vs. prior quarter; triggers include "pipeline velocity," "cycle time," "deals aging," "stalling pipeline."
- **What it produces:** A velocity tracking report with aging-deal flags routed to `next-best-action-recommendation`.

### quota-sensitivity-analysis
- **Version:** 1.0.0
- **What it does:** Builds quota models from UoG-confirmed AE headcount and revenue target; models structural achievability at five attainment levels.
- **How it does it:** Models at 50/65/75/85/100% attainment; flags when hitting plan requires attainment above the 75th percentile of historical actuals; used inside `annual-planning-workflow` Phase 4 or standalone.
- **What it produces:** A quota sensitivity table showing structural achievability across attainment scenarios.

### revenue-brief-generation
- **Version:** 1.0.0
- **What it does:** Produces a weekly or monthly executive revenue narrative by coordinating outputs from all rev-ops agents.
- **How it does it:** Five sections — forecast + pipeline, pipeline health, CS capacity status, NRR trajectory, top 3 risks with recommended owner and action; one decision required per brief; delivered as `[DRAFT]` for RevOps lead review.
- **What it produces:** An executive revenue brief gated on RevOps-lead review before distribution.

### revenue-leakage-scanning
- **Version:** 1.0.0
- **What it does:** Identifies deal structures leaving money on the table before close.
- **How it does it:** Fires at Negotiation stage — before close locks structure; detects underpriced professional services, missing expansion clauses in multi-year deals, renewal terms misaligned with ARR classification, missing success milestone gates for expansion.
- **What it produces:** A pre-close revenue leakage flag list with specific structural fixes.

### sales-cs-handoff-quality-scoring
- **Version:** 1.0.0
- **What it does:** Scores each closed/won deal on five handoff dimensions (0–100) — OCV entry referenced, trigger match, measurement source accessible, stakeholder map, risk flags documented.
- **How it does it:** Pass threshold 80; below-threshold deals trigger a Linear issue assigned to the AE manager with 48-hour SLA; CS onboarding proceeds but CSM is notified of the open issue.
- **What it produces:** A per-deal handoff score with Linear-ticketed remediation when below threshold.

### scenario-modeling
- **Version:** 1.0.0
- **What it does:** Builds P10/P50/P90 range forecasts from current pipeline state.
- **How it does it:** Models win rate sensitivity, slip scenarios, and enterprise concentration risk; structured output consumed by `annual-planning-workflow` for three-scenario UoG capacity modeling.
- **What it produces:** P10/P50/P90 scenario output consumed by annual-planning-workflow and `closed-won-to-cs-capacity-modeling`.

### stage-integrity-audit
- **Version:** 1.0.0
- **What it does:** Detects CRM hygiene issues that distort forecasts — stage-skipping, backward movement, and stale stage.
- **How it does it:** Stale stage = stuck >2x historical avg; produces audit report for human review before any CRM edits; never updates CRM autonomously.
- **What it produces:** A stage integrity audit report with proposed corrections for human review.

### unit-of-growth-calculator
- **Version:** 1.0.0
- **What it does:** Computes GTM headcount requirements, capacity imbalances, and efficiency diagnostics for B2B SaaS organizations using the AE-anchored or CSM-anchored pod model.
- **How it does it:** Reads company profile config (primary_segment, CRM, benchmark overrides); supports AE-anchored (revenue target → headcount), CS-anchored reverse (CSM headcount → ARR ceiling), scenario comparison, and imbalance-only diagnostic; all defaults traced to `references/benchmark-library.md`; G5 applies (outputs are analytical inputs, not approved plans).
- **What it produces:** An under/over-capacity signal set across AE, SDR, CSM, and Support functions with specific remediation guidance, consumed by `annual-planning-workflow` and `closed-won-to-cs-capacity-modeling`.

---

## Notes on frontmatter consistency

- **All 81 files have `name` and `version` fields.** No missing-frontmatter cases.
- **`version` values:** 79 skills at `1.0.0`; 2 skills above 1.0.0 — `csm:expansion-onboarding` (2.0.0) and `cs-ops:customize` and `cs-ops:process-doc` (both 1.1.0). (Recount: 78 at 1.0.0, 1 at 2.0.0, 2 at 1.1.0.)
- **`name` value note:** `rev-ops/skills/csql-tracking/SKILL.md` declares `name: rev-ops:csql-tracking` (with the plugin prefix in the name) — all other 80 skills declare bare names without the plugin prefix.
- **Frontmatter shape variation:** Most skills use `description: >` (block scalar). `csm:success-plan-canvas`, `csm:success-plan-progress-review`, `renewals:churn-rca`, `renewals:downgrade-analysis`, and `rev-ops:csql-tracking` use inline `description: "..."` quoted strings. `csm:expansion-business-case` and `csm:expansion-onboarding` carry additional fields (`enhancement_level`, `task_id`, `duration_minutes`, `lifecycle_stage`). `rev-ops:csql-tracking` carries eval-result fields (`eval_pass_rate`, `eval_delta`, `eval_workspace`). All rev-ops skills carry `status:` (`PROPOSED` or `VALIDATED`).
- **`config_skill: true` marker:** Set on all 5 cold-start-interview skills and all 4 customize skills (plus `cs-ops:customize`) — confirming the config-skill class is uniformly tagged.
