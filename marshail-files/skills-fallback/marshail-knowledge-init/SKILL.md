---
name: marshail-knowledge-init
description: 'First-time bootstrap of the MARSHAIL knowledge layer. Fallback skill for environments without subagent support — runs inline in the current session. Use when the user asks to "bootstrap the knowledge layer", "initialize .marshail/knowledge/", "scan the repo and draft repo/ and domains/ knowledge files", "build initial INDEX.md files", "set up the knowledge tree from scratch", or when running marshail-init in a repo whose .marshail/knowledge/ is empty or near-empty. Run inline, the agent (curator in `init` mode) enumerates entrypoints, detects subsystems / domains, scans the code to the depth each area warrants, drafts the knowledge files, and generates indexes per the autonomy mode — writing them and returning a short summary in `auto`, or a summary diff for approval in `review`.'
---

# marshail-knowledge-init (fallback — no-subagent environments)

Run the [`marshail-knowledge-curator`](../../agents/marshail-knowledge-curator.md) workflow in mode `init` inline in the current session, for tools that don't support custom subagents.
The agent file is the source of truth for the behavior; this skill only describes how it gets activated.

## Call contract

- **Agent (run inline):** `marshail-knowledge-curator`
- **Mode:** `init`
- **Context needed (request-specific only):** the intent ("bootstrap the knowledge layer for this repo") and any scoping hints.
  The agent reads the repo, build / package manifests, `.marshail/config.yml`, and the knowledge contract / implementation itself — do not pass them.
  The exact files produced are defined by the active knowledge implementation, not here.
- **Produces:** a **summary** of detected subsystems / domains and unresolved heuristics.
  Not the full knowledge diff.
- **Then:** under `knowledge.autonomy: auto` (default) the tree is already written — read the summary and resolve any open follow-ups.
  Under `review`, work the diff with the human before it is applied.
