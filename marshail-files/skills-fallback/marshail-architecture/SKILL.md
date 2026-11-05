---
name: marshail-architecture
description: 'MARSHAIL Architecture / design stage. Fallback skill for environments without subagent support — runs inline in the current session. Use when the user asks to "design the solution", "agree the implementation concept", "discuss the architecture", "propose components / modules / APIs / schemas", "produce architecture-notes.md", "draft an ADR for this", "what''s the right shape for this?", "compare design tradeoffs", "high-level layout for this change". Also use when change-brief.md and repo-recon.md exist and the change is large enough that the solution shape isn''t obvious yet. Run inline, the agent drives the design conversation, captures rationale, and (when significant) promotes durable decisions to ADRs.'
---

# marshail-architecture (fallback — no-subagent environments)

Run the [`marshail-architect`](../../agents/marshail-architect.md) workflow inline in the current session, for tools that don't support custom subagents.
The agent file is the source of truth for the behavior; this skill only describes how it gets activated.

## Call contract

- **Agent (run inline):** `marshail-architect`
- **Context needed:** `change-brief.md`, `repo-recon.md`.
- **Produces:** approved `architecture-notes.md`, optional ADR file(s), plus `logs/stage-4-architecture.changelog.md` and `learning/stage-4-architecture.learning.md`.
- **Then:** hand off to [`marshail-plan`](../marshail-plan/SKILL.md) (Plan stage).
  If new ADRs were drafted, route them through [`marshail-knowledge-maintain`](../marshail-knowledge-maintain/SKILL.md) for promotion into the canonical knowledge tree.
