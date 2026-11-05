---
name: marshail-verify
description: 'MARSHAIL Verify stage — REQUIRED before any PR. Fallback skill for environments without subagent support — runs inline in the current session. Use when the user asks to "verify the change", "run the verification gate", "check requirements coverage", "audit the tests", "do Dev-QA", "produce verification-report.md", "is this ready for PR?", "check static / lint / typecheck / migrations / observability / security for this PR boundary". Also use automatically after each implementation cycle that fills a coherent PR boundary, and to re-verify after a fixup loop. Run inline, the agent walks every requirement, applies the Implement-stage testing strategy, runs Dev-QA where possible, and either passes (→ PR) or pushes back with plan updates (→ Implement).'
---

# marshail-verify (fallback — no-subagent environments)

Run the [`marshail-verifier`](../../agents/marshail-verifier.md) workflow inline in the current session, for tools that don't support custom subagents.
The agent file is the source of truth for the behavior; this skill only describes how it gets activated.

## Call contract

- **Agent (run inline):** `marshail-verifier`
- **Context needed:** `change-brief.md` (if it exists); `delivery-plan.md` (packets in scope); the diff / changed files for the PR boundary; `implementation-report.md`; any prior `verification-report.md`.
- **Produces:** `verification-report.md` (or, for trivial changes, a verification paragraph appended to the affected phase changelog); appended results to `logs/phase-<n>.changelog.md`; reusable lessons in `learning/phase-<n>.learning.md`.
- **On result (pass):** hand off to [`marshail-pr`](../marshail-pr/SKILL.md) (PR stage) or directly to [`marshail-rollout`](../marshail-rollout/SKILL.md) (Rollout stage) if PR is skipped.
- **On result (fail):** hand back to [`marshail-implement`](../marshail-implement/SKILL.md) with failing items as plan updates ([FIXUP] / [CHANGED] / [ADDED]).

Nothing merges without an explicit verification step recorded somewhere.
