---
name: marshail-knowledge-branch-merge
description: 'Reconcile MARSHAIL knowledge files diverged on two branches. Fallback skill for environments without subagent support — runs inline in the current session. Use when the user asks to "merge knowledge files from two branches", "resolve a conflict in .marshail/knowledge/", "reconcile knowledge changes between feature branch and trunk", "3-way merge knowledge frontmatter and bodies", "handle overlapping repo_paths between branches", "two branches independently added domain notes — merge them". Use at merge time when both sides changed `.marshail/knowledge/`, when there''s a git conflict in a knowledge file, or when both branches added content in overlapping areas without textual conflict. Run inline, the agent (curator in mode `branch-merge`) performs the merge per the autonomy mode — applying it and returning a short summary in `auto`, or returning a summary diff plus any items needing human resolution in `review`.'
---

# marshail-knowledge-branch-merge (fallback — no-subagent environments)

Run the [`marshail-knowledge-curator`](../../agents/marshail-knowledge-curator.md) workflow in mode `branch-merge` inline in the current session, for tools that don't support custom subagents.
The agent file is the source of truth for the behavior; this skill only describes how it gets activated.

## Call contract

- **Agent (run inline):** `marshail-knowledge-curator`
- **Mode:** `branch-merge`
- **Context needed (request-specific only):** the intent (what the merge is for), two branch refs (or merge base + two heads), and an optional file focus list.
  The agent reads `.marshail/config.yml`, the knowledge contract / implementation, and the knowledge tree itself — do not pass them.
- **Produces:** a **summary** of what was merged and, in `review` mode, the list of items needing explicit human resolution.
  Not the full knowledge diff.
- **Then:** under `knowledge.autonomy: auto` (default) the merge is already applied — read the summary.
  Under `review`, resolve the flagged items with the human and approve.
