---
name: marshail-drive-end-to-end
description: Run a whole MARSHAIL change end-to-end, hands-off. Fallback skill for environments without subagent support. Triggers ONLY on hands-off intent — "run MARSHAIL on this autonomously", "do the whole change for me and ask only at the gates", "drive this end-to-end without me", "orchestrate this hands-off". Runs marshail-driver inline in the current session, coming back to the user only at approval gates and key decisions. For collaborative or interactive runs, or when the involvement level is not stated, use marshail-drive instead.
---

# marshail-drive-end-to-end (fallback — no-subagent environments)

This skill performs the hands-off end-to-end run that [`marshail-delegate-to-driver`](../../skills/marshail-delegate-to-driver/SKILL.md) spawns [`marshail-driver`](../../agents/marshail-driver.md) for, but inline in the current session, for tools that don't support custom subagents.

**Source of truth:** [`marshail-driver.md`](../../agents/marshail-driver.md) — for the agent's behavior; this skill only describes how it gets activated.

## Behavior

Run `marshail-driver` inline at the **hands-off** involvement level: agree scope once, then run the stages end-to-end and return to the user only at approval gates and for decisions that genuinely need them.
Stage work runs through the fallback stage skills, since no spawn tool is available.

Triggers are mutually exclusive with [`marshail-drive`](../../skills/marshail-drive/SKILL.md), which runs the driver inline for collaborative / interactive runs and resumes; when the user's involvement level is not stated, `marshail-drive` applies.

> If subagents *are* available, prefer [`marshail-delegate-to-driver`](../../skills/marshail-delegate-to-driver/SKILL.md) so the orchestration runs in fresh context.
