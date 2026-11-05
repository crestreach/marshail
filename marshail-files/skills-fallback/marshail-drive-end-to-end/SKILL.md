---
name: marshail-drive-end-to-end
description: Run a whole MARSHAIL change end-to-end, hands-off. Fallback skill for environments without subagent support. Triggers ONLY on hands-off intent — "run MARSHAIL on this autonomously", "do the whole change for me and ask only at the gates", "drive this end-to-end without me", "orchestrate this hands-off". Runs marshail-driver inline in the current session, coming back to the user only at approval gates and key decisions. For collaborative or interactive runs, or when the involvement level is not stated, use marshail-drive instead.
---

# marshail-drive-end-to-end (fallback — no-subagent environments)

Run the [`marshail-driver`](../../agents/marshail-driver.md) workflow inline in the current session at the hands-off involvement level, for tools that don't support custom subagents.
The agent file is the source of truth for the behavior; this skill only describes how it gets activated.

## Behavior

Run `marshail-driver` inline at the **hands-off** involvement level: agree scope once, then run the stages end-to-end and return to the user only at approval gates and for decisions that genuinely need them.
Stage work runs through the fallback stage skills, since no spawn tool is available.

Triggers are mutually exclusive with [`marshail-drive`](../../skills/marshail-drive/SKILL.md), which runs the driver inline for collaborative / interactive runs and resumes; when the user's involvement level is not stated, `marshail-drive` applies.

## Call contract

- **Agent (run inline):** `marshail-driver`
- **Context needed:** the user's prompt describing the change and the explicit hands-off instruction; the repo with `.marshail/` initialized.
- **Produces:** the full canonical artifact chain in the working folder (`change-brief.md` → `repo-recon.md` → optional `architecture-notes.md` → `delivery-plan.md` → code + phase logs + phase learnings → `verification-report.md` → `rollout-note.md` → `learning-rollup.md`); approval requests surfaced in the conversation only at gates and key decisions; a stage-summary report.
- **Then:** review the stage summary and pursue any flagged follow-ups.

For one-off stage runs, prefer the per-stage fallback `marshail-<x>` skills directly instead of the driver.

