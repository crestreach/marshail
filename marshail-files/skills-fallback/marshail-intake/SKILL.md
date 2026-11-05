---
name: marshail-intake
description: 'MARSHAIL Intake / framing stage. Fallback skill for environments without subagent support — runs inline in the current session. Use when the user asks to "frame the change", "write the change brief", "produce change-brief.md", "structure the engineering framing for this change", "list the non-goals and acceptance criteria", "convert the spec into a brief". Also use when the Specification stage is finished (specification.md exists) and the Intake stage is in scope, or when the user has a clear prompt but wants a structured brief before analysis/planning. Run inline, the agent turns the spec or prompt into change-brief.md (problem, scope, non-goals, acceptance criteria, constraints, rollout expectations).'
---

# marshail-intake (fallback — no-subagent environments)

Run the [`marshail-framer`](../../agents/marshail-framer.md) workflow inline in the current session, for tools that don't support custom subagents.
The agent file is the source of truth for the behavior; this skill only describes how it gets activated.

## Call contract

- **Agent (run inline):** `marshail-framer`
- **Context needed:** `specification.md` if it exists; otherwise the user prompt.
- **Produces:** approved `change-brief.md` plus `logs/stage-2-intake.changelog.md` and `learning/stage-2-intake.learning.md`.
- **Then:** hand off to [`marshail-analysis`](../marshail-analysis/SKILL.md) (Analysis stage) or directly to [`marshail-plan`](../marshail-plan/SKILL.md) (Plan stage) per the agreed scope.
