---
name: marshail-implement
description: 'MARSHAIL Implement stage. Fallback skill for environments without subagent support — runs inline in the current session. Use when the user asks to "implement phase X", "work on packet Y", "code this packet", "execute the plan", "start the next implementation cycle", "do the work for the current target", "write the code + tests for this", "run a Dev-QA pass", "close this cycle". Also use after the Plan stage (plan approved) to drive each implementation cycle, and after a verifier failure or reviewer fixup loop to resume coding. Run inline, the agent picks the cycle target, confirms / deepens the plan, executes (code + tests + Dev-QA where possible), offers the human a chance to inspect and give feedback, and closes the cycle by updating plan status, changelog, and learning files.'
---

# marshail-implement (fallback — no-subagent environments)

Run the [`marshail-implementer`](../../agents/marshail-implementer.md) workflow inline in the current session, for tools that don't support custom subagents.
The agent file is the source of truth for the behavior; this skill only describes how it gets activated.

## Call contract

- **Agent (run inline):** `marshail-implementer`
- **Context needed:** `delivery-plan.md`; `change-brief.md` (if it exists); the picked target item (phase / packet / step).
- **Produces:** code + tests committed against the plan; updated `delivery-plan.md` (status markers, [FIXUP] / [ADDED] / [CHANGED] / [REVERT] / [DROPPED] entries with dates); `logs/phase-<n>.changelog.md` and `learning/phase-<n>.learning.md` (per L1 phase).
- **Then:** when the PR boundary is filled, hand off to [`marshail-verify`](../marshail-verify/SKILL.md).
  After the cycle, if tracked code paths changed, hand off to [`marshail-knowledge-maintain`](../marshail-knowledge-maintain/SKILL.md) (mode `from-changes`).
  If assumptions break, hand back to [`marshail-plan`](../marshail-plan/SKILL.md) for the affected phase.
