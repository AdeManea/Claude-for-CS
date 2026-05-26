# csm.expansion-onboarding

Runs a 5-phase expansion kickoff workflow for expansion engagements — takes either a confirmed CSQL close or a CSM-initiated expansion and produces a complete expansion launch package: success plan scaffold, OCV entry log, milestone set, and kickoff agenda. All three filesystem writes require explicit CSM confirmation before execution.

Two modes:
- **`csql` mode** — CSQL-driven expansion; requires a confirmed close and CSQL ID.
- **`csm-led` mode** — CSM-initiated expansion without a CSQL; no CSQL ID required.

**Version:** 2.0.0 · **Status:** [VALIDATED]

---

## Use it for

- Full expansion kickoff workflow from CSQL close or CSM-initiated expansion to CSM execution readiness
- Success plan scaffold for the expansion cohort
- OCV (Outcome & Value) entry log for the expansion opportunity
- 90-day milestone set for the expansion engagement
- Kickoff agenda for the expansion launch meeting

## Don't use it for

- CSQL tracking before close (use `rev-ops.csql-tracking`)
- Initial onboarding for new accounts (use onboarding domain skills)
- Single-document outputs without the full kickoff workflow
- Expansion business case development before close (use `csm.expansion-business-case`)

## How to trigger it

Say something like:

- "run expansion onboarding for [account]"
- "expansion kickoff for [account]"
- "CSQL won, start expansion onboarding"
- "expansion onboarding workflow"
- "kick off expansion for [account]"
- "csm-led expansion for [account]"

## What you get

A 5-phase workflow producing:

1. **CLASSIFY & VALIDATE** — confirms CSQL close (csql mode) or CSM-initiated expansion (csm-led mode); establishes account context, loads carry-in files
2. **SUCCESS PLAN SCAFFOLD** — writes `context/expansion-success-plan-{safe_account}-{YYYY-MM-DD}.md` (requires G9 confirmation; `safe_account` = filesystem slug of account name; `YYYY-MM-DD` = run date)
3. **OCV ENTRY LOG** — appends to `context/ocv-{safe_account}.md` (requires G9 confirmation)
4. **MILESTONE SET** — writes `context/expansion-milestones-{safe_account}-{YYYY-MM-DD}.md` (requires G9 confirmation)
5. **KICKOFF AGENDA** — generates kickoff agenda (display only, no filesystem write)

## Prerequisites

- csm CLAUDE.md loaded
- CSM name
- **csql mode**: Confirmed CSQL close (CSQL ID, account name, expansion product, expansion amount, committed outcomes)
- **csm-led mode**: Account name, expansion product, expansion amount, committed outcomes (no CSQL ID required)
- Carry-in context files (optional, loaded from `context/` if present):
  - `context/health-{safe_account}.md`
  - `context/success-plan-{safe_account}.md`
  - `context/milestones-{safe_account}.md`

## Governance

- **csql mode**: Requires confirmed CSQL close before initiating
- **csm-led mode**: No CSQL required; CSM initiates directly
- All three filesystem write operations (phases 2–4) require explicit CSM confirmation (G9 gate) before execution
- No writes outside the `context/` directory
- Input injection defense: 7 input-derived document variables sanitized before output; 3 carry-in files scanned before use (discard-and-continue semantics on detection)

## Security notes

- `display_account` → document output only
- `safe_account` → filesystem paths only — never cross-used
- `xml_structural_escape()` applied to 7 input-derived document variables before inclusion in any output document
- `safe_account` uses `re.sub()` path-slug sanitization for filesystem paths only (not `xml_structural_escape()`)
- Carry-in file injection detection uses discard-and-continue (not halt) to prevent DoS on pre-existing files

## See also

- `rev-ops.csql-tracking`
- `csm.expansion-business-case`

---

## Changelog

### v2.0.0 — 2026-05-19

**Full rebuild via DVT-2 (TDD+ methodology, sessions 23–24)**

- Complete rewrite from v1.0.0 (legacy 3-operation tracker, 894 lines) to v2.0.0 (5-phase expansion kickoff workflow)
- New 5-phase structure: CLASSIFY & VALIDATE → SUCCESS PLAN SCAFFOLD → OCV ENTRY LOG → MILESTONE SET → KICKOFF AGENDA
- Added G9 human-in-the-loop confirmation gate before each of 3 filesystem write operations
- Added 2-layer injection defense: `xml_structural_escape()` (Layer 1) + `scan_for_injection()` per-item loop (Layer 2)
- Added `display/safe` variable separation: `display_*` for document output, `safe_*` for filesystem paths
- Added carry-in context file loading with injection scan (discard-and-continue semantics)
- Added OCV append safety: line-anchored `## Expansion OCV Entry` heading validation before any append
- Added newline normalization on multi-line carry-in content before injection scan
- Security contract moved to body sections (`## Security & Permissions` + `## Trust & Verification`) per plugin deployment target requirements
- Line count: ~650 lines (NOTE threshold only under v5.3 >500=NOTE, >750=WARN, >1000=BLOCK)
- Gate status: CSE pre-ship gate PASSED (0 BLOCKs, 0 WARNs post-remediation); adversarial review PASSED

### v1.0.0 — (prior)

- Initial implementation: 3-operation legacy tracker
- 894 lines; no phase structure; no injection defense; no human confirmation gates

---

*Domain: `csm` · Skill ID: `csm.expansion-onboarding` · Version: 2.0.0*
