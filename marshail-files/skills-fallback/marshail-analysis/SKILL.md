---
name: marshail-analysis
description: 'MARSHAIL Repo recon / analysis stage. Fallback skill for environments without subagent support — runs inline in the current session. Use when the user asks to "analyze the repo for this change", "do recon", "narrow the search surface", "find the likely files / services / contracts", "identify invariants and test seams", "produce repo-recon.md", "what touches X in this codebase?", "where does Y live?", "scope the code areas before planning". Also use when change-brief.md exists and the Analysis stage is in scope, or when a planner / implementer flags they don''t know enough about an area. Run inline, the agent reads the brief and existing knowledge, walks likely paths, and returns a tight repo-recon.md plus a stale-knowledge list — keeping intermediate exploration out of the parent context.'
---

# marshail-analysis (fallback — no-subagent environments)

Run the [`marshail-code-archaeologist`](../../agents/marshail-code-archaeologist.md) workflow inline in the current session, for tools that don't support custom subagents.
The agent file is the source of truth for the behavior; this skill only describes how it gets activated.

## Call contract

- **Agent (run inline):** `marshail-code-archaeologist`
- **Context needed:** `change-brief.md`.
- **Produces:** draft `repo-recon.md`, a list of stale/missing knowledge files, plus `logs/stage-3-analysis.changelog.md` and `learning/stage-3-analysis.learning.md`.
- **Then:** review and save the recon under the change folder; surface the stale-knowledge list (consider [`marshail-knowledge-research`](../marshail-knowledge-research/SKILL.md) or [`marshail-knowledge-maintain`](../marshail-knowledge-maintain/SKILL.md)).
  Hand off to [`marshail-architecture`](../marshail-architecture/SKILL.md) (Architecture stage) or [`marshail-plan`](../marshail-plan/SKILL.md) (Plan stage).
