---
name: marshail-rollout
description: 'MARSHAIL Release / rollout stage. Fallback skill for environments without subagent support — runs inline in the current session. Use when the user asks to "draft the rollout note", "produce rollout-note.md", "list the toggles / properties / log changes / migrations / rollback path", "write the porting instructions", "list user-visible docs changes", "write a manual test scenario list for release", "what''s the deploy plan?", "how do we roll this back?". Also use after merge (PR stage) or after the Verify stage if PR was skipped, for any change with operational impact. Run inline, the agent walks the change for operational shape and emits rollout-note.md.'
---

# marshail-rollout (fallback — no-subagent environments)

Run the [`marshail-releaser`](../../agents/marshail-releaser.md) workflow inline in the current session, for tools that don't support custom subagents.
The agent file is the source of truth for the behavior; this skill only describes how it gets activated.

## Call contract

- **Agent (run inline):** `marshail-releaser`
- **Context needed:** `change-brief.md`; `delivery-plan.md` (rollout boundary on phases); `verification-report.md`; `implementation-report.md`; migration / config / feature-flag files touched.
- **Produces:** `rollout-note.md` (toggles, properties, log changes, migrations, rollback path, porting instructions, user-visible docs); `logs/stage-7-rollout.changelog.md`; `learning/stage-7-rollout.learning.md`.
- **Then:** hand off to [`marshail-learn`](../marshail-learn/SKILL.md) (Learn stage) with the rollout note plus pointers to all phase learning files.
