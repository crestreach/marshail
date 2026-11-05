---
name: marshail-architecture
description: MARSHAIL Architecture stage. Fallback skill for environments without subagent support. Triggers on "do the architecture step", "produce architecture-notes.md", "compare design options", "do a design conversation", "ADR-worthy decision", "draft an ADR". Runs architecture conversation inline in the current session.
---

# marshail-architecture (fallback — no-subagent environments)

This skill performs the work that the [`marshail-architect`](../../agents/marshail-architect.md) subagent does, but inline in the current session, for tools that don't support custom subagents.

**Source of truth:** [`marshail-architect.md`](../../agents/marshail-architect.md) — for the agent's behavior; this skill only describes how it gets activated.

> If subagents *are* available, prefer [`marshail-delegate-to-architecture`](../../skills/marshail-delegate-to-architecture/SKILL.md) so the work runs in fresh context.
