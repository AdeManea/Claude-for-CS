---
name: auq
description: AUQ control command. Use when the user types /auq with an argument — force-prose (skip the AskUserQuestion widget and use prose multiple-choice blocks for the rest of the session), status (report the current AUQ mode and any queued questions), or reset (return to widget-first behavior). This is the Cowork-native entry point for the AskUserQuestion (AUQ) Resilience protocol; the protocol mechanics live in CLAUDE.md.
---

The user invoked `/auq` with argument: $ARGUMENTS

Apply the AskUserQuestion (AUQ) Resilience rules defined in CLAUDE.md. Do not
restate the full protocol — act on the argument below.

- **force-prose** → For the remainder of this session, replace every
  `AskUserQuestion` call with a T2 prose multiple-choice block (one question
  per block, last option marked `← proceeding with this if no response`).
  Acknowledge once: "Switching to prose-only questions for this session."
  Then apply silently for the rest of the session.

- **status** → Report the current AUQ mode (widget-first or prose-only) and
  list any pending/queued questions you are aware of from session context.

- **reset** → Return to widget-first (T1) behavior for new questions; clear
  any prose-only mode set earlier this session.

- **(no argument or unrecognized)** → Briefly list the three subcommands
  above (`force-prose`, `status`, `reset`) and ask which one the user wants.
