# Knowledge Implementation — MARSHAIL Markdown Spine: maintenance guide

This is the **maintenance guide** for the [MARSHAIL Markdown Spine](knowledge-markdown-spine.md) knowledge implementation.
It holds everything an agent needs to **create, update, split, merge, rebuild, or re-index** the markdown tree: the organizing dimension, the depth and code-derivation policy, the conventional layout, index rules, size limits, staleness detection, and the update protocol.

It is loaded **only as needed** — by the **full** load tier (see [activation-protocol](activation-protocol.md)), i.e. the knowledge curator and any agent that maintains canonical knowledge.
Agents that only read knowledge, or that drop a note into the inbox, need the reading guide alone.
The reading guide ([knowledge-markdown-spine.md](knowledge-markdown-spine.md)) defines the node model, the frontmatter, and the cross-reference rules this guide builds on; this file does not restate them.

## Organizing dimension (chosen, not fixed)

The tree is **not** a fixed schema.
How knowledge is grouped at each level — its **split dimension** — is chosen by the curator (`init` / `rebuild`) to be whatever makes the knowledge easiest for a later agent to find:

- by **subsystem / service / module**,
- by **domain** (area of the product),
- by **layer** (api / domain / data / infra),
- by **concern** (logic, contracts, tests, hotspots, conventions),
- by **feature**, **entity**, **API surface**, **lifecycle**, or **time** — or any other dimension that fits.

Rules:

- The dimension may **differ between siblings and between levels**: one group may split by subsystem while another splits by concern.
- Record the chosen dimension in the group's `INDEX.md` so later agents — and any re-split — understand the grouping.
- A `rebuild` (or a `rescan` review) may **re-split a group along a different dimension** when a better one emerges; surface that as its own change.
- When a group becomes trivially small, **merge** it back into its parent.

## Depth and code derivation (not optional)

Knowledge is the **cached result of a real code scan**. `init` and `rebuild` MUST derive content by reading the code, not by transcribing existing prose docs (`AGENTS.md`, READMEs). Existing docs are corroborating input and a fast way to find the right files — they are never the sole source, and any claim taken from them is verified against the code.

### Entrypoint-driven scan

Start from the system's **entrypoints** and follow the call paths inward:

- deployable units (WARs / services / binaries), HTTP / REST controllers, RPC / SOAP endpoints, message / event consumers, scheduled jobs, CLIs, and public library APIs;
- from each entrypoint, trace into the modules and classes that do the real work, recording the path concept → entrypoint → key types → data / IO.

The goal is that a later agent can answer "where does X happen and how?" from the knowledge alone.

### Depth is decided locally, per node — not a fixed taxonomy

There is **no fixed set of levels** and no enumerated tier definitions. Depth is decided **locally by the agent at each node**, recursively: at every node, judge — from the code's complexity and from how a future agent will look for the information — whether the node is a single topic file or should expand into a group (a folder with its own `INDEX.md` plus child nodes), then repeat that judgment at each child. The tree is as deep along each branch as that branch needs, and no deeper. This is the same recursive nesting described in the reading guide (*The spine: a recursive tree of nodes*) and in *Organizing dimension* above, applied at bootstrap — not only when a file later outgrows a size cap.

For a large repository, **multiple levels are expected, not exceptional.** A flat one-topic-per-module map is almost always too shallow: the high-value, complex areas — for example several distinct search strategies in one module, more than one persistence backend, an expression engine, a state machine, or a non-trivial end-to-end flow — each warrant their own deeper node, derived from the code.

### Functional, not just structural

Organize by **what the code does**, not only by Maven / package structure. When one module contains several distinct capabilities (multiple search strategies, multiple persistence backends, several action handlers), give **each capability its own node** rather than a single module overview — group by feature / concern / strategy where that is how an agent will look for it.

### Multi-level by default

The tree MUST use the hierarchy, not collapse to one level. A non-trivial area is a group (folder + `INDEX.md`) with child topics, and those children may themselves be groups. Use the size caps as a *floor for splitting*, not as permission to stay shallow — an area can warrant its own deeper nodes well before it hits `topic_max_lines`, when those nodes map to how agents will search.

### Confidence and provenance

A topic derived from reading the code is `confidence: high`; one inferred from docs but not yet verified against code is `confidence: medium` and should say so in the body. Record the key source files in `repo_paths` so the topic is both verifiable and re-findable.

`knowledge.scan_depth` in `.marshail/config.yml` (`shallow` / `standard` / `deep`) sets the agent's default depth bias; the per-node decision above still applies — go deeper than the default for complex / high-value areas and shallower for trivial ones.

## Conventional starting layout (a default, adaptable)

`init` typically starts from the layout below, then adapts it to the repo.
Treat it as a sensible default, **not** a required shape — areas may be renamed, split further, merged, or organized along a different dimension.

