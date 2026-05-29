# auq-resilience

**AskUserQuestion (AUQ) Resilience System — hook-based fallback for `AskUserQuestion`**

The `ask_user_input_v0` tool (AUQ) renders an interactive multiple-choice widget in Claude Code. When the widget renders correctly, the user picks an option and Claude proceeds. When it fails — due to an unsupported client, a missed render, or a tool error — Claude receives an empty or null response and has no recoverable path.

This plugin installs three Claude Code hooks that fix that. They run on every `AskUserQuestion` call, enforce the single-question protocol, detect failures, and inject a plain-text fallback so Claude can always proceed.

---

## The T1 / T2 / T3 Protocol

| Tier | Mechanism | When |
|------|-----------|------|
| **T1** | `AskUserQuestion` widget renders normally | Default path |
| **T2** | Prose multiple-choice block injected by PostToolUse hook | Widget fails to render or returns empty/null |
| **T3** | Proceed on last-option default (embedded in T2 prose) | User doesn't respond to T2 within the session |

T2 output looks like this:

```
**Deploy where?**

**A)** Staging
**B)** Production
**C)** Cancel ← proceeding with this if no response

*(Type A, B, C, or D — or describe your preference)*
```

T3 is not a separate hook trigger — it's a behavioral instruction embedded in every T2 block. If the user doesn't respond, Claude reads the `← proceeding with this if no response` marker and uses that option.

---

## What the Hooks Do

**PreToolUse** (fires before every `AskUserQuestion` call):
- Trims batched questions to one — only the first question passes through; extras are queued to a pending file and surfaced after the current question is answered
- Saves question text, options, and default to a temp state file for PostToolUse to read
- Passes the (possibly trimmed) call through with `permissionDecision: allow`

**PostToolUse** (fires after every `AskUserQuestion` response):
- Reads the saved state and the tool result
- If the response is valid — a non-empty string that isn't a null sentinel — passes through silently
- If the response is empty, null, a JSON echo, or a tool error — returns `decision: block` with the T2 prose block as the reason, preventing Claude from acting on a bad response

Both hooks are stateless across invocations except for the temp state file (`/tmp/auq_hook_state.json`) — each hook event is a new OS process, so the file is the bridge.

---

## Package Contents

```
auq-resilience/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   └── auq.md             ← /auq command (force-prose | status | reset) — works in Cowork
├── hooks/
│   ├── hooks.json         ← self-contained Pre/PostToolUse + UserPromptSubmit registration (Claude Code)
│   ├── auq_hook.py        ← PreToolUse + PostToolUse + Notification handlers
│   └── auq_parser.py      ← Response parser (letter / keyword / semantic / default)
├── design/
│   └── AUQ_PROTOCOL_SPEC.md   ← full behavioral spec for the T1/T2/T3 protocol
├── CHANGELOG.md
└── README.md              ← this file
```

---

## Install

### 1. Install the plugin

Install `auq-resilience` from the `claude-for-customer-success` plugin marketplace, or copy the `auq-resilience/` directory alongside your Claude for Customer Success plugins.

### 2. Wire into each Claude for Customer Success plugin you want protected

For each plugin (`csm`, `cs-ops`, `renewals`, `onboarding`, `handoff`, `rev-ops`), open its `hooks/hooks.json` and add:

```json
{
  "hooks": [
    {
      "event": "PreToolUse",
      "matcher": "ask_user_input_v0",
      "command": "python3 ../auq-resilience/hooks/auq_hook.py"
    },
    {
      "event": "PostToolUse",
      "matcher": "ask_user_input_v0",
      "command": "python3 ../auq-resilience/hooks/auq_hook.py"
    },
    {
      "event": "UserPromptSubmit",
      "matcher": ".*",
      "command": "python3 ../auq-resilience/hooks/auq_hook.py"
    }
  ]
}
```

**Path assumption:** `../auq-resilience/hooks/auq_hook.py` assumes `auq-resilience/` is installed as a sibling directory to the plugin being wired. If your layout differs, adjust the relative path accordingly.

