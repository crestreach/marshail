---
name: marshail-help
description: 'MARSHAIL on-demand help. Fallback skill for environments without subagent support — runs inline in the current session. Use when the user asks "how does MARSHAIL work?", "what does the X stage do?", "which skill / agent should I use next?", "what stage am I in?", "explain the knowledge layer", "how do I write a delivery plan?", "what goes in repo-recon.md?", "how does the config sync fit in?", "what''s the difference between marshail-init and marshail-load?", "what''s the marshail acronym?", "MARSHAIL?", or any procedural / conceptual question about the MARSHAIL process, the knowledge layer, the available skills / agents, or how MARSHAIL applies to the current change. Also use when the caller is unsure which skill / agent to use, or when another skill / agent encounters a MARSHAIL-meta question that would pollute its context. Run inline, the agent reads marshail.md and the repo''s .marshail/ tree, returns a concise answer, and (when asked) hands off — read-only.'
---

# marshail-help (fallback — no-subagent environments)

Run the [`marshail-helper`](../../agents/marshail-helper.md) workflow inline in the current session, for tools that don't support custom subagents.
The agent file is the source of truth for the behavior; this skill only describes how it gets activated.

## Call contract

- **Agent (run inline):** `marshail-helper`
- **Context needed:** the user's question (free text); optional working folder path for situational questions like "what stage am I in?".
- **Produces:** a single concise answer block (default ≤ ~30 lines), ending with a one-line "next step" pointer.
  No files written.
- **Then:** if the answer includes a handoff to a stage agent (driver or specific stage), invoke the matching fallback `marshail-<x>` skill next.
