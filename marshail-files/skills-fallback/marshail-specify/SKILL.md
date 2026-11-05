---
name: marshail-specify
description: 'MARSHAIL Specification / clarify stage. Fallback skill for environments without subagent support — runs inline in the current session. Use when the user asks to "spec out", "clarify", "frame", or "agree the intent of" a feature / bugfix / refactor / tech-debt prompt before any coding — or says things like "what exactly do you want me to build?", "let''s pin down the requirements", "produce a specification.md", "ask me clarifying questions", "I have a vague idea, help me sharpen it", "list the open questions", "draft acceptance criteria". Also use when starting a new MARSHAIL change with an ambiguous prompt and the Specification stage is in scope. Run inline, the agent drives the clarification dialog (no assumptions, explicit disagreement, optional acceptance checklist) and writes specification.md.'
---

# marshail-specify (fallback — no-subagent environments)

Run the [`marshail-specifier`](../../agents/marshail-specifier.md) workflow inline in the current session, for tools that don't support custom subagents.
The agent file is the source of truth for the behavior; this skill only describes how it gets activated.

## Call contract

- **Agent (run inline):** `marshail-specifier`
- **Context needed:** the user's raw prompt; pointers to any prior context the user named (issues, prior briefs, screenshots).
- **Produces:** an approved `specification.md` plus `logs/stage-1-specification.changelog.md` and `learning/stage-1-specification.learning.md`.
- **Then:** confirm the spec is approved and hand off to [`marshail-intake`](../marshail-intake/SKILL.md) (Intake stage) or [`marshail-plan`](../marshail-plan/SKILL.md) (Plan stage) per the agreed scope.