**Python command:** Use `python3` (or the full path to your Python 3.9+ interpreter) rather than `python`. The hook has no external dependencies — standard library only.

### 3. Verify

Start a Claude Code session with one of the wired plugins active and run:

```
/setup
```

Then trigger a skill that calls `AskUserQuestion`. You should see the widget render normally (T1). To verify T2, you can temporarily return an empty response — the hook should immediately inject the prose fallback block.

---

## Requirements

- Python 3.9+
- Claude Code with hook support enabled
- `auq-resilience/` co-installed as a sibling directory to the plugins being wired

---

## Wiring is Optional

The `auq-resilience` plugin is an add-on — the Claude for Customer Success plugins work without it. Wiring it in adds resilience on top of the existing `AskUserQuestion` calls those plugins already make. Nothing in the Claude for Customer Success plugins fails or degrades if this plugin is absent.

---

## Cowork Deployment — Behavioral Fallback

**Hooks do not run in Cowork.** The Claude Cowork desktop application does not support the Claude Code hook infrastructure. `PreToolUse`, `PostToolUse`, and `UserPromptSubmit` hook events never fire in a Cowork session — the Python scripts in this plugin's `hooks/` directory will not execute, and the mechanical T2 injection and single-question enforcement the hooks provide are unavailable.

### The alternative: CLAUDE.md behavioral rules

Rather than leave Cowork sessions unprotected, the T1/T2/T3 protocol was written directly into each plugin's `CLAUDE.md` as a set of behavioral rules. Claude reads these rules at session start and applies them using its own judgment throughout the session. No hook infrastructure required.

The following six plugin files received this treatment:

- `csm/CLAUDE.md`
- `cs-ops/CLAUDE.md`
- `renewals/CLAUDE.md`
- `onboarding/CLAUDE.md`
- `handoff/CLAUDE.md`
- `rev-ops/CLAUDE.md`

Each file includes an **AskUserQuestion (AUQ) Resilience** section that covers:

- **Single-question protocol** — never batch multiple questions into one `AskUserQuestion` call; if more than one decision is needed, ask the first and wait for a response before asking the next
- **T2 prose fallback** — if the widget returns empty, null, or unparseable output, immediately present a formatted prose multiple-choice block and do not proceed as if the question was answered
- **T3 embedded default** — every T2 block marks one option with `← proceeding with this if no response`; if the user doesn't respond within the session, Claude proceeds with that option
- **`/auq` command** — a real slash command shipped in `commands/auq.md`, invoked
  as `/auq-resilience:auq`. Subcommands: `force-prose` (skip the widget and use
  prose blocks for the rest of the session), `status` (report the current mode),
  `reset` (return to widget-first). See below.

### The `/auq` command (works in Cowork)

As of v1.1.0 the plugin ships `commands/auq.md`. In Cowork, slash commands are the
one part of a plugin that does run — so this is the plugin's Cowork-native control
surface, and it **auto-registers on install with no wiring required**. Invoke it as:

```
/auq-resilience:auq force-prose   # prose-only for the rest of this session
/auq-resilience:auq status        # report current AUQ mode + queued questions
/auq-resilience:auq reset         # back to widget-first
```

The command does not contain the protocol — it points at the **AskUserQuestion
(AUQ) Resilience** rules in each plugin's `CLAUDE.md` and sets the session mode.
Those `CLAUDE.md` rules remain the single source of truth for T1/T2/T3 behavior.

### Behavioral vs. mechanical enforcement

The hook-based approach is deterministic: the PostToolUse script intercepts the raw tool response before Claude sees it and blocks forward progress if the response is bad. Claude has no opportunity to misread or skip the fallback.

The CLAUDE.md approach is behavioral: Claude reads the rules, understands the intent, and applies them through its own judgment. This works reliably under normal conditions but has different failure modes — Claude could theoretically proceed on a null response if the behavioral rule wasn't salient in a long context window, or if an unusual response format wasn't recognized as a failure.

For Cowork deployments, the CLAUDE.md approach is the best available option given the hook constraint. For Claude Code deployments, the hook-based approach provides stronger guarantees and should be preferred.
