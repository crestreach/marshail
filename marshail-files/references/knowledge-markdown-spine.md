# Knowledge Implementation — MARSHAIL Markdown Spine

The default MARSHAIL knowledge implementation is **MARSHAIL Markdown Spine**.
It stores canonical knowledge as small markdown files under `.marshail/knowledge/`, organized as a hierarchical, progressively-disclosed tree.

This implementation satisfies the general [knowledge contract](knowledge-contract.md), which carries the goals, the two-tree separation, and the read-order rationale.
This file is the **reading guide**: what any agent needs to find, read, and understand Spine knowledge, and to drop a note into the inbox.
It is part of the **standard** load tier (see [activation-protocol](activation-protocol.md)), so it is kept short on purpose.
Everything about *maintaining* the tree — organizing dimension, depth and code-derivation policy, conventional layout, index regeneration, size limits, splitting / merging, staleness detection, and the update protocol — lives in the separate **maintenance guide**, [knowledge-markdown-spine-maintenance.md](knowledge-markdown-spine-maintenance.md), loaded only by agents that maintain knowledge (the **full** tier).
Together the two files are the single source of truth for the Spine; other MARSHAIL files refer here instead of restating them.

## The spine: a recursive tree of nodes

"Spine" is the always-loaded index backbone — the root `INDEX.md` plus the folder indexes — that holds the body of knowledge together.
Topic files hang off the spine and are pulled in only when needed.

Every node in the tree is one of:

- **Topic** — a single markdown file `<slug>.md` (frontmatter + body): one coherent unit of knowledge.
- **Group** — a folder `<slug>/` holding an `INDEX.md` (its sub-index) plus child nodes.

Groups nest **to any depth**: a child of a group can itself be a group.
There is no fixed number of levels — the tree is as deep as the knowledge needs and no deeper.
How knowledge is grouped at each level (the **split dimension**) is chosen per group and recorded in that group's `INDEX.md`.

**Root entry:** `.marshail/knowledge/INDEX.md`.
Read order is always: root `INDEX.md` → the group `INDEX.md` for the area you need → the specific topic file.
Load the cheap index first and descend only into the branch you need; use each entry's summary to decide whether to open it.
Each `INDEX.md` lists its node's direct children as `- [path](path): summary`, most important first; the root lists only the top-level areas, but every file stays reachable through the hierarchy.

Two locations are fixed by the knowledge contract: `decisions/` holds decision records (lightweight ADRs, `adr-NNNN-<slug>.md`) and `learn/inbox/` holds raw notes awaiting promotion.

## Frontmatter

Every topic and every `INDEX.md` starts with:

```yaml
---
id: domains/payments/contracts        # stable slug, matches path
kind: overview|architecture|logic|contract|schema|convention|guide|reference|decision|index
summary: One-line description (used verbatim in the parent INDEX.md).
repo_paths:                           # scope: the code this topic analyzes
  - "src/billing/**"
  - "src/payments/**"
parent: domains/payments/INDEX        # optional
children: []                          # optional
links: [repo/architecture]            # optional cross-refs
importance: high|medium|low
confidence: high|medium|low
updated: 2026-04-26                   # ISO date
verified_against_commit: 0a3f75e      # short SHA
---
```

- Required: `id`, `kind`, `summary`, `importance`, `confidence`, `updated`.
- Recommended: `verified_against_commit`.
- **Required for any code-derived topic: `repo_paths`** pointing at the *specific* files / classes the topic analyzes (not just a top-level `module/**` glob) — this is what lets a later agent jump straight to the code and what drives precise staleness detection.
- `confidence: high` means the topic was derived from reading the code; `medium` means it was inferred from docs and not yet verified against code (the body says so).
- `updated` + `verified_against_commit` together are the topic's **freshness marker**: when, and against which commit, its claims were last verified.

### Kinds

`kind` classifies what a file describes, so an agent can tell at a glance how to use it:

- `overview` — what something is: purpose, scope, orientation.
- `architecture` — structure, components, boundaries, and how the pieces fit and flow.
- `logic` — behavior: business rules, invariants, algorithms.
- `contract` — interfaces between parts: APIs, RPC / CLI signatures, events / topics.
- `schema` — data shapes: database schemas, message / payload formats, config / types.
- `convention` — repo-specific standards and patterns.
- `guide` — procedures: build / test / run, runbooks, how-to steps.
- `reference` — factual lookups and maps (e.g. entrypoints, command lists).
- `decision` — a decision record with its rationale (ADR).
- `index` — a folder or sub-index (`INDEX.md`) file.

## Cross-references

Knowledge files may cross-reference other knowledge content / files, in two ways:

- the `links:` frontmatter field — a list of related knowledge ids (e.g. `links: [repo/architecture, domains/payments/contracts]`);
- inline relative markdown links in the body, pointing at other topics, decisions, or indexes within `.marshail/knowledge/`.

Prefer a cross-reference over duplicating content: when a topic needs facts owned by another topic, link to it and keep each fact in one place.
Agents follow cross-references only as needed (progressive disclosure applies to links too).

## Staleness while reading

A topic may be out of date: its `repo_paths` may have changed since `verified_against_commit`, or its facts may no longer match the code.
If an agent using a topic notices this, it treats the entry as stale and re-researches the topic — asking [`marshail-researcher`](../agents/marshail-researcher.md) for a fresh note and routing the refresh through the curator — rather than trusting the stale entry.
Systematic staleness *detection* is the curator's job (see the maintenance guide).

## Writing an inbox note

Agents that discover reusable knowledge mid-process (per `knowledge.capture_during_process`) do not edit canonical topics; they drop a **knowledge-shaped note** into `.marshail/knowledge/learn/inbox/`.
A note is a single markdown file with the topic frontmatter above — `id` (proposed), `kind`, `summary`, `repo_paths`, `importance`, `confidence`, `updated`, `verified_against_commit` — and a condensed, source-linked body; `parent` / `children` are left for the curator.
The curator (`from-learning`) decides where the note lands, fills in the hierarchy, and regenerates the indexes.
