# Changelog

All notable changes to `claude-for-customer-success` are recorded here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).
Versions follow [Semantic Versioning](https://semver.org/).

---

## [1.2.2] — 2026-06-27

### Fixed
- `renewals` **v1.0.2**, `cs-ops` **v1.0.2**, `onboarding` **v1.0.2**, `rev-ops` **v1.1.2** — added `commands/` directory with one `.md` stub per skill so all slash commands (e.g. `/renewals:renewal-forecast`, `/rev-ops:pipeline-coverage-analysis`) are recognized by Claude Code. Same fix applied to `csm` in v1.2.1; now extended to all remaining plugins.

---

## [1.2.1] — 2026-06-27

### Fixed
- `csm` **v1.0.8** — added `commands/` directory with one `.md` entry per skill so all `/csm:<skill>` slash commands are recognized by Claude Code. Previously the plugin exposed skills only via `skills/*/SKILL.md` (readable by the Skill tool), but not via `commands/` (required for slash command registration). Affected all 17 skills including `/csm:cold-start-interview`.

---

## [1.2.0] — 2026-05-29

### Added
- `auq-resilience` **v1.1.0** — `/auq` command (`commands/auq.md`, invoked as
  `/auq-resilience:auq` with `force-prose` / `status` / `reset`). This is the
  Cowork-native control surface for the AUQ protocol and auto-registers on
  install; Claude Code hooks do not run in Cowork.
- `auq-resilience/CHANGELOG.md` — previously linked from the root README but
  missing.
- Root README "Enabling in Cowork" subsection under AUQ Resilience, documenting
  the hooks-vs-command/CLAUDE.md split and the no-wiring Cowork path.

### Changed
- `auq-resilience` `plugin.json` and marketplace entry: version 1.0.x → 1.1.0,
  descriptions updated to cover both the Claude Code hook path and the Cowork
  command path. (Marketplace previously listed 1.0.0 while `plugin.json` was
  1.0.1 — drift corrected.)
- Root README and QUICKSTART: AUQ/dist references updated from the nonexistent
  `auq-resilience-v1.0.0.plugin` to `v1.1.0`; "hooks only" corrected to note the
  `/auq` command; `ask_user_input_v0` references aligned to `AskUserQuestion`.
- `dist/auq-resilience-v1.1.0.plugin` rebuilt.

---

## [1.1.0] — 2026-05-18

### Added
- 80 per-skill README files co-located with each SKILL.md (`README.md` in each skill directory)
- Root `README.md` sections: License, Disclaimer, Contributing, Bugs and Issues, Support, Security, Changelog
- `CHANGELOG.md` (this file)

### Changed
- Expanded Disclaimer to cover all artifact types: skills, plugins, managed agent cookbooks, commands, Claude Code hooks, MCP configurations, connectors, and methodology frameworks

### Deprecated
- `rev-ops/skills/territory-optimization/` — marked deprecated with migration note; not removed (see skill README for details)

---

## [1.0.0] — 2026-04-01

### Added
- Initial release: six plugins across 81 skills covering the full Customer Success lifecycle
  - `csm/` — Core CSM workflows: health scoring, QBR prep, executive engagement, expansion, TARO plays
  - `renewals/` — Renewal pipeline: risk briefing, renewal call prep, negotiation prep, post-renewal retrospective
  - `cs-ops/` — CS operations: tech stack audit, tooling ROI, metrics framework, capacity planning
  - `rev-ops/` — Revenue operations: territory optimization, capacity planning, pipeline intelligence, compensation modeling
  - `onboarding/` — Customer onboarding: kickoff facilitation, implementation tracking, adoption acceleration
  - `auq-resilience/` — Claude Code hooks for AUQ fallback resilience (PreToolUse + PostToolUse)
- Managed agent cookbooks in `managed-agent-cookbooks/`
- Machine-readable capability registry: `docs/claude-for-cs-agent-capability-model.yaml`
- Extended documentation: capability model, lifecycle guide, integrated journey value realization guide
