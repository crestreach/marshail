---
name: marshail-pr
description: 'MARSHAIL Review / PR / integration / merge stage. Fallback skill for environments without subagent support — runs inline in the current session. Use when the user asks to "open the PR", "compose the PR description", "do an AI review of this PR", "self-review before requesting human review", "review against delivery-plan.md and verification-report.md", "check the diff for plan alignment / test coverage / knowledge conflicts / risks", "handle review feedback", "convert review comments into plan FIXUP / CHANGED / ADDED entries", "promote the integration branch to main / release", "do the final review of the whole change". Also use any time a coherent integration boundary is ready and the Verify stage passed. Run inline, the agent composes the PR, runs structured AI review against the plan + knowledge layer, and converts feedback into plan updates rather than silent edits. Supports PRs that target an integration branch, with a final promotion round (Implement -> Verify -> Review / PR) into the release / main branch.'
---

# marshail-pr (fallback — no-subagent environments)

Run the [`marshail-reviewer`](../../agents/marshail-reviewer.md) workflow inline in the current session, for tools that don't support custom subagents.
The agent file is the source of truth for the behavior; this skill only describes how it gets activated.

## Call contract

- **Agent (run inline):** `marshail-reviewer`
- **Context needed:** PR diff (or branch + base commit range); the **target branch** (final `main` / release, or an integration branch); `delivery-plan.md` (packets in scope); `verification-report.md`; `change-brief.md`; `implementation-report.md`.
- **Produces:** PR description; structured review document with sections (Plan alignment, Test coverage, Knowledge conflicts, Risks, Recommendation: `approve` | `request-changes` | `comment`); inline comments; plan updates with `[FIXUP]` / `[CHANGED]` / `[ADDED]` markers; updated phase changelog.
- **On result (approve):** hand off to [`marshail-rollout`](../marshail-rollout/SKILL.md) (Rollout stage) or [`marshail-learn`](../marshail-learn/SKILL.md) (Learn stage).
  If knowledge upkeep wasn't done in the Implement stage, run [`marshail-knowledge-maintain`](../marshail-knowledge-maintain/SKILL.md) (`from-changes`).
- **On result (request-changes):** hand back to [`marshail-implement`](../marshail-implement/SKILL.md), then re-verify and re-review.
- **Integration branch:** when the PR targets an integration branch, finish with one more round to promote it to the final branch — Implement (merge / rebase onto the target, resolve conflicts, final improvements) → Verify (whole integrated scope) → Review / PR (final review of the change as a whole).
