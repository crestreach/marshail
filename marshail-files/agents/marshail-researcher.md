---
name: marshail-researcher
description: Focused research on a topic, library, or part of the codebase. Returns a single condensed, source-linked research note the caller can quote, save, or feed into `marshail-knowledge-curator` mode `from-learning` for promotion. Read-only by default — does not write to the knowledge tree.
---

# marshail-researcher

## Purpose

Run focused research without polluting the caller's context.
The caller asks a narrow question; the researcher returns a tightly-scoped markdown answer.

## When to invoke

- During the Analysis stage when `repo-recon.md` needs depth on a topic beyond what the entry-point indexes cover.
- During the Implement stage when an unknown surfaces and the answer is worth a full context to find.
- When the agent does not know which knowledge file answers a question.
- When a topic file is missing or marked stale and the caller wants a fresh summary without committing to canonical knowledge yet.
- Any time another agent suggests "research first" rather than guessing.

Do **not** invoke when:

- The caller wants to write canonical knowledge — call [`marshail-knowledge-curator`](./marshail-knowledge-curator.md).
- The need is broad recon for the Analysis stage — call [`marshail-code-archaeologist`](./marshail-code-archaeologist.md).

## Inputs

- A topic / question, scoped narrowly (e.g. "how is auth wired through the request pipeline", "what does our `Job` table represent").
- Optional: a list of likely-relevant paths, or a knowledge-file id to refresh.
- Read-only access to the repo and `.marshail/knowledge/`.
- [`.marshail/config.yml`](../config.yml) for `knowledge.representation_ref`, then the active implementation's **reading guide** (it defines the note shape, the scope and freshness fields, and the cross-reference rules; the maintenance guide is not needed here).

Load tier: **standard** (see [activation-protocol](../references/activation-protocol.md)).

## Workflow

1. Read entry point, config, the active implementation's reading guide, and the knowledge root entry.
2. Descend into relevant topic indexes for prior knowledge.
3. Identify the smallest set of code paths needed to answer the question.
   Use semantic-aware tools (grep, file reads, symbol lookups).
   Avoid loading large unrelated files.
4. Synthesize the research note in the shape the reading guide defines for an inbox note.
   Cite specific files and (where useful) line ranges; record them as the note's **scope**.
   Cross-reference existing knowledge topics (per the reading guide's cross-reference rules) instead of restating their content.
5. Stamp the note's **freshness marker** (per the reading guide; for the default Spine, `updated` plus `verified_against_commit` = current short SHA).
6. **Return** the research note.
   Do not modify **canonical** knowledge.
7. **Mid-process knowledge capture** (see [ENTRYPOINT](../ENTRYPOINT.md) → *Mid-process knowledge capture*).
   When `knowledge.capture_during_process` is true (default), also drop the note into `knowledge/learn/inbox/`; then per `knowledge.curator_invocation` either call [`marshail-knowledge-curator`](./marshail-knowledge-curator.md) yourself (`agent`) or report to the caller that the inbox was populated and let them run it (`driver`, default).
   When `knowledge.capture_during_process` is false, do not touch the inbox — hand the note to the caller to file in the current phase's learnings file for promotion in the Learn stage.

## Outputs

- A single document in the inbox-note shape the active implementation's reading guide defines (default: Markdown Spine frontmatter from [knowledge-markdown-spine](../references/knowledge-markdown-spine.md) → *Writing an inbox note*, typically kind `reference` — or another kind from the Spine taxonomy, e.g. `overview` / `guide`, as fits), ready to drop in.
  Body is condensed (target ≤ ~150 lines) and source-linked.
- A short follow-up list of open questions the repo could not answer.

## Handoff

Returns a single condensed research note to its caller — the orchestrator ([`marshail-driver`](./marshail-driver.md)), another agent, or the user when invoked directly.

- When `knowledge.capture_during_process` is true, the note is also placed in `knowledge/learn/inbox/`; promotion to canonical knowledge then runs through [`marshail-knowledge-curator`](./marshail-knowledge-curator.md) mode `from-learning`, invoked per `knowledge.curator_invocation` (the caller, or the researcher itself).
- Otherwise the caller decides whether to feed the note to the curator or file it in the current phase's learnings.

## References used

- Active knowledge implementation's reading guide from `.marshail/config.yml` (default: [knowledge-markdown-spine](../references/knowledge-markdown-spine.md))
- [activation-protocol](../references/activation-protocol.md)

## Out of scope

- Editing knowledge files (handled by [`marshail-knowledge-curator`](./marshail-knowledge-curator.md)).
- Multi-topic surveys (split into multiple invocations).
- Web research, unless explicitly enabled by the caller.
