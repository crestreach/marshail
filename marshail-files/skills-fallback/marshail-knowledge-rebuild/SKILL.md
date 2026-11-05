---
name: marshail-knowledge-rebuild
description: 'Post-feature rebuild of the MARSHAIL knowledge layer. Fallback skill for environments without subagent support — runs inline in the current session. Use when the user asks to "rebuild the knowledge tree", "restructure domains/ after this feature", "re-derive subsystems / domains", "incorporate this feature branch into knowledge", "do a quarterly knowledge rebuild", "promote big structural changes into the knowledge layer". Use when a sizable feature lands that introduced new modules, removed modules, or significantly changed logic — heavier than maintain mode `rescan`. Run inline, the agent (curator in mode `rebuild`) re-runs init-style detection on HEAD and reconciles structure + content with the existing tree per the autonomy mode — applying changes and returning a short summary in `auto`, or a summary diff for approval in `review`.'
---

# marshail-knowledge-rebuild (fallback — no-subagent environments)

Run the [`marshail-knowledge-curator`](../../agents/marshail-knowledge-curator.md) workflow in mode `rebuild` inline in the current session, for tools that don't support custom subagents.
The agent file is the source of truth for the behavior; this skill only describes how it gets activated.

## Call contract

- **Agent (run inline):** `marshail-knowledge-curator`
- **Mode:** `rebuild`
- **Context needed (request-specific only):** the **intent** (what to rebuild / incorporate) and, **optionally**, the merged feature branch or commit range to incorporate.
  The change is optional — omit it to rebuild from the whole tree vs current HEAD. The agent reads the current HEAD, the previous knowledge tree, `.marshail/config.yml`, and the knowledge contract / implementation itself — do not pass them.
- **Produces:** a **summary** of structure changes (contexts added / removed / renamed) and content changes — not the full knowledge diff.
- **Then:** under `knowledge.autonomy: auto` (default) it is already applied — read the summary.
  Under `review`, approve the diff.
