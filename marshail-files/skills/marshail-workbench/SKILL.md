---
name: marshail-workbench
description: Start or continue a MARSHAIL workbench session — iterative, plan-less work with marshail-workbench-driver. Use when the user wants "ad-hoc" work, to "iterate on X with me", "go through this in rounds", "work on this part without a plan", "just rework / polish / tune this", "a workbench session", or to "resume the workbench session". Runs the agent inline in the current session (it is a conversational role and is never spawned as a subagent); when it is unclear whether the user wants an iterative session or a staged change, asks first.
---

# marshail-workbench

Main-session skill for [workbench mode](../../marshail.md#workbench-mode).
The agent it runs is [`marshail-workbench-driver`](../../agents/marshail-workbench-driver.md); that file is the source of truth for the agent's behavior — this skill only describes how it gets activated.

## Behavior

1. **Already the session agent?**
   If the current chat agent *is* `marshail-workbench-driver`, this skill has nothing to do — the agent's own workflow applies.
2. **Intent is clear** — the user named MARSHAIL, the workbench, or the agent, or `.marshail/` is initialized and the request is unmistakably iterative focused work (rounds, rework, polish, tuning, exploration of one area) → run `marshail-workbench-driver` **inline** now, without asking.
3. **Intent is unclear** — the skill fired on a weak cue (e.g. "let's work on the UI" could be a quick edit or a session), or the request may be a staged change rather than an iterative session → ask one question offering the real alternatives: an iterative workbench session (this skill), a staged change with a plan ([`marshail-drive`](../marshail-drive/SKILL.md)), or neither.
   Proceed inline on "workbench"; hand to `marshail-drive` on "change"; step aside otherwise.

Never spawn `marshail-workbench-driver` as a subagent: it is a conversational role, and a spawned subagent cannot talk to the user (see [marshail.md § How an agent runs](../../marshail.md#how-an-agent-runs)).
The specialists the workbench driver uses *are* spawned, per its workflow.

## Call contract

- **Agent run inline:** `marshail-workbench-driver`.
- **Pass:** the user's request; the repo with `.marshail/` initialized.
- **Expect:** a session working folder `.marshail/work/wb-<date>-<slug>/` with `workbench-session.md`, `logs/resume.md` (`Mode: workbench`), per-agent run logs, `learning/workbench.learning.md`; short per-round reports; knowledge kept in sync.
- **On handover:** when the agent says the work has outgrown a session, the user starts a session with `marshail-driver` (or runs [`marshail-drive`](../marshail-drive/SKILL.md)) with the one-line prompt the agent gives.
