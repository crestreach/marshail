---
name: marshail-knowledge-research
description: 'Focused research on a topic, codebase area, or library, returning a condensed source-linked research note. Fallback skill for environments without subagent support — runs inline in the current session. Use when the user asks to "research X", "study how Y is wired up", "summarize the auth pipeline", "what does the Job table represent?", "deep-dive on this module", "answer this narrow question without polluting the main context", "find a fresh summary I can drop into the knowledge tree", "give me a research note I can promote later". Also use during the Analysis stage when repo-recon needs depth, during the Implement stage when an unknown surfaces, and any time another agent / skill suggests "research first" rather than guessing. Read-only — does not write to the knowledge tree.'
---

# marshail-knowledge-research (fallback — no-subagent environments)

Run the [`marshail-researcher`](../../agents/marshail-researcher.md) workflow inline in the current session, for tools that don't support custom subagents.
The agent file is the source of truth for the behavior; this skill only describes how it gets activated.

## Call contract

- **Agent (run inline):** `marshail-researcher`
- **Context needed (request-specific only):** the **intent** — a narrowly-scoped topic / question — plus optional likely-relevant paths or a knowledge-file id to refresh, and whether the result is intended for promotion to knowledge.
  The agent reads `.marshail/config.yml`, the active knowledge implementation's reading guide, and the repo / knowledge tree itself — do not pass them.
- **Produces:** a single condensed, source-linked markdown **research note** (target ≤ ~150 lines body); an "open questions" trailer.
  When the request says it is for knowledge, the note follows the active knowledge implementation so it can be promoted as-is.
- **Then:** consume the note inline.
  For promotion to canonical knowledge, drop it into `.marshail/knowledge/learn/inbox/` and invoke [`marshail-knowledge-maintain`](../marshail-knowledge-maintain/SKILL.md) (`from-learning`).
