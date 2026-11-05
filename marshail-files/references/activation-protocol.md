# Startup context-loading protocol — reference

Shared by every MARSHAIL agent and skill.
It answers one question: **what does an agent load at the start of a session so it can work without researching from zero?**

Each agent names its own **load tier** below in its own definition; this file defines what each tier means so the agents stay consistent.

## Load tiers

- **Minimal** — the tool instruction file (synced from `.marshail/AGENTS.md`), `.marshail/ENTRYPOINT.md`, and `.marshail/marshail-override.md` when present.
  Enough to know the process exists and where things live.
  Used by lightweight / procedural agents.
- **Standard** — minimal, plus `.marshail/config.yml`, the active knowledge implementation's **reading guide** (`knowledge.representation_ref`), and the knowledge **root entry** that guide names (default: `.marshail/knowledge/INDEX.md`), descending into area indexes and topics only as needed.
  The reading guide is short by design; it tells the agent how the knowledge is laid out, what the metadata means, and how to write an inbox note.
  Do **not** load the implementation's maintenance guide at this tier — it covers only how the tree is built and kept in shape.
  Used by most stage agents.
- **Full** — standard, plus the knowledge contract (`knowledge.contract_ref`), the implementation's **maintenance guide** (linked from the reading guide), and the relevant per-change artifacts from the working folder (`.marshail/work/<change-id>/`).
  Used by knowledge agents and any agent that writes canonical knowledge.

An agent loads the **smallest tier** that lets it do its job, then descends into specific knowledge topic files or artifacts on demand.
If knowledge is missing or stale, it asks the researcher for a fresh note rather than guessing.

## `marshail.md` is reference, not startup context

The canonical spec `marshail.md` is large and belongs to **no load tier**; `ENTRYPOINT.md` is the compact stand-in that every tier loads instead.
Only [`marshail-helper`](../agents/marshail-helper.md) reads the spec in full — answering questions about it is its job.
Every other agent and skill treats links into `marshail.md` as **section references**: follow one only when the task needs that section, locate the linked heading, and read to the next heading of the same level — never load the whole file.
Links into the spec therefore carry a section anchor (e.g. `marshail.md#6a-implement`).

## Logging and resume

Every change has a working folder `.marshail/work/<change-id>/`, created by whichever component starts the change (the driver, or the first specialist agent / skill in the direct model) together with the one-line pointer `.marshail/work/current`, so the active change never has to be guessed.
A workbench session uses the same layout with a `wb-`-prefixed id and `workbench-session.md` as its root artifact (see [marshail.md § Workbench mode](../marshail.md#workbench-mode)).

The folder holds the artifact chain plus `logs/`, which keeps three kinds of file so that resuming stays cheap:

- **`resume.md`** — one short "where are we" file: current stage (or, for a session, `Mode: workbench` on the first line and `Return to change: <change-id>` on the second when the session is a detour), active phase / cycle, next action, open decisions / questions.
  Rewritten and compacted on every update, never appended; the only log read by default on resume.
- **`<agent>.log.md`** — one append-only log per agent role (e.g. `planner.log.md`).
  Detailed; read only when that role's thread must be reconstructed.
- **`stage-<n>-<name>.changelog.md` / `phase-<n>.changelog.md`** — the changelog of record: one per lifecycle stage, plus one per delivery-plan L1 phase during Implement (optionally `phase-<n>-<slug>`).

**Run sections.**
An agent can run many times for one change, so each invocation works in a run section of its `<agent>.log.md`:

```
## Run <n> — <ISO-8601 timestamp>
```

The caller passes no run id.
An agent marks its section **finished** when its work is done and leaves it open while in progress; on the next invocation it reads the last section — finished → open a fresh run, unfinished → resume that run.
The prompt overrides this heuristic ("start over", "continue run 2").
Previous runs may be read for continuity but are never overwritten.

**On activation**, an agent reads `resume.md` first and descends into its own log or the changelogs only if it needs more detail.
**Before handing back**, every agent that changed state appends to its run section, refreshes `resume.md`, and — when its work is actually complete — marks the section finished, so the next dispatch (by the driver or directly by the user) continues from disk.
