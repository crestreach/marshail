---
name: marshail-help
description: MARSHAIL on-demand help. Fallback skill for environments without subagent support. Triggers on "how does MARSHAIL work?", "which skill / agent next?", "what stage am I in?", "explain the knowledge layer", "what does X stage do?", "MARSHAIL?". Answers procedural / conceptual questions about MARSHAIL inline in the current session — read-only.
---

# marshail-help (fallback — no-subagent environments)

This skill performs the work that the [`marshail-helper`](../../agents/marshail-helper.md) subagent does, but inline in the current session, for tools that don't support custom subagents.

**Source of truth:** [`marshail-helper.md`](../../agents/marshail-helper.md) — for the agent's behavior; this skill only describes how it gets activated.

> If subagents *are* available, prefer [`marshail-delegate-to-help`](../../skills/marshail-delegate-to-help/SKILL.md) so the work runs in fresh context.
