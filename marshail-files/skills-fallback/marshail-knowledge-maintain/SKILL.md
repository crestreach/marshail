---
name: marshail-knowledge-maintain
description: 'Maintain the MARSHAIL knowledge layer. Fallback skill for environments without subagent support — runs inline in the current session. Use when the user asks to "update the knowledge tree from this diff / commit range", "promote learnings from learn/inbox/", "rescan the knowledge tree", "refresh stale knowledge files", "split oversize topics", "sweep the knowledge layer", "update knowledge after this implementation cycle", "after the Learn stage promote inbox items into canonical files". Modes — `from-changes` (after an implementation cycle that changed code), `from-learning` (after the Learn stage), `rescan` (full sweep). Run inline, the curator updates topic files and indexes, splits oversize topics into sub-indexes recursively, respects autonomy mode, and returns a short summary of what changed (a full diff only in `review` mode).'
---

# marshail-knowledge-maintain (fallback — no-subagent environments)

Run the [`marshail-knowledge-curator`](../../agents/marshail-knowledge-curator.md) workflow in mode `from-changes`, `from-learning`, or `rescan` inline in the current session, for tools that don't support custom subagents.
The agent file is the source of truth for the behavior; this skill only describes how it gets activated.

## Call contract

- **Agent (run inline):** `marshail-knowledge-curator`
- **Mode:** one of `from-changes` | `from-learning` | `rescan`
- **Context needed (request-specific only):**
  - The **intent** (what knowledge update is wanted), always.
  - `from-changes`: a list of changed paths or a git diff range.
  - `from-learning`: paths to the inbox items to promote.
  - `rescan`: nothing extra — operates on the whole tree vs HEAD.
  - The agent reads `.marshail/config.yml`, the knowledge contract / implementation, and the indexes itself — do not pass them.
- **Produces:** a **summary** of what changed (counts: created / updated / removed / flagged) — not the full knowledge diff.
- **Then:** under `knowledge.autonomy: auto` (default) it is already applied — read the summary.
  Under `review`, approve the diff.
  For larger restructures, escalate to [`marshail-knowledge-rebuild`](../marshail-knowledge-rebuild/SKILL.md).
