---
name: marshail-learn
description: 'MARSHAIL Learn / improve stage. Fallback skill for environments without subagent support — runs inline in the current session. Use when the user asks to "merge the phase learnings", "produce learning-rollup.md", "promote learnings into AGENTS.md / README / rules / skills / agents / prompts / checklists", "draft a new rule from the learnings", "draft a new skill / agent from the learnings", "promote durable items into the knowledge layer", "close the loop on this change", "what should we keep from this change?". Also use after the Rollout stage (or the PR stage if rollout was skipped) for any change with phase learning files worth promoting. Run inline, the agent merges and dedupes learnings, groups them into buckets, proposes per-bucket diffs (human-approved), and routes the knowledge bucket via marshail-knowledge-curator mode from-learning.'
---

# marshail-learn (fallback — no-subagent environments)

Run the [`marshail-learner`](../../agents/marshail-learner.md) workflow inline in the current session, for tools that don't support custom subagents.
The agent file is the source of truth for the behavior; this skill only describes how it gets activated.

## Call contract

- **Agent (run inline):** `marshail-learner`
- **Context needed:** all `learning/*.learning.md` files for the change (or `learning/workbench.learning.md` for a workbench session); `architecture-notes.md` if the Architecture stage ran.
- **Produces:** `learning-rollup.md`; per-bucket proposed diffs (human-approved before applying); files dropped into `.marshail/knowledge/learn/inbox/` for the knowledge bucket.
- **Then:** for the knowledge bucket, hand off to [`marshail-knowledge-maintain`](../marshail-knowledge-maintain/SKILL.md) (`from-learning`) with paths to the inbox files just added.
  For new skills / subagents / rules drafted under `.marshail/`, run the agent-config sync afterwards.
