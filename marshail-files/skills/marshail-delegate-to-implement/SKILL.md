---
name: marshail-delegate-to-implement
description: MARSHAIL Implement stage. Delegate when the user asks to "implement phase X", "work on packet Y", "code this packet", "execute the plan", "start the next implementation cycle", "do the work for the current target", "write the code + tests for this", "run a Dev-QA pass", "close this cycle". Also delegate after the Plan stage (plan approved) to drive each implementation cycle, and after a verifier failure or reviewer fixup loop to resume coding. The subagent picks the cycle target, confirms / deepens the plan, executes (code + tests + Dev-QA where possible), offers the human a chance to inspect and give feedback, and closes the cycle by updating plan status, changelog, and learning files.
---

# marshail-delegate-to-implement

Delegate this to the [`marshail-implementer`](../../agents/marshail-implementer.md) subagent.
Do **not** inline the workflow — the agent owns it.

## Call contract

- **Subagent:** `marshail-implementer`
- **Pass:** `delivery-plan.md`; `change-brief.md` (if it exists); the picked target item (phase / packet / step); the knowledge units whose scope covers the affected code paths.
- **Expect back:** code + tests committed against the plan; updated `delivery-plan.md` (status markers, [FIXUP] / [ADDED] / [CHANGED] / [REVERT] / [DROPPED] entries with dates); `logs/phase-<n>.changelog.md` and `learning/phase-<n>.learning.md` (per L1 phase).
- **On result:** when the PR boundary is filled, hand off to [`marshail-delegate-to-verify`](../marshail-delegate-to-verify/SKILL.md).
  After the cycle, if tracked code paths changed, hand off to [`marshail-delegate-to-knowledge-maintain`](../marshail-delegate-to-knowledge-maintain/SKILL.md) (mode `from-changes`).
  If assumptions break, hand back to [`marshail-delegate-to-plan`](../marshail-delegate-to-plan/SKILL.md) for the affected phase.
