---
name: marshail-delegate-to-driver
description: Run a whole MARSHAIL change end-to-end, hands-off. Delegate ONLY when the user wants the driver to run autonomously and come back just at approval gates and key decisions — "run MARSHAIL on this autonomously", "do the whole change for me and ask only at the gates", "drive this end-to-end without me", "orchestrate this hands-off". The subagent agrees scope, detects current stage from on-disk artifacts, dispatches per-stage agents, and surfaces approval gates. Do NOT use for collaborative or interactive runs, for resuming a change, or when the involvement level is not stated — use marshail-drive for those (it runs the driver inline so it can talk to the user).
---

# marshail-delegate-to-driver

Delegate this to the [`marshail-driver`](../../agents/marshail-driver.md) subagent — **only for a hands-off end-to-end run**.
Do **not** inline the orchestration — the driver owns it.

A spawned driver cannot talk to the user: each approval gate or question comes back here, is relayed to the user, and the driver is re-dispatched with the answer (it resumes from `.marshail/work/<change-id>/`).
That relay is acceptable only when there are few interaction points, which is why this skill's triggers are limited to hands-off runs; for anything interactive, [`marshail-drive`](../marshail-drive/SKILL.md) runs the driver inline instead (see [marshail.md § How an agent runs](../../marshail.md#how-an-agent-runs)).
The two skills' triggers are mutually exclusive.

## Call contract

- **Subagent:** `marshail-driver`
- **Pass:** the user's prompt describing the change and the explicit hands-off instruction; the repo with `.marshail/` initialized; `.marshail/config.yml`.
- **Expect back:** the full canonical artifact chain in the working folder (`change-brief.md` → `repo-recon.md` → optional `architecture-notes.md` → `delivery-plan.md` → code + phase logs + phase learnings → `verification-report.md` → `rollout-note.md` → `learning-rollup.md`); approval requests and open questions to relay; a stage-summary report.
- **On result:** relay any approval request or question to the user and re-dispatch the driver with the answer; at the end, review the stage summary and pursue any flagged follow-ups.

For one-off stage runs, prefer the per-stage `marshail-delegate-to-*` skills directly instead of the driver.