```text
.marshail/knowledge/
  INDEX.md            # always-loaded root index: links the top-level areas
  repo/               # repo-wide facts not owned by a single area
    INDEX.md
    overview.md       # what this repo is; top-level layout
    architecture.md   # high-level shape, layering, key flows
    subsystems.md     # map of subsystems / domains and their boundaries
    entrypoints.md    # handlers, jobs, CLIs, public APIs
    build-test-run.md # canonical build / test / run commands
    conventions.md    # repo-specific conventions (only if not already in synced rules)
  domains/            # deep knowledge per subsystem / domain — one group each
    <area>/
      INDEX.md        # sub-index; records this group's split dimension
      overview.md     # what this area is, its boundaries, dependency edges, entrypoints
      <module-or-capability>/        # expand a non-trivial module OR a distinct capability into its own group
        INDEX.md
        purpose.md    # what it is for
        logic.md      # behavior, rules, invariants (derived from the code)
        contracts.md  # APIs, schemas, events it exposes / consumes
        data.md       # persistence / IO / external calls
        hotspots.md   # risky / complex / frequently-touched spots
        tests.md      # test seams and coverage notes
        <flow-or-component>.md       # a focused topic for a genuinely complex flow / component
  decisions/          # lightweight ADRs
    adr-NNNN-<slug>.md
  learn/
    inbox/            # raw phase learnings awaiting promotion (consumed by from-learning)
```

`domains/<area>/` is one **group** per subsystem / domain; any of its topics may itself become a group when it grows (e.g. `domains/payments/logic/` with its own sub-index).
`decisions/` and `learn/inbox/` are fixed parts of the pipeline (the [knowledge contract](knowledge-contract.md) requires both).
Knowledge content covers code facts, logic, architecture, design rationale, decisions, and conventions — not just code.

This shape is **illustrative, not a required schema** — names, kinds, and grouping are chosen per area (see *Organizing dimension*). The depth shown is the **expected shape for non-trivial areas**, not an upper bound and not a fixed set of tiers: a trivial module may stay a single short topic, while a complex or multi-capability area is expanded — recursively, as deep as the code warrants — with the decision made locally at each node (see *Depth and code derivation (not optional)*).

## Index rules

- **Discovery is the source of truth.**
  A directory walk plus frontmatter defines what exists; a file dropped from an `INDEX.md` is not lost.
- **Every `INDEX.md` is regenerated**, never hand-edited except to fix a bug in the index logic itself.
- Each `INDEX.md` lists its node's **direct children** as `- [path](path): summary`, ordered by `importance` then tree position.
- The root `INDEX.md` lists the **top-level areas** only, not every leaf.
  This is progressive disclosure, not omission: every file stays reachable through the hierarchy (root → group `INDEX.md` → topic), so an agent loads the cheap root first and descends only into the branch it needs.
- On a group `INDEX.md`, record the group's **split dimension** (in the body or a `dimension:` field) so the grouping is self-describing.

## Size limits, splitting, and merging

- Caps live in `.marshail/config.yml` under the `markdown_spine:` section: `root_index_max_lines`, `subindex_max_lines`, `topic_max_lines`.
- When a **topic** exceeds `topic_max_lines`, convert it into a **group**: a folder with an `INDEX.md` plus subtopic files, split along a chosen dimension (see *Organizing dimension*).
- When an **`INDEX.md`** exceeds its cap, split the area it covers further.
- Splits **recurse with no fixed depth**; merge trivially-small groups back into their parent.
- When a unit is split, merged, renamed, or archived, update the cross-references that point at it (`links:` fields and inline links) so they stay valid.

## Staleness detection

A file is **stale** when any path in its `repo_paths` has changed in git between `verified_against_commit` and current HEAD.
Detection is on demand via `marshail-knowledge-maintain` (mode `from-changes` or `rescan`); there are no git hooks.
`knowledge.rescan_period_days` in `.marshail/config.yml` is advisory only — it suggests how often a `rescan` should be requested.

A refreshed topic is re-verified **by re-reading the code**, not by re-dating it: update the body where the code moved on, then stamp `updated` and `verified_against_commit`.

## Update protocol

All writes follow `knowledge.autonomy` in `.marshail/config.yml`:

- `review`: produce a unified diff and wait for human approval.
- `auto`: apply the update directly and report the changed files.

Update paths:

- `marshail-knowledge-init` performs an **entrypoint-driven code scan** and creates the initial tree, expanded to whatever depth each area warrants (decided locally per node, see *Depth and code derivation*), then generates the indexes.
- `marshail-knowledge-maintain from-changes` re-verifies files whose `repo_paths` intersect changed paths and refreshes touched indexes.
- `marshail-knowledge-maintain from-learning` promotes approved learning items from `learn/inbox/` into canonical knowledge and drops the rest (see [promotion-rules](promotion-rules.md)).
  An inbox note already carries Spine frontmatter (see the reading guide → *Writing an inbox note*); promotion fills in `parent` / `children`, places the file, and regenerates the affected indexes.
- `marshail-knowledge-maintain rescan` checks every knowledge file for staleness and size-limit violations.
- `marshail-knowledge-branch-merge` reconciles divergent knowledge updates across branches: union `repo_paths`, newest-wins on `updated` / `verified_against_commit`, merge bodies, and flag genuine conflicts (same unit created on both sides, contradictory summaries) under `review`.
- `marshail-knowledge-rebuild` re-runs the entrypoint-driven scan on HEAD and may restructure the tree after major repo changes — including deepening under-analyzed areas and re-splitting groups along a better dimension.

## Possible future extensions

These are not implemented; they are recorded here as directions the implementation may grow into.
The markdown tree remains the canonical source of truth even if any of them is added.

- **Generated maps** — a `generated/` group of mechanically-built maps (e.g. `symbol-map.md`, `dependency-map.md`) produced with tree-sitter / ctags / LSP, useful for medium-to-large repos.
- **Vector retrieval sidecar** — an embedding-based index alongside the markdown tree for fuzzy recall.
- **Cross-repo / user-scoped knowledge** — knowledge shared beyond a single repository. The current scope is repo-only.
