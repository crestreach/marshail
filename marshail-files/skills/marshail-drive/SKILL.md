---
name: marshail-drive
description: Run or resume a staged MARSHAIL change with marshail-driver in the current session. Use when the user wants to "work through this change with MARSHAIL", "drive this change together", "plan and implement this with me", "resume this MARSHAIL change", "pick up where the change left off", "run MARSHAIL on this" with any involvement in the stages, or when a MARSHAIL repo gets a change request and the involvement level is not stated. Runs the driver inline (it is a conversational role); when it is unclear whether the user wants a staged change or an iterative workbench session, asks first. For a fully hands-off end-to-end run that should return only at approval gates, use marshail-delegate-to-driver instead.
---

# marshail-drive

Main-session skill for running a staged change with [`marshail-driver`](../../agents/marshail-driver.md).
The agent file is the source of truth for the agent's behavior; this skill only describes how it gets activated.

## Behavior

1. **Already the session agent?**
   If the current chat agent *is* `marshail-driver`, this skill has nothing to do — the agent's own workflow applies.
2. **Intent is clear** — the user named MARSHAIL, the driver, or a stage, asked to resume a change, or `.marshail/` is initialized and the request is unmistakably a change (feature, bugfix, refactor, tech debt) → run `marshail-driver` **inline** now, without asking.
   The driver still spawns every stage agent as a fresh-context subagent, per its workflow.
3. **Intent is unclear** — the skill fired on a weak cue, or the request may be iterative focused work rather than a change → ask one question offering the real alternatives: a staged change with a plan (this skill), an iterative workbench session ([`marshail-workbench`](../marshail-workbench/SKILL.md)), or neither.
   Proceed inline on "change"; hand to `marshail-workbench` on "workbench"; step aside otherwise.

Do **not** spawn the driver from this skill.
Spawning the driver is right only for a hands-off end-to-end run with few interaction points — that is [`marshail-delegate-to-driver`](../marshail-delegate-to-driver/SKILL.md), whose triggers are mutually exclusive with this skill's (see [marshail.md § How an agent runs](../../marshail.md#how-an-agent-runs)).

## Call contract

- **Agent run inline:** `marshail-driver`.
- **Pass:** the user's prompt describing the change; the repo with `.marshail/` initialized.
- **Expect:** the canonical artifact chain in `.marshail/work/<change-id>/`; approval gates surfaced in the conversation; a stage-summary at the end.
- **On handover:** when the driver says a stretch of work fits a workbench session better, the user starts a session with `marshail-workbench-driver` (or runs [`marshail-workbench`](../marshail-workbench/SKILL.md)) with the one-line prompt the driver gives.
