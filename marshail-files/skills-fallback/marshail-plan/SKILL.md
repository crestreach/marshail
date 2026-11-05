---
name: marshail-plan
description: 'MARSHAIL Plan / shape stage — MANDATORY for every MARSHAIL change. Fallback skill for environments without subagent support — runs inline in the current session. Use whenever the user asks to "plan this change", "make a delivery plan", "produce delivery-plan.md", "shape the work", "break this into phases / packets / steps", "decide L1 / L2 / L3 / L4 depth", "decide PR boundaries", "decide rollout boundaries", "mark parallelizable work", "deepen the plan for phase X", "replan phase Y", or simply "let''s plan". Also use at the start of any MARSHAIL change once the earlier stages (Specification through Architecture) are agreed (or skipped), and mid-change to deepen a previously high-level phase before its implementation cycle. Run inline, the agent picks the shallowest workable depth unless the user pins a target depth, and chooses a timing mode (full / staged / mixed).'
---

# marshail-plan (fallback — no-subagent environments)

Run the [`marshail-planner`](../../agents/marshail-planner.md) workflow inline in the current session, for tools that don't support custom subagents.
The agent file is the source of truth for the behavior; this skill only describes how it gets activated.

## Call contract

- **Agent (run inline):** `marshail-planner`
- **Context needed:** whichever upstream artifacts exist (`specification.md`, `change-brief.md`, `repo-recon.md`, `architecture-notes.md`); planning timing mode (`full` | `staged` | `mixed`) if the user has a preference; target depth if pinned (e.g. "L2", "L4 in P1, L2 elsewhere"); existing `delivery-plan.md` if deepening / amending.
- **Produces:** approved `delivery-plan.md` (Scope line + Planning mode + Target depth, then phases / packets / steps to the agreed depth), plus `logs/stage-5-plan.changelog.md` and `learning/stage-5-plan.learning.md`.
- **Then:** confirm approval and hand off to [`marshail-implement`](../marshail-implement/SKILL.md) (Implement stage).
  For staged deepening, re-invoke this skill before each implementation cycle of a still-shallow phase.

The Plan stage is **mandatory** in MARSHAIL — every change has a plan, even a single phase with one step.
